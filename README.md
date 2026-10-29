# Bank Loan Risk Analysis | Power BI

An interactive Power BI dashboard built on **1,000 bank loan records** to analyze loan amounts, loan status, risk ratings, interest rates, and branch performance. Data cleaning was done in **Power Query** and KPIs were developed using **DAX**.

![Dashboard](dashboard.png)

## Project Objective

To understand a bank's loan portfolio and identify where risk is concentrated, by answering:

- How much has been lent, and where (branch / loan type)?
- What share of loans are Active, Paid, or Overdue?
- Are High Risk loans really more likely to go overdue?
- How do interest rates differ across loan types?
- How does lending change over time?

## Tools Used

- **Power BI Desktop**: dashboard and data model
- **Power Query**: data cleaning and transformation
- **DAX**: KPI measures and Date table
- **Excel**: source data

## Dataset

Source file: `loan_data_1000.xlsx` (1,000 rows, 12 columns)

| Column | Description |
|---|---|
| Loan_ID, Customer_ID | Identifiers |
| Customer_Name | Customer name |
| Branch | Colombo, Galle, Kandy, Matara, Kurunegala, Negombo, Jaffna |
| Loan_Type | Home, Business, Personal, Vehicle, Education |
| Loan_Amount_LKR | Loan amount in LKR |
| Interest_Rate_% | Interest rate (7% - 13%) |
| Loan_Date | Loan issue date (2023 - 2026) |
| Status | Active / Paid / Overdue |
| Risk_Rating | Low / Medium / High |
| Loan_Officer | Officer handling the loan |
| Monthly_Income_LKR | Customer monthly income |

## Data Cleaning (Power Query)

- Renamed `Interest_Rate_%` to `Interest_Rate_Pct` (the % symbol causes issues in DAX)
- Corrected data types (`Loan_Date` to Date, money columns to Fixed decimal)
- Trimmed, cleaned, and capitalized text columns to avoid duplicate categories
- Removed 10 duplicate records using `Loan_ID`
- Removed rows missing `Loan_Amount_LKR` or `Loan_Date` (critical fields)
- Replaced missing text values with `Unknown`
- Left missing `Interest_Rate_Pct` and `Monthly_Income_LKR` as null, since averages ignore nulls
- Result: 1,000 raw rows reduced to **977 clean records**

### New columns created

- `Loan_Size_Band`: Under 1M / 1M-3M / 3M-6M / Over 6M
- `Income_Band`: Low / Middle / High Income
- `Loan_Year`, `Month_Name`, `Month_Number`

## Data Model

A dedicated Date table was created with DAX, marked as the date table, and related to the loan data (one-to-many on `Loan_Date`).

```DAX
DateTable =
ADDCOLUMNS(
    CALENDAR(DATE(2023,1,1), DATE(2026,12,31)),
    "Year", YEAR([Date]),
    "Month Number", MONTH([Date]),
    "Month Name", FORMAT([Date], "MMM")
)
```

## DAX Measures

```DAX
Total Loans = DISTINCTCOUNT(Loans[Loan_ID])

Total Loan Amount = SUM(Loans[Loan_Amount_LKR])

Avg Interest Rate = AVERAGE(Loans[Interest_Rate_Pct])

Avg Loan Amount = AVERAGE(Loans[Loan_Amount_LKR])

Overdue Loans = CALCULATE([Total Loans], Loans[Status] = "Overdue")

Overdue Rate % = DIVIDE([Overdue Loans], [Total Loans], 0)

High Risk Loans = CALCULATE([Total Loans], Loans[Risk_Rating] = "High")

High Risk % = DIVIDE([High Risk Loans], [Total Loans], 0)


## Dashboard Features

- KPI cards: Total Loans, Total Loan Amount, Avg Interest Rate, Overdue Rate %, High Risk %
- Total Loan Amount by Branch
- Loans by Status (donut chart)
- Overdue Rate % by Risk Rating
- Avg Interest Rate by Loan Type
- Monthly Loan Amount Trend by Year (line chart)
- Slicers for Year, Branch, and Loan Type

## Key Insights

- 977 clean loan records analyzed, with an overall **Overdue Rate of 26.9%**
- **32.0%** of loans are rated High Risk
- High-risk loans are about **4.5x more likely to be overdue** than low-risk loans (~45% vs ~10%), which suggests the risk rating is effective
- **Galle** has the highest total loan amount
- **Personal loans** have the highest interest rate (~12.5%) and **Education loans** the lowest (~7.5%)
- Average interest rate across the portfolio is **9.59%**

## Limitations

- 2026 data runs only until October, so full-year 2026 comparisons are incomplete
- A few records have `Unknown` branch, loan type, or risk rating because of missing source values

## Repository Contents

- `Bank_Loan_Risk_Analysis.pbix`: Power BI report (open with Power BI Desktop)
- `loan_data_1000.xlsx`: source dataset
- `dashboard.png`: dashboard screenshot
- `README.md`: project documentation

