

# Raw Data
```
SELECT *
FROM world.layoffs
;
```

# Data Cleaning

## 1. Remove Duplicates
```sql
CREATE TABLE layoffs_analysis AS
SELECT *,
       ROW_NUMBER() OVER(
           PARTITION BY company, location, industry, total_laid_off,
           percentage_laid_off, `date`, stage, country, funds_raised_millions
       ) AS row_num
FROM layoffs;
```

```sql
SELECT *
FROM layoffs_analysis
WHERE row_num > 1;
```

```sql
DELETE FROM layoffs_analysis
WHERE row_num > 1;
```
