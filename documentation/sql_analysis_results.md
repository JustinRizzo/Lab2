# Cell 5: Verify the Final Result in BigQuery

Purpose: After waiting a few minutes for the Dataflow job to show "Succeeded" in the GCP console, you can run this final cell. It connects to BigQuery, queries the new table created by your pipeline, and displays the results in a clean pandas DataFrame, confirming that your entire ETL process was successful.

from google.cloud import bigquery
import pandas as pd

# --- Verify the Final Result in BigQuery ---

# Use the BigQuery client we configured earlier
client = bigquery.Client(project=PROJECT_ID)

# The full ID of the table your Beam pipeline created
table_id = f"{PROJECT_ID}.{BIGQUERY_DATASET}.{BIGQUERY_TABLE}"

# SQL query to select the first 15 rows from your table
sql_query = f"""
SELECT
    *
FROM
    `{table_id}`
ORDER BY
    Date DESC
LIMIT 15
"""

try:
    # Execute the query and load the results into a pandas DataFrame
    df = client.query(sql_query).to_dataframe()

    print(f"--- Successfully queried data from '{table_id}' ---")
    # Display the DataFrame. Colab will format it nicely.
    display(df)

except Exception as e:
    print(f"An error occurred while querying the table: {e}")

--- Successfully queried data from 'mgmt599-justinrizzo-lab2.nvidia.nvidia' ---
Date	Open	High	Low	Close	Volume	Dividends	Stock Splits
0	2024-08-28 04:00:00+00:00	128.119995	128.330002	122.639999	125.175003	241795982	0.0	0.0
1	2024-08-27 04:00:00+00:00	125.050003	129.199997	123.879997	128.300003	301726100	0.0	0.0
2	2024-08-26 04:00:00+00:00	129.570007	131.259995	124.370003	126.459999	331964700	0.0	0.0
3	2024-08-23 04:00:00+00:00	125.860001	129.600006	125.220001	129.369995	323230300	0.0	0.0
4	2024-08-22 04:00:00+00:00	130.020004	130.750000	123.099998	123.739998	376189100	0.0	0.0
5	2024-08-21 04:00:00+00:00	127.320000	129.350006	126.660004	128.500000	257883600	0.0	0.0
6	2024-08-20 04:00:00+00:00	128.399994	129.880005	125.889999	127.250000	300087400	0.0	0.0
7	2024-08-19 04:00:00+00:00	124.279999	130.000000	123.419998	130.000000	318333600	0.0	0.0
8	2024-08-16 04:00:00+00:00	121.940002	125.000000	121.180000	124.580002	302589900	0.0	0.0
9	2024-08-15 04:00:00+00:00	118.760002	123.239998	117.470001	122.860001	318086700	0.0	0.0
10	2024-08-14 04:00:00+00:00	118.529999	118.599998	114.070000	118.080002	339246400	0.0	0.0
11	2024-08-13 04:00:00+00:00	112.440002	116.230003	111.580002	116.139999	312646700	0.0	0.0
12	2024-08-12 04:00:00+00:00	106.320000	111.070000	106.260002	109.019997	325559900	0.0	0.0
13	2024-08-09 04:00:00+00:00	105.639999	106.599998	103.430000	104.750000	290844200	0.0	0.0
14	2024-08-08 04:00:00+00:00	102.000000	105.500000	97.519997	104.970001	391910000	0.0	0.0

# Linear Regression (Predicting a Stock's High Price)

Objective: Use Linear Regression to predict a continuous value. In this case, we will predict the High price of a stock for a given day based on its Open, Low, and Close_Last prices.

## Summary:

### Data Analysis Key Findings

*   The linear regression model, trained on 'Open', 'Low', and 'Close' prices, achieved an R-squared score of approximately 0.987 on the test data, indicating that about 98.7% of the variance in the 'High' price is explained by the model.
*   The Mean Absolute Error (MAE) on the test data was approximately 0.428, suggesting that the model's predictions for the 'High' price are, on average, off by about \$0.428.
*   The Mean Squared Error (MSE) on the test data was approximately 0.319.

### Insights or Next Steps

*   Given the high R-squared value, the model demonstrates a strong ability to predict the 'High' stock price based on the 'Open', 'Low', and 'Close' prices.
*   Further analysis could involve exploring other potential features or different regression techniques to potentially improve the model's accuracy further, although the current performance is already quite good.

Objective: Use Logistic Regression for binary classification. We will predict whether a given day was a "high volume" trading day. To do this, we first need to create our target label: is_high_volume. We'll define "high volume" as any day where the volume was greater than the average volume for the entire dataset.

## Summary:

### Data Analysis Key Findings

*   The average volume for the dataset is approximately 1,402,300.
*   A new binary variable `is_high_volume` was successfully created, identifying days with volume greater than the average volume.
*   The features selected for the model were 'Open', 'High', 'Low', and 'Close', with 'is\_high\_volume' as the target variable.
*   The dataset was split into training (80%) and testing (20%) sets, resulting in 12 training samples and 3 testing samples.
*   A Logistic Regression model was successfully instantiated and trained on the training data.
*   The model's performance on the test set showed an accuracy of 0.333, precision of 0.333, recall of 1.0, and an F1-score of 0.5.
*   The confusion matrix indicated 0 True Negatives, 2 False Positives, 0 False Negatives, and 1 True Positive, suggesting the model is biased towards predicting "high volume".

### Insights or Next Steps

*   The model's low accuracy and precision suggest that using only 'Open', 'High', 'Low', and 'Close' prices may not be sufficient predictors of high volume days.
*   Consider incorporating additional features such as technical indicators, historical volume trends, or news sentiment to potentially improve model performance.

Objective: Use K-Means, an unsupervised learning algorithm, to group similar data points into clusters. We will group trading days into 4 clusters based on their price and volume characteristics. Since Volume is on a much larger scale than the price columns, we must standardize our features using ML.STANDARD_SCALER to ensure all features are weighted equally.

## Summary:

### Data Analysis Key Findings

*   The K-Means clustering model successfully grouped trading days into 4 clusters based on scaled 'Open', 'High', 'Low', 'Close', and 'Volume' features.
*   The resulting cluster labels were added as a new column ('cluster') to the original DataFrame.
*   Analysis of the average feature values within each cluster revealed distinct characteristics for each group. For instance, Cluster 0 showed generally lower average prices and volume compared to the other clusters.

### Insights or Next Steps

*   Visualize the clusters using techniques like PCA or t-SNE to understand their separation in a lower-dimensional space.
*   Further investigate the trading days within each cluster to identify specific patterns, events, or market conditions that might define them.
