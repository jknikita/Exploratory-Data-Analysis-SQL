# Layoffs Exploratory Data Analysis (MySQL)

An exploratory analysis in MySQL of a global layoffs dataset, covering **2020 to 2023**. Using the cleaned table from the [data cleaning project](https://github.com/jknikita/Layoffs-Data-Cleaning-SQL), I looked at which companies, industries, countries and time periods were hit hardest by layoffs.

## Project Overview

**Goal:** Answer practical questions about the layoffs data using SQL only.

- Which companies laid off the most people?
- Which companies shut down completely?
- Which industries, countries and funding stages were most affected?
- How did layoffs change over time (year, month and cumulative)?
- Who were the top 5 companies by layoffs in each year?

## Tools & Skills Demonstrated

- **MySQL** (MySQL Workbench)
- Aggregations (`SUM`, `AVG`, `MAX`, `MIN`) with `GROUP BY`
- CTEs, including multiple chained CTEs
- Window functions: `SUM() OVER` for rolling totals and `DENSE_RANK() OVER (PARTITION BY ...)` for yearly rankings
- Date functions (`YEAR`, `DATE_FORMAT`) and type casting (`CAST`)

## Dataset

- **Input table:** `layoffs_staging2`, the output of the Layoffs-Data-Cleaning-SQL(https://github.com/jknikita/Layoffs-Data-Cleaning-SQL)
- **Columns:** company, location, industry, total laid off, percentage laid off, date, stage, country, funds raised (millions)
- **Source:** Layoffs dataset

## Analysis Sections

| # | Section | Question answered |
|---|---------|-------------------|
| 1 | **Dataset overview** | What period does the data cover, and what are the maximum layoff values? |
| 2 | **Complete shutdowns** | Which companies laid off 100% of staff, and how much funding had they raised? |
| 3 | **By company** | Which companies had the most layoffs in total, and the highest average share of workforce cut? |
| 4 | **By industry, country, stage** | Where were layoffs concentrated? |
| 5 | **Over time** | How did layoffs trend by year and month, and what is the cumulative total? |
| 6 | **Yearly top 5** | Which five companies had the most layoffs in each year? |

## Key Techniques

**Rolling total by month**

```sql
WITH Rolling_Total AS
(
select SUBSTRING(`date`, 1, 7) AS `MONTH`, SUM(total_laid_off) AS total_off
from layoffs_staging2
WHERE  SUBSTRING(`date`, 1, 7) IS NOT NULL
GROUP BY `MONTH`
ORDER BY 1 ASC
)
SELECT `MONTH`, total_off
,SUM(total_off) OVER(order by `MONTH`) AS rolling_total
FROM Rolling_Total;
```
<img width="213" height="189" alt="EDA1" src="https://github.com/user-attachments/assets/ed2b2bdb-1af7-467b-93e9-8bf3c61514b4" />

**Top 5 companies per year with chained CTEs**

```sql

WITH Company_Year(company, years, total_laid_off) AS
(
select company , year(`date`), sum(total_laid_off)
from layoffs_staging2
group by company, year(`date`)
), Company_Year_Rank AS
(SELECT *, 
dense_rank() OVER (PARTITION BY years order by total_laid_off desc) AS Ranking
FROM Company_Year
WHERE years is not null
)
SELECT *
FROM Company_Year_Rank
WHERE Ranking <= 5;
```
<img width="285" height="186" alt="EDA2" src="https://github.com/user-attachments/assets/2973ac56-654e-4232-9480-b239f678b210" />

## Key Findings

- **Peak year:** 2023 had the most layoffs, with 127277 people.
- **Most affected company:** Amazon laid off 18150 in total.
- **Most affected industry:** Consumer, with 47082 layoffs.
- **Most affected country:** United States, with 258159 layoffs.
- **Complete shutdowns:** 116 companies laid off 100% of staff, the largest being Katerra.
- **Trend:** layoffs accumulated to 385879 by March 2023, with the sharpest single month being January 2023 (84714).

<img width="179" height="190" alt="eda3" src="https://github.com/user-attachments/assets/be3569d2-75b3-47be-93ef-45abdd49d13e" />

<img width="195" height="171" alt="image" src="https://github.com/user-attachments/assets/72c1bb80-8339-4568-9fb6-dd8fd6083312" />


## Author

**Nikita Maria Johnson**
[LinkedIn](https://www.linkedin.com/in/nikita2000) | [GitHub](https://github.com/Nikitaj-11)
