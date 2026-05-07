RevStream | Predictive Revenue Intelligence & Data Warehouse
RevStream is a high-performance Revenue Operations (RevOps) environment designed to migrate legacy financial ledgers into a cloud-native "Single Source of Truth." By architecting a Star-Schema in Google BigQuery, the system automates complex commission structures, protects corporate margins, and provides executive-level forecasting.

📊 Executive Intelligence
Figure 1: Executive Looker Studio Dashboard visualizing $1.2M+ in net revenue and regional variance analysis.

🚀 Key Technical Pillars
Cloud Data Architecture: Engineered a full-stack migration from flat-file Excel storage to a relational BigQuery Star-Schema, optimizing data retrieval for multi-million row scalability.

Advanced Fiscal Engineering: Developed a tiered commission capping engine using SQL Window Functions and nested subqueries to automate the transition from "Split" to "100% Payout" structures.

Revenue Variance Forensics: Built interactive Looker Studio dashboards to visualize the delta between "Potential Revenue" and "Actual Net Revenue," identifying 15% growth opportunities in high-production territories.

AI-Validated Prototyping: Utilized Google Gemini for architectural validation, schema normalization, and rapid SQL prototyping.

🧬 SQL Logic: The "Split-to-Cap" Engine
The core of the system is a sophisticated script that calculates revenue on a deal-by-deal basis, identifies when an agent hits their contractual ceiling, and "zeros out" corporate collection once the cap is satisfied.

## 🏗️ Data Architecture & Relational Schema
To ensure data integrity and query efficiency, I implemented a relational schema that joins high-frequency transaction data with agent-specific contractual metadata.

![Relational Schema Map](assets/joining_of_tables.png)
*Figure 2: Logical Join between 'Transactions' (Fact Table) and 'Agents' (Dimension Table) via Agent_Name.*

**Key Architectural Decisions:**
* **Normalization:** Separated agent-specific attributes (Splits, Caps) into a dedicated dimension table to reduce redundancy.
* **Typing:** Enforced strict data typing (Dates, Integers, Floats) within BigQuery to ensure mathematical accuracy in revenue calculations.
* **Scalability:** The star-schema design allows for seamless integration of additional dimension tables (e.g., Marketing Spend, Lead Sources) without disrupting the core Fact table.

SQL
SELECT 
    Agent_Name,
    Closing_Date,
    Location,
    Calculated_Company_Dollar,
    -- Case logic to detect the 'Cliff' where Agent reaches their Cap
    CASE 
        WHEN Running_Total_Company_Dollar > Agent_Cap THEN Agent_Cap 
        ELSE Running_Total_Company_Dollar 
    END AS Capped_Running_Total,
    Agent_Cap,
    -- Automated Revenue Reconciliation logic
    CASE 
        WHEN (Running_Total_Company_Dollar - Calculated_Company_Dollar) >= Agent_Cap THEN 0
        WHEN Running_Total_Company_Dollar > Agent_Cap THEN Agent_Cap - (Running_Total_Company_Dollar - Calculated_Company_Dollar)
        ELSE Calculated_Company_Dollar
    END AS Actual_Company_Revenue
FROM (
    -- Subquery utilizing Window Functions for chronological Running Totals
    SELECT 
        t.Closing_Date,
        t.Agent_Name,
        t.Location,
        (t.GCI * (1 - a.Agent_Split)) as Calculated_Company_Dollar,
        a.Agent_Cap,
        SUM((t.GCI * (1 - a.Agent_Split))) OVER(PARTITION BY t.Agent_Name ORDER BY t.Closing_Date) AS Running_Total_Company_Dollar
    FROM `RealEstate_2025.Transactions` t
    JOIN `RealEstate_2025.Agents` a ON a.Agent_Name = t.Agent_Name
)
ORDER BY Agent_Name, Closing_Date;
🛠️ The Tech Stack
Engine: Google BigQuery (SQL)

BI Layer: Looker Studio

Schema: Relational Star-Schema

Key Functions: Window Functions (SUM OVER PARTITION), CTEs, Join Logic, Case Statements.
