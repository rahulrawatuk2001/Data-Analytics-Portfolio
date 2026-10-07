# Excel Reporting Dashboard

## Overview
This project analyzes an AI-generated synthetic retail transactions dataset (2019–2022). The raw data was cleaned and an interactive dashboard was built using Pivot Tables, Pivot Charts and Slicers.

## Aim
To clean and analyze the retail transactions dataset and calculate key business metrics — Total Sales, Total Profit, Profit Margin % and Order Lines — across Category, Region, Sub-Category and Customers. The final output is an interactive dashboard with KPI cards, charts and slicers, plus short written insights.

## Tools Used
- Excel-format workbook (.xlsx) built in WPS Spreadsheets
- Pivot Tables, Pivot Charts, Slicers, Calculated Field, Formulas (`TRIM`, `ABS`, `IF`, `YEAR`, `TEXT`, `MONTH`), Find & Replace, Remove Duplicates

## Workbook Structure (5 sheets)
- **Raw Data** – original, unedited dataset (10,014 rows)
- **Working Data** – cleaned data (9,986 rows) plus 3 helper columns
- **Metrics** – Pivot Tables behind the KPIs and charts
- **Dashboard** – KPI cards, slicers, charts and a table
- **Insights** – short written observations for each chart

## Data Cleaning Process
1. **Data types:** `Order Date` and `Ship Date` set as real Dates (dd/mm/yyyy); `Sales`, `Profit`, `Quantity` and `Discount` set as numbers/currency.
2. **Text cleaning:**
   - `TRIM()` applied to `Ship Mode` (28 rows) and `Customer Name` (18 rows).
   - 10 `City` values with a state name added (e.g. "Mobile, Alabama") were corrected to the city name only.
   - 6 misspelled `Sub-Category` values (e.g. "Bookcasess") corrected with Find & Replace.
3. **Missing values (kept as true blanks, rows not deleted):**
   - `Order Date`: 22 "N/A" values → blank
   - `Profit`: 12 "unknown" and 8 "N/A" values → blank (55 blanks in total with 35 already blank)
   - `Sales`: 45 blanks left as they were
4. **Numeric fixes:**
   - Removed the stray `?` from 13 `Sales` values.
   - Removed 8 rows with negative `Sales` (corrupted transactions).
   - Cleared 5 invalid `Discount` values (1.5, outside 0–1) to blank; the rows were kept.
   - Applied `ABS()` to 9 negative `Quantity` values (treated as entry errors, not returns).
5. **Duplicates:** Removed 20 duplicate rows (all columns except `Row ID` and `Order ID`).

Row count: 10,014 − 8 (negative Sales) − 20 (duplicates) = **9,986 rows**.

## Handling the 22 Missing Order Dates
The 22 rows without an `Order Date` were **not** deleted and no fake date was added. They still count in every total (Sales $100,119.33, Profit $631.82). To build the monthly chart, 3 helper columns were added in Working Data (they stay blank when `Order Date` is blank):
- `Order Year` = `=IF(C2="","",YEAR(C2))`
- `Order Month` = `=IF(C2="","",TEXT(C2,"mmm"))`
- `Order Month No` = `=IF(C2="","",MONTH(C2))`

Only the Monthly chart leaves out these 22 rows, because they have no month. All KPIs and the other charts include them.

## Metrics (Pivot Tables)
- KPIs: Total Sales, Total Profit, Profit Margin % (calculated field = Profit ÷ Sales) and Order Lines (count of `Order ID`)
- Monthly Sales and Profit Margin % by Order Year and Order Month (48 months)
- Sales by Category and by Region
- Top Customers by Sales
- Average Discount and Total Profit by Sub-Category
- Sales and Profit by Sub-Category (placed on the Dashboard sheet)

## Dashboard
- **KPI cards:** Total Sales $38,780,361 | Total Profit $3,268,695 | Profit Margin 8.43% | Order Lines 9,986
- **Slicers:** Order Year (includes a blank option for the 22 undated rows), Region, Category
- **Charts:**
  - Monthly Sales vs Profit Margin % (Sales as columns, Margin % as a line)
  - Average Discount % vs Profit by Sub-Category
  - Region-wise Sales
  - Top 10 Customers by Sales
  - Category Share (Sales split by Category)
- **Table:** Sub-Category by Sales & Profit

## Insights
Each chart has a short note in three parts: Pattern (what the data shows), Reason (what the chart does not show), and Suggestion (what to check next).

- **Monthly Sales vs Profit Margin %:** Monthly Sales mostly stay between $606K and $969K, and were similar from 2019 to 2021 and higher in 2022. Profit Margin % is mostly between 5% and 12.5%, with the lowest in January 2020 (about 4.3%). Higher Sales do not always mean a higher margin.
- **Average Discount % vs Profit by Sub-Category:** Average Discount is fairly similar across sub-categories, while Profit differs more. Higher Discount does not always mean lower Profit.
- **Region-wise Sales:** Central has the highest Sales, followed by East and South. West has the lowest.
- **Top 10 Customers:** Brooke Gillingham is the top customer ($1.40M) and Benjamin Patterson is 10th ($1.21M). The 10 customers are close in Sales, and together they make up about one-third of total Sales.
- **Sub-Category by Sales & Profit:** Bookcases have the highest Sales and Profit, and Labels the lowest. Tables have lower Sales than Chairs, Phones and Copiers but higher Profit than all three.
- **Category Share:** Furniture (33.7%) and Office Supplies (33.7%) are almost equal, and Technology is slightly lower (32.5%).
  
## Project Workflow
Raw Data → Cleaning (Working Data) → Helper Columns → Pivot Table Metrics → Dashboard (KPI Cards, Slicers, Charts) → Insights
