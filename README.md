# RevStream | Predictive Revenue Intelligence Suite

**RevStream** is a Revenue Operations (RevOps) environment designed to transform fragmented financial ledgers into a cloud-native "Single Source of Truth." By migrating legacy data to a **Google BigQuery Star-Schema**, the system provides a high-concurrency pipeline for revenue forecasting and commission automation.

## 📊 Project Visuals
![Executive Dashboard](assets/your_dashboard_screenshot.png)
*Figure 1: Executive Looker Studio Dashboard visualizing $1.2M+ in net revenue and regional variance.*

## 🚀 Key Technical Accomplishments
* **Data Warehouse Architecture:** Architected a full-stack migration from Excel to BigQuery, implementing a relational **Star-Schema** to optimize query performance.
* **Advanced SQL Engineering:** Leveraged **Window Functions (`SUM OVER PARTITION`)** to calculate YTD pacing and daily run-rates across 5 regional territories.
* **Automated Fiscal Logic:** Engineered complex `CASE WHEN` structures to automate commission reconciliation against agent contractual caps, reducing manual audit time by 85%.

## 🛠️ The Tech Stack
* **Storage/Engine:** Google BigQuery (SQL)
* **BI Layer:** Looker Studio (Dark Mode Executive Dashboard)
* **Logic:** Advanced SQL (CTEs, Window Functions, Relational Joins)
* **Design Tool:** Google Gemini (Architectural validation and prototyping)

## 🧬 SQL Showcase: YTD Revenue Pacing
Below is a sample of the logic used to calculate running totals and regional contribution percentages:

```sql
SELECT 
    transaction_date,
    region,
    revenue,
    -- Calculating the YTD Running Total per Region
    SUM(revenue) OVER (PARTITION BY region ORDER BY transaction_date) as ytd_revenue,
    -- Calculating % Contribution to the total Region revenue
    ROUND((revenue / SUM(revenue) OVER (PARTITION BY region)) * 100, 2) as pct_contribution
FROM `your_project.revenue_fact`
WHERE status = 'Finalized';
