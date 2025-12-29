# Incremental_Ingestion_loop

Incremental_Ingestion_loop is an Azure Data Factory pipeline designed to ingest data from Azure SQL Database into Azure Data Lake Storage Gen2 using incremental and backfill processing.

Description

The pipeline is metadata-driven and processes multiple tables in parallel. It ingests data based on a configurable change data capture or timestamp column and writes the output to the Bronze layer in Parquet format. The pipeline maintains ingestion state using JSON-based CDC files stored in the data lake.

Processing Logic

For each table, the pipeline determines the ingestion mode at runtime. When no backfill date is provided, the pipeline performs an incremental load by reading only records newer than the last processed CDC value. When a backfill date is supplied, the pipeline ignores the stored CDC value and ingests data starting from the specified date.

Backfill Handling

Backfilling is supported at a table level and can be used for initial historical loads, partial reprocessing, or recovery scenarios. Backfill execution does not update CDC state unless new data is successfully ingested, ensuring future incremental runs remain consistent.

Empty File Handling

Azure Data Factory may create output files even when no records are returned from the source. The pipeline includes validation logic to detect this condition and automatically deletes such files. This ensures that only meaningful data files exist in the Bronze layer and prevents downstream processing issues.

State Management

CDC state is stored per table in JSON files within the data lake. These files act as the single source of truth for incremental processing and are updated only when new data is ingested successfully.

Design Notes

The pipeline is optimized for Bronze-layer ingestion in a medallion architecture. CDC columns should be indexed in the source database to ensure efficient extraction. Downstream deduplication, transformation, and merging are expected to occur in later processing layers.
<img width="1197" height="453" alt="image" src="https://github.com/user-attachments/assets/5a5f8912-410a-4def-ba1e-ca206f47bfe9" />
<img width="1151" height="597" alt="image" src="https://github.com/user-attachments/assets/e5d0b708-25e0-459f-a07d-01d30aa853b8" />
<img width="973" height="377" alt="image" src="https://github.com/user-attachments/assets/f9200732-aa86-4d9e-ab30-6a7535b47c77" />
<img width="977" height="356" alt="image" src="https://github.com/user-attachments/assets/dd9ab3f3-c6ea-4866-be94-9a4bbf6d517f" />

