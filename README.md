# Análise de Portfólio Amazon Índia: Preço, Engajamento e Satisfação

Análise de como preço e desconto se relacionam com o engajamento e a satisfação dos clientes no portfólio da Amazon Índia, com dashboard em Power BI, análise de sentimento em Python e recomendações para o negócio.
O objetivo principal foi atuar como um time de Dados e Estratégia, extraindo insights acionáveis a partir de uma base massiva de produtos e avaliações da Amazon Índia. O projeto engloba a limpeza e modelagem dos dados em Python, análise de sentimento dos reviews (VADER) e a entrega de um Dashboard Executivo no Power BI para suportar a tomada de decisão da liderança.


Tech Challenge, Fase 4 · Pós-graduação em Data Analytics · FIAP
**Grupo 158:** Marília Aparecida da Silva Lima, Fernando Rosa Salles e Guilherme Alfredo Palitos Alves

---

## Pergunta de negócio

Quais produtos, categorias e estratégias comerciais merecem maior atenção, e quais oportunidades podem ser identificadas a partir dos dados disponíveis?

## Dados

- **Fonte:** [Amazon Sales Dataset](https://www.kaggle.com/datasets/karkavelraja/amazon-sales-dataset), por Karkavelraja J. (Kaggle).
- **Conteúdo:** 1.337 variações de produtos, com preço original, preço com desconto, % de desconto, nota média, número de avaliações, categoria e trechos dos comentários dos clientes.
- **Período:** a base é um retrato de um momento, sem datas.
- **Moeda:** valores originais em rúpias indianas (₹). O dashboard converte para dólar e real com a cotação de 13/11/2026 do Banco Central do Brasil.

## Estrutura do repositório

```
├── README.md
├── Relatório Executivo.pdf          # relatório com análises e recomendações
├── TechChallenge4.pbix              # dashboard em Power BI
├── requirements.txt                 # bibliotecas Python necessárias
├── data/
│   └── amazon.csv                   # arquivo original
├── notebooks/                       # limpeza, sentimento e gráficos em Python
│   └── TechChallenge4.ipynb         # temas citados nos comentários por produto
├── Imagens/                         # criada ao rodar o notebook: gráficos exportados (.png)
└── output/                          # criada ao rodar o notebook: arquivos gerados pela análise
```

> As pastas `Imagens/` e `output/` não estão no repositório: elas são criadas automaticamente na primeira execução do notebook.

## Como reproduzir

### 1. Notebook (Python)

**Localmente (Jupyter ou VS Code):**

```bash
git clone <link-do-repositório>
cd <nome-do-repositório>
pip install -r requirements.txt
```

Abra o notebook **de dentro da pasta `notebooks/`** e execute as células na ordem.

**No Google Colab:**

```python
!git clone <link-do-repositório>
%cd <nome-do-repositório>/notebooks
!pip install -r ../requirements.txt
```

O notebook:
- baixa o léxico do VADER (`nltk`) na primeira execução;
- cria as pastas `Imagens/` e `output/`;
- gera `produtos_sentimento.csv` e `produtos_temas.csv` (separados por `;` e com vírgula decimal, no padrão brasileiro) e os gráficos em `.png`.

## Tratamento dos dados

O principal problema da base: **variações do mesmo produto compartilham as mesmas avaliações.** Linhas diferentes (nome alterado pelo vendedor, ou variações de cor, memória ou conector) têm exatamente o mesmo conjunto de `review_id`. Somá-las inflaria o engajamento.
Como tratamos:
- Mantivemos as linhas originais e agrupamos as variações pelo conjunto de `review_id`: **1.337 variações = 1.186 produtos agrupados**.
- O engajamento total (**16.767.393 avaliações**) é calculado sem duplicatas.
- A análise de sentimento usa uma linha por grupo.
- Um único grupo aparece em duas categorias. Por isso, a soma por categoria é 14.184 avaliações maior que o total.

Tratamento de Strings: Remoção de caracteres de escape, aspas e sujeira de scraping.

Modelagem Dimensional: Aplicação de explode alinhado para separar listas de user_id, user_name e review_id, criando uma Tabela Fato robusta

## Indicadores criados

| Indicador | Definição |
| --- | --- |
| Produtos agrupados | Contagem distinta de `review_id` |
| Engajamento | Número de avaliações, sem duplicatas por grupo |
| Quadrantes | Nota (corte em 4) × engajamento (corte na mediana): Estrela, Joia Escondida, Problemático e A Repensar |
| Faixa de desconto | Sem (0%), Baixo (1–20%), Médio (21–40%), Alto (41–60%) e Muito Alto (61%+) |
| Faixa de preço em dias de salário | Preço com desconto comparado ao piso salarial indiano de ₹178/dia: até 1 dia, até 1 semana (6 dias), até 1 mês (26 dias) e mais de 1 mês |
| Sentimento | Score VADER (−1 a +1) dos comentários: Positivo > 0,5; Misto entre −0,05 e 0,5; Negativo ≤ −0,05 |
| Índice de Reclamação | Proporção de frases com score < −0,05 |
| Temas | 10 temas por palavras-chave: 4 de valor e 6 de problema |

## Dashboard

| Página | Conteúdo |
| --- | --- |
| Visão Executiva | Indicadores gerais, avaliação por categoria e concentração do engajamento (Pareto) |
| Produtos e Categorias | Quadrantes de engajamento × satisfação, Top 10 produtos e perfil por categoria |
| Preços e Estratégia Comercial | Desconto × nota, engajamento por faixa de desconto e desconto por faixa de preço |
| Customer Insights | Sentimento, Índice de Reclamação e temas valorizados e criticados por categoria |
| Recomendações | Cinco ações com os produtos prioritários |

Recursos de interação: filtros sincronizados entre páginas, botão de troca de moeda, dicas de ferramenta com resumo por categoria e botões "?" e "i" que explicam cada métrica.

## Principais resultados

1. **O engajamento está concentrado:** Três categorias somam 98% do engajamento, e Electronics sozinha responde por 51,32%.
2. **A nota sozinha esconde problemas:** 93% dos produtos têm comentários positivos, mas 12,34% das frases são reclamações. Dos 112 produtos problemáticos, 7 entre os 10 mais avaliados são fones de ouvido.
3. **Desconto tem limite:** Descontos de até 40% geram o maior engajamento; acima disso, ele cai pela metade. Os 438 produtos bem avaliados e pouco vistos são uma oportunidade de visibilidade.
4. **Padrão de Falhas:** Análise textual revelou que produtos com notas altas ainda sofrem com reclamações recorrentes de "Defeito" (ex: botões de controle quebrando após 6 meses), exigindo revisão de garantia ou fornecedores.

## Limitações

- A base não tem datas, então não permite analisar tendências.
- O número de avaliações indica procura, mas não é o volume de vendas.
- Algumas categorias têm 1 ou 2 produtos e foram lidas com cautela.
- O VADER foi feito para inglês e não capta comentários em hindi ou hinglish (99,9% dos textos foram identificados como inglês).
- A busca por temas usa palavras-chave e pode captar contextos neutros.
- A base traz apenas alguns comentários por produto.

## Ferramentas

Python (pandas, NumPy, Matplotlib, NLTK/VADER, langdetect, deep-translator) · Power BI · Google Colab

## Vídeo Executivo
https://www.youtube.com/watch?v=G0G8jABV5mY