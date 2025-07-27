# My Dataflow Pipeline Documentation

## Pipeline Overview
- **Purpose:** This pipeline reads historical stock data from Google Cloud Storage, performs necessary transformations (like reformatting dates and handling data types), and loads the processed data into a BigQuery table.
- **Source:** Google Cloud Storage (GCS) bucket: `gs://mgmt599-justinrizzzo-data-lake-lab2/pipeline_input/NVidia_stock_history.csv`
- **Transformations:**
    - Read each line from the CSV, skipping the header.
    - Parse each line, extracting fields (Date, Open, High, Low, Close, Volume, Dividends, Stock Splits).
    - Parse and reformat the Date field to a format suitable for BigQuery TIMESTAMP (`YYYY-MM-DD HH:MM:SS`).
    - Explicitly convert numeric fields (Open, High, Low, Close, Volume, Dividends, Stock Splits) to Float or Integer, handling empty strings as None.
    - Filter out any records that failed parsing and resulted in an empty dictionary.
- **Destination:** Google BigQuery table: `mgmt599-justinrizzo-lab2.pipeline_processed_data.nvda_stocks_transformed`

## Pipeline Configuration
- Job name: `nvda-stock-pipeline-justinrizzo`
- Region: `us-central1`
- Machine type: `n1-standard-1` (This is the default worker type for Dataflow, not explicitly set in the code but a common default)
- Max workers: Not explicitly set in the code, Dataflow will determine based on job size and available resources (defaults vary, but typically starts with 1 and scales up).

## Data Flow
1. Read from: `gs://mgmt599-justinrizzzo-data-lake-lab2/pipeline_input/NVidia_stock_history.csv`
2. Transform: Parse CSV, reformat date, handle data types, filter invalid records.
3. Write to: `mgmt599-justinrizzo-lab2.pipeline_processed_data.nvda_stocks_transformed`

## Lessons Learned
- [What was challenging?] (e.g., Handling dependency conflicts in Colab, debugging Dataflow job errors, ensuring correct date and data type parsing for BigQuery, understanding Dataflow's error logging.)
- [What would you do differently?] (e.g., Implement more detailed logging for specific data parsing errors, use a more robust method for handling potential data inconsistencies, define the BigQuery schema programmatically from the start if compatible with the Beam version, set explicit machine types and max workers for cost control/performance tuning.)
