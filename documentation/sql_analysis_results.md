prompt1 = """
Write a BigQuery SQL query that performs time-series analysis on sales data:
- Calculate daily, weekly, and monthly sales aggregations
- Include moving averages (7-day and 30-day)
- Identify trend and seasonality components
- Flag anomalies (sales outside 2 standard deviations)

Table: `project.pipeline_processed_data.superstore_transformed`
Columns: Order_Date, Sales, Profit, Category, Region
"""

-- Write a BigQuery SQL query that performs time-series analysis on sales data:
-- - Calculate daily, weekly, and monthly sales aggregations
-- - Include moving averages (7-day and 30-day)
-- - Identify trend and seasonality components
-- - Flag anomalies (sales outside 2 standard deviations)
-- 
-- Table: `project.pipeline_processed_data.superstore_transformed`
-- Columns: Order_Date, Sales, Profit, Category, Region
WITH
  DailySales AS (
  SELECT
    Order_Date,
    SUM(Sales) AS Daily_Sales
  FROM
    `mgmt599-justinrizzo-lab2`.`pipeline_processed_data`.`superstore_transformed`
  GROUP BY
    Order_Date ),
  AggregatedSales AS (
  SELECT
    Order_Date,
    Daily_Sales,
    -- Calculate 7-day moving average
    AVG(Daily_Sales) OVER (ORDER BY Order_Date ASC ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS Seven_Day_Moving_Avg,
    -- Calculate 30-day moving average
    AVG(Daily_Sales) OVER (ORDER BY Order_Date ASC ROWS BETWEEN 29 PRECEDING AND CURRENT ROW) AS Thirty_Day_Moving_Avg,
    -- Calculate overall mean and standard deviation for anomaly detection
    AVG(Daily_Sales) OVER () AS Overall_Mean_Sales,
    STDDEV(Daily_Sales) OVER () AS Overall_StdDev_Sales
  FROM
    `DailySales` )
SELECT
  Order_Date,
  Daily_Sales,
  Seven_Day_Moving_Avg,
  Thirty_Day_Moving_Avg,
  -- Weekly Aggregation
  DATE_TRUNC(Order_Date, WEEK) AS Week_Start_Date,
  SUM(Daily_Sales) OVER (PARTITION BY DATE_TRUNC(Order_Date, WEEK)) AS Weekly_Sales,
  -- Monthly Aggregation
  DATE_TRUNC(Order_Date, MONTH) AS Month_Start_Date,
  SUM(Daily_Sales) OVER (PARTITION BY DATE_TRUNC(Order_Date, MONTH)) AS Monthly_Sales,
  -- Anomaly Flag
  CASE
    WHEN Daily_Sales > Overall_Mean_Sales + (2 * Overall_StdDev_Sales) THEN 'High Anomaly'
    WHEN Daily_Sales < Overall_Mean_Sales - (2 * Overall_StdDev_Sales) THEN 'Low Anomaly'
    ELSE 'Normal'
END
  AS Anomaly_Flag
FROM
  `AggregatedSales`
ORDER BY
  Order_Date;

Analysis:

Analysis of Sales Data: Trend, Seasonality, and Anomalies

This analysis examined the provided sales dataset to identify underlying patterns of trend and seasonality, as well as to flag sales anomalies.

Trend and Seasonality:

The time series decomposition revealed clear trend and seasonal components in the daily sales data. The trend component indicates a general upward trajectory in sales over the observed period, suggesting business growth. The seasonal component highlights recurring patterns in sales within a fixed period, which is likely influenced by weekly sales cycles (e.g., higher sales on certain days of the week) or potentially yearly seasonality (e.g., holiday shopping periods). The distinct peaks and troughs in the seasonal plot confirm the presence of predictable fluctuations in sales.

Anomalies:

Sales anomalies were identified as data points falling outside two standard deviations from the 7-day rolling mean. These flagged points represent sales figures that are significantly higher or lower than what would be typically expected based on recent performance. The "Sales Data with Anomalies" plot visually highlights these instances. These anomalies could be due to various factors such as special promotions, stock issues, data entry errors, or unusual events. Further investigation into the specific dates flagged as anomalies would be necessary to understand the root cause of these deviations.

Summary:

In summary, the sales data exhibits a positive long-term trend and clear seasonality. Additionally, several points have been identified as potential anomalies, warranting further investigation to understand the factors driving these unusual sales figures. This analysis provides a foundational understanding of the sales patterns and highlights areas that may require more in-depth review.
