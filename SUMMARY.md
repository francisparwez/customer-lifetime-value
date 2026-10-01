# Customer Lifetime Value — Project Summary

## Current Stage

✅ Part 01 - Data Understanding, Cleaning & Customer Behaviour Analysis is complete.

## Dataset

The supplied Excel workbook contains two transaction sheets:

- `Year 2009-2010`
- `Year 2010-2011`

Both sheets contain:

- `Invoice`
- `StockCode`
- `Description`
- `Quantity`
- `InvoiceDate`
- `Price`
- `Customer ID`
- `Country`

The combined raw dataset contains **1,067,371 transaction records**.

## Part 01 Goal

The goal of this stage was to understand the raw transaction data, identify data-quality issues, clean the transaction records, explore customer purchasing behaviour, and prepare the data for customer-level CLV analysis.

## Data Audit Completed

The following checks were performed:

- Workbook and sheet inspection
- Data types
- Missing values
- Exact duplicate rows
- Cancelled invoices
- Negative quantities
- Zero quantities
- Negative prices
- Zero prices
- Special adjustment records
- Combined dataset validation

### Key Audit Results

| Check                 | Year 2009-2010 | Year 2010-2011 |  Combined |
| --------------------- | -------------: | -------------: | --------: |
| Rows                  |        525,461 |        541,910 | 1,067,371 |
| Missing `Description` |          2,928 |          1,454 |     4,382 |
| Missing `Customer ID` |        107,927 |        135,080 |   243,007 |
| Exact duplicate rows  |          6,865 |          5,268 |    34,335 |
| `C`-prefixed invoices |         10,206 |          9,288 |    19,494 |
| Negative quantities   |         12,326 |         10,624 |    22,950 |
| Zero quantities       |              0 |              0 |         0 |
| Negative prices       |              3 |              2 |         5 |
| Zero prices           |          3,687 |          2,515 |     6,202 |

Negative quantities were analysed separately from `C`-prefixed invoices. There were **3,457 negative-quantity records without a `C` invoice prefix**, and several of these represented special records such as `lost`, `damages`, `short`, and `sold as gold`.

The five negative-price records were inspected and had the description `Adjust bad debt` with missing customer IDs.

## Cleaning Completed

A separate `cleaned_transactions` dataframe was created from the combined raw dataset.

The following rules were applied:

- Removed exact duplicate rows
- Removed cancelled invoices beginning with `C`
- Removed transactions with missing `Customer ID`
- Kept only positive quantities
- Kept only positive unit prices
- Converted `Customer ID` to integer after missing values were removed
- Created `TotalPrice = Quantity × Price`

## Cleaning Results

| Metric                            |        Result |
| --------------------------------- | ------------: |
| Raw rows                          |     1,067,371 |
| Cleaned rows                      |       779,425 |
| Rows removed                      |       287,946 |
| Percentage removed                |        26.98% |
| Columns after adding `TotalPrice` |             9 |
| Unique customers                  |         5,878 |
| Unique invoices                   |        36,969 |
| Recorded transaction value        | 17,374,804.27 |

Final validation confirmed:

- 0 missing `Customer ID` values
- 0 duplicate rows
- 0 non-positive quantities
- 0 non-positive prices
- 0 missing values in the cleaned dataset

The cleaned dataset covers transactions from **1 December 2009 to 9 December 2011**.

## Customer Behaviour Analysis Completed

The following customer-level analysis was completed:

- Customer total spend
- Unique invoice count
- Total items purchased
- First purchase date
- Last purchase date
- Top customers by total spend
- Purchasing behaviour by country
- Monthly transaction value
- Monthly active customers

### Customer-Level Findings

The cleaned dataset contains **5,878 customers**.

Customer spend is strongly uneven:

- Mean total spend: **2,955.90**
- Median total spend: **867.74**
- Maximum total spend: **580,987.04**

The customer with the highest recorded total spend had **145 unique invoices**.

Country-level analysis showed the **United Kingdom** had the largest recorded transaction value.

## Outputs Created

### Processed Dataset

```text
data/processed/cleaned_transactions.csv
```

### Charts

```text
images/01_monthly_revenue.png
images/02_monthly_active_customers.png
images/03_top_countries_by_revenue.png
```

## Current Progress

✅ Part 01 is complete.

The cleaned transaction dataset is ready for customer-level feature engineering.

## Next Step

Move to **Part 02 - Customer-Level Feature Engineering**.
