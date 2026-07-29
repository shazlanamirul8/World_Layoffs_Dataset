# World_Layoffs_Dataset
SQL analysis of global layoffs, exploring trends by company, industry, company stage, and geography using MySQL.

# Raw Data
- Below is a screenshot of raw data
![Layoffs table](images/raw_data.png)




# Data Cleaning

## 1. Remove Duplicates

Since the dataset does not have a unique identifier, I needed to 
find another way to detect duplicates. Here is what I did:

- Created a staging table called `layoffs_analysis` to work on, 
  keeping the raw data untouched
- Used `ROW_NUMBER()` to flag duplicates by partitioning records 
  that share the same company, location, industry, total_laid_off, 
  date, stage, country, and funds_raised_millions
- Any record that appears more than once gets a row number greater 
  than 1 and those are the duplicates
- Deleted all records where row number is greater than 1
- After deleting, I ran the same query to check if any duplicates still exist. No results means the duplicates were successfully removed.
![Layoffs table](images/remove_duplicates.png)

## 2. Standardizing Data

Checking data types is an important step before doing any analysis. 
ost of the time, raw data captures the `date` column as text instead 
of a proper date format, so that needs to be fixed. Here is what I did:

- Trimmed all text columns to remove any leading or trailing spaces 
  that could affect the analysis
- Some columns had inconsistent spellings for the same value, for 
  example `Crypto`, `Cryptocurrency` and `Crypto Currency` all refer 
  to the same industry. I standardized these so the data is consistent 
  and accurate
  ![Layoffs table](images/before_crypto.png)
  *Before: Industry column with inconsistent spellings*
  
  ![Layoffs table](images/after_crypto.png)
  *After: Standardized to a single value*
  
- Converted the `date` column from text to a proper DATE format so it 
  can be used correctly in time-based analysis
  ![Layoffs table](images/before_date.png)
  *Before: Date is stored as text in MM/DD/YYYY format*

  ![Layoffs table](images/after_date.png)
  *After: Date is converted to proper DATE format*

## 3. NULL & Blank Columns

Before doing the analysis, I handled NULL and blank values to ensure 
the data is clean and accurate. Here is what I did:

- Checked text columns for blank values. In some cases, two rows 
  belong to the same company but one has an industry value and the 
  other is blank. Where possible, I filled in the missing industry 
  based on the other row. If it cannot be determined, I converted 
  it to NULL
  ![Layoffs table](images/before_industry.png)
  *Before: Both companies are Airbnb but one industry is Travel and the other one is blank*

  ![Layoffs table](images/after_industry.png)
  *After: Airbnb industry is fixed
  
- Since this dataset is about global layoffs, companies with no 
  layoff data in both `total_laid_off` and `percentage_laid_off` 
  are not useful for analysis. I removed those rows entirely
- Dropped the `row_num` column as it was only needed for duplicate 
  detection and is no longer required
