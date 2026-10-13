# Microsoft Fabric Medallion Architecture – ShoppingMart Data Engineering Project

## Overview

This project demonstrates an end-to-end data engineering solution built using **Microsoft Fabric** and the **Medallion Architecture**.

The project processes ShoppingMart data through three layers:

**Bronze → Silver → Gold → Power BI**

The objective is to ingest raw data, clean and transform it using PySpark, generate business-level KPIs, and prepare the data for reporting and analytics.

## Architecture

The following diagram illustrates the overall architecture implemented in this project.

![Microsoft Fabric Medallion Architecture](Medalion%20architure%20layout.png)

### Architecture Flow

```text
Source Data
    |
    v
Fabric Data Factory / Pipeline
    |
    v
Bronze Lakehouse
(Raw Data)
    |
    v
Silver Lakehouse
(Cleaned and Transformed Data)
    |
    v
Gold Lakehouse
(Business-Ready Data and KPIs)
    |
    v
Power BI
(Reporting and Analytics)
```

## Technologies Used

* Microsoft Fabric
* Fabric Data Factory
* Fabric Pipelines
* Fabric Lakehouse
* PySpark
* Python
* Parquet
* Delta Tables
* SQL Analytics Endpoint
* Power BI
* GitHub

---

# 1. Bronze Layer – Raw Data Ingestion

The Bronze layer is responsible for ingesting source data into the Fabric Lakehouse with minimal transformation.

A metadata-driven pipeline was created to process multiple datasets dynamically using a reusable workflow.

## Bronze Pipeline Architecture

![Bronze Layer Pipeline](Bronze%20layer%20pipeline.png)

### Pipeline Flow

```text
Lookup
   |
   v
ForEach
   |
   v
Delete Data
   |
   v
Copy Data
   |
   v
Bronze Lakehouse
```

### 1.1 Lookup – Datamart_Bronzelayer Lookup

The Lookup activity retrieves metadata and configuration required for the Bronze data load.

The returned metadata is passed to the ForEach activity to process multiple datasets.

### 1.2 ForEach – ForEach1

The ForEach activity iterates through the datasets returned by the Lookup activity.

This approach allows multiple files or datasets to be processed using a single reusable pipeline rather than creating a separate pipeline for each dataset.

### 1.3 Delete Data

The Delete Data activity removes existing data from the configured target location before a new load.

This step supports a reload pattern. It should be configured carefully to avoid deleting data that must be retained.

### 1.4 Copy Data

The Copy Data activity copies source data into the Bronze Lakehouse.

The Bronze layer preserves the ingested data with minimal transformation, making it available for downstream processing.

### Bronze Layer Objectives

* Store raw ingested data.
* Preserve source data for downstream processing.
* Support repeatable data ingestion.
* Reduce duplicated pipeline development through metadata-driven processing.
* Provide a foundation for Silver and Gold transformations.

---

# 2. Silver Layer – Data Cleaning and Transformation

The Silver layer transforms raw Bronze data into clean, structured, and reliable datasets.

A Fabric notebook uses PySpark to perform data cleaning, standardization, and data-quality operations before writing the results to the Silver Lakehouse.

## Datasets Processed

* Orders
* Reviews
* Social Media
* Web Logs

## Orders Transformation

The Orders dataset is processed using PySpark.

The transformations include:

* Removing records with missing required values.
* Handling the `OrderDate` column.
* Converting `OrderDate` into a date data type.
* Preparing the cleaned dataset for downstream analytics.

The transformed Orders data is then stored in the Silver Lakehouse.

## Silver Layer Output

```text
Silver Lakehouse
|
|-- Orders
|
|-- Reviews
|
|-- Social Media
|
|-- Web Logs
```

The Silver layer provides clean and structured data for the Gold transformation layer.

### Silver Layer Objectives

* Improve data quality.
* Standardize data types and formats.
* Handle missing or invalid values.
* Prepare datasets for business-level calculations.
* Create reliable inputs for downstream analytics.

---

# 3. Gold Layer – Business Transformations and KPIs

The Gold layer contains business-ready datasets generated from the cleaned Silver data.

A Fabric notebook reads the Silver datasets and performs aggregations to create useful business KPIs.

## Datasets Processed

* Orders
* Reviews
* Social Media
* Web Logs

## KPI 1 – Web Log Engagement

Web log data is grouped by:

* `user_id`
* `page`
* `action`

The number of occurrences is calculated using `count()`.

This provides insight into user engagement across different pages and actions.

### Example Output

| user_id | page     | action | count |
| ------: | -------- | ------ | ----: |
|     101 | Home     | Click  |    15 |
|     101 | Products | View   |    24 |
|     102 | Checkout | Click  |     8 |

The aggregated results are stored in the Gold Web Logs dataset.

## KPI 2 – Social Media Sentiment

Social media data is grouped by:

* `platform`
* `sentiment`

The number of records for each combination is calculated.

This helps identify sentiment trends across social media platforms.

### Example Output

| platform  | sentiment | count |
| --------- | --------- | ----: |
| Instagram | Positive  |   120 |
| Instagram | Negative  |    35 |
| Facebook  | Positive  |    95 |
| Twitter   | Neutral   |    52 |

The results are stored in the Gold Social Media dataset.

## KPI 3 – Average Product Rating

The Reviews dataset is grouped by `product_id`.

The average product rating is calculated for each product.

### Example Output

| product_id | AvgRating |
| ---------: | --------: |
|        101 |       4.5 |
|        102 |       3.8 |
|        103 |       4.2 |

The results are stored in the Gold Reviews dataset.

*Note: The tables above illustrate the expected output format. Replace the example values with actual results from your notebook when available.*

## Gold Layer Output

```text
Gold Lakehouse
|
|-- Gold Orders
|
|-- Gold Reviews
|
|-- Gold Social Media
|
|-- Gold Web Logs
```

The Gold layer provides business-ready datasets for reporting and analytics.

---

# 4. Power BI – Reporting and Analytics

The Gold layer is designed to provide the final analytical datasets required for reporting.

Gold data can be consumed by Power BI through supported Fabric connectivity options, such as the Lakehouse SQL analytics endpoint where appropriate.

Potential dashboard areas include:

* Order analysis
* Product ratings
* Social media sentiment
* User engagement
* Customer and product insights

**Status:** Power BI reporting is in progress.

---

# 5. End-to-End Data Flow

The complete project follows this workflow:

```text
                  SOURCE DATA
                      |
                      v
           FABRIC DATA FACTORY
                  PIPELINE
                      |
                      v
              LOOKUP ACTIVITY
                      |
                      v
              FOREACH ACTIVITY
                      |
                      v
               DELETE / COPY
                      |
                      v
              BRONZE LAKEHOUSE
                      |
                      v
               SILVER NOTEBOOK
                      |
                      v
           CLEANING AND TRANSFORMATION
                      |
                      v
              SILVER LAKEHOUSE
                      |
                      v
                GOLD NOTEBOOK
                      |
                      v
               KPI AGGREGATIONS
                      |
                      v
               GOLD LAKEHOUSE
                      |
                      v
                   POWER BI
```

---

# 6. Project Objectives

The main objectives of this project are:

1. Build a metadata-driven data ingestion pipeline.
2. Implement the Bronze layer for raw data ingestion.
3. Implement the Silver layer for data cleaning and transformation.
4. Implement the Gold layer for business-level aggregations.
5. Generate analytical KPIs from ShoppingMart datasets.
6. Prepare Gold datasets for Power BI reporting.
7. Understand how Fabric Pipelines, Lakehouse, and PySpark work together.
8. Demonstrate an end-to-end Microsoft Fabric data engineering workflow.

---

# 7. Project Status

| Component              | Status      |
| ---------------------- | ----------- |
| Bronze Pipeline        | Completed   |
| Bronze Lakehouse       | Completed   |
| Silver Transformations | Completed   |
| Silver Lakehouse       | Completed   |
| Gold Transformations   | Completed   |
| Gold Lakehouse         | Completed   |
| KPI Aggregations       | Completed   |
| Power BI Reporting     | In Progress |

---

# 8. Repository Structure

```text
microsoft-fabric-medallion-architectures/
|
|-- README.md
|
|-- Medalion architure layout.png
|
|-- Bronze layer pipeline.png
|
|-- Gold_layer_Notebook/
|
|-- Silver_layer_Notebook/
```

---

# 9. Key Learnings

Through this project, I explored how Microsoft Fabric can support an end-to-end data engineering workflow using the Medallion Architecture.

Key concepts include:

* Building reusable, metadata-driven Fabric Pipelines.
* Ingesting source data into a Bronze Lakehouse.
* Cleaning and transforming data using PySpark.
* Creating Silver and Gold data layers.
* Generating business KPIs through aggregations.
* Preparing structured datasets for Power BI reporting.

The project follows the principle:

**Raw Data → Clean Data → Business-Ready Data → Reporting**

This layered approach helps separate ingestion, data quality, and business transformations, making data solutions easier to maintain, troubleshoot, and extend.
