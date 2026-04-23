# 📊 Data Engineering Challenge: Electronics Sales ETL & Modeling

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![DuckDB](https://img.shields.io/badge/duckdb-FFF000?style=for-the-badge&logo=duckdb&logoColor=black)
![Pandas](https://img.shields.io/badge/pandas-%23150458.svg?style=for-the-badge&logo=pandas&logoColor=white)
![SQL](https://img.shields.io/badge/sql-%234479A1.svg?style=for-the-badge&logo=postgresql&logoColor=white)

Este projeto apresenta um pipeline de dados completo (ETL) que transforma dados brutos de vendas de eletrônicos em um modelo analítico otimizado (**Star Schema**), utilizando **Python**, **DuckDB** e técnicas avançadas de **Engenharia de Dados**.

---

## 🎯 Objetivo do Projeto
Demonstrar a aplicação de boas práticas de engenharia de dados, desde a ingestão de arquivos CSV brutos até a criação de um Data Warehouse simulado, focado em métricas de rentabilidade e performance de consulta.

## 🏗️ Arquitetura e Metodologia
O projeto segue o conceito de **Medallion Architecture** (simplificado):
1.  **Bronze (Raw):** Dados originais em CSV.
2.  **Silver (Cleaned):** Dados limpos, tipados e enriquecidos com regras de negócio.
3.  **Gold (Star Schema):** Modelagem dimensional com tabelas Fato e Dimensões para BI.

### Regras de Negócio Implementadas:
Como o dataset original não continha custos, implementamos uma simulação de **COGS (Cost of Goods Sold)** baseada em benchmarks de mercado:
- **Laptops:** 85% do valor bruto (Alta competitividade).
- **Smartphones:** 80% do valor bruto.
- **Periféricos:** 50% do valor bruto (Alta margem).
- **Default:** 70% para demais categorias.

---

## 🛠️ Tecnologias Utilizadas
- **Python 3.12:** Linguagem principal para o pipeline.
- **Pandas:** Manipulação e limpeza de dados (Camada Silver).
- **DuckDB:** Banco de dados analítico (OLAP) em memória para a modelagem dimensional.
- **Google Colab:** Ambiente de desenvolvimento e execução.
- **WSL & Git:** Controle de versão e gerenciamento de ambiente local.

---

## 📐 Modelagem Star Schema
A modelagem final foi estruturada para máxima performance analítica:
- **Fato_Vendas:** Contém métricas financeiras (Net Sales, Gross Profit, etc).
- **Dim_Produto:** Hierarquia de categorias, subcategorias e marcas.
- **Dim_Cliente:** Informações demográficas e segmentação.
- **Dim_Vendedor:** Estrutura regional de vendas.
- **Dim_Config_Venda (Junk Dimension):** Consolidação de canais de venda e métodos de pagamento.
- **Dim_Calendario:** Atributos temporais para análise de séries históricas.

---

## 📜 Diferencial: O Manifesto ETL
Um dos destaques deste projeto é o uso do [MANIFESTO_ETL_QUALIDADE.md](./MANIFESTO_ETL_QUALIDADE.md), um guia de passos obrigatórios seguidos rigorosamente para garantir que nenhuma métrica fosse calculada sobre dados sujos ou inconsistentes.

## 📁 Estrutura de Arquivos
```text
├── electronics_sales_raw.csv      # Dados brutos
├── electronics_sales_silver.csv  # Dados após ETL (Camada Silver)
├── etl_vendas.ipynb               # Pipeline de limpeza e transformação
├── modelagem_star_schema.ipynb    # Implementação do modelo dimensional
├── modelagem_vendas.sql           # Script SQL para criação do Warehouse
├── MANIFESTO_ETL_QUALIDADE.md     # Guia de boas práticas aplicado
└── README.md                      # Documentação do projeto
```

---

## 🚀 Como Executar
1. Clone o repositório:
   ```bash
   git clone https://github.com/SEU_USUARIO/SEU_REPO.git
   ```
2. Abra os arquivos `.ipynb` no Google Colab ou Jupyter Notebook.
3. Certifique-se de ter o `duckdb` e o `pandas` instalados:
   ```bash
   pip install pandas duckdb
   ```

---
**Desenvolvido por [Seu Nome]** - Conecte-se comigo no [LinkedIn](https://www.linkedin.com/in/renato-avellar)
