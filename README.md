# Swiggy-Snowflake
An end-to-end data pipeline showing how raw CSV data moves into Snowflake. A Stage → Clean → Consumption flow creates dimension/fact tables with SCD handling. Finally, a Streamlit dashboard delivers interactive insights on daily, monthly, and yearly KPIs, mirroring real-world data warehousing.

This is the link to view my project on snowsight:
https://app.snowflake.com/jpujfls/qd31171/#/swiggy-fEkcwawxs

# End-to-End Data Pipeline: Snowflake & Streamlit

## Table of Contents
1. [Introduction](#introduction)  
2. [Architecture Overview](#architecture-overview)  
3. [Project Setup](#project-setup)  
4. [Snowflake Objects](#snowflake-objects)  
5. [Data Loading and Transformation](#data-loading-and-transformation)  
6. [Streamlit Dashboard](#streamlit-dashboard)  

---

## 1. Introduction
This repository demonstrates an end-to-end data pipeline using **Snowflake** for data warehousing and **Streamlit** for building a quick, interactive dashboard. It simulates a **food aggregator** scenario, where user, order, menu, and other data sets are ingested and transformed. The final result is a set of **dimensional tables** and a **fact table** ready for analytics.

## 2. Architecture Overview
- **Stage Layer**: Raw data is ingested into Snowflake via the internal stage using `COPY`.  
- **Clean Layer**: Data is merged and cleansed into properly typed tables, often replicating SCD1 patterns.  
- **Consumption Layer**: Final dimension and fact tables (SCD2 patterns, if needed) along with aggregated views, used for analytics or BI tools.

Raw CSV Files → Stage_SCH Tables → MERGE → Clean_SCH Tables → MERGE → Consumption_SCH (Facts/Dimensions/Views)


## 3. Project Setup
- **Snowflake Account**  
  You must have access to a Snowflake account (free trial or enterprise).  
- **Python 3.8+**  
  Recommended version for the Streamlit application.  
- **Dependencies**  
  - `streamlit`  
  - `pandas`  
  - `snowflake-snowpark-python` (or `snowflake-connector-python`, depending on your approach)

## 4. Snowflake Objects
1. **Warehouses**: Scripts define a warehouse named `SWIGGY_WH`.  
2. **Databases & Schemas**: `SWIGGY` database with multiple schemas: `STAGE_SCH`, `CLEAN_SCH`, `CONSUMPTION_SCH`.  
3. **Tables**: 
   - Stage tables mirror the raw CSV layout.  
   - Clean tables define typed columns.  
   - Consumption tables apply SCD logic (dimensions/facts).  
4. **Streams**: Append-only streams track incremental changes between loads.  
5. **Views**: Aggregated KPI views for daily, monthly, and yearly revenue/orders.

## 5. Data Loading and Transformation
1. **Ingestion**: CSV files are copied into staging tables with the Snowflake `COPY` command.  
2. **Data Cleansing**: `MERGE` statements apply conversions and data validations in `CLEAN_SCH`.  
3. **Dimensional Modeling**: SCD Type 2 merges insert new records or update existing ones with `EFF_END_DT` for historical tracking.  
4. **Fact Table**: Consolidates reference keys from dimension tables, storing measures like `quantity`, `price`, and `subtotal`.  
5. **KPI Views**: Several views (e.g., `vw_monthly_revenue_kpis`) provide quick insight queries (revenue trends, orders, etc.).

## 6. Streamlit Dashboard
The `app.py` (or similar) file in this repo demonstrates how to:

- Connect to Snowflake using the Snowpark context.  
- Query dimension and fact data.  
- Display aggregated metrics and time-series plots (monthly/yearly).  
- Provide selection boxes to compare monthly or yearly performances.
