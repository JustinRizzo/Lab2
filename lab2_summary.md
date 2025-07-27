# Lab 2 Summary: Pipeline Analytics

## Pipeline Configuration
- Pipeline Name: `nvda-stock-pipeline-justinrizzo`
- Source: `gs://mgmt599-justinrizzzo-data-lake-lab2/pipeline_input/NVidia_stock_history.csv`
- Destination: `pipeline_processed_data.nvda_stocks_transformed` (within project `mgmt599-justinrizzo-lab2`)
- Schedule: Configured for daily runs (via Cloud Scheduler - manual step in GCP Console)

## Key Technical Achievements
1. Successfully built Dataflow pipeline processing all records in the input file.
2. Processed and loaded structured data into BigQuery.
3. Created and evaluated multiple analytical models (Linear Regression for prediction, Logistic Regression for classification, K-Means for clustering) using the processed data.

## Business Insights from Pipeline Data
1. **High Predictability of Daily High:** Linear Regression showed the 'High' price is highly predictable (R2 > 0.99) using just the average of 'Open' and 'Close' prices on the same day.
2. **High Volume Classification Difficulty:** Logistic Regression using Open, High, Low, and Close prices alone was not effective at predicting high volume days (low accuracy/precision), suggesting these features are insufficient for this task.
3. **Identified Trading Day Clusters:** K-Means clustering grouped trading days into distinct clusters based on price and volume characteristics, potentially revealing different market behaviors or patterns on those days.

## DIVE Analysis Summary (Focused on Linear Regression)
- Question: How accurately can we predict a stock's 'High' price using its 'Open', 'Low', and 'Close' prices, and what are the most influential factors?
- Key Finding: The 'High' price is highly predictable (R2 > 0.99) using a simple linear model. An engineered feature, the average of 'Open' and 'Close', is the single best predictor, outperforming models with more features due to multicollinearity.
- Business Impact: The simplified engineered model can be used for real-time high price estimation, potential integration into short-term trading strategies, and data quality checks.

## Cost Analysis
- Dataflow job: ~$\[X] per run (Estimate based on job duration and worker type/count)
- BigQuery storage: ~$\[Y] per month (Estimate based on table size)
- Total within university credits: [Yes/No - based on your GCP usage]

## Challenges & Solutions
1. Challenge: Resolving authentication errors when launching Dataflow job from Colab.
   Solution: Enabled Colab's built-in authentication using `auth.authenticate_user()`.
2. Challenge: Debugging persistent BigQuery data loading errors ("JSON table encountered too many errors").
   Solution: Iteratively refined data parsing (date format, empty strings), added logging to inspect data before writing, and reverted to string-based BigQuery schema to avoid programmatic schema import issues.
3. Challenge: High multicollinearity among original features (Open, Low, Close) in the Linear Regression model.
   Solution: Performed VIF analysis to confirm multicollinearity and experimented with simpler models, identifying an effective engineered feature (Avg Open/Close).

## Next Steps
- Additional pipelines to build: Process historical data for other stocks or cryptocurrencies; build pipelines for real-time data ingestion (e.g., streaming stock tickers).
- Optimizations identified: Explore other feature engineering ideas for high volume prediction; investigate time series modeling techniques for future price prediction; optimize Dataflow worker types/counts for cost or performance.
