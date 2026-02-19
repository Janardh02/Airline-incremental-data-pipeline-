#  Airline Incremental Data Processing Pipeline (AWS)



## Architecture<img width="700" height="842" alt="Architetcture" src="https://github.com/user-attachments/assets/18ccf244-2abe-47a2-abf1-bd35d330d5ab" />






##  Project Overview

This project implements an event-driven incremental data pipeline on AWS to process daily airline flight data, enrich it with airport reference data, and load the curated dataset into a data warehouse.

The architecture follows modern data engineering principles including:
- Event-driven processing
- Workflow orchestration
- Incremental ETL
- Dimensional data modeling
- Automated monitoring & notifications


##  Architecture Overview

###  End-to-End Flow

1. Daily flight CSV file is uploaded to Amazon S3.
2. Amazon EventBridge detects the object creation event.
3. EventBridge triggers AWS Step Functions.
4. Step Function:
   - Starts Glue Crawler
   - Waits for crawler completion
   - Triggers Glue ETL Job (incremental processing)
   - Sends success/failure notification via SNS
5. Glue job enriches flight data using airport dimension table from Redshift.
6. Transformed data is loaded into Amazon Redshift.


##  AWS Services Used

- Amazon S3 – Raw data storage
- AWS Glue – ETL processing
- AWS Glue Crawler – Metadata cataloging
- AWS Step Functions – Workflow orchestration
- Amazon EventBridge – Event trigger
- Amazon SNS – Notifications
- Amazon Redshift – Data warehouse


##  Data Architecture

### Raw Layer (S3)
- Stores daily flight CSV files
- Stores airport_codes reference file

### Reference Layer (Redshift)
- `dim_airport_codes` table
- Preloaded once using COPY command
- Treated as static reference data

### Curated Layer (Redshift)
- `daily_flights_processed`
- Contains enriched, analytics-ready dataset


##  Incremental Processing Logic

The AWS Glue job is configured with **Job Bookmarking** enabled.

This ensures:
- Only newly uploaded flight files are processed
- Previously processed files are skipped
- No duplicate data ingestion
- Scalable and production-ready pipeline behavior

Airport dimension data is static and not incremental.


##  Step Function Workflow

1. Start Glue Crawler
2. Poll crawler status until completion
3. Trigger Glue ETL job (synchronous execution)
4. Send SNS notification:
   - Success message on completion
   - Failure message on error

This provides controlled orchestration and error handling.


##  Glue ETL Logic

The Glue job performs the following steps:

1. Read raw flight data from Glue Catalog
2. Read airport dimension table from Redshift
3. Join for departure airport enrichment
4. Join for arrival airport enrichment
5. Apply schema mappings
6. Load curated data into Redshift target table






