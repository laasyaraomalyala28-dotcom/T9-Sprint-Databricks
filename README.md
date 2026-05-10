# Retail Sales Data Warehouse Validation Framework

A complete end-to-end Data Warehouse and ETL validation project built using Databricks, Delta Lake, AWS S3, SQL, and Python.

This implementation simulates a real-world retail analytics platform with incremental processing, SCD Type 2 handling, archival validation, and automated data quality checks.

# Project Objective

The objective of this project is to design and validate a retail sales data warehouse pipeline that processes data from multiple source systems into analytics-ready warehouse tables.

The solution focuses on:

- End-to-end ETL workflow implementation
- Data quality validation
- Incremental data processing
- Historical customer tracking using SCD Type 2
- Automated archival verification
- Workflow orchestration
- Logging and audit monitoring

The project replicates real-world ETL QA and Data Engineering scenarios commonly used in enterprise environments.

# Business Scenario

A retail organization receives daily source files containing:

- Customer information
- Product details
- Store information
- Sales transactions

Files arrive in an ingestion zone using timestamp-based naming conventions:

```text
<filename>_DDMMYYYY_HHMMSS.csv
```

Whenever a new incremental file arrives:

- The latest file remains in the active processing zone
- Older files are archived automatically
- Validation checks confirm archival consistency across all zones

The pipeline validates whether data is correctly processed from source files into dimensional warehouse tables.

# Solution Architecture

```text
SFTP / Source Files
          ↓
Bronze Layer (Raw Ingestion)
          ↓
Silver Layer (Cleansed & Validated Data)
          ↓
Gold Layer (Warehouse Model)
          ↓
Incremental MERGE Processing
          ↓
SCD Type 2 Processing
          ↓
ETL Validation Framework
          ↓
Archival & Monitoring System
```

# Technology Stack

| Technology | Purpose |
|---|---|
| Databricks | Data Engineering & Processing |
| Delta Lake | Transactional Lakehouse Storage |
| AWS S3 | Cloud Storage |
| SQL | ETL Transformations & Validation |
| Python | Automation & Archival Validation |
| Databricks Workflows | Pipeline Scheduling |
| Delta MERGE | Incremental Load Processing |

# Data Warehouse Model

The warehouse follows a dimensional modeling approach consisting of:

## Dimension Tables

### DimCustomer
Implements Slowly Changing Dimension Type 2 logic to maintain customer history.

### DimProduct
Stores product reference information.

### DimStore
Stores store and regional information.

## Fact Table

### FactSales
Captures daily retail sales transactions and business metrics.

# ETL Pipeline Layers

## Bronze Layer — Raw Data Ingestion

### Purpose
- Load raw CSV files from source storage
- Preserve original source structure
- Store raw data in Delta format

### Notebooks
- `02_bronze_layer.ipynb`

### Tables
- `bronze.customers`
- `bronze.products`
- `bronze.stores`
- `bronze.sales`

## Silver Layer — Data Cleansing & Standardization

### Purpose
Transform and validate raw data before warehouse loading.

### Transformations Performed
- Remove leading/trailing spaces
- Convert emails to lowercase
- Proper case formatting for names
- Date standardization
- Data type conversion
- Duplicate removal using business keys

### Deduplication Keys
- CustomerID
- ProductID
- StoreID
- TransactionID

### Notebook
- `03_silver_layer.ipynb`

## Gold Layer — Warehouse & Analytics

### Purpose
Build analytics-ready dimensional tables and fact relationships.

### Features
- Surrogate key generation
- Fact-to-dimension lookups
- Derived amount calculation
- Warehouse optimization

### Notebook
- `04_gold_layer.ipynb`

# Incremental Load Processing

Incremental loading is implemented using Delta Lake MERGE operations.

## Supported Operations
- Insert new records
- Update modified records
- Prevent duplicate ingestion
- Support rerunnable execution

### Notebooks
- `05_incremental_load.ipynb`

# Slowly Changing Dimension (SCD Type 2)

SCD Type 2 logic is implemented for customer dimension tracking.

## Business Rules
- Detect changes in City or Address
- Expire previous active record
- Insert updated active record
- Maintain historical customer versions

## Validation Scenarios

| Scenario | Expected Result |
|---|---|
| No customer change | No new row inserted |
| City changed | Old record expired, new active row created |
| Address changed | New version inserted |
| Duplicate customer | Only one active record maintained |

### Notebooks
- `06_scd.ipynb`

# ETL Validation Framework

Comprehensive testing is implemented to validate data accuracy and integrity.

## 1. Source-to-Target Validation

Checks include:

- Row count reconciliation
- Column mapping verification
- Data type validation

## 2. Transformation Validation

Validation rules:

- Proper case formatting
- Lowercase email conversion
- Trim validation
- Amount derivation checks
- Date format validation

## 3. Data Quality Validation

Quality checks include:

- Duplicate detection
- Null validation
- Invalid record rejection
- Referential integrity validation

## 4. Incremental Load Validation

Tests include:

- Incremental insert verification
- Update validation
- Duplicate prevention

## 5. SCD Type 2 Validation

Validation checks:

- Active vs inactive records
- Historical data verification
- StartDate & EndDate validation

### Notebook
- `07_validation_testing.ipynb`

# Archival Validation Framework

The project includes automated validation for file archival processes across all zones.

## Validation Objectives

- Ensure previous files are archived
- Ensure only latest files remain active
- Verify timestamp-based naming conventions
- Detect archival failures
- Validate consistency across zones

## Naming Convention

```text
<filename>_DDMMYYYY_HHMMSS.csv
```

# Logging & Audit Monitoring

Audit and logging mechanisms are included to track pipeline execution and failures.

## Features
- ETL execution tracking
- Validation status monitoring
- Error logging
- Audit reporting

### Notebook
- `09_etl_log_audit.ipynb`

# CDC Demonstration

Delta Lake Change Data Feed (CDF) is used to demonstrate change tracking capabilities.

## Tracked Operations
- INSERT
- UPDATE
- DELETE

# Workflow Automation

The entire ETL lifecycle is orchestrated using Databricks Workflows.

# Repository Structure

```text
T9-Sprint-Databricks/
│
├── 01_initial_setup.ipynb
├── 02_bronze_layer.ipynb
├── 03_silver_layer.ipynb
├── 04_gold_layer.ipynb
├── 05_incremental_load.ipynb
├── 06_scd.ipynb
├── 07_validation_testing.ipynb
├── 08_Archival.ipynb
├── 09_etl_log_audit.ipynb
├── incremental_load_02.ipynb
├── scd_02.ipynb
└── README.md
```

# Key Features Implemented

- End-to-End ETL Pipeline
- Bronze-Silver-Gold Lakehouse Architecture
- Incremental Data Processing
- SCD Type 2 Historical Tracking
- ETL Validation Framework
- Data Quality Checks
- Referential Integrity Validation
- Automated Archival Validation
- Audit Logging & Monitoring
- Workflow Orchestration
- Delta Lake MERGE Operations

# Sample Validation Areas

The project validates:

- Source vs target row counts
- Transformation accuracy
- Duplicate handling
- Active customer versioning
- Incremental processing logic
- Fact-to-dimension relationships
- File archival consistency

# Future Improvements

Potential future enhancements include:

- CI/CD Integration
- Automated Alert Notifications
- Data Quality Dashboards
- Rejection Tables
- Metadata-Driven Pipelines
- Performance Optimization
- Real-Time Streaming Support
- Unity Catalog Integration

# Conclusion

This project demonstrates a practical implementation of a modern ETL validation and Lakehouse data engineering solution using Databricks and Delta Lake.

The pipeline covers the complete lifecycle of enterprise ETL processing including ingestion, transformation, historical tracking, validation testing, archival management, and workflow automation while maintaining strong data quality and auditing standards.
