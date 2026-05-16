# Lead Scoring B2B com Machine Learning

Modelo preditivo para ranquear e priorizar leads B2B por probabilidade de conversão, com dashboard interativo para o time comercial.

## Problema de negócio

Em vendas B2B, o time comercial tem energia limitada. Abordar todos os leads com a mesma prioridade desperdiça esforço e deixa oportunidades quentes esperando. Este projeto resolve isso com um modelo que **rankeia leads por probabilidade de conversão**, permitindo que o time foque nos 20% que geram 80% dos resultados.

> **Resultado:** o modelo captura ~70% das conversões reais abordando apenas os top 30% dos leads ranqueados.

---

## Estrutura do projeto

```
.
├── analise_b2b.ipynb   # Pipeline completo: dados → EDA → features → modelo → SHAP
├── dashboard.ipynb     # Dashboard interativo para o time comercial (roda após analise_b2b)
├── requirements.txt    # Dependências com versões fixadas
└── README.md
```

---

## Técnicas utilizadas

| Etapa | Técnica |
|---|---|
| Geração de dados | Dataset sintético com estrutura de funil B2B real |
| Feature engineering | Engajamento por dia, lead quente, ticket por porte, venda complexa |
| Modelagem | `GradientBoostingClassifier` dentro de `sklearn.Pipeline` |
| Calibração | `CalibratedClassifierCV` isotônico — probabilidades confiáveis para regras de negócio |
| Avaliação | ROC-AUC + Average Precision + Curva de Ganhos |
| Interpretabilidade | SHAP values — explica cada predição individualmente |
| Dashboard | HTML/JS com Chart.js renderizado via `IPython.display` |

---

## Como executar

```bash
# 1. Clone o repositório
git clone https://github.com/<seu-usuario>/lead-scoring-b2b.git
cd lead-scoring-b2b

# 2. Crie um ambiente virtual e instale as dependências
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 3. Abra o Jupyter e execute os notebooks em ordem
jupyter notebook
```

Execute `analise_b2b.ipynb` primeiro (todas as células), depois `dashboard.ipynb`. O dashboard usa `%run analise_b2b.ipynb` internamente para carregar o modelo já treinado.

---

## Métricas do modelo

| Métrica | Valor |
|---|---|
| ROC-AUC | ~0.85 |
| Average Precision | ~0.76 |
| Conversões capturadas no top 30% | ~70% |

> Métricas variam levemente por dependerem de geração aleatória com seed fixo.

---

## Por que ROC-AUC e não acurácia?

Em lead scoring o objetivo é **rankear**, não classificar. Com taxa de conversão de ~23%, um modelo que chuta "não converte" para todos teria acurácia de 77% — e seria inútil. ROC-AUC mede se leads bons aparecem consistentemente acima dos ruins no ranking, que é exatamente o que importa para o comercial.

---

## Segmentação de leads (tiers)

| Tier | Score | Ação recomendada |
|---|---|---|
| Crítico | > 0.80 | Ligar hoje |
| Quente | 0.60 – 0.80 | Agendar reunião esta semana |
| Morno | 0.30 – 0.60 | Nutrir com conteúdo + follow-up em 15 dias |
| Frio | < 0.30 | Manter em automação de marketing |

---

## Próximos passos

- Conectar à API do CRM (HubSpot / Salesforce / Pipedrive) para dados reais
- Adicionar features temporais: janelas de engajamento dos últimos 7, 14 e 30 dias
- Modelo de sobrevivência para prever *quando* o lead converte, não só *se*
- Deploy via FastAPI com endpoint consumido em tempo real pelo CRM
