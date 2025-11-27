# 🚀 Azure Data Factory – End-to-End ETL Pipeline (Bronze → Silver Delta Lakehouse)

This repository showcases a complete Azure Data Engineering pipeline built using **Azure Data Factory**, **ADLS Gen2**, **Delta Lake**, **Self-Hosted Integration Runtime**, and **Azure Logic Apps**.  
The solution follows the **Medallion Architecture (Bronze → Silver)** and implements:

- API ingestion  
- On-premises ingestion  
- SQL incremental ingestion  
- Delta-based transformation  
- Dynamic mapping  
- Parent pipeline orchestration  
- Failure alerting using Logic Apps  

All components were built without Synapse or Databricks, keeping the project **cost-optimized and production-ready**.

---

## 📘 Architecture Overview

             On-Prem / API / SQL Sources
                         │
                         ▼
                   BRONZE LAYER
           Raw Zone • JSON • CSV • SQL Dumps
                         │
                         ▼
                   SILVER LAYER
          Delta Lake • Cleaned • Curated • UPSERT
                         │
                         ▼
                  Analytics / BI Layer


---

## 📂 Technologies Used

| Purpose             | Service                           |
|---------------------|-----------------------------------|
| Orchestration       | Azure Data Factory                |
| Storage             | ADLS Gen2                         |
| File Format         | Delta (Silver), JSON/CSV (Bronze) |
| Transformations     | ADF Data Flows                    |
| Integration Runtime | AutoResolve IR + Self-Hosted IR   |
| Alerts              | Azure Logic Apps                  |
| Monitoring          | ADF Pipeline Runs                 |

---

# 🟦 1. API Ingestion (GitHub → ADLS Bronze)

This pipeline ingests JSON data from GitHub using the **Web API + Copy Activity** pattern.

### Key Features
- REST API ingestion using Web activity  
- Base URL + Relative URL handling  
- Writes raw JSON into: adls/bronze/github/


### Workflow
1. Web activity triggers API call  
2. Copy Activity writes API response to ADLS  
3. Data stored as JSON in the Bronze zone  

---

# 🟩 2. On-Premise Ingestion using Self-Hosted IR

This pipeline ingests CSV files from a local file system using a **Self-Hosted Integration Runtime**.

### Key Features
- Reads multiple CSV files dynamically  
- Uses a pipeline parameter: @pipeline().parameters.files
- Dynamic column mapping based on filename  
- Writes files into: adls/bronze/onprem/


### Dynamic Mapping Parameters
- `p_mapping_passenger`  
- `p_mapping_airline`  
- `p_mapping_flight`  

### Flow
- ForEach → Loop through files  
- Copy Activity → Map columns based on metadata  

---

# 🟧 3. SQL → Data Lake Incremental Ingestion (Watermark Load)

A robust incremental ingestion pipeline retrieves only changed rows from SQL using a **watermark** file stored in ADLS.

### Steps
1. **Lookup LastLoad** → Read previous timestamp  
2. **Lookup LatestLoad** → Read SQL table’s MAX(modified_date)  
3. **CopySQLData** → Copy only new/updated rows  
4. **WaterMark** → Update ADLS watermark file  

### Benefits
- Efficient incremental ingestion  
- Reduces load and cost  
- Reliable change tracking  

---

# 🟪 4. Silver Layer Transformations Using Delta Lake

ADF Mapping Data Flows are used to clean and prepare data for analytics.

### Outputs (Delta Tables)
- `DimPassenger`
- `DimAirline`
- `DimFlight`
- `DimAirport`
- `FactBookings`

### Transformations Include
- Data type casting  
- Null and inconsistency handling   
- Derived columns  
- UPSERT into Delta files  

### Example Transformation (Gender)
`iif(gender == 'M', 'Male', 'Female')`

---

# 🟩 5. Parent Pipeline – End-to-End Orchestration

The parent pipeline orchestrates all ingestion and transformation workflows sequentially.

Tasks Executed in Sequence:

- On-Prem ingestion
- API ingestion
- SQL incremental ingestion
- Web activity → Trigger Logic App
  
### Parameter Passing
- Parent pipeline passes dynamic parameters to each child pipeline.

### Example Web Activity Payload
```
{
"pipeline_name": "@{pipeline().Pipeline}",
  "run_id": "@{pipeline().RunId}",
  "status": "@{activity('Execute SQL To DataLake').Status}",
  "error": "@{if(equals(activity('Execute SQL To DataLake').Status,'Failed'), string(activity('Execute SQL To DataLake').error), 'No error')}"
}
```

---

# 🟪 6. Logic Apps – Pipeline Failure Alerts

Logic Apps is used to send an email when any child pipeline fails.

### Components
1. HTTP Trigger
2. JSON Schema validation
3. Send Email (V2) connector

### JSON Schema
```
{
  "type": "object",
  "properties": {
    "pipeline_name": { "type": "string" },
    "run_id": { "type": "string" },
    "status": { "type": "string" },
    "error": { "type": "string" }
  }
}
```
Email Includes
  1. Pipeline name
  2. Status
  3. Error message
  4. Run ID


# 🗄️ ADLS Folder Structure
```
adls/
 ├── bronze/
 │     ├── github/
 │     ├── onprem/
 │     └── sql/
 │
 ├── silver/
 │     ├── DimPassenger/
 │     ├── DimAirline/
 │     ├── DimFlight/
 │     ├── DimAirport/
 │     └── FactBookings/
 │
 └── gold/   (future enhancement)
```

# 🏆 Key Highlights

```
1. Fully automated Medallion Architecture
2. Metadata-driven ingestion flows
3. API, On-Prem, and SQL sources unified
4. Delta Lake UPSERT logic implemented
5. Watermark-based incremental SQL ingestion
6. Parent pipeline for full orchestration
7. Logic Apps for real-time failure alerts
8. Optimized for zero-cost Azure usage
```
