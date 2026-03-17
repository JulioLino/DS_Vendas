# 📊 End-to-End Analytics Pipeline: Microsoft Fabric & PySpark

Bem-vindo ao repositório do projeto **Sales Analytics End-to-End**. Este projeto demonstra a construção de uma arquitetura de dados moderna (Data Lakehouse) utilizando o ecossistema completo do **Microsoft Fabric**.

O objetivo principal foi processar dados transacionais de vendas, aplicando engenharia de dados avançada para modelar um *Star Schema* otimizado em formato Delta, garantindo alta performance para consumo no Power BI através do modo **Direct Lake**.

🔗 **[Acesse o Portfólio Completo deste Projeto no Google Sites] ([link](https://sites.google.com/view/portifoliojuliolino/modelagem-vendas))**

---

## 🏗️ Arquitetura do Projeto (Medallion Architecture)

O pipeline foi desenhado respeitando a separação de camadas lógicas e físicas, garantindo governança, escalabilidade e o conceito de *Single Source of Truth*.

### 1. Ingestão e Preparação (Camada Raw)
* **Ferramenta:** Dataflow Gen2 (`DFL_Raw_Ajuste_Para_Tabela_Vendas`) (Utilizado para fins didáticos)
* **Processo:** Ingestão de dados brutos (arquivos locais/Excel) para o OneLake. Realização de limpezas primárias, padronização de cabeçalhos e tipagem de dados, isolando a complexidade da fonte original.

### 2. Modelagem Dimensional (Camada Curated)
* **Ferramenta:** Notebook PySpark (`NB_Raw_To_Curated.ipynb` - *Disponível neste repositório*)
* **Processo:** * Extração de entidades únicas e geração de **Surrogate Keys** sequenciais utilizando `row_number()`.
  * Criação de uma tabela Fato (`fat_venda`) e dimensões (`dim_cliente`, `dim_produto`, `dim_vendedor`).
  * Geração programática de uma `dim_calendario` para inteligência de tempo.
  * **Otimização:** Salvamento físico das tabelas em formato **Delta Parquet** utilizando caminhos absolutos do OneLake (`abfss://`), garantindo integração perfeita com o SQL Analytics Endpoint.

### 3. Orquestração e Observabilidade
* **Ferramenta:** Data Pipeline (`PL_Carga_Vendas_Completa`)
* **Processo:** Automação *Zero-Touch* baseada em dependências de sucesso.
  * O pipeline orquestra a execução sequencial: `Dataflow` ➔ `PySpark Notebook` ➔ `Atualização do Modelo Semântico`.
  * **Data Observability:** Implementação de rotas de monitoramento para falha e sucesso, com gatilhos de alerta via Microsoft Teams/Office 365, garantindo governança proativa.

### 4. Camada Semântica e BI (Direct Lake)
* **Ferramenta:** Power BI (Serviço Fabric)
* **Processo:** Criação de um Modelo Semântico físico estruturado em **Star Schema** (Relacionamentos 1:* unidirecionais).
* **Performance:** Utilização do modo **Direct Lake**, permitindo que o motor do Power BI leia os arquivos Delta diretamente da memória do OneLake, combinando a velocidade do modo *Import* com a atualização em tempo real do *DirectQuery*.

---

## 💻 Tecnologias Utilizadas
* **Plataforma:** Microsoft Fabric, OneLake
* **Engenharia de Dados:** Apache Spark (PySpark), Dataflow Gen2 (Power Query)
* **Armazenamento:** Delta Lake (Delta Parquet)
* **Orquestração:** Fabric Data Factory (Pipelines)
* **Visualização e Semântica:** Power BI, DAX

---

## 📈 Regras de Negócio e DAX (Highlights)
As métricas do projeto foram homologadas em um Dicionário de Dados. Abaixo, alguns exemplos das medidas criadas para o consumo no Dashboard:

* **Faturamento Total:** `SUM(fat_venda[valor_total])`
* **Margem de Lucro (%):** `DIVIDE([Faturamento Total] - [Custo Total], [Faturamento Total], 0)`
* **Ticket Médio:** `DIVIDE([Faturamento Total], COUNTROWS(fat_venda), 0)`

---
*Projeto desenvolvido por **Julio Lino** como demonstração de competências avançadas em Engenharia de Dados e Business Intelligence.*
