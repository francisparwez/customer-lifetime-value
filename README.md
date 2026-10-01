# Customer Lifetime Value Prediction

This project focuses on predicting Customer Lifetime Value (CLV) using retail transaction data.

The goal is to understand customer purchasing behaviour, prepare the transaction data, and later build a model that predicts the future value of each customer.

## Project Stages

1. ✅ Data Understanding, Cleaning & Customer Behaviour Analysis
2. Customer-Level Feature Engineering
3. CLV Prediction Model
4. Model Evaluation & Business Insights

## Part 01 - Data Understanding, Cleaning & Customer Behaviour Analysis

Part 01 focused on understanding the raw retail transaction data, investigating data-quality issues, cleaning the transaction records, and exploring customer purchasing behaviour before moving to customer-level CLV modelling.

### Work Completed

- Loaded the Excel workbook and inspected both yearly sheets
- Checked the structure and data types of each sheet
- Checked missing values
- Checked exact duplicate rows
- Investigated cancelled transactions using the `C` invoice prefix
- Compared cancelled invoices with negative-quantity records
- Checked zero and negative quantities
- Checked zero and negative unit prices
- Inspected special negative-price `Adjust bad debt` records
- Combined both yearly sheets into one transaction dataset
- Created a separate cleaned transaction dataset
- Removed exact duplicate rows
- Removed cancelled invoices
- Removed transactions without a `Customer ID`
- Kept only positive quantities
- Kept only positive unit prices
- Converted `Customer ID` to integer after missing values were removed
- Created `TotalPrice` as `Quantity × Price`
- Validated the cleaned transaction dataset
- Created customer-level purchasing summaries
- Reviewed top customers by total spend
- Analysed purchasing behaviour by country
- Analysed monthly transaction value
- Analysed monthly active customers
- Saved the cleaned transaction dataset for the next stage

## Dataset

The supplied Excel workbook contains two transaction sheets:

- `Year 2009-2010`
- `Year 2010-2011`

Both sheets contain the same transaction fields:

- `Invoice`
- `StockCode`
- `Description`
- `Quantity`
- `InvoiceDate`
- `Price`
- `Customer ID`
- `Country`

The two sheets contain a combined **1,067,371 raw transaction records**.

## Initial Data Audit

| Check                 | Year 2009-2010 | Year 2010-2011 |  Combined |
| --------------------- | -------------: | -------------: | --------: |
| Transaction rows      |        525,461 |        541,910 | 1,067,371 |
| Missing `Description` |          2,928 |          1,454 |     4,382 |
| Missing `Customer ID` |        107,927 |        135,080 |   243,007 |
| Exact duplicate rows  |          6,865 |          5,268 |    34,335 |
| `C`-prefixed invoices |         10,206 |          9,288 |    19,494 |
| Negative quantities   |         12,326 |         10,624 |    22,950 |
| Zero quantities       |              0 |              0 |         0 |
| Negative prices       |              3 |              2 |         5 |
| Zero prices           |          3,687 |          2,515 |     6,202 |

### Cancellation and Special Transaction Findings

Invoice values beginning with `C` were investigated as cancellation records. Sample records showed negative quantities.

Negative quantities were also checked independently. The counts were higher than the `C`-prefixed invoice counts, so negative quantity was not treated as a direct replacement for the cancellation rule.

A further check found **3,457 negative-quantity records without a `C` invoice prefix**. These records included descriptions such as `lost`, `damages`, `short`, and `sold as gold`, with many records also having a zero price and missing customer information.

The five negative-price records were inspected and were identified as `Adjust bad debt` records with missing `Customer ID` values. They were excluded from the positive-price purchase dataset through the final cleaning rule.

## Cleaning Rules

A separate `cleaned_transactions` dataframe was created from the combined raw transaction data.

The following rules were applied:

1. Remove exact duplicate rows
2. Remove invoices beginning with `C`
3. Remove rows with missing `Customer ID`
4. Keep rows where `Quantity > 0`
5. Keep rows where `Price > 0`

These rules were used to create a customer-linked, positive-value purchase dataset for later CLV analysis.

## Cleaning Results

| Metric                     |        Result |
| -------------------------- | ------------: |
| Raw combined rows          |     1,067,371 |
| Cleaned rows               |       779,425 |
| Rows removed               |       287,946 |
| Percentage removed         |        26.98% |
| Cleaned columns            |             9 |
| Unique customers           |         5,878 |
| Unique invoices            |        36,969 |
| Recorded transaction value | 17,374,804.27 |

The cleaned transaction period runs from **1 December 2009 to 9 December 2011**.

Final validation returned:

- Missing `Customer ID` → 0
- Duplicate rows → 0
- Non-positive quantities → 0
- Non-positive prices → 0
- Missing values across the cleaned dataset → 0

The raw `online_retail_dataset.xlsx` file was kept unchanged. Cleaning was performed on a working copy.

## Customer Purchasing Behaviour

A customer-level summary was created using:

- Total spend
- Number of unique invoices
- Total items purchased
- First purchase date
- Last purchase date

The cleaned customer-level dataset contains **5,878 customers**.

Customer spending is highly uneven:

- Mean total spend: **2,955.90**
- Median total spend: **867.74**
- Maximum total spend: **580,987.04**
- Maximum unique invoices for one customer: **398**

The top customers by total spend were also reviewed.

Country-level analysis was performed using total transaction value, unique invoices, and unique customers. The **United Kingdom** had the largest recorded transaction value in the cleaned dataset.

## Visual Analysis

The following charts were generated during Part 01:

### Monthly Transaction Value

![Monthly Transaction Value](images/01_monthly_revenue.png)

### Monthly Active Customers

![Monthly Active Customers](images/02_monthly_active_customers.png)

### Top 10 Countries by Transaction Value

![Top 10 Countries by Transaction Value](images/03_top_countries_by_revenue.png)

## Processed Data

The cleaned transaction dataset was saved as:

```text
data/processed/cleaned_transactions.csv
```

## Current Status

✅ **Part 01 - Data Understanding, Cleaning & Customer Behaviour Analysis is complete.**

The cleaned transaction-level dataset is prepared for the next stage.

## Next Stage

The next stage will focus on **Customer-Level Feature Engineering**, where customer-level variables will be prepared for CLV analysis.

## Project Structure

```text
customer-lifetime-value/
│
├── data/
│   ├── raw/
│   │   └── online_retail_dataset.xlsx
│   └── processed/
│       └── cleaned_transactions.csv
│
├── images/
│   ├── 01_monthly_revenue.png
│   ├── 02_monthly_active_customers.png
│   └── 03_top_countries_by_revenue.png
│
├── notebooks/
│   └── 01_data_understanding_cleaning.ipynb
│
├── README.md
├── SUMMARY.md
├── requirements.txt
└── .gitignore
```
