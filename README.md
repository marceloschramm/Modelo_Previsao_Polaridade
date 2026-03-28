# 📊 Análise de Reclamações Financeiras – Sentiment Pipeline

## 📌 Visão Geral
Este projeto realiza **análise de sentimentos** em narrativas de reclamações do setor financeiro americano, utilizando dados públicos disponibilizados pelo Consumer Financial Protection Bureau (CFPB).

A base utilizada foi filtrada para o período de **01/07/2024 a 19/03/2026**, considerando apenas registros que possuíam narrativa textual.

O objetivo é construir uma **pipeline de classificação de sentimentos** que combina diferentes modelos de NLP, atribui pesos às predições e gera uma **label final** para cada relato. Essa base enriquecida pode ser usada para treinar modelos especializados de Machine Learning.

---
# Estrutura do projeto:
  - Processar os dados baixados da CFPB com o notebook pipeline_processamento_dados;
  - Utilizar o arquivo de saída df_ml.csv, para alimentar o modelo de predição, com o notebook treinamento_modelo
  - Adicionalmente, deixei um notebook, graficos_e_analises, para EDA.

## ⚙️ Notebook pipeline_processamento_dados
1. **Configuração inicial**
   - Classe `Config` com parâmetros principais (arquivos, chunks, modelos).
   - Sistema de logging para monitorar execução.

2. **Importação de bibliotecas**
   - `pandas`, `numpy`, `sklearn` para manipulação de dados.
   - `nltk` para pré-processamento e análise com VADER.
   - `transformers` para uso do modelo DistilRoBERTa.
   - `sentence-transformers` para embeddings semânticos (MiniLM).

3. **Leitura e tratamento dos dados**
   - Classe `DataLoader` para carregar CSV em chunks.
   - Remoção de duplicados e normalização das colunas.

4. **Pré-processamento textual**
   - Classe `TextPreprocessor` com funções para normalização, tokenização e lematização.
   - Processamento específico para VADER.

5. **Modelos de análise de sentimento**
   - **VADER**: baseado em léxico.
   - **DistilRoBERTa**: transformer especializado em notícias financeiras.
   - **MiniLM (embeddings)**: classificação por similaridade com seeds.

6. **Orquestração da pipeline**
   - Classe `SentimentPipeline` aplica os três modelos.
   - Combinação dos resultados com pesos ajustados manualmente.
   - Geração de escala contínua e label final.

7. **Execução em larga escala**
   - Processamento em blocos de 2000 registros.
   - Salvamento de resultados parciais em CSV.
   - Exportação final consolidada.

8. **Inclusão de dados sintéticos**
   - Relatos gerados por LLM para balancear classes.

---

## 🧩 Modelos Utilizados
- **VADER (NLTK)** → rápido, baseado em léxico.
- **DistilRoBERTa** → modelo transformer especializado em notícias financeiras.
- **MiniLM (Sentence-Transformers)** → embeddings semânticos para classificação por similaridade.

---

## 📂 Estrutura de Saída
O pipeline gera arquivos CSV com as seguintes colunas principais:

- `product` → Produto financeiro relacionado.
- `narrative` → Texto da reclamação.
- `vader_labels`, `roberta_labels_def`, `embeddings_polarity` → Labels de cada modelo.
- `escala` → Valor contínuo entre -1 e 1 representando a polaridade final.

---

## 🚀 Como Executar
1. Instale as dependências:
   ```bash
   pip install -r requirements.txt

---------------------------------------------------------------------------------------------

# 📊 Classificação de Sentimentos com XGBoost

## 📌 Visão Geral
Este notebook complementa a pipeline de análise de sentimentos aplicada às reclamações financeiras americanas.  
Após a etapa inicial de atribuição de uma **escala contínua (-1 a 1)** para cada narrativa, este notebook prepara os dados para treinar um **modelo de classificação supervisionado (XGBoost)**, transformando a escala em **labels discretas**.

O objetivo é construir um classificador robusto que consiga prever a polaridade de novos relatos com base em texto processado e no produto financeiro associado.

---

## ⚙️ Estrutura do Notebook
1. **Carregamento dos dados**
   - Importação do dataset `df_ml.csv`.
   - Remoção de valores nulos.
   - Verificação da distribuição das classes pela escala.

2. **Balanceamento das classes**
   - Amostragem estratificada para equalizar a quantidade de exemplos por classe.
   - Classes consideradas:
     - Muito Negativo
     - Negativo
     - Neutro
     - Positivo

3. **Criação das labels**
   - Função `labelization` converte a escala contínua em labels numéricas:
     - `0` → Muito Negativo
     - `1` → Negativo
     - `2` → Neutro
     - `3` → Positivo

4. **Preparação dos dados**
   - Features: `product` e `processada_ml`.
   - Pré-processamento:
     - **TF-IDF** para texto (`processada_ml`).
     - **OneHotEncoder** para produto (`product`).
   - Pipeline salvo em `preprocess_class.pkl`.

5. **Treinamento do modelo**
   - Divisão em treino e teste (80/20) com estratificação.
   - Modelo: `XGBClassifier`.
   - Treinamento e avaliação com métricas de classificação.

6. **Avaliação**
   - Relatório de precisão, recall e F1-score.
   - Matriz de confusão.
   - Acurácia final: ~72%.

7. **Exportação**
   - Modelo treinado salvo em `modelo_treinado_class.pkl`.

---

## 🧩 Tecnologias Utilizadas
- **Pandas / NumPy** → manipulação de dados.
- **Scikit-learn** → pré-processamento, métricas e pipeline.
- **XGBoost** → modelo de classificação.
- **Joblib** → persistência de modelos.

---

## 📂 Estrutura de Saída
- `preprocess_class.pkl` → pipeline de pré-processamento (TF-IDF + OneHot).
- `modelo_treinado_class.pkl` → modelo XGBoost treinado.

---

## 🚀 Como Executar
1. Instale as dependências:
   ```bash
   pip install -r requirements.txt


