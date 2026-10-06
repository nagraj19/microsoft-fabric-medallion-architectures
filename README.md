Bronze Layer Pipeline
Overview

The Bronze Layer Pipeline is responsible for ingesting source data into the Bronze layer of the Medallion Architecture.

The pipeline uses a metadata-driven approach, where a Lookup activity retrieves the required datasets/tables from the configuration. A ForEach activity then iterates through each item and executes the required data-loading process.

Pipeline Flow
Lookup → ForEach → Delete Data → Copy Data
Activities
1. Lookup – Datamart_Bronzelayer Lookup

The Lookup activity retrieves the metadata and configuration required for the Bronze layer data load.

The output from the Lookup activity contains the information required to process each dataset/table and is passed to the ForEach activity.

2. ForEach – ForEach1

The ForEach activity iterates through each item returned by the Lookup activity.

For each item, the pipeline performs the following operations:

Removes the existing data from the target location.
Copies the latest source data into the Bronze layer.

This approach allows multiple datasets/tables to be processed dynamically using a single pipeline.

3. Delete Data

The Delete Data activity removes the existing data from the target location before the new data is loaded.

This ensures that the target location contains the latest version of the source data.

4. Copy Data

The Copy Data activity transfers data from the source system into the Bronze layer.

The data is stored in the Bronze layer with minimal transformation, preserving the source data for downstream processing.

Execution Flow
        Datamart_Bronzelayer Lookup
                    ↓
                ForEach1
                    ↓
               Delete Data
                    ↓
                Copy Data
                    ↓
              Bronze Layer
Bronze Layer Purpose

The Bronze layer acts as the raw data layer in the Medallion Architecture.

Its primary purpose is to:

Store ingested source data.
Preserve the source data with minimal transformation.
Provide a reliable input for the Silver transformation layer.
Support repeatable and scalable data ingestion.
