# 🤖 SOFIA — Smart Optimizing Financial Intelligence Advisor

> Desafio de Projeto: Agente Financeiro Inteligente com IA Generativa para a DIO (Digital Innovation One).

A **SOFIA** é uma agente financeira inteligente e proativa desenvolvida para transformar a relação das pessoas com o próprio dinheiro. Diferente dos aplicativos bancários tradicionais que funcionam apenas como um "retrovisor" passivo de gastos, a SOFIA atua como uma copiloto consultiva e educacional, antecipando necessidades e sugerindo caminhos de otimização patrimonial em tempo real.

---

## 🎯 Caso de Uso e Diferenciais

* **Atuação Proativa:** Analisa o fluxo de caixa recente para encontrar padrões de consumo e espaços para poupar antes do fechamento do mês.
* **Segurança Absoluta (Anti-Alucinação):** Implementa uma arquitetura **RAG Estrito (Retrieval-Augmented Generation)** conectada ao modelo `gemini-2.5-flash`. A SOFIA está programaticamente blindada para responder *apenas* com base nos produtos e dados oficiais homologados pela instituição.
* **Adequação de Perfil (Suitability):** Cruza as opções de investimento do catálogo com as preferências de risco do cliente, bloqueando ativamente sugestões inadequadas ou nocivas ao patrimônio do usuário.

---

## 📁 Estrutura do Repositório

O projeto segue estritamente a árvore de diretórios recomendada para o desafio:

```text
lab-agente-financeiro/
│
├── 📄 README.md                      # Você está aqui (Contextualização da SOFIA)
│
├── 📁 data/                          # Dados locais e mockados do ecossistema
│   ├── historico_atendimento.csv     # Conversas e dores anteriores do cliente
│   ├── perfil_investidor.json        # Nível de risco e preferências do usuário
│   ├── produtos_financeiros.json     # Catálogo oficial de ativos homologados
│   └── transacoes.csv                # Histórico de fluxo de caixa dos últimos dias
│
├── 📁 docs/                          # Documentação detalhada do projeto
│   ├── 01-documentacao-agente.md     # Escopo, persona, tom de voz e diagrama
│   ├── 02-base-conhecimento.md       # Estratégia de integração de dados
│   ├── 03-prompts.md                 # Engenharia de prompts e Few-Shot
│   ├── 04-metricas.md                # Cenários de teste e avaliação de qualidade
│   └── 05-pitch.md                   # Roteiro técnico do pitch de 3 minutos
│
└── 📁 src/                           # Código-fonte do protótipo funcional
    ├── app.py                        # Interface visual e interativa (Streamlit)
    ├── agente.py                     # Motor de IA e RAG (Google GenAI API)
    ├── config.py                     # Validador de ambiente e caminhos locais
    └── requirements.txt              # Dependências de execução do Python
```

---

## 🛠️ Tecnologias Utilizadas

* **LLM Core:** [Google Gemini 2.5 Flash](https://google.dev) (via SDK oficial `google-genai`)
* **Interface Web:** [Streamlit](https://streamlit.io)
* **Processamento de Dados:** [Pandas](https://pydata.org)
* **Orquestração de Contexto:** Python nativo com baixa temperatura (`0.2`) para mitigação completa de alucinações.

---

## 🚀 Como Executar o Protótipo Localmente

### 1. Clonar o Repositório
```bash
git clone https://github.com
cd NOME_DO_REPOSITORIO
```

### 2. Configurar as Variáveis de Ambiente
Crie um arquivo chamado `.env` na **raiz** do seu projeto (mesmo nível deste arquivo README) e adicione a sua chave de API do Google AI Studio:
```text
GEMINI_API_KEY=AIzaSyYourActualGeminiKeyHere
```

### 3. Instalar as Dependências
Recomenda-se o uso de um ambiente virtual (venv). Instale os pacotes necessários rodando:
```bash
pip install -r src/requirements.txt
```

### 4. Iniciar a SOFIA
Execute o comando do Streamlit para abrir o painel de controle e a janela de chat diretamente no seu navegador:
```bash
streamlit run src/app.py
```

---

## 📈 Critérios de Validação Técnico-Comercial

A SOFIA foi testada e aprovada sob três pilares fundamentais descritos na pasta `docs/`:
1. **Assertividade:** Extração correta de valores monetários a partir das linhas do arquivo `transacoes.csv`.
2. **Segurança de Escopo:** Bloqueio e recusa elegante a perguntas sobre meteorologia, esportes ou ativos externos não catalogados (ex: criptomoedas fictícias).
3. **Coerência de Risco:** Filtro de conformidade automatizado impedindo que o cliente receba ofertas desalinhadas com seu perfil.
