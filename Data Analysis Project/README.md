# Phase 2. Data Analysis  
With Phase 1 wrapped up, Phase 2—exploratory data analysis—can dive deeper into meaningful insights.  
## Maximum Layoffs Analysis  
1. Executed the folowing query to retrieves the maximum values for both the `total_laid_off` and `percentage_laid_off` columns from the layoffs_staging2 table. For `percentage_laid_off` 1 = 100%
```
SELECT MAX(total_laid_off), MAX(percentage_laid_off)
FROM layoffs_staging2;
```
![1  Max Laid Off](https://github.com/user-attachments/assets/6d075b3c-1dae-4d35-be04-f9aa2222148e)   

## Companies with Highest Workforce Layoffs

2. This query aims to extract specific rows from the layoffs_staging2 table where the percentage_laid_off column equals 1 (representing companies or entities that laid off their entire workforce).  
```
SELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1
```
![2  Total Laid Off](https://github.com/user-attachments/assets/869e5a65-b7ae-4ac7-8ff7-985101143958)  

## Companies with Highest Workforce Layoffs Ordered by Funding Raised

3. This query identifies companies that laid off 100% of their workforce while highlighting those with the highest funding.
```
SELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY funds_raised_millions DESC;
```
- percentage_laid_off = 1: Filters rows where the entire workforce was laid off.
- ORDER BY `funds_raised_millions DESC`: Sorts results by the funds_raised_millions column in descending order to show companies with the most funding at the top.
  
![3  Most Funding](https://github.com/user-attachments/assets/d15afb6f-35e4-4e14-871e-e495fe7fdad4)  

## Total Layoffs by Company Ranked in Descending Order  
4. This query aggregates the total number of layoffs for each company and ranks them in descending order based on the total layoffs.
```
SELECT company, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```
- SUM(total_laid_off):- Calculates the total number of employees laid off for each company.
- GROUP BY company:- Groups the data by each unique company so that the aggregation `(SUM)` is applied per company.
- ORDER BY 2 DESC:- Sorts the results by the second column `(SUM(total_laid_off))` in descending order, showing companies with the highest layoffs at the top.

![Total Layoffs by Company Ranked in Descending Order](https://github.com/user-attachments/assets/d619ad85-e9f5-437b-82e3-663ce56e9958)  

## Industry-Wise Total Layoffs Ranked in Descending Order  
5. This query aggregates the total layoffs by industry and ranks them in descending order based on the total number of layoffs.
```
SELECT industry, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY industry
ORDER BY 2 DESC;
```
- SUM(total_laid_off)- Calculates the total number of layoffs for each industry.
- GROUP BY industry- Groups the data by each unique industry, so that the total layoffs are calculated for each group.
- ORDER BY 2 DESC - Sorts the results by the second column`(SUM(total_laid_off))` in desending order, showing industries with the highest total layoffs at the top.

 ![Industry-Wise Total Layoffs Ranked in Descending Order](https://github.com/user-attachments/assets/4522dece-48a1-40ae-9ea8-cbfe1057ec39)  

 ## Country-Wise Total Layoffs Ranked in Descending Order
 6. This query aggregates the total number of layoffs by country and ranks them in descending order of total layoffs.
```
SELECT country, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;
```
- SUM(total_laid_off):- Adds up all the layoffs for each country.
- GROUP BY country:- Groups the data by unique country names so that the aggregation `(SUM)` is applied for each country.
- ORDER BY 2 DESC:- Sorts the results by the second column `(SUM(total_laid_off))` in descending order, showing countries with the highest layoffs at the top.
  
![Country-Wise Total Layoffs Ranked in Descending Order](https://github.com/user-attachments/assets/8c00149d-4310-431f-b669-61313437dd7a)  

## Yearly Total Layoffs Ranked by Most Recent Year  
7. This query aggregates the total number of layoffs by year and ranks them in descending order by year.  
```
SELECT YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY YEAR(`date`)
ORDER BY 1 DESC;
```
- YEAR(date):- Extracts the year portion from the date column to group layoffs by year.
- SUM(total_laid_off):- Calculates the total layoffs for each year.
- GROUP BY YEAR(date):- Groups the data by unique years, ensuring the totals are calculated for each year.
- ORDER BY 1 DESC:- Sorts the results by the year column in descending order, displaying the most recent years at the top.

![Yearly Total Layoffs Ranked by Most Recent Year](https://github.com/user-attachments/assets/3848c685-c41c-4233-99d7-dc805ebdc831)  

## Monthly Total Layoffs Ordered Chronologically  
8. This query is designed to aggregate the total number of layoffs by month, extracting the year-month portion from the `date` column for grouping.  
```
SELECT SUBSTRING(`date`,1,7) AS `MONTH`, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY `MONTH`
ORDER BY 1 ASC;
```
- SUBSTRING(date,1,7):- Extracts the first 7 characters of the `date` column, which typically correspond to the year and month in the format `YYYY-MM`. This creates a new column alias called `MONTH`.
- SUM(total_laid_off):- Calculates the total layoffs for each month.
- GROUP BY MONTH:- Groups the data by the extracted year-month value, enabling monthly aggregation.
- ORDER BY 1 ASC:- Sorts the results by the `MONTH` column in ascending order to display months sequentially.

  ![Monthly Total Layoffs Ordered Chronologically](https://github.com/user-attachments/assets/9b0dfd4c-919c-401c-9bce-c81294c93425)

## Monthly Layoffs with Rolling Total Cumulative Analysis  
9. This query calculates a rolling total of layoffs by month, providing both monthly layoffs and their cumulative total over time.
```
WITH Rolling_Total AS
(
SELECT SUBSTRING(`date`,1,7) AS `MONTH`, SUM(total_laid_off) AS total_off
FROM layoffs_staging2
GROUP BY `MONTH`
ORDER BY 1 ASC
)
SELECT `MONTH`, total_off,
SUM(total_off) OVER(ORDER BY `MONTH`) AS rolling_total
FROM Rolling_Total;
```
- WITH Rolling_Total AS:- Creates a Common Table Expression `(CTE)` named `Rolling_Total` to encapsulate the initial aggregation of data.
- Inside the CTE:- Extracts the year-month portion `(SUBSTRING(date, 1, 7) AS MONTH)` for grouping.
- Aggregates layoffs `(SUM(total_laid_off) AS total_off)` by month.
- Orders the results chronologically `(ORDER BY 1 ASC)` for later cumulative calculations.
- Main Query:- Selects `MONTH` and `total_off` from the CTE.
- Uses the `SUM(total_off) OVER(ORDER BY MONTH) ` window function to calculate a rolling total of layoffs over months. This provides a cumulative sum as each month progresses.

![Monthly Layoffs with Rolling Total Cumulative Analysis](https://github.com/user-attachments/assets/8b4c30fc-0d25-4689-89bb-ef555caf89e3)  

## Top 5 Companies with Highest Layoffs by Year  
10. This query is designed to rank companies by total layoffs for each year and retrieve the top 5 companies per year.
```
WITH Company_Year (company, years, total_laid_off)  AS
(
SELECT company, YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
), Company_Year_Rank AS
(SELECT *, 
DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC) AS Ranking
FROM Company_Year
WHERE total_laid_off IS NOT NULL
)
SELECT *
FROM Company_Year_Rank
WHERE Ranking <= 5;
````
- Common Table Expression (CTE) - `Company_Year`:- Groups the data by `company` and `YEAR(date)`, calculating the total layoffs `(SUM(total_laid_off))` for each company annually.
- Columns included: `company`, `years` (extracted from the date), and `total_laid_off`.
- CTE - Company_Year_Rank:- Introduces a `DENSE_RANK()` window function that assigns a rank to companies based on their total layoffs in descending order, partitioned by years.
- The PARTITION BY years clause ensures ranking is done separately for each year.
- Filters out rows where `total_laid_off` is `NULL`.
- Final Query:- Selects all columns from `Company_Year_Rank`.
- Filters to include only rows where `Ranking <= 5,` i.e., the top 5 companies with the highest layoffs for each year.

![Top 5 Companies with Highest Layoffs by Year](https://github.com/user-attachments/assets/1fc79086-ea94-48ef-9743-d342660e1f47)  

## Conclusion  
The data cleaning phase focused on standardizing key columns, removing null values in critical fields, and preparing the dataset for analysis. Exploratory analysis then delved into trends in total layoffs and layoff percentages, uncovering patterns across industries, companies, and regions, while also tracking time-based trends like yearly and monthly cumulative layoffs. These steps laid the groundwork for actionable insights and meaningful conclusions.




