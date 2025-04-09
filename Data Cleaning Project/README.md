## Phase One: Data Cleaning  
### Remove Duplicates    
As a best practice, a staging table (layoffs_staging) was created to preserve the integrity of the original layoffs table.  
```
CREATE TABLE layoffs_staging
LIKE layoffs;

SELECT *
FROM layoffs_staging;

INSERT layoffs_staging
SELECT *
FROM layoffs;
```
### The initial cleaning step involved removing duplicate records to ensure data accuracy and consistency.  

1. This query is utilizing the `ROW_NUMBER` function to assign a unique row number to records within the layoffs_staging table.
```
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company, industry, total_laid_off, percentage_laid_off, `date`) AS row_num
FROM layoffs_staging;
```
![1  Remove Dup](https://github.com/user-attachments/assets/d3feaa3a-eb2e-45d4-a825-3198485d6b8d)  

2. This query is identifying and isolating duplicate records from the layoffs_staging table by leveraging a Common Table Expression (CTE).
```
WITH duplicate_cte AS
(
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company, location, 
industry, total_laid_off, percentage_laid_off, `date`, stage, 
country, funds_raised_millions) AS row_num
FROM layoffs_staging
)
SELECT *
FROM duplicate_cte
WHERE row_num > 1;
```
- Inside the CTE, the `ROW_NUMBERE` function is used to assign a unique number (row_num) to each record within groups of duplicate rows.
- The `PARTITION BY` clause defines the grouping logic, where records are grouped by columns like company, location, date, stage, etc. This ensures that each unique combination of these attributes starts a new group, and duplicates within the same group are numbered sequentially.
- The `SELECT` statement outside the CTE pulls rows from duplicate_cte where row_num > 1.
- This effectively filters out the "first" occurrence of each unique record, leaving only the duplicates for further review or removal.

![2  Remove Dup](https://github.com/user-attachments/assets/3ba28e2e-a33b-414a-887c-7687c9531724)  

3. This query establishes a new table, layoffs_staging2, to handle the deletion of duplicate rows efficiently.
```
CREATE TABLE `layoffs_staging2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` text,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` int
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```
- Used an `INSERT INTO` query to populate the layoffs_staging2 table. This included all columns from layoffs_staging while adding a calculated row_num column using `ROW NUMBER`. The row_num helps identify duplicates within groups defined by columns like company, location, industry, and others.
```
INSERT INTO layoffs_staging2
SELECT*,
ROW_NUMBER() OVER(
PARTITION BY company, location, 
industry, total_laid_off, percentage_laid_off, `date`, stage, 
country, funds_raised_millions) AS row_num
FROM layoffs_staging;
```
4. Delete Statement
- This final delete statement removes the duplicate rows from the layoffs_staging table.
```
DELETE 
FROM layoffs_staging2
WHERE row_num > 1;
```

### Standardizing Data  
Standardizing the data is an essential step to ensure consistency, improve analysis accuracy, and make the dataset easier to work with.  
I used the `TRIM()` function to remove any leading or trailing white spaces.  
```
SELECT company, TRIM(company)
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET company = TRIM(company);
```
1.   `SELECT` company, `TRIM`(company)
   - This query is helpful for previewing the changes before applying them. It shows the company column's original values alongside the trimmed values, allowing you to verify that the `TRIM()` function works as 
     intended.

2.   UPDATE layoffs_staging2 `SET` company = `TRIM`(company)
   - This query actually updates the company column in the table to remove white spaces permanently. It's a crucial step in standardizing your data.

I had to standardize the values in the industry column to display Crypto instead of Crypto Currency  
```
SELECT *
FROM layoffs_staging2
WHERE industry LIKE 'Crypto%';

UPDATE layoffs_staging2
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';
```
![3  Standardize Industry Column](https://github.com/user-attachments/assets/1f4112e7-8119-40b5-9ee2-62da1bb33bfe)  

In preparation for the exploratory analysis phase, rows with `NULL` values in the `total_laid_off` and `percentage_laid_off` columns will be removed. These columns are critical for analysis, as they provide essential data for identifying patterns, trends, and key metrics related to layoffs.  
```
SELECT *
FROM layoffs_staging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;
```
This query helps you identify rows in the layoffs_staging2 table where both `total_laid_off` and `percentage_laid_off` columns have `NULL` values. This allows you to review the affected data and confirm the necessity of their removal.  
![4  Null Values](https://github.com/user-attachments/assets/893d14c4-55c4-4407-b786-f51f11f0be8f)  
The below query removes the rows from the layoffs_staging2 table  
```
DELETE
FROM layoffs_staging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;
```
![5 Null Deleted](https://github.com/user-attachments/assets/c8200774-7967-410c-a05e-22df513572d3)  
By executing this, I have ensured that all remaining data will be reliable for analyzing layoffs trends and patterns.

