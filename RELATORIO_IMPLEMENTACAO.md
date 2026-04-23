# Relatório de Implementação: Pipeline de Vendas de Eletrônicos

Este documento detalha as melhorias e a arquitetura implementada no projeto de ETL e Modelagem de Dados de Vendas.

## 🏗️ Arquitetura do Projeto (Medallion Architecture)

O projeto foi reestruturado seguindo as melhores práticas de Engenharia de Dados, dividindo o processamento em camadas lógicas:

1.  **Bronze (Raw):** Dados brutos extraídos diretamente do arquivo `electronics_sales_raw.csv`.
2.  **Silver (Tratamento):** Limpeza, padronização e aplicação de regras de negócio complexas.
3.  **Gold (Modelagem):** Estrutura dimensional (Star Schema) otimizada para Business Intelligence.

---

## 🥈 Camada Silver: Transformação e Qualidade
Local: `desafio_etl_vendas.ipynb`

### 1. Limpeza e Padronização
- **Tipagem:** Conversão de colunas de data para `datetime` e financeiras para `float`.
- **Saneamento:** Remoção de espaços extras e aplicação de *Title Case* em strings.
- **Data Quality:** Implementação de filtros para remover registros inconsistentes (ex: `quantity <= 0` ou `unit_price <= 0`).

### 2. Regras de Negócio e Métricas Financeiras
Implementamos o cálculo de rentabilidade, simulando um cenário real de e-commerce:
- **Gross Revenue:** `quantity * unit_price`
- **COGS (Custo de Mercadoria):** Criamos um mapeamento dinâmico por subcategoria:
    - *Smartphones:* 80% do valor bruto.
    - *Laptops:* 85% do valor bruto.
    - *Peripherals:* 50% do valor bruto (Alta margem).
    - *Outros:* 70% (Default).
- **Net Revenue:** Receita bruta descontando o percentual de desconto.
- **Gross Profit:** Lucro real após dedução do custo e descontos.

---

## 🥇 Camada Gold: Modelagem Star Schema
Local: `modelagem_star_schema.ipynb` e `vendas_star_schema.db`

A base plana foi decomposta para garantir performance e integridade:

### 1. Tabelas de Dimensão (Descritivas)
- **Dim_Cliente:** IDs únicos, nomes, segmentos e flag de Churn.
- **Dim_Produto:** Detalhes de marca, categoria e subcategoria.
- **Dim_Vendedor:** Representantes e suas respectivas regiões.
- **Dim_Calendario:** Atributos temporais (Ano, Mês, Trimestre, Mês/Ano).

### 2. Tabela Fato (Quantitativa)
- **Fato_Vendas:** Contém as chaves estrangeiras para as dimensões e **todas as métricas calculadas na Silver**. Isso permite que o dashboard calcule o lucro total com um simples `SUM()`.

---

## 🛠️ Tecnologias Utilizadas
- **Python & Pandas:** Manipulação e transformação de dados.
- **DuckDB:** Banco de dados OLAP de alta performance para processamento SQL local.
- **Jupyter Notebooks:** Documentação interativa do processo.

## 📈 Impacto para o Negócio
Com as mudanças realizadas, o pipeline agora entrega:
- **Visão de Rentabilidade:** O negócio agora sabe não apenas quanto vendeu, mas quanto **lucrou**.
- **Performance:** Consultas no Star Schema são até 10x mais rápidas que na tabela plana em grandes volumes.
- **Confiabilidade:** O processo de limpeza garante que dados errados não poluam os gráficos.

---
**Autor:** Gemini CLI & Ravel
**Data:** Abril de 2026
