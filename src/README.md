# Código da Aplicação

Esta pasta contém o código do seu agente financeiro.

## Estrutura Sugerida

```
meu-projeto/
├── data/
│   ├── historico_atendimento.csv
│   ├── perfil_investidor.json
│   ├── produtos_financeiros.json
│   └── transacoes.csv
└── src/
    ├── app.py              # Interface Visual (Streamlit)
    ├── agente.py           # Core do Orquestrador e RAG da SOFIA
    ├── config.py           # Gerenciamento de chaves e caminhos
    └── requirements.txt    # Dependências do Python
```

## 1. Arquivo de Dependências (src/requirements.txt)

```
streamlit>=1.35.0
google-genai>=0.1.1
pandas>=2.0.0
python-dotenv>=1.0.0
```

## 2. Arquivo de Configuração (src/config.py)

```
import os
from pathlib import Path
from dotenv import load_dotenv

# Carrega variáveis do arquivo .env se existir
load_dotenv()

# Definição de caminhos absolutos baseados na estrutura do projeto
BASE_DIR = Path(__file__).resolve().parent.parent
DATA_DIR = BASE_DIR / "data"

# Caminhos dos arquivos de dados da SOFIA
PATH_PERFIL = DATA_DIR / "perfil_investidor.json"
PATH_PRODUTOS = DATA_DIR / "produtos_financeiros.json"
PATH_TRANSACOES = DATA_DIR / "transacoes.csv"
PATH_HISTORICO = DATA_DIR / "historico_atendimento.csv"

# Recuperação da API Key do Gemini
GEMINI_API_KEY = os.getenv("GEMINI_API_KEY")

def validar_ambiente():
    """Valida se todos os componentes e chaves necessárias estão presentes."""
    erros = []
    if not GEMINI_API_KEY:
        erros.append("Variável de ambiente 'GEMINI_API_KEY' não foi encontrada.")
    
    arquivos = [PATH_PERFIL, PATH_PRODUTOS, PATH_TRANSACOES, PATH_HISTORICO]
    for arq in arquivos:
        if not arq.exists():
            erros.append(f"Arquivo de dados obrigatório ausente: {arq.name}")
            
    return erros
```

## 3. Arquivo do Mecanismo da IA (src/agente.py)

```
import json
import pandas as pd
from google import genai
from google.genai import types
import config

class SofiaAgente:
    def __init__(self):
        # Inicializa o cliente oficial do Google GenAI utilizando a chave centralizada
        self.client = genai.Client(api_key=config.GEMINI_API_KEY)
        self.model_name = "gemini-2.5-flash"
        
    def _carregar_contexto_local(self) -> str:
        """Lê os arquivos padrão do repositório e monta uma string rica de contexto."""
        try:
            # Leitura do Perfil do Investidor (JSON)
            with open(config.PATH_PERFIL, "r", encoding="utf-8") as f:
                perfil = json.load(f)
                
            # Leitura do Catálogo de Produtos (JSON)
            with open(config.PATH_PRODUTOS, "r", encoding="utf-8") as f:
                produtos = json.load(f)
                
            # Leitura das Tabelas Cronológicas (CSV)
            df_transacoes = pd.read_csv(config.PATH_TRANSACOES)
            df_historico = pd.read_csv(config.PATH_HISTORICO)
            
            # Formatação textual limpa do cenário para não estourar a janela da LLM
            contexto = f"""
            --- PERFIL DO CLIENTE ---
            {json.dumps(perfil, ensure_ascii=False, indent=2)}
            
            --- CATÁLOGO DE PRODUTOS FINANCEIROS HOMOLOGADOS ---
            {json.dumps(produtos, ensure_ascii=False, indent=2)}
            
            --- ÚLTIMAS TRANSAÇÕES DO CLIENTE (CSV) ---
            {df_transacoes.to_string(index=False)}
            
            --- HISTÓRICO DE ATENDIMENTOS ANTERIORES (CSV) ---
            {df_historico.to_string(index=False)}
            """
            return contexto
        except Exception as e:
            return f"Erro ao consolidar base de dados local: {str(e)}"

    def gerar_resposta(self, historico_chat: list) -> str:
        """Orquestra o envio do prompt de sistema + dados locais + histórico de chat."""
        contexto_dados = self._carregar_contexto_local()
        
        system_prompt = f"""
        Você é a SOFIA (Smart Optimizing Financial Intelligence Advisor), uma especialista em inteligência financeira proativa, educativa e acolhedora. Seu objetivo principal é analisar o histórico de transações, o perfil do investidor e o histórico de atendimento do cliente para propor melhorias em sua saúde financeira e sugerir investimentos adequados.

        Siga rigorosamente as diretrizes abaixo para garantir a segurança operacional:

        ## REGRAS DE OURO (Anti-Alucinação):
        1. ANCORAGEM ESTRITA: Baseie suas análises, cálculos e respostas UNICAMENTE nos dados textuais injetados no contexto fornecido abaixo.
        2. TRATAMENTO DE DESCONHECIMENTO: Se o usuário perguntar sobre um produto financeiro, taxa de mercado ou movimentação que NÃO conste explicitamente nos dados fornecidos, responda que não possui essa informação em sua base oficial e ofereça ajuda com o catálogo disponível. Nunca invente ou assuma dados externos (como ações ou criptoativos da internet).
        3. ALINHAMENTO DE PERFIL: Você só pode sugerir produtos listados no catálogo que correspondam exatamente ou fiquem abaixo do nível de risco permitido pelo perfil do investidor do cliente.
        4. CONTINUIDADE: Use o histórico de atendimento para relembrar interações passadas e evitar que o cliente precise repetir dúvidas.

        ## DIRETRIZES DE TOM DE VOZ:
        - Seja empática, clara e utilize metáforas simples antes de introduzir jargões do mercado financeiro.
        - Adote uma postura proativa se notar anomalias nas transações.

        --- CONTEXTO ATUAL DO SISTEMA (DADOS REAIS DO CLIENTE) ---
        {contexto_dados}
        """
        
        # Converte o formato de histórico do Streamlit para o formato aceito pela API do Gemini
        contents = []
        for msg in historico_chat:
            role = "user" if msg["role"] == "user" else "model"
            contents.append(types.Content(
                role=role,
                parts=[types.Part.from_text(text=msg["content"])]
            ))
            
        try:
            # Executa a chamada do modelo de alta velocidade
            response = self.client.models.generate_content(
                model=self.model_name,
                contents=contents,
                config=types.GenerateContentConfig(
                    system_instruction=system_prompt,
                    temperature=0.2 # Baixa temperatura reduz drasticamente a criatividade/alucinação
                )
            )
            return response.text
        except Exception as e:
            return f"Oi! Desculpe, tive um probleminha técnico ao me conectar ao meu servidor de IA: {str(e)}"

```

## 4. Interface da Aplicação (src/app.py)

```
import streamlit as st
import json
import pandas as pd
import config
from agente import SofiaAgente

st.set_page_config(page_title="SOFIA - Inteligência Financeira", page_icon="🤖", layout="wide")

# 1. Validação crítica de arquivos e chaves antes de carregar a tela
erros_ambiente = config.validar_ambiente()
if erros_ambiente:
    st.error("⚠️ Falha na inicialização do sistema. Verifique os seguintes pontos:")
    for erro in erros_ambiente:
        st.markdown(f"- {erro}")
    st.info("💡 Certifique-se de preencher o arquivo `.env` com a sua 'GEMINI_API_KEY' na raiz do projeto e ter a pasta 'data' populada.")
    st.stop()

# 2. Inicialização do Agente no estado da sessão do Streamlit
if "sofia_agente" not in st.session_state:
    st.session_state.sofia_agente = SofiaAgente()
if "messages" not in st.session_state:
    st.session_state.messages = []

# 3. Sidebar - Painel de Auditoria Visual do Cliente (Carrega direto do RAG)
with st.sidebar:
    st.title("📑 Dados Carregados")
    st.markdown("Veja as informações que a SOFIA está consultando localmente em tempo real:")
    
    try:
        with open(config.PATH_PERFIL, "r", encoding="utf-8") as f:
            perf = json.load(f)
        st.subheader("👤 Perfil do Cliente")
        st.markdown(f"**Nome:** {perf.get('nome', 'Não encontrado')}")
        st.markdown(f"**Perfil:** `{perf.get('perfil', 'Não definido')}`")
        
        st.subheader("📊 Extrato de Transações")
        df_t = pd.read_csv(config.PATH_TRANSACOES)
        st.dataframe(df_t, use_container_width=True, hide_index=True)
    except Exception as e:
        st.warning("Não foi possível renderizar a barra lateral de dados.")

# 4. Área Principal do Chatbot
st.title("🤖 SOFIA — Consultora Financeira Ativa")
st.caption("Arquitetura RAG Estrita — Homologada de acordo com as regras da carteira local.")

# Exibe o histórico de mensagens mantido em sessão
for message in st.session_state.messages:
    with st.chat_message(message["role"]):
        st.markdown(message["content"])

# Caixa de texto de entrada do usuário
if prompt := st.chat_input("O que deseja analisar ou investir hoje?"):
    # Registra e exibe a pergunta do usuário
    st.session_state.messages.append({"role": "user", "content": prompt})
    with st.chat_message("user"):
        st.markdown(prompt)
        
    # Processa e gera a resposta contextualizada através do agente SOFIA
    with st.chat_message("assistant"):
        with st.spinner("Analisando bases locais e gerando recomendação segura..."):
            resposta_sofia = st.session_state.sofia_agente.gerar_resposta(st.session_state.messages)
            st.markdown(resposta_sofia)
            
    # Salva a resposta no histórico para manter o contexto na próxima rodada
    st.session_state.messages.append({"role": "assistant", "content": resposta_sofia})
```

## Como Rodar

- Crie um arquivo .env na raiz do seu projeto e insira a sua chave do Gemini:
```
GEMINI_API_KEY=AIzaSyYourActualKeyHere... (Sua chave API)
````

```bash
# Instalar dependências
pip install -r src/requirements.txt

# Rodar a aplicação
streamlit run src/app.py
```
