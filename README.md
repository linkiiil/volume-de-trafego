# 🚗 Previsão de Volume de Tráfego Interestadual Metropolitano

### 📖 Visão Geral

Este projeto tem como foco a análise e previsão do volume de tráfego rodoviário metropolitano (na autoestrada I-94) através de modelos de *Machine Learning*. O volume de veículos é fortemente influenciado por condições meteorológicas, hora do dia, dia da semana e o próprio histórico de circulação. Compreender estas relações é crucial para o planeamento urbano e a gestão da mobilidade.

### 🎯 Objetivo

Desenvolver modelos preditivos capazes de prever o volume horário de tráfego utilizando variáveis meteorológicas, temporais e valores defasados da própria série temporal (lags). Adicionalmente, procurou-se adotar uma metodologia estrita para dados temporais de forma a evitar fugas de dados (*data leakage*) e garantir a explicabilidade do modelo utilizando a técnica SHAP.

### 📊 Conjunto de Dados (Dataset)
A análise baseia-se no **Metro Interstate Traffic Volume Dataset** (Repositório UCI, ID 492), que contém cerca de seis anos de registos horários do volume de tráfego da Interstate 94 Westbound, no Minnesota, Estados Unidos, em conjunto com dados meteorológicos e indicação de feriados.

### 🛠️ Metodologia e Pré-Processamento
- **Engenharia de Features:**
  - **Variáveis Cíclicas:** Transformação de horas e dias da semana em componentes de seno e cosseno para capturar a continuidade temporal.
  - **Defasagens (Lags):** Criação de variáveis históricas do volume de tráfego (lag 1 hora, lag 24 horas, lag 168 horas) para incorporar a inércia e a sazonalidade diária e semanal.
- **Validação Cruzada:** Utilização de `TimeSeriesSplit` (5 *folds*) para garantir que o modelo é treinado apenas com dados passados e avaliado em dados futuros, prevenindo o *data leakage*.

### 🤖 Modelos Avaliados e Resultados
Vários algoritmos foram testados, demonstrando que modelos não lineares baseados em árvores capturam muito melhor a dinâmica do tráfego. Os melhores resultados após a otimização de hiperparâmetros foram:

| Modelo | MAE | RMSE | MAPE | R² |
|---|---|---|---|---|
| **LGBM (Otimizado)** | **168.80** | **267.98** | **0.077** | **0.981** |
| Random Forest (Otimizado)| 165.35 | 270.37 | 0.071 | 0.981 |
| XGBoost | 177.63 | 280.30 | 0.082 | 0.979 |
| Regressão Linear | 453.20 | 624.65 | 0.267 | 0.899 |

*O modelo LightGBM (LGBM) foi selecionado como o modelo final devido ao seu desempenho robusto no RMSE e R² e facilidade de captura de relações complexas.*

### 💡 Principais Descobertas (Insights e SHAP)
- **A Inércia é o Maior Preditor:** O volume de tráfego na hora anterior (`traffic_volume(-1)`) e os padrões da hora do dia (`hour_cos`) representam o maior peso preditivo para o modelo.
- **Sazonalidade:** Existem picos muito vincados de tráfego às 7h e às 16h durante os dias úteis. Aos fins de semana e feriados, o volume de veículos decresce substancialmente.
- **Fatores Meteorológicos:** A correlação direta e linear das variáveis climáticas com o volume de trânsito é relativamente fraca quando comparada com a dimensão temporal.
- **Análise de Resíduos:** O modelo tem um excelente ajuste global ($R^2  pprox 0.98$), mas a análise aos resíduos indica que ainda permanece alguma autocorrelação em ciclos de 24h e 168h, provavelmente devido a variáveis contextuais não presentes no *dataset* (ex: obras, acidentes).

### 📁 Ficheiros do Projeto
- `Projeto - Volume de Tráfego Interestadual Metropolitano.pdf` - Relatório principal com o código, análise exploratória (EDA) e modelagem detalhada.
- `shap_summary.svg` - Gráfico demonstrativo da importância global das *features* (SHAP Summary Plot).
- `shap_waterfall_top5.svg` - Gráfico de cascata SHAP demonstrando o impacto das top 5 variáveis numa previsão específica.
---
*Projeto pessoal com foco em machine learning para séries temporais e interpretabilidade algorítmica.*
