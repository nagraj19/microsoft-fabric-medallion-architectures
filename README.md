# Microsoft Fabric Medallion Architecture – ShoppingMart Data Engineering Project

## Overview

This project demonstrates an end-to-end data engineering solution built using **Microsoft Fabric** and the **Medallion Architecture**.

The project processes ShoppingMart data through three layers:

**Bronze → Silver → Gold → Power BI**

The objective is to ingest raw data, clean and transform it, create business-level KPIs, and prepare the data for analytics and reporting.

---

# Architecture

```text
Source Data
    │
    ▼
┌──────────────────────┐
│   Bronze Layer       │
│   Raw Data           │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   Silver Layer       │
│ Cleaning &           │
│ Transformation       │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   Gold Layer         │
│ KPIs & Aggregations  │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│      Power BI        │
│ Reporting & Analytics│
└──────────────────────┘
```

---

# Technologies Used

* Microsoft Fabric
* Fabric Data Factory / Pipelines
* Fabric Lakehouse
* PySpark
* Python
* Parquet
* Delta Tables
* SQL Analytics Endpoint
* Power BI
* GitHub

---

# Bronze Layer

The Bronze layer is responsible for ingesting source data into the Fabric Lakehouse with minimal transformation.

A metadata-driven pipeline was created to dynamically process multiple datasets.

## Bronze Pipeline Flow

```text
Lookup
   ↓
ForEach
   ↓
Delete Data
   ↓
Copy Data
   ↓
Bronze Lakehouse
```

### 1. Lookup – Datamart_Bronzelayer Lookup

The Lookup activity retrieves metadata and configuration required for the Bronze data load.

The metadata is passed to the ForEach activity for processing.

### 2. ForEach – ForEach1

The ForEach activity iterates through each dataset returned by the Lookup activity.

This allows multiple datasets to be processed using a single reusable pipeline.

### 3. Delete Data

The Delete Data activity removes existing data from the target location before the new data is loaded.

### 4. Copy Data

The Copy Data activity copies the source data into the Bronze Lakehouse.

The Bronze layer preserves the source data with minimal transformation.

## Bronze Layer Purpose

The Bronze layer:

* Stores raw ingested data
* Preserves source data
* Provides a reliable source for downstream processing
* Supports repeatable data ingestion
* Enables scalable processing of multiple datasets

---

# Silver Layer

The Silver layer is responsible for cleaning and transforming the raw Bronze data into structured and usable datasets.

The Silver notebook reads the Bronze data using PySpark and performs data-quality and transformation operations before writing the results to the Silver Lakehouse.

## Silver Layer Processing

The following datasets are processed:

* Orders
* Reviews
* Social Media
* Web Logs

## Orders Transformation

The Orders dataset is cleaned using PySpark.

The processing includes:

* Removing records with missing required values
* Handling the `OrderDate` column
* Converting `OrderDate` into a date data type
* Preparing the dataset for downstream analytics

The cleaned Orders data is stored in the Silver Lakehouse.

## Silver Layer Output

```text
Silver Lakehouse
│
├── customers.ordersdata
│
├── ShoppingMart_Silver_Reviews
│   └── ShoppingMart_review
│
├── ShoppingMart_Silver_Social_Media
│   └── ShoppingMart_social_media
│
└── ShoppingMart_Silver_Web_Logs
    └── ShoppingMart_web_logs
```

The Silver layer provides clean and structured data for the Gold transformation layer.

---

# Gold Layer

The Gold layer contains business-ready data created from the Silver datasets.

The Gold notebook reads the cleaned Silver data and performs aggregations to create useful business KPIs.

## Gold Layer Processing

The following datasets are read from the Silver Lakehouse:

* Orders
* Reviews
* Social Media
* Web Logs

---

## KPI 1 – Web Log Engagement

Web log data is aggregated by:

* `user_id`
* `page`
* `action`

The number of occurrences is calculated using `count()`.

This provides an indication of user engagement across different pages and actions.

### Example Output

```text
user_id | page       | action | count
--------------------------------------
101     | Home       | Click  | 15
101     | Products   | View   | 24
102     | Checkout   | Click  | 8
```

The aggregated results are stored in the Gold Web Logs dataset.

---

## KPI 2 – Social Media Sentiment

Social media data is grouped by:

* `platform`
* `sentiment`

The number of records for each combination is calculated.

This helps identify sentiment trends across different social media platforms.

### Example Output

```text
platform | sentiment | count
----------------------------
Instagram| Positive   | 120
Instagram| Negative   | 35
Facebook | Positive   | 95
Twitter  | Neutral    | 52
```

The results are stored in the Gold Social Media dataset.

---

## KPI 3 – Average Product Rating

The Reviews dataset is grouped by:

* `product_id`

The average rating is calculated for each product.

```text
product_id | AvgRating
----------------------
101        | 4.5
102        | 3.8
103        | 4.2
```

The results are stored in the Gold Reviews dataset.

---

# Gold Layer Output

```text
Gold Lakehouse
│
├── ShoppingMart_Gold_Orders
│   └── ShoppingMart_customers_orderdata
│
├── ShoppingMart_Gold_Reviews
│   └── ShoppingMart_review
│
├── ShoppingMart_Gold_Social_Media
│   └── ShoppingMart_social_media
│
└── ShoppingMart_Gold_Web_Logs
    └── ShoppingMart_web_logs
```

The Gold layer provides business-ready datasets for reporting and analytics.

---

# Power BI

The Gold layer is designed to provide the final analytical datasets required for reporting.

The Gold data can be exposed through the Fabric Lakehouse and SQL Analytics Endpoint and then consumed by Power BI.

The reporting layer can be used to create dashboards for:

* Order analysis
* Product ratings
* Social media sentiment
* User engagement
* Customer and product insights

---

# End-to-End Data Flow

The complete project follows this flow:

```text
                 SOURCE DATA
                     │
                     ▼
          ┌─────────────────────┐
          │   BRONZE PIPELINE   │
          │                     │
          │ Lookup              │
          │    ↓                │
          │ ForEach             │
          │    ↓                │
          │ Delete Data         │
          │    ↓                │
          │ Copy Data           │
          └──────────┬──────────┘
                     │
                     ▼
              BRONZE LAKEHOUSE
                     │
                     ▼
             SILVER NOTEBOOK
                     │
          Cleaning & Transformation
                     │
                     ▼
              SILVER LAKEHOUSE
                     │
                     ▼
              GOLD NOTEBOOK
                     │
            KPI Aggregations
                     │
                     ▼
               GOLD LAKEHOUSE
                     │
                     ▼
                  POWER BI
```

---

# Project Objectives

The main objectives of this project are:

1. Build a metadata-driven ingestion pipeline.
2. Implement the Bronze layer for raw data ingestion.
3. Implement the Silver layer for data cleaning and transformation.
4. Implement the Gold layer for business-level aggregations.
5. Create analytical KPIs from customer and product data.
6. Prepare Gold data for Power BI reporting.
7. Demonstrate an end-to-end Microsoft Fabric data engineering workflow.

---

# Project Status

| Layer                  | Status      |
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

# Repository Structure

```text
microsoft-fabric-medallion-architectures/
│
├── README.md
│
├── Gold_layer_Notebook/
│
├── Silver_layer_Notebook/
│
├── Screenshot 2026-10-06 172656.png
│
└── Bronze Pipeline Documentation
```

---

# Key Learning

This project demonstrates how Microsoft Fabric can be used to build a complete modern data platform using the Medallion Architecture.

The project separates:

**Raw Data → Clean Data → Business Data → Reporting**

This separation makes the data pipeline easier to maintain, troubleshoot, scale, and consume for analytics.
