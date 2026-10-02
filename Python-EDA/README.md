# Python Exploratory Data Analysis (EDA)

## Overview
This project is an Exploratory Data Analysis (EDA) of an AI-generated synthetic retail transactions dataset using Python. The goal was to clean the raw data, check it for quality issues, and find patterns in Sales and Profit using statistics and visualizations.

## Aim
The main aim of this project was to clean and analyze the retail transactions dataset using Python to understand Sales, Profit, and Profit Margin patterns across Category, Sub-Category, Region, and Discount levels. The goal was to detect data quality issues and outliers, evaluate statistical relationships between Sales, Profit, Discount, and Quantity using correlation analysis, and present key business insights through exploratory data visualizations.

## Tools Used
- Python
- Pandas (data cleaning, statistics, aggregation)
- NumPy
- Matplotlib and Seaborn (visualization)
- Jupyter Notebook

## Dataset
- Raw file: `retail_transactions.csv`
- Total rows before cleaning: 10,014
- Total rows after cleaning: 9,986
- Total columns: 21 (22 after adding `Profit_margin`)

## Step 1: Basic Exploration
Before touching the data, I explored it first using `.head()`, `.tail()`, `.sample()`, `.shape`, `.info()`, `.describe()`, `.columns`, and `.dtypes`. I also checked unique values and count of unique values for key categorical columns (Ship Mode, Segment, Region, Category, Sub-Category) to understand what values exist before cleaning.

This showed the problems to fix: Ship Mode had 8 unique values (extra spaces), Region had 16 (mixed upper/lower case), and Sub-Category had 23 (spelling mistakes).

## Step 2: Data Cleaning

**Missing Values**
- Checked missing values using `.isnull().sum()`.
- Order Date had 22 missing values, City had 18, Sales had 45, and Profit had 43.
- Sales and Profit missing values were kept as NaN (no fill, no drop) because Pandas automatically ignores NaN values in functions like `.sum()` and `.mean()`, similar to how NULL works in SQL.

**Standardization**
- Removed invalid `?` characters from the Sales column.
- Removed extra spaces from Ship Mode and Customer Name using `.str.strip()`.
- Standardized Region text to proper case using `.str.title()`.
- Fixed spelling mistakes in Sub-Category (example: "Furnishingss" → "Furnishings", "Bookcasess" → "Bookcases") using `.replace()`.
- Cleaned City column by removing State name that was incorrectly attached to some City values (split on `,` and kept the first part).
- Replaced invalid text like "unknown" and "N/A" in Profit column with proper NA (`pd.NA`) so they are treated as missing, not as text.

**Duplicate Check (Initial)**
- Ran `.duplicated().sum()` at this stage, which returned 0. (Final duplicate removal was done later, after all cleaning was complete, so that duplicates hidden by formatting/typos could also be caught.)

**Data Type Conversion**
- Converted Order Date and Ship Date to proper datetime format using `pd.to_datetime()` with `format="mixed"`, `dayfirst=True` and `errors="coerce"` (mixed date formats in the raw data).
- Converted Sales and Profit to numeric type using `pd.to_numeric()` with `errors="coerce"`.

**Invalid Value Correction**
- Negative Sales: Found 8 rows with negative Sales values. Verified them first, then removed these rows completely since there is no Refund column to explain them as valid (10,014 → 10,006 rows).
- Invalid Discount: Found 5 rows where Discount was greater than 1 (not a valid percentage). Instead of deleting the row, only the Discount value was set to null (NaN), keeping the rest of the row's data intact. The same rule is also applied to Discount below 0.
- Negative Quantity: Found 9 rows with negative Quantity. Fixed these using `.abs()` to convert them to positive, treating them as sign-entry mistakes rather than deleting the rows.

**Duplicate Removal (Final)**
- After all cleaning steps were done, duplicate rows were removed using `drop_duplicates()`, excluding only Row ID from the comparison (since it is a unique identifier and would never match even for genuine duplicate records). Order ID is included in the comparison.
- This removed 20 duplicate rows (10,006 → 9,986).

## Step 3: Feature Engineering
- Added a new column, **Profit_margin**, calculated as `(Profit / Sales) * 100`.

## Step 4: Outlier Check
- Used the IQR (Interquartile Range) method to detect outliers (values below Q1 − 1.5×IQR or above Q3 + 1.5×IQR).
- Sales column: 210 outliers found.
- Profit column: 1,163 outliers found.

## Step 5: Data Selection & Filtering
Practiced basic Pandas operations for selecting and filtering data:
- Selecting single/multiple columns, row slicing with `.iloc`, conditional selection with `.loc` (example: Category and Sales for the South region).
- Filtering examples: Sales greater than 1000; Furniture orders with negative Profit.
- Sorting data by Profit (ascending).

## Step 6: Statistics
Calculated statistics for the Sales column:

| Metric | Value |
|---|---|
| Mean | 3,901.05 |
| Median | 2,943.74 |
| Standard Deviation | 3,398.87 |
| Variance | ~11.55 million |

- Mean is higher than Median, which also points to a right-skewed Sales distribution.
- Most common Category (Mode): Office Supplies.
- Checked correlation between Sales, Profit, Discount, and Quantity using `.corr()`.

## Step 7: Aggregation
- Grouped Sales by Region using `.groupby()` and `.agg()` (sum, mean, count).
- Central had the highest total Sales (~10.24M) and West the lowest (~8.61M). Average Sales per order was similar across regions (~3,860–3,995), so the West gap comes mainly from fewer orders (2,155 vs 2,651 in Central).
- Checked value counts for Category: Office Supplies (3,377), Furniture (3,367), Technology (3,242).
- Built a pivot table showing Sales by Region and Category using `pd.pivot_table()`.

## Step 8: Visualizations & Insights

Each chart insight follows the same format: **Pattern** (what the chart shows), **Reason** (why the chart alone can't explain it), **Suggestion** (what to analyze next).

**1. Line Plot – Monthly Sales Trend**

<img src="chart1_monthly_sales_trend.png" width="500">

- **Pattern:** Sales go up and down every month from 2019 to 2022, with no steady increase or decrease. Some months show very high sales, while others drop a lot.
- **Reason:** The chart uses only Order Date and Sales, so it doesn't show which Category, Region, or Segment is behind the changes.
- **Suggestion:** Look at sales by Category, Sub-Category, Region, or Segment to understand why certain months performed better or worse.

**2. Bar Plot – Category-wise Sales**

<img src="chart2_category_sales.png" width="500">

- **Pattern:** Office Supplies has the highest Sales, and Furniture is almost equal (less than 0.1% difference). Technology is about 3.6% lower than the other two.
- **Reason:** The chart compares Category and Sales only, so it doesn't show which Sub-Category or Product Name drives the difference.
- **Suggestion:** Analyze Sub-Category and Product Name level sales to see which products contribute more or less to Technology sales.

**3. Scatter Plot – Discount vs Profit**

<img src="chart3_discount_vs_profit.png" width="500">

- **Pattern:** Discount only occurs at fixed levels (0.0, 0.1, 0.2, 0.3, 0.5 — no orders at 0.4). As Discount increases, the maximum Profit decreases — at 0 discount, Profit goes up to ~7,200, but at 0.5 discount, it only reaches ~2,800.
- **Reason:** The chart uses only Discount and Profit, so it doesn't show which Category, Sub-Category, or Product Name the pattern comes from.
- **Suggestion:** Analyze high-discount orders (0.3 and 0.5) separately at Category, Sub-Category, and Product Name level to see where low or negative profit occurs.

**4. Box Plot – Profit by Category**

<img src="chart4_profit_by_category.png" width="500">

- **Pattern:** All three categories contain both profit and loss orders, and all have many outliers (both high-profit and high-loss). Technology has some of the highest positive profit outliers.
- **Reason:** The chart uses only Category and Profit, so it doesn't show which Sub-Category or Product Name is behind these unusual values.
- **Suggestion:** Analyze the outlier orders separately, and compare Product Name and Sub-Categories within each category.

**5. Heatmap – Correlation Matrix**

<img src="chart5_correlation_heatmap.png" width="500">

- **Pattern:** Sales and Quantity: moderate positive relationship (0.61). Sales and Profit: weak positive (0.23). Sales and Discount: weak negative (-0.2). Profit and Discount: almost no relationship (-0.049).
- **Reason:** The heatmap shows how the four columns move together, but not which Category, Sub-Category, or Region is behind these patterns.
- **Suggestion:** Compare these relationships across Category, Sub-Category, or Region to see if specific areas behave differently.

**6. Histogram – Sales Distribution**

<img src="chart6_sales_distribution.png" width="500">

- **Pattern:** Most Sales transactions are concentrated at lower values, and the number of transactions gradually decreases as Sales amount increases. A small number of very high Sales transactions form a long right tail (positively/right skewed distribution).
- **Reason:** The histogram uses only the Sales column, so it doesn't show which Product Name, Category, or Customer Name contributes to the high values.
- **Suggestion:** Analyze high-value transactions separately, and compare low-value and high-value transactions to understand the business pattern.

## Key Findings
- Multiple data-quality issues were identified and corrected: Region inconsistencies, Sub-Category typos, invalid Discount values, and negative Sales/Quantity values. 20 duplicate rows were removed at the end.
- Monthly Sales fluctuated across 2019–2022 with no consistent upward or downward trend.
- Office Supplies and Furniture had nearly equal Sales, while Technology was about 3.6% lower.
- Central and East regions had the highest total Sales; West had the lowest due to fewer orders.
- Higher Discount levels showed lower maximum Profit, though the overall Discount–Profit relationship was weak.
- Sales and Quantity had a moderate positive relationship.
- All categories had both high-profit and high-loss outliers, with Technology showing some of the highest profit outliers.
- Most Sales transactions were low-value, with a few high-value orders forming a long right tail.

## Business Recommendations
- Review high-discount orders (especially 0.3 and 0.5) to understand their impact on profitability.
- Investigate high-profit and high-loss outlier orders to understand why they performed differently.
- Analyze high-value Sales orders to understand which categories or regions generate the most Sales.
- Check Category and Sub-Category performance before making business decisions.
