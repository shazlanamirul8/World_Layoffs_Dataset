# Analysis & Findings

## Layoffs by Company in a Single Event

```sql
SELECT company, total_laid_off, percentage_laid_off
FROM layoffs_analysis
ORDER BY total_laid_off DESC;
```

## Layoffs Period

```sql
SELECT MIN(`date`) AS started, MAX(`date`) AS ended
FROM layoffs_analysis;
```

## Total Layoffs by Companies

```sql
SELECT company, SUM(total_laid_off) AS layoffs_num
FROM layoffs_analysis
GROUP BY company
ORDER BY 2 DESC;
```

## Total Layoffs by Industries

```sql
SELECT industry, SUM(total_laid_off) AS layoffs_num
FROM layoffs_analysis
GROUP BY industry
ORDER BY 2 DESC;
```

SELECT country, SUM(total_laid_off)
FROM layoffs_analysis
GROUP BY country
ORDER BY 2 DESC;

# Raw Data
```sql
-- View raw data
SELECT *
FROM world.layoffs;
```

# Data Cleaning

## 1. Remove Duplicates
```sql
-- Creating new table with row_num included
CREATE TABLE layoffs_analysis AS
SELECT *,
       ROW_NUMBER() OVER(
           PARTITION BY company, location, industry, total_laid_off,
           percentage_laid_off, `date`, stage, country, funds_raised_millions
       ) AS row_num
FROM layoffs;
```

```sql
-- Checking duplicates
SELECT *
FROM layoffs_analysis
WHERE row_num > 1;
```

```sql
-- Delete duplicates
DELETE FROM layoffs_analysis
WHERE row_num > 1;
```

## 2. Standardizing Data

```sql
-- Trim all text columns
UPDATE layoffs_analysis
SET company = TRIM(company),
    location = TRIM(location),
    industry = TRIM(industry),
    stage = TRIM(stage),
    country = TRIM(country);
```

```sql
-- Checking industry column
SELECT DISTINCT industry
FROM layoffs_analysis
ORDER BY 1;
```

```sql
-- Standardize industry column
UPDATE layoffs_analysis
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';
```

```sql
-- Checking country column
SELECT DISTINCT country
FROM layoffs_analysis
ORDER BY 1;
```

```sql
-- Standardize country column
UPDATE layoffs_analysis
SET country = 'United States'
WHERE country LIKE 'United States%';
```

```sql
-- Convert date column from text to DATE format
UPDATE layoffs_analysis
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE layoffs_analysis
MODIFY COLUMN `date` DATE;
```

## 3. NULL & Blank Columns

```sql
-- Check text columns for blank values
SELECT *
FROM layoffs_analysis
WHERE company = '' OR location = '' OR industry = '' OR stage = '' OR country = '';
```

```sql
-- Fill in missing industry values
UPDATE layoffs_analysis
SET industry = 'Travel'
WHERE company = 'Airbnb';

UPDATE layoffs_analysis
SET industry = 'Transportation'
WHERE company = 'Carvana';

UPDATE layoffs_analysis
SET industry = 'Consumer'
WHERE company = 'Juul';
```

```sql
-- Remove rows with no layoff data
DELETE
FROM layoffs_analysis
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;
```

```sql
-- Drop row_num column as it is no longer needed
ALTER TABLE layoffs_analysis
DROP COLUMN row_num;
```
