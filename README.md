# 🌐 Azure Data Factory End-to-End ETL Pipeline Using Medallion Architecture (Bronze → Silver → Lakehouse)

This project demonstrates a **real-world Data Engineering pipeline** built on **Azure Data Factory**, using the **Medallion Architecture** (Bronze, Silver, Gold) and **Delta Lake** on ADLS Gen2 for scalable, incremental, and cost-efficient data processing.

> **Goal:** Build an enterprise-style ETL pipeline to ingest raw data → clean & transform → create analytics-ready Delta tables, all with minimal compute cost and no paid services beyond ADLS storage.

---

## 📁 Architecture Overview

This project implements the **Medallion Architecture**:

### **Bronze Layer**
- Raw files ingested into ADLS Gen2 using ADF Copy Activity
- No transformations applied
- Schema is semi-structured or structured depending on the source

### **Silver Layer**
- Cleaned & structured Delta tables
- UPSERT logic applied (using ADF data flows)
- Data quality checks
- Stored in `silver/` container in ADLS Gen2

### **Gold Layer (Lakehouse Ready)**
- **No SQL Warehouse, No Synapse, No Logic Apps**
- Data is already analytics-ready in **Delta format**
- Intended for Power BI or notebook-based analytics
- Zero additional Azure cost

---

## 🚀 Key Features

### ✔ Multipe Source Data Integration
- **OnPrem Data** Ingested using **Microsoft Integration Runtime**
- Data Ingestion via **REST API** from Github
- Data pulled using Copy activity of ADF from **Azure SQL DB**
  
### ✔ Azure Data Factory Pipelines
- Multiple pipelines orchestrating ingestion → transformation
- Data validation & failure logging
- Parameterized for extensibility

### ✔ Delta Lake on ADLS Gen2
- Efficient storage format for incremental loads  
- Supports **UPSERT / MERGE** operations  
- Future-proof Lakehouse structure

### ✔ Cost-Optimized Design
This project intentionally avoids:
- Synapse Dedicated SQL Pool
- Logic Apps
- SQL Database
- Serverless SQL
- Databricks

Only **ADLS Gen2 + ADF** are used.

### ✔ Incremental Data Processing
- Silver layer handles updated records without duplication  
- Uses surrogate keys / hash comparisons (optional)

### ✔ Data Modeling
- Fact and Dimension folders created in Silver  
- Star-schema aligned structure for reporting tools

---

## 🛠️ Tech Stack

| Component      | Technology                                |
|----------------|-------------------------------------------|
| Ingestion      | Azure Data Factory (Copy Activity)        |
| Transformation | ADF Mapping Data Flow (Silver layer only) |
| Storage        | ADLS Gen2 (Delta + Parquet)               |
| Format         | Delta Lake                                |
| Orchestration  | Azure Data Factory                        |
| Visualization  | Power BI (optional, import mode)          |

---

## 📂 Folder Structure (ADLS Gen2)

