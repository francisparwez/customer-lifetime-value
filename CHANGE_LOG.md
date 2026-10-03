# CHANGE_LOG

## 2026-10-03

### Part 02 - Customer-Level Feature Engineering & CLV Target Creation

- Added `02_customer_level_feature_engineering.ipynb`
- Loaded the cleaned transaction data from Part 01
- Defined a time-based observation period and 90-day future window
- Set the cutoff date to 2011-09-10
- Checked invoice consistency
- Created one row per invoice for the observation period
- Built customer-level Recency, Frequency, and Monetary features
- Created Average Order Value
- Created Purchase Frequency
- Created Product Diversity
- Created Customer Tenure in days and months
- Created `Future_90d_Orders`
- Created `Future_90d_Value` as the future CLV target
- Validated the final customer-level dataset
- Saved `customer_clv_dataset.csv`
- Added Monetary and Future 90-day value distribution plots

### Part 02 Output

The final customer-level dataset contains 5,281 customers and 14 columns.
