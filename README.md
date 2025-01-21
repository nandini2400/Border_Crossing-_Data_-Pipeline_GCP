# Border Crossing Data Pipeline
## Overview
### The Border Crossing Data Pipeline is a data processing pipeline designed to ingest, clean, transform, and aggregate border crossing data. It uses Google Cloud Storage (GCS) for file storage and Google BigQuery for data processing and analysis. The pipeline follows the Bronze-Silver-Gold architecture, which is a common pattern in data engineering for managing data quality and transformation.

## Architecture
The pipeline consists of three layers:

## Bronze Layer:

Raw data is ingested from CSV files stored in GCS.

Data is loaded into BigQuery without any transformations.

## Silver Layer:

Raw data is cleaned and standardized (e.g., column names are lowercased and spaces are replaced with underscores).

Cleaned data is stored in BigQuery.

## Gold Layer:

Data is aggregated and summarized (e.g., total values by measure).

Business-ready data is stored in BigQuery for reporting and analysis.
# Components
## 1. Google Cloud Storage (GCS)
Input Folder: cross_border/

New CSV files are uploaded here for processing.

Processed Folder: cross_border/processed/

Processed files are moved here to avoid reprocessing.

## 2. Google BigQuery
**Bronze Dataset**: dataset_bronze

Stores raw data loaded from GCS.

**Silver Dataset:** dataset_silver

Stores cleaned and standardized data.

**Gold Dataset:** dataset_gold

Stores aggregated and summarized data.

## 3. Cloud Function
The pipeline is triggered by a Cloud Function (border_crossing_entry) whenever a new file is uploaded to the GCS bucket.

# Functionality
## 1. border_crossing_entry Function
**Purpose:** Entry point for the pipeline. Processes new files uploaded to GCS.

**Steps:**

Lists all CSV files in the cross_border/ folder.

For each file:

Loads raw data into the Bronze Layer.

Transforms data into the Silver Layer.

Aggregates data into the Gold Layer.

Moves the processed file to the processed/ folder.

## 2. load_to_bigquery Function
**Purpose:** Loads raw CSV data from GCS into BigQuery (Bronze Layer).

**Parameters:**

uri: GCS URI of the CSV file.

table_id: BigQuery table ID for the Bronze Layer.

**Details:**

Uses bigquery.LoadJobConfig to configure the load job.

Automatically detects the schema and skips the header row.

## 3. transform_bronze_to_silver Function
**Purpose:** Cleans and standardizes raw data (Bronze Layer) and saves it to the Silver Layer.

**Parameters:**

bronze_table_id: BigQuery table ID for the Bronze Layer.

silver_table_id: BigQuery table ID for the Silver Layer.

**Details:**

Queries data from the Bronze Layer.

Cleans column names (e.g., lowercasing and replacing spaces with underscores).

Saves the cleaned data to the Silver Layer.

## 4. transform_silver_to_gold Function
**Purpose:** Aggregates cleaned data (Silver Layer) and saves it to the Gold Layer.

**Parameters:**

silver_table_id: BigQuery table ID for the Silver Layer.

gold_table_id: BigQuery table ID for the Gold Layer.

**Details:**

Aggregates data by measure and calculates the total value.

Saves the aggregated data to the Gold Layer.

## 5. move_to_processed_folder Function
Purpose: Moves processed files to the processed/ folder in GCS.

**Parameters:**

blob: GCS blob object representing the file.

**Details:**

Renames the blob to move it to the processed/ folder.



