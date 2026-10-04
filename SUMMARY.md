# Customer Lifetime Value — Project Summary

## Current Stage

✅ Part 01 - Data Understanding, Cleaning & Customer Behaviour Analysis is complete.

✅ Part 02 - Customer-Level Feature Engineering & CLV Target Creation is complete.

✅ Part 03 - CLV Prediction Model Development & Evaluation is complete.

✅ Part 04 - Model Tuning, Interpretability & Customer Value Analysis is complete.

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

## Part 04 - Model Tuning, Interpretability & Customer Value Analysis

Part 04 focused on tuning the Random Forest selected in Part 03, understanding the main factors behind its predictions, and grouping customers by predicted future value.

### Model Tuning

The original Random Forest was rebuilt using the same feature set and train-test split as Part 03.

5-fold cross-validation and `RandomizedSearchCV` were used to test 20 randomly selected hyperparameter combinations.

Best hyperparameters:

```text
n_estimators      = 300
max_depth         = 10
min_samples_split = 5
min_samples_leaf  = 4
max_features      = 1.0
```

Best cross-validation MAE:

```text
447.26
```

### Original vs Tuned Random Forest

| Model                  |    MAE |    RMSE |     R² |
| ---------------------- | -----: | ------: | -----: |
| Original Random Forest | 592.08 | 5662.33 | 0.0214 |
| Tuned Random Forest    | 576.78 | 5622.81 | 0.0350 |

Tuning improved:

- MAE by **2.59%**
- RMSE by **0.70%**
- R² by **0.0136**

### Interpretability

The tuned model was analysed using:

- built-in Random Forest feature importance
- permutation importance
- partial dependence plots

`Monetary` was the dominant feature.

The model generally predicted higher CLV for customers with higher historical monetary value and higher purchase frequency, while higher recency was associated with lower predicted CLV.

These are model relationships rather than causal effects.

### Customer Value Analysis

The tuned Random Forest generated predicted 90-day CLV for all **5,281 customers**.

The final customer scoring table contains:

```text
CustomerID
Future_90d_Value
Predicted_90d_Value
Value_Segment
```

The scoring output was saved as:

```text
data/processed/customer_clv_predictions.csv
```

Customers were grouped by predicted CLV into:

- Low: bottom 50%
- Medium: 50th–80th percentile
- High: 80th–95th percentile
- VIP: top 5%

### Segment Results

| Segment | Customers | Predicted Value Share | Actual Value Share |
| ------- | --------: | --------------------: | -----------------: |
| Low     |     2,641 |                 7.83% |             11.35% |
| Medium  |     1,584 |                17.15% |             16.92% |
| High    |       792 |                24.71% |             22.44% |
| VIP     |       264 |            **50.32%** |         **49.29%** |

The main business finding is that **5% of customers account for about half of both predicted and observed future customer value**.

VIP customers also showed much stronger historical customer behaviour, including higher monetary value, frequency, average order value, product diversity, and lower recency.

### Business Recommendations

1. Protect VIP customers with focused retention and loyalty activity.
2. Work to move High-value customers toward the VIP group.
3. Use reactivation campaigns for customers with high recency.
4. Use lower-cost or automated campaigns for Low-value customers.

### Limitation

The tuned model improved the Random Forest, but the final test-set R² was **0.0350**. The model should therefore be used mainly for customer prioritisation rather than as an exact revenue forecast for every customer.

### Part 04 Outputs

```text
notebooks/04_model_tuning_interpretability_customer_value_analysis.ipynb
data/processed/customer_clv_predictions.csv
images/11_tuned_rf_feature_importance.png
images/12_permutation_importance.png
images/13_partial_dependence_monetary.png
images/14_partial_dependence_recency.png
images/15_partial_dependence_purchase_frequency.png
images/16_customer_value_segments.png
images/17_predicted_clv_by_segment.png
images/18_historical_monetary_by_segment.png
images/19_actual_vs_predicted_clv_by_segment.png
```

## Part 05 - CLV Business Strategy & Final Executive Report

Part 05 translated the final customer predictions and value segments into practical retention, marketing, reactivation, customer development, and resource-allocation strategies.

### Final CLV Snapshot

The final scoring dataset contains **5,281 customers**.

- Total predicted 90-day value: **2,812,461.83**
- VIP customers: **264**
- VIP customer share: **5.00%**
- VIP predicted value share: **50.32%**
- VIP actual future value share: **49.29%**

The main finding is that the top 5% of customers account for about half of both predicted and observed future customer value.

### Segment Strategy

| Segment | Customers | Customer Share | Predicted Value Share | Priority |
| ------- | --------: | -------------: | --------------------: | -------- |
| Low     |     2,641 |         50.01% |                 7.83% | Low      |
| Medium  |     1,584 |         29.99% |                17.15% | Medium   |
| High    |       792 |         15.00% |                24.71% | High     |
| VIP     |       264 |          5.00% |                50.32% | Critical |

### Business Strategy

- **VIP:** Protect and retain with personalised offers, loyalty rewards, priority service, and proactive retention.
- **High:** Grow customer value through cross-sell, upsell, loyalty incentives, and targeted promotions.
- **Medium:** Increase engagement and repeat purchasing using product recommendations, reminders, and moderate promotions.
- **Low:** Maintain efficiently using automated and lower-cost campaigns, with targeted reactivation where appropriate.

### Marketing & Retention KPIs

- VIP retention rate and repeat purchase rate
- High-to-VIP conversion and average order value
- Medium repeat purchase rate and purchase frequency
- Low reactivation rate and cost per reactivated customer

### 90-Day Business Action Plan

| Period     | Focus              | Main Goal                   |
| ---------- | ------------------ | --------------------------- |
| Days 1-30  | Prepare and launch | Build the process           |
| Days 31-60 | Test and measure   | Learn what works            |
| Days 61-90 | Scale and improve  | Improve resource allocation |

### Resource Allocation

Marketing intensity was defined as:

- VIP — **Very High**
- High — **High**
- Medium — **Medium**
- Low — **Low**

### Immediate Priorities

1. Protect VIP customers.
2. Grow High-value customers.
3. Reactivate valuable inactive customers.
4. Manage Low-value customers efficiently.

### Final Business Recommendations

1. Protect VIP customers.
2. Grow High-value customers toward VIP.
3. Reactivate customers with declining engagement.
4. Manage Low-value customers efficiently.
5. Refresh CLV scores regularly.

### Part 05 Outputs

```text
notebooks/05_clv_business_strategy_final_executive_report.ipynb
images/20_segment_value_concentration.png
images/21_segment_average_value.png
images/22_customer_strategy_matrix.png
```

### Final Executive Conclusion

The end-to-end CLV project is complete. The tuned Random Forest achieved **MAE 576.78, RMSE 5622.81, and R² 0.0350**. Because predictive performance remains limited, the model is best used as a customer prioritisation tool.

The final joined strategy dataset was validated at **5,281 rows × 12 columns**, with no missing values and 5,281 unique customers.

The final business strategy is driven by the strong concentration of customer value: a 5% VIP group represents 50.32% of predicted future value. The framework therefore prioritises VIP retention, High-value growth, Medium customer engagement, and efficient Low-value management.

## Current Progress

✅ Part 01 complete  
✅ Part 02 complete  
✅ Part 03 complete  
✅ Part 04 complete  
✅ Part 05 complete

## Final Status

### Project Complete

The complete CLV workflow is finished, including data preparation, customer-level feature engineering, CLV target creation, model development, tuning, interpretability, customer value segmentation, and final business strategy.
