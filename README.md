# Layoffs Exploratory Data Analysis in SQL (MySQL)

A practice project. After cleaning a public layoffs dataset (see the data cleaning repo), I followed Alex the Analyst's SQL tutorial to explore it with queries. This repo has my script and a short write-up.

## Data

- The cleaned table `layoffs_staging2`: company, location, industry, total_laid_off, percentage_laid_off, date, stage, country, funds_raised_millions.
- Covers 2020 to 2023.
- The script needs that cleaned table to exist first. [Link to your data cleaning repo.]

## Questions the script answers

- What are the largest single layoffs, and which companies laid off 100% of their staff?
- Which companies, industries, countries and funding stages had the most layoffs in total?
- How many layoffs were there each year?
- What is each company's average percentage laid off?
- How did layoffs change month by month, and what is the running total?
- Which five companies had the most layoffs in each year?

## Queries worth a look

- **Monthly totals.** `SUBSTRING(date, 1, 7)` pulls the year and month so layoffs can be grouped by month.
- **Rolling total.** A CTE of monthly totals, then `SUM(total_off) OVER (ORDER BY month)` to add each month to the ones before it.
- **Top five per year.** Two CTEs: the first totals layoffs by company and year, the second ranks companies within each year with `DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC)`. The final query keeps ranks 1 to 5.

## What I found

[Add three or four findings from your own query results, with numbers. For example: the year with the most layoffs, the industries and countries at the top, and which companies ranked first each year.]

## Limits

- Many rows have no `total_laid_off` or percentage figure, so totals are lower than the real numbers.
- `percentage_laid_off` is a share of each company's own staff, so it can't be compared with totals across companies of different sizes.
- The data shows reported layoffs only.

## SQL used

`GROUP BY` and aggregates, `MAX`, `AVG`, `SUM`, `SUBSTRING`, `YEAR`, CTEs, window functions (`SUM() OVER`, `DENSE_RANK()`).

## How to run

1. Use MySQL 8 or later.
2. Build `layoffs_staging2` first by running the cleaning script.
3. Run `exploratory_data_analysis.sql` one query at a time and look at each result.

## Files

- `exploratory_data_analysis.sql`: the exploration script
