SELECT 
    Agent_Name,
    Closing_Date,
    Location,
    Calculated_Company_Dollar,
    -- Now that this is "calculated" in the subquery, the CASE statement can see it!
    CASE 
        WHEN Running_Total_Company_Dollar > Agent_Cap THEN Agent_Cap 
        ELSE Running_Total_Company_Dollar 
    END AS Capped_Running_Total,
    Agent_Cap,
    CASE 
        WHEN (Running_Total_Company_Dollar - Calculated_Company_Dollar) >= Agent_Cap THEN 0
        WHEN Running_Total_Company_Dollar > Agent_Cap THEN Agent_Cap - (Running_Total_Company_Dollar - Calculated_Company_Dollar)
        ELSE Calculated_Company_Dollar
    END AS Actual_Company_Revenue
FROM (
    -- We moved the Window Function inside here
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
