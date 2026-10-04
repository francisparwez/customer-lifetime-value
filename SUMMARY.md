# Customer Lifetime Value — Project Summary

## Current Stage

✅ Part 01 - Data Understanding, Cleaning & Customer Behaviour Analysis is complete.

✅ Part 02 - Customer-Level Feature Engineering & CLV Target Creation is complete.

✅ Part 03 - CLV Prediction Model Development & Evaluation is complete.

## Dataset

The supplied Excel workbook contains two transaction sheets:

- `Year 2009-2010`
- `Year 2010-2011`

The combined raw dataset contains **1,067,371 transaction records**.

After Part 01 cleaning, the transaction dataset contains **779,425 records** and **5,878 unique customers**.

The cleaned transaction data covers **1 December 2009 to 9 December 2011**.

## Part 01 Output

The cleaned transaction dataset was saved as:

```text
data/processed/cleaned_transactions.csv
```

The raw Excel workbook was kept unchanged.

## Part 02 Goal

The goal of Part 02 was to turn the cleaned transaction data into one row per customer and create meaningful historical features plus a future-value target for later CLV modelling.

## Time-Based Split

A 90-day future window was used.

```text
Latest transaction: 2011-12-09 12:50:00
Cutoff date:        2011-09-10 12:50:00
```

Historical customer features were calculated from transactions up to the cutoff.

The future target was calculated from transactions after the cutoff and within the 90-day future window.

## Order-Level Preparation

The observation-period data was converted into order-level data:

- 30,343 orders
- 30,343 unique invoices
- One row per invoice
- No missing values

No invoice was linked to multiple customers.

For invoices with more than one timestamp, the earliest timestamp was used when creating the final order-level dataset.

## Customer-Level Features

The final customer-level table contains **5,281 customers**.

Features created:

- `Recency`
- `Frequency`
- `Monetary`
- `AvgOrderValue`
- `PurchaseFrequency`
- `ProductDiversity`
- `Tenure_days`
- `Tenure_months`
- `active_days`

## Future Target

The future period was used to create:

- `Future_90d_Orders`
- `Future_90d_Value`

The main modelling target is:

```text
Future_90d_Value
```

Of the 5,281 customers with historical activity:

- **2,292** made at least one future purchase
- **2,989** had no purchase during the future period

An exploratory Spearman correlation between historical `Monetary` and `Future_90d_Value` was **0.502**.

## Final Output

The final customer-level CLV dataset contains:

- **5,281 rows**
- **14 columns**

It was saved as:

```text
data/processed/customer_clv_dataset.csv
```

Final columns:

```text
CustomerID
first_purchase_date
last_purchase_date
Recency
Tenure_days
Tenure_months
active_days
Frequency
Monetary
AvgOrderValue
PurchaseFrequency
ProductDiversity
Future_90d_Orders
Future_90d_Value
```

## Part 02 Visuals

```text
images/04_monetary_distribution.png
images/05_future_90d_value_distribution.png
```

## Part 03 - CLV Prediction Model

Part 03 used the customer-level CLV dataset to predict `Future_90d_Value` from historical customer behaviour.

### Model Features

The model used:

- `Recency`
- `Tenure_days`
- `Tenure_months`
- `active_days`
- `Frequency`
- `Monetary`
- `AvgOrderValue`
- `PurchaseFrequency`
- `ProductDiversity`

Excluded fields:

- `CustomerID`
- `first_purchase_date`
- `last_purchase_date`
- `Future_90d_Value`
- `Future_90d_Orders`

`Future_90d_Orders` was excluded to prevent future-data leakage.

### Train-Test Split

An 80/20 train-test split was used with `random_state=42`.

### Models Compared

- DummyRegressor mean baseline
- Random Forest Regressor
- XGBoost Regressor

### Test Set Results

| Model         |    MAE |    RMSE |      R² |
| ------------- | -----: | ------: | ------: |
| Baseline      | 873.34 | 5725.76 | -0.0006 |
| Random Forest | 592.08 | 5662.33 |  0.0214 |
| XGBoost       | 638.70 | 5939.51 | -0.0767 |

Random Forest was the best-performing model across all three metrics.

### Feature Importance

Random Forest feature importance showed:

- `Monetary`: **0.7671**
- `AvgOrderValue`: **0.1110**
- `ProductDiversity`: **0.0306**
- `PurchaseFrequency`: **0.0218**
- `Recency`: **0.0212**
- `Frequency`: **0.0196**
- `active_days`: **0.0115**
- `Tenure_days`: **0.0088**
- `Tenure_months`: **0.0082**

The future CLV target is highly skewed, with many zero-value customers and a smaller number of very high-value customers. This helps explain the relatively low R² despite Random Forest improving on the baseline.

### Part 03 Outputs

- `notebooks/03_clv_prediction_model.ipynb`
- `images/06_model_comparison_mae.png`
- `images/07_model_comparison_rmse.png`
- `images/08_model_comparison_r².png`
- `images/09_random_forest_actual_vs_predicted.png`
- `images/10_random_forest_feature_importance.png`

## Current Progress

✅ Part 01 complete  
✅ Part 02 complete  
✅ Part 03 complete

## Next Step

Move to **Part 04 - Model Evaluation & Business Insights**.
