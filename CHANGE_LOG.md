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

### Part 03 Output

The modelling notebook and five model-evaluation visuals were added to the project.

### Part 04 - Model Tuning, Interpretability & Customer Value Analysis

- Added `04_model_tuning_interpretability_customer_value_analysis.ipynb`
- Recreated the original Random Forest using the same features and train-test split as Part 03
- Tuned the Random Forest using 5-fold cross-validation and `RandomizedSearchCV`
- Evaluated 20 randomly selected hyperparameter combinations across 5 folds
- Selected `n_estimators=300`, `max_depth=10`, `min_samples_split=5`, `min_samples_leaf=4`, and `max_features=1.0`
- Achieved a best cross-validation MAE of 447.26
- Evaluated the tuned model on the untouched test set
- Improved test MAE from 592.08 to 576.78
- Improved test RMSE from 5662.33 to 5622.81
- Increased test R² from 0.0214 to 0.0350
- Added tuned Random Forest feature importance
- Added permutation importance
- Added partial dependence analysis for Monetary, Recency, and PurchaseFrequency
- Generated predicted 90-day CLV for all 5,281 customers
- Created Low, Medium, High, and VIP customer value segments
- Analysed actual and predicted value by segment
- Analysed customer behaviour by value segment
- Added business recommendations based on the model and segmentation
- Saved the final customer scoring data to `customer_clv_predictions.csv`

### Part 04 Results

The tuned Random Forest achieved:

- **MAE:** 576.78
- **RMSE:** 5622.81
- **R²:** 0.0350

The strongest predictor was `Monetary`.

The final segmentation contained:

- **Low:** 2,641 customers
- **Medium:** 1,584 customers
- **High:** 792 customers
- **VIP:** 264 customers

The **VIP segment represented 50.32% of predicted future value and 49.29% of actual future value**, while containing only 5% of customers.

### Part 04 Output

The modelling notebook, nine new visualisations, and the customer scoring output were added to the project.
