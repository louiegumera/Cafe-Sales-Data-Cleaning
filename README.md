# Cafe Sales Data Cleaning

## 🛠️ Tools Used In This Project

• SQL - Data Cleaning, Data Validation, Query, Data Transformation

• MySQL Workbench

## Summary

Cleaned a cafe sales dataset with over 8,000 transactions using SQL queries by identifying missing, invalid, and inconsistent entries, improving data quality and consistency for further analysis.

## Key Features

• Identified and checked duplicate records, confirming that no duplicates were found.

• Cleaned text fields by removing unnecessary white spaces and identifying missing, invalid, and inconsistent entries.

• Corrected missing and invalid values using SQL CASE statements and validated item names against their expected prices.

• Standardized transaction dates by converting date strings into a consistent format and changing the column data type to DATE.

• Prepared the cleaned dataset for reliable SQL querying and further data analysis.

## Data Cleaning With SQL

### Creating a backup table

A backup table is necessary before doing any changes, it keeps the original table
intact for future revisions.

```sql
CREATE TABLE `sales_staging` (
`Transaction_ID` text,
`Item` text,
`Quantity` int DEFAULT NULL,
`Price_Per_Unit` double DEFAULT NULL,
`Total_Spent` double DEFAULT NULL,
`Payment_Method` text,
`Location` text,
`Transaction_Date` text
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

INSERT INTO sales_staging
SELECT *
FROM cafe_sales;
```

### Identifying duplicates

Identifying duplicates helps keep the dataset clean and reliable. In this dataset, the
author did not find any duplicates.

```sql
WITH duplicate_test AS(
SELECT *,
ROW_NUMBER() OVER(PARTITION BY Transaction_ID) AS Row_Num
FROM sales_staging
)
SELECT COUNT(Row_Num)
FROM duplicate_test
WHERE Row_Num > 1;
```

### Removing white spaces

Removing white spaces in a dataset ensures that text values are clean and
consistent.

```sql
UPDATE sales_staging
SET Transaction_ID = TRIM(Transaction_ID);
UPDATE sales_staging
SET Item = TRIM(Item);
UPDATE sales_staging
SET Payment_Method = TRIM(Payment_Method);
UPDATE sales_staging
SET Location = TRIM(Location);
```

### Identifying misspellings

Identifying misspellings finds and correct incorrectly spelled values so that the data remains accurate and consistent. The author did not find any misspellings but found invalid entries including “ERROR” and “UNKNOWN”.

```sql
SELECT DISTINCT Item
FROM sales_staging;
SELECT DISTINCT Payment_Method
FROM sales_staging;
SELECT DISTINCT Location
FROM sales_staging;
```

### Identifying missing and invalid entries

The author identified missing and invalid entries finds incomplete or incorrect values that could affect the accuracy of the data and future analysis. The author found a total of 7,261 missing and invalid entries including “ERROR” and “UNKNOWN”. 812 in Item column, 2,691 in Payment_Method column, 3,373 in Location column, and 385 in Transaction_Date column.

```sql
SELECT
SUM(Transaction_ID IS NULL OR Transaction_ID = '' OR Transaction_ID =
'UNKNOWN' OR Transaction_ID = 'ERROR') AS Transaction_ID_Missing,
SUM(Item IS NULL OR Item = '' OR Item = 'UNKNOWN' OR Item = 'ERROR')
AS Item_Missing,
SUM(Quantity IS NULL OR Quantity = '' OR Quantity = 'UNKNOWN' OR
Quantity = 'ERROR') AS Quantity_Missing,
SUM(Price_Per_Unit IS NULL OR Price_Per_Unit = '' OR Price_Per_Unit =
'UNKNOWN' OR Price_Per_Unit = 'ERROR') AS Price_Per_Unit_Missing,
SUM(Total_Spent IS NULL OR Total_Spent = '' OR Total_Spent = 'UNKNOWN'
OR Total_Spent = 'ERROR') AS Total_Spent_Missing,
SUM(Payment_Method IS NULL OR Payment_Method = '' OR Payment_Method =
'UNKNOWN' OR Payment_Method = 'ERROR') AS Payment_Method_Missing,
SUM(Location IS NULL OR Location = '' OR Location = 'UNKNOWN' OR
Location = 'ERROR') AS Location_Missing,
SUM(Transaction_Date IS NULL OR Transaction_Date = '' OR
Transaction_Date = 'UNKNOWN' OR Transaction_Date = 'ERROR') AS
Transaction_Date_Missing
FROM sales_staging;
```

