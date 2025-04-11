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
- `percentage_laid_off = 1`: Filters rows where the entire workforce was laid off.
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
- GROUP BY company:- Groups the data by each unique company so that the aggregation (SUM) is applied per company.
- ORDER BY 2 DESC:- Sorts the results by the second column (SUM(total_laid_off)) in descending order, showing companies with the highest layoffs at the top.

![Total Layoffs by Company Ranked in Descending Order](https://github.com/user-attachments/assets/d619ad85-e9f5-437b-82e3-663ce56e9958)  

## Industry-Wise Total Layoffs Ranked in Descending Order  
5. This query aggregates the total layoffs by industry and ranks them in descending order based on the total number of layoffs.
```
SELECT industry, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY industry
ORDER BY 2 DESC;
```
- `SUM(total_laid_off)`- Calculates the total number of layoffs for each industry.
- `GROUP BY industry`- Groups the data by each unique industry, so that the total layoffs are calculated for each group.
- `ORDER BY 2 DESC` - Sorts the results by the second column`(SUM(total_laid_off))` in desending order, showing industries with the highest total layoffs at the top.

 ![Industry-Wise Total Layoffs Ranked in Descending Order](https://github.com/user-attachments/assets/4522dece-48a1-40ae-9ea8-cbfe1057ec39)  

 





