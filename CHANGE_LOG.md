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

## 2026-10-04

### Part 03 - CLV Prediction Model Development & Evaluation

- Added `03_clv_prediction_model.ipynb`
- Loaded the customer-level CLV dataset created in Part 02
- Defined historical customer features for modelling
- Used `Future_90d_Value` as the prediction target
- Excluded `CustomerID`, raw date fields, `Future_90d_Value`, and `Future_90d_Orders` from the model inputs
- Used an 80/20 train-test split with `random_state=42`
- Built a `DummyRegressor` mean baseline
- Built a Random Forest regression model
- Built an XGBoost regression model
- Evaluated all models using MAE, RMSE, and R²
- Compared model performance using tables and visualisations
- Selected Random Forest as the best-performing model across MAE, RMSE, and R²
- Created a Random Forest actual-vs-predicted CLV plot
- Created Random Forest feature importance analysis
- Found `Monetary` to be the most important feature, followed by `AvgOrderValue`
- Documented the limited R² performance and the highly skewed future CLV target

### Part 03 Results

| Model         |    MAE |    RMSE |      R² |
| ------------- | -----: | ------: | ------: |
| Baseline      | 873.34 | 5725.76 | -0.0006 |
| Random Forest | 592.08 | 5662.33 |  0.0214 |
| XGBoost       | 638.70 | 5939.51 | -0.0767 |

Random Forest was selected as the best-performing model on the test set.

Top Random Forest feature importances:

- `Monetary`: 0.7671
- `AvgOrderValue`: 0.1110
- `ProductDiversity`: 0.0306

### Part 03 Output

The modelling notebook and five model-evaluation visuals were added to the project.
