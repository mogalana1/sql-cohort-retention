# Block 2, Day 1: Data Profiling & Decision Log

## 1. Data Grain
One row represents a single line item (product) on an invoice, not a unique customer transaction. One invoice can contain multiple rows.

## 2. Exclusions (The "Clean" Dataset)
To build a valid Cohort Retention Matrix, I will apply the following filters:
- Exclude rows where `Invoice` starts with 'C' (Cancellations/Returns).
- Exclude rows where `Customer ID` IS NULL (Anonymous/Guest checkouts cannot be tracked for retention).
- Filter to `Country = 'United Kingdom'` to remove international shipping noise from the baseline retention curve.

## 3. Data Quality Traps Identified
- **Price Column:** DuckDB read this as `varchar` (text) because the data uses commas for decimals (e.g., "2,55"). I must replace commas with dots and cast to `DOUBLE` before calculating revenue.
- **InvoiceDate Column:** DuckDB read this as `varchar`. String sorting breaks chronological MIN/MAX queries. I must cast this to a proper `DATE` type.

## 5 Things I Remembered from Memory
1. Data Ingestion Rule:
Refinement: DuckDB requires the file to be saved specifically as "CSV UTF-8 (Comma delimited)". Standard CSV or Excel formats will cause encoding errors or fail to load.

2. Cancellation Logic:
Refinement: Invoices starting with "C" are credit memos (cancellations/returns). They are paired with negative quantities. They must be excluded from revenue and retention calculations.

3. Anonymous Traffic:
Refinement: Customer ID IS NULL represents guest checkouts. Because they lack a unique identifier, they cannot be tracked across time. They must be excluded from cohort analysis.

4. The Date Sorting Trap:
Refinement: DuckDB inferred InvoiceDate as varchar (text). Therefore, MIN() and MAX() sort alphabetically, not chronologically. Fix: We must cast the column to a proper date type using CAST("InvoiceDate" AS DATE).

5. Geographic Noise:
Refinement: Mixing 38 different countries introduces noise (varying shipping times, currencies, and market maturity). For a clean baseline Cohort Retention Matrix, we must filter to Country = 'United Kingdom'.

6. Data Grain (Crucial Concept):
Refinement: The "grain" of this dataset is line-item level, not transaction level. One unique Invoice number contains multiple rows (one for each product bought). We must group by Invoice to find true transaction counts.

7. The Price/Decimal Trap:
Fact: DuckDB read the Price column as varchar (text) instead of a number.
Why: The dataset uses European formatting (commas for decimals, e.g., 2,55 instead of 2.55). DuckDB saw the comma and assumed it was text.
Fix: Before calculating revenue, we must replace commas with dots and cast the column to a DOUBLE or DECIMAL type.