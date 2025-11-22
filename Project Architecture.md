<center> ## Project Architecture (High - Level) </center>

             ┌──────────────────────┐
             │   Azure DevOps Repo  │
             └─────────┬────────────┘
                       │ CI/CD
        ┌──────────────┴─────────────────┐
        │Azure DevOps Pipeline (YAML)    │
        └──────────────┬─────────────────┘
                       │ Release
        ┌──────────────┴──────────────────────┐
        │ Azure Data Factory  (DEV → PROD)    │
        └──────────────┬──────────────────────┘
                       │ Triggers
       ┌───────────────┴───────────────┐
Raw →  │   Azure Data Lake Gen2        │ → Processed
       └───────────────┬───────────────┘
                       │ Mount
       ┌───────────────┴───────────────┐
       │        Databricks Notebook     │
       └───────────────┬───────────────┘
                       │ Load
       ┌───────────────┴───────────────┐
       │    Azure SQL Database          │
       └────────────────────────────────┘
