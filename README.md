# Smart Desk — SQL Sales & Profitability Analysis

SQL analysis of a B2B company's (**Smart Desk**) sales data on **Snowflake**, turning raw transactional tables into account, regional, and industry-level insights — and a set of strategic recommendations for each.

## Business questions
- How does a key account perform across product categories?
- Which regions and countries are most profitable — and where is high volume hiding thin margins?
- Which industries generate the most profit, and which combine high sales with low margins?
- How did actual sales and profit track against quarterly targets (2019–2021)?

## Dataset & stack
- **Stack:** SQL (Snowflake)
- **Tables:** `Accounts` (188 rows), `Forecasts` (135), `Sales` (~14.5k), `Targets` (12)

## Approach
1. **Data cleaning & QA** — verified data types and row counts, checked for duplicates with `SELECT DISTINCT`, and found 184 null values in the `SUPPORT` column. Built a clean `CLEANED_SALES` view using `COALESCE(SUPPORT, 0)` so downstream math wouldn't break.
2. **Account analysis** — category-level revenue and profit for a single account using filtered aggregations.
3. **Regional comparison** — average revenue, units, and profit by country across APAC and EMEA using `INNER JOIN` between `Accounts` and `CLEANED_SALES`.
4. **Industry profit (committed pipeline)** — `SUM(PROFIT)` by industry, scoped with a subquery on `Forecasts` (`PREDICTION_CATEGORY = 'Commit'`, `HAVING SUM(FORECAST) > 500000`) and a `CASE` tier (High / Normal).
5. **Actual vs Target** — quarterly variance (sales and profit) 2019–2021 by joining `CLEANED_SALES` to `Targets`.
6. **Margin analysis** — profit margin % per industry with `ROUND` and `NULLIF`, classified into High / Medium / Low tiers, then compared year over year (2019 → 2021).

**Techniques:** `COALESCE`, `NULLIF`, views, `INNER`/`LEFT JOIN`, correlated subqueries, `GROUP BY` / `HAVING`, `CASE`, aggregate functions, `ROUND`, `TO_VARCHAR` formatting.

## Key findings
- **Regions:** APAC leads on profitability — Australia and Singapore post the highest average profit. Korea and Germany move large volumes but at much thinner margins, pointing to logistics cost or over-aggressive pricing.
- **Industries:** Consulting is the most strategically valuable sector — the **only high-volume industry that also holds a high margin (~36.6%)**. Healthcare and Hospitality run smaller volumes but the highest margins (~38%), while Retail and Entertainment drive the biggest absolute profit on medium (~34%) margins.
- **Targets:** Smart Desk missed targets through early 2019, hit an inflection point in Q4 2019, and posted record target-beating growth across 2021.
- **Risk:** Automotive sales collapsed entirely in 2020 before a partial 2021 recovery — a portfolio-concentration risk flagged for diversification.

## Files
- `smartdesk_report.pdf` — full write-up: queries, result tables, charts, and the strategic recommendation behind each analysis.
- `*.sql` — the SQL queries.
