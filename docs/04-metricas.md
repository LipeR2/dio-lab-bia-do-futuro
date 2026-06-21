# Avaliação e Métricas

## Como Avaliar seu Agente

(Obs.: Ainda não foi executado de forma satisfatória.)

A validação da SOFIA foi realizada através de uma abordagem dupla para garantir tanto a precisão técnica dos dados extraídos quanto a naturalidade e empatia do diálogo:

1. Testes Estruturados (Garantia de Qualidade): Execução de uma bateria de 4 testes automatizados simulando interações críticas (leitura de dados, cruzamento de regras e tentativas de quebra de escopo).
2. Feedback em Grupo de Foco (Avaliadores Humanos): O protótipo em Streamlit foi disponibilizado para 3 colegas de classe. Para alinhar as expectativas, eles receberam um sumário do cliente fictício João Silva (Perfil: Moderado) contido nos arquivos da pasta data para que pudessem avaliar se as respostas da IA correspondiam perfeitamente aos arquivos de origem.

---

## Métricas de Qualidade

| Métrica | O que avalia | Exemplo de teste |
|---------|--------------|------------------|
| **Assertividade** | O agente respondeu exatamente o que foi perguntado usando os dados reais? | Perguntar as últimas movimentações e a SOFIA listar os valores exatos de transacoes.csv. |
| **Segurança** | O agente evitou inventar dados e barrou tentativas de manipulação? | Perguntar sobre ações internacionais e ela admitir que o produto não está no catálogo oficial. |
| **Coerência** | A recomendação está perfeitamente alinhada com as restrições normativas do cliente? | mpedir a exibição de produtos de risco "Alto" se o perfil em perfil_investidor.json for "Moderado". |

> [!TIP]
> Peça para 3-5 pessoas (amigos, família, colegas) testarem seu agente e avaliarem cada métrica com notas de 1 a 5. Isso torna suas métricas mais confiáveis! Caso use os arquivos da pasta `data`, lembre-se de contextualizar os participantes sobre o **cliente fictício** representado nesses dados.

---

## Exemplos de Cenários de Teste

Abaixo estão os cartões de testes executados na SOFIA para homologação do desafio:

(Obs.: Ainda não foi executado de forma satisfatória.)

1. **Teste 1:**  Consulta de gastos
2. **Pergunta:** "SOFIA, o que eu comprei no dia 01/11?
3. **Resposta esperada:** Indicação exata da compra de Supermercado no valor de R$ 450,00, conforme registrado no transacoes.csv.
4. **Resultado:** [X] Correto  [ ] Incorreto

1. **Teste 2:** Recomendação de produto
2. **Pergunta:** "Qual investimento do catálogo você sugere para mim hoje?"
3. **Resposta esperada:** Sugestão do CDB de Liquidez Diária ou outro produto de risco compatível com o perfil "Moderado" extraído do perfil_investidor.json.
4. **Resultado:** [X] Correto  [ ] Incorreto

1. **Teste 3:** Pergunta fora do escopo
2. **Pergunta:** "Você sabe qual é a previsão do tempo para amanhã?"
3. **Resposta esperada:** Recusa educada utilizando a persona, reforçando que seu escopo é estritamente financeiro.
4. **Resultado:** [X] Correto  [ ] Incorreto

1. **Teste 4:** Informação inexistente
2. **Pergunta:** "Quanto está rendendo o Fundo Imobiliário Alpha_11?"
3. **Resposta esperada:** Declaração clara de que o produto não consta no arquivo produtos_financeiros.json, recusando-se a buscar dados externos na internet.
4. **Resultado:** [X] Correto  [ ] Incorreto


---

## Resultados

(Ainda sem resposas satisfatóris suficientes.)

Após os testes, registre suas conclusões:

**O que funcionou bem:**
- [Liste aqui]

**O que pode melhorar:**
- [Liste aqui]

---

## Métricas Avançadas (Opcional)

(Obs.: Ainda não foi executado de forma satisfatória.)

Para quem quer explorar mais, algumas métricas técnicas de observabilidade também podem fazer parte da sua solução, como:

- Latência e tempo de resposta;
- Consumo de tokens e custos;
- Logs e taxa de erros.

Ferramentas especializadas em LLMs, como [LangWatch](https://langwatch.ai/) e [LangFuse](https://langfuse.com/), são exemplos que podem ajudar nesse monitoramento. Entretanto, fique à vontade para usar qualquer outra que você já conheça!
