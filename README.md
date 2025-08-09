[200~# Telecom X — ETL e EDA do Churn de Clientes

## 1. Introdução
A Telecom X enfrenta um alto índice de evasão de clientes (churn). Este trabalho tem como objetivo executar o ciclo **ETL + EDA** para entender padrões associados ao churn e gerar insumos para o time de Data Science reduzir a evasão.

**Objetivos específicos**
- Extrair os dados operacionais disponibilizados pela empresa.
- Transformar/limpar os dados (padronização de tipos, expansão de campos aninhados).
- Realizar análise exploratória (EDA) para identificar fatores associados à evasão.
- Documentar o processo e apontar recomendações práticas.

**Escopo**
- Foco em dados de perfil do cliente, serviços contratados, cobrança e status de churn.
- Entrega de um dataset tratado e um relatório com achados e sugestões.

**Tecnologias**
- Python (Pandas, Matplotlib/Seaborn), Google Colab e Git/GitHub para versionamento.
## 2. Extração (Extract)
Os dados foram disponibilizados pela empresa no ficheiro **TelecomX_Data.json**. Para este projeto, realizámos o upload do arquivo para o Google Colab e efetuámos a leitura com **Pandas**.

**Reprodutibilidade**
- Requisitos: Python 3.10+, pandas
- Fonte dos dados: `TelecomX_Data.json` (incluído neste repositório)

**Leitura do JSON no Colab**
```python
import pandas as pd

# 1) Se o ficheiro já estiver no ambiente do Colab (upload manual):
df = pd.read_json("TelecomX_Data.json")

# 2) Alternativa: montar Google Drive e ler do Drive
# from google.colab import drive
# drive.mount('/content/drive')
# df = pd.read_json("/content/drive/MyDrive/seu_caminho/TelecomX_Data.json")

df.head()

