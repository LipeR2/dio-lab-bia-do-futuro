# Base de Conhecimento

## Dados Utilizados

A SOFIA utiliza estritamente os arquivos fornecidos na pasta data do repositório para contextualizar suas respostas e garantir que nenhuma informação ou produto financeiro seja alucinado.

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
| `historico_atendimento.csv` | CSV | Contextualizar interações anteriores |
| `perfil_investidor.json` | JSON | Personalizar recomendações |
| `produtos_financeiros.json` | JSON | Sugerir produtos adequados ao perfil |
| `transacoes.csv` | CSV | Analisar padrão de gastos do cliente |

> [!TIP]
> **Quer um dataset mais robusto?** Você pode utilizar datasets públicos do [Hugging Face](https://huggingface.co/datasets) relacionados a finanças, desde que sejam adequados ao contexto do desafio.

---

## Adaptações nos Dados

> Os dados mockados originais do repositório não sofreram qualquer tipo de modificação ou expansão. A SOFIA consome as tabelas e arquivos JSON em seu formato nativo, estruturando sua inteligência e filtros de segurança diretamente através da engenharia de prompt e da lógica do código em Python, sem alterar as colunas ou chaves originais.

---

## Estratégia de Integração

### Como os dados são carregados?
> Os arquivos CSV e JSON são carregados na memória do servidor no início da sessão do usuário utilizando o gerenciador de estado e o cache do Streamlit (@st.cache_data). O Pandas realiza a leitura das tabelas cronológicas, enquanto a biblioteca padrão de JSON do Python processa os dados de perfil e o catálogo de produtos.


### Como os dados são usados no prompt?
> Os dados vão no system prompt? São consultados dinamicamente?

Os dados são consultados de forma dinâmica a cada turno de conversa. O script orquestrador em Python lê o estado atual dos arquivos e os converte em texto estruturado. Esse texto é injetado diretamente dentro do system prompt (contexto de sistema) da LLM antes do envio da mensagem do usuário. Dessa forma, a IA toma decisões sabendo exatamente o saldo, as últimas movimentações e o perfil atual do cliente.

---

## Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.
>Abaixo está o exemplo de como os dados originais são formatados textualmente e entregues como contexto para a tomada de decisão da SOFIA:

```
Dados do Cliente (perfil_investidor.json):
- Nome: João Silva
- Perfil: Moderado

Catálogo Oficial (produtos_financeiros.json):
- CDB Liquidez Diária (Renda Fixa | Risco: Baixo)
- Fundo de Ações XPTO (Renda Variável | Risco: Alto)

Últimas Transações (transacoes.csv):
- 01/11: Saída - Supermercado - R$ 450,00
- 03/11: Saída - Streaming - R$ 55,00
- 05/11: Entrada - Salário - R$ 4.000,00

Histórico de Atendimento (historico_atendimento.csv):
- 02/11: Cliente perguntou sobre como poupar mais este mês.
```
