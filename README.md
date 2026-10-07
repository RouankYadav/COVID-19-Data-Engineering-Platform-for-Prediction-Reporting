# COVID-19 Data Engineering Platform (Azure)

An end-to-end Azure data engineering project that ingests European COVID-19 and population data, transforms it, and serves it to two audiences: a **data lake** for machine learning teams and a **data warehouse** with **Power BI** reporting for analysts.

**Tech stack:** Azure Data Factory · ADLS Gen2 · Azure Blob Storage · Azure Databricks (PySpark) · Azure SQL Database · Power BI

> Built as a hands-on learning project while following an Azure data engineering course. The architecture follows that course's design.

---

## Problem

COVID-19 case data and population data live in separate public sources. ML engineers need detailed, combined datasets, while analysts need clean, structured tables for reporting. This project builds one pipeline that feeds both.

## Architecture

```text
   ECDC COVID-19 data (HTTP)        Eurostat population data (Blob Storage)
                 \                          /
                  v                        v
                 Azure Data Factory (ingestion + orchestration)
                              |
                              v
                    ADLS Gen2 - raw layer
                              |
                              v
             Transformation (ADF + PySpark notebooks)
                    /                      \
                   v                        v
       Data lake (processed)         Azure SQL Database
       for ML / data science         (data warehouse)
                                              |
                                              v
                                          Power BI
```

## Data sources

- **ECDC:** COVID-19 cases, deaths, hospital and ICU admissions, and testing numbers by country.
- **Eurostat:** population by age group, used to add demographic context for analysis.

## Pipeline

**1. Ingestion (Azure Data Factory)**
Copy activities pull ECDC files over HTTP and population data from Blob Storage into the raw zone of ADLS Gen2.

**2. Transformation**
Data is cleaned and prepared for both use cases: data type conversion, filtering invalid records, handling missing values, standardising country information, and joining COVID-19 data with population data.

**3. Serving**
- **Data lake:** processed, ML-ready datasets for spread and mortality analysis.
- **Data warehouse:** analytics-ready tables in Azure SQL Database.

**4. Reporting**
Power BI connects to the warehouse for KPIs such as total cases, deaths, hospital and ICU admissions, testing, and case fatality rate, plus trends by country and by population.

## Orchestration and monitoring

Azure Data Factory orchestrates the workflow using pipelines, parameters, Lookup and ForEach activities, validation and conditional checks, and triggers for automated runs. Pipeline and activity runs are monitored through ADF monitoring.

## Warehouse model

```text
DimDate ----\
             >---- FactCOVID19 (cases, deaths, hospitalisations, ICU, tests)
DimCountry -/
```

## Repository structure

```text
raw/               raw source data
processed/         processed output
ecdc_data/         ECDC source files
eurostat_data/     Eurostat population files
lookup_data/       lookup / reference data
pyspark_notebooks/ PySpark transformation notebooks
hdinsight_scripts/ HDInsight scripts
sql_scripts/       SQL for the warehouse
power_bi_reports/  Power BI reports
config/            pipeline configuration
cicd/              CI/CD configuration
```

## Possible improvements

- Build and serve an ML model on top of the data lake (the lake is prepared for this; no model is trained yet).
- Add automated data quality checks and alerting on failed pipeline runs.
- Add screenshots of pipeline runs and the Power BI dashboard.

## License

For learning and portfolio purposes.
