# 🚀 Manifesto do ETL de Respeito: O Caminho para Dados Confiáveis

Este guia estabelece os passos **obrigatórios** que todo Engenheiro ou Analista de Dados deve seguir após a extração e **antes** da criação de qualquer métrica de negócio. 

---

## 1. Ingestão e Validação de Esquema (Schema Check)
Antes de qualquer coisa, você deve garantir que o arquivo tem o que você espera.
*   **O que fazer:** Verificar se todas as colunas necessárias estão presentes e se os nomes estão padronizados (sem espaços extras ou caracteres especiais).
*   **Por que é essencial:** Se o sistema de origem mudar o nome de uma coluna, seu código quebrará. Validar isso no início evita erros silenciosos.

```python
# Padronizando nomes de colunas (snake_case)
df.columns = [col.strip().lower().replace(' ', '_') for col in df.columns]

# Verificando se colunas essenciais existem
cols_obrigatorias = ['order_id', 'customer_id', 'unit_price']
assert all(c in df.columns for c in cols_obrigatorias), "Erro: Colunas críticas ausentes!"
```


## 2. Tipagem de Dados (Casting)
O erro mais comum em ETL é tratar números ou datas como texto.
*   **O que fazer:** Converter colunas de data para o formato `datetime` e garantir que preços/quantidades sejam numéricos (`float`/`int`).
*   **Por que é essencial:** Você não consegue somar "R$ 10,00" se for texto. Datas como texto impedem análises temporais (como vendas por mês).

```python
# Convertendo para Data
df['order_date'] = pd.to_datetime(df['order_date'])

# Convertendo para Numérico (forçando erros a virarem NaN para limpeza posterior)
df['unit_price'] = pd.to_numeric(df['unit_price'], errors='coerce')
```

## 3. Limpeza de Nulos e Duplicatas (Deduplication)
Dados duplicados inflam as métricas; dados nulos quebram os cálculos.
*   **O que fazer:** Remover registros duplicados (baseado no ID único) e decidir se linhas com valores nulos críticos devem ser excluídas ou preenchidas.
*   **Por que é essencial:** Se você tiver 10% de vendas duplicadas, seu lucro parecerá 10% maior do que a realidade.

```python
# Removendo duplicatas baseadas no ID único
df = df.drop_duplicates(subset=['order_id'], keep='first')

# Tratando nulos em colunas críticas
df = df.dropna(subset=['order_id', 'order_date'])
df['category'] = df['category'].fillna('Não Informado')
```

## 4. Padronização Textual (Sanitization)
Em bancos de dados, "Smartphones" e "smartphones" são lidos como coisas diferentes.
*   **O que fazer:** Aplicar padronização de capitalização (`.upper()` ou `.title()`) e remover espaços em branco invisíveis (`.strip()`).
*   **Por que é essencial:** Sem isso, seus filtros e agrupamentos ficarão "sujos", mostrando a mesma marca ou categoria em linhas separadas.

```python
# Limpando espaços e padronizando capitalização
df['brand'] = df['brand'].str.strip().str.upper()
df['region'] = df['region'].str.strip().str.title()
```

## 5. Higienização Lógica (Data Quality)
Aqui você verifica se os dados fazem sentido para o mundo real.
*   **O que fazer:** Verificar se existem preços negativos, quantidades zeradas ou datas no futuro.
*   **Por que é essencial:** Um erro de digitação no sistema (ex: preço de -100) pode arruinar toda a sua métrica de Receita Bruta.

```python
# Filtrando apenas valores lógicos (Preço e Quantidade positivos)
df = df[df['unit_price'] > 0]
df = df[df['quantity'] > 0]

# Removendo datas impossíveis (futuras)
from datetime import datetime
df = df[df['order_date'] <= datetime.now()]
```

## 6. Mapeamento e Enriquecimento (Pre-Metrics)
Este é o passo final onde você prepara as colunas que servirão de base para os cálculos.
*   **O que fazer:** Aplicar regras de negócio externas (como o seu `cogs_map`) e garantir o uso de valores padrão (`fillna`) para novos dados.
*   **Por que é essencial:** As métricas financeiras dependem de premissas. Se o mapeamento falhar para um produto novo, seu cálculo de lucro ficará errado.

```python
# Mapeamento com "Rede de Segurança" (Fallback para 70% se a categoria for nova)
cogs_map = {'Smartphones': 0.80, 'Laptops': 0.85, 'Peripherals': 0.50}
df['cogs_pct'] = df['sub_category'].map(cogs_map).fillna(0.70)
```

---

### 💡 A "Lei de Ouro" do ETL
> **"Garbage In, Garbage Out"**

Somente após esses 6 passos é que você deve criar as métricas finais:
```python
# CRIAÇÃO DE MÉTRICAS SEGURAS
df['gross_revenue'] = df['quantity'] * df['unit_price']
df['cost_of_goods_sold'] = df['gross_revenue'] * df['cogs_pct']
df['net_profit'] = df['gross_revenue'] - df['cost_of_goods_sold']
```

**Lembre-se:** Um dashboard bonito sobre dados sujos é apenas uma mentira bem apresentada.
