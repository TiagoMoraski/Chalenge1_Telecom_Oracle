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

## 3. Transformação (Transform)
Após a extração, identificou-se que quatro colunas (`customer`, `phone`, `internet`, `account`) continham estruturas aninhadas (dicionários armazenados como texto).

**Principais etapas realizadas**
1. Expansão das colunas aninhadas usando `pd.json_normalize` e `map(parse_obj)` para lidar com células contendo tanto strings quanto dicionários.
2. Adição de prefixos (`customer_`, `phone_`, `internet_`, `account_`) para evitar conflitos de nomes.
3. Conversão de tipos:
   - `Churn` transformado para valores binários (Yes → 1, No → 0).
   - `account_Charges.Total` convertido para `float`.
4. Limpeza de espaços extras em colunas de texto.
5. Verificação de nulos e padronização de tipos.

**Código simplificado**
```python
import pandas as pd
import ast
import json

def parse_obj(x):
    if pd.isna(x):
        return {}
    if isinstance(x, dict):
        return x
    if isinstance(x, str):
        try:
            return ast.literal_eval(x)
        except Exception:
            try:
                return json.loads(x)
            except Exception:
                return {}
    return {}

customer_df = pd.json_normalize(df['customer'].map(parse_obj)).add_prefix('customer_')
phone_df    = pd.json_normalize(df['phone'].map(parse_obj)).add_prefix('phone_')
internet_df = pd.json_normalize(df['internet'].map(parse_obj)).add_prefix('internet_')
account_df  = pd.json_normalize(df['account'].map(parse_obj)).add_prefix('account_')

df_expanded = pd.concat(
    [df[['customerID','Churn']], customer_df, phone_df, internet_df, account_df],
    axis=1
)
[200~
## 4. Análise Exploratória (EDA)
Com o dataset limpo e padronizado, foi realizada a Análise Exploratória para identificar padrões associados ao churn.

**Principais análises e gráficos gerados**
1. **Distribuição geral do churn**  
   - Aproximadamente X% dos clientes saíram (Churn = 1).
   - Gráfico: `countplot` mostrando o equilíbrio entre clientes retidos e evadidos.

2. **Churn por gênero**  
   - Pequena variação entre masculino e feminino, sem impacto significativo.

3. **Churn por tipo de contrato**  
   - Contratos mensais apresentaram taxa de churn significativamente maior.
   - Contratos anuais ou de dois anos retêm mais clientes.

4. **Impacto de variáveis numéricas**  
   - **Tempo de permanência (tenure)**: clientes novos saem mais.
   - **Mensalidade**: planos mais caros têm maior churn.
   - **Total gasto**: clientes com gasto total baixo também têm alta evasão (pouco tempo de permanência).

5. **Serviços contratados**
   - Fibra óptica tem maior churn que DSL.
   - Clientes sem segurança online (`OnlineSecurity = No`) e sem suporte técnico (`TechSupport = No`) têm churn mais elevado.

**Exemplo de código para gráficos**
```python
import seaborn as sns
import matplotlib.pyplot as plt

# Churn por tipo de contrato
plt.figure(figsize=(7,4))
sns.countplot(data=df_expanded, x='account_Contract', hue='Churn')
plt.title("Churn por Tipo de Contrato")
plt.show()

# Boxplot: mensalidade vs churn
plt.figure(figsize=(6,4))
sns.boxplot(data=df_expanded, x='Churn', y='account_Charges.Monthly')
plt.title("Mensalidade por Churn")
plt.show()

## 5. Conclusão e Recomendações
A análise exploratória revelou padrões claros que podem orientar ações para reduzir o churn:

**Principais achados**
- Contratos mensais têm churn muito mais alto que contratos anuais ou de dois anos.
- Clientes novos (pouco tempo de permanência) são mais propensos a sair.
- Planos com mensalidades mais caras apresentam maior taxa de evasão.
- Ausência de serviços como segurança online e suporte técnico está associada a churn elevado.
- Fibra óptica apresenta churn maior que DSL.

**Recomendações**
1. **Incentivar contratos de longo prazo**  
   - Oferecer descontos ou benefícios para clientes que migrem de contrato mensal para anual ou bienal.

2. **Apoio e engajamento nos primeiros meses**  
   - Programas de onboarding e acompanhamento para novos clientes.
   - Contato proativo com clientes de baixo tenure.

3. **Revisão de planos e preços**  
   - Avaliar o valor percebido dos planos mais caros.
   - Criar pacotes com benefícios adicionais para justificar o custo.

4. **Inclusão de serviços de valor agregado**  
   - Oferecer segurança online e suporte técnico como parte de pacotes básicos.
   - Comunicar claramente o valor desses serviços para retenção.

> Com base nessas ações, espera-se reduzir o churn, aumentar a fidelização e melhorar a experiência geral do cliente.

---
**Autor:** Tiago Moraski  
**Projeto:** Desafio Telecom X — Análise de Evasão de Clientes
