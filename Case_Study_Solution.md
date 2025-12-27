 ## Q1. Calculated OP by Month
```SQL
SELECT month, Round((SUM(ActRevenue) - (SUM(ActLabCost) + SUM(ActMatCost) + SUM(ActEnerCost) + SUM(ActFixCost)))) AS ActProfit
FROM OFRD
GROUP BY month
ORDER BY month;
```
<img width="206" height="356" alt="image" src="https://github.com/user-attachments/assets/e1128d45-1a6c-4830-9d75-f6435884a93e" />
<img width="383" height="217" alt="image" src="https://github.com/user-attachments/assets/5337013e-a893-485e-8980-0a0508009f52" />

### Analysis: 
-Operating profit increases steadily from Month 1 to Month 3, indicating an improving revenue–cost balance early in the year. The highest profit occurs in Month 8 (₹532,551), coinciding with higher overall business activity, while costs also increase proportionally. Profit remains relatively stable through the mid-year period (Months 5–11), reflecting consistent operational performance despite rising production and cost levels. A soft decline appears in Month 12, suggesting possible seasonal effects or elevated year-end costs. Overall, the trend shows healthy profitability with mid-year peaks and mild fluctuations.


##	Q2. Calculated OP by Quarter
```SQL
Select Quarter, Round((SUM(ActRevenue)) - (Sum(ActLabCost)) -(Sum(ActMatCost)) - (Sum(ActEnerCost)) - (Sum(ActFixCost)) ) AS Operating_Profit
FROM OFRD
GROUP BY Quarter
ORDER BY Quarter;
```
<img width="276" height="148" alt="image" src="https://github.com/user-attachments/assets/6e54fbd4-ad83-4927-8e57-9e170917bbc3" />

### Analysis:
- Growth can be observed in the first 3 quarters with operating profit growing rapidly from Q1 to Q3 until Q4 where the profit fell. 
The decline in Q4 operating profit reflects rising total costs relative to revenue growth, as confirmed by the declining operating margin in Q4. 
Overall growth is observed across the year, with Q4 showing a moderation in operating profit driven by rising costs rather than a structural decline.

##	Q3. Plot and analyse the difference between Planned OP & Actual OP
<img width="603" height="345" alt="image" src="https://github.com/user-attachments/assets/3d55fcc4-5baf-4834-8849-5288a269d05a" />

 
### Analysis:
- A clear difference can be observed between Planned Operating Profit and Actual Operating Profit. The planned figures show consistent growth throughout the 12 months, whereas the actual performance is highly inconsistent, with noticeable fluctuations and a downward trend toward the end of the year.
This highlights the need for stronger predictive models to accurately forecast future performance and guide planning. Additionally, robust variance analysis is essential to understand what caused the deviations from the plan so that corrective actions can be implemented before the next production cycle. The divergence widens in the final quarter, indicating that planning inaccuracies increase at higher production scale

## Q4. Calculate the monthly Production Efficiency (%) as:
Actual Volume / Planned Volume * 100, and identify the top 3 most efficient months.
```SQL
SELECT month, (Sum(ActVol)/SUM(PlanVol))*100 AS Production_Efficiency
FROM OFRD
GROUP BY month
ORDER BY Production_Efficiency DESC
LIMIT 3;
```
<img width="301" height="116" alt="image" src="https://github.com/user-attachments/assets/05fe38a4-a8b6-4318-921b-fd530e3bc65d" />

### Analysis:
- Production efficiency remains consistently high (~98%), indicating strong operational execution. The small gap between actual and planned volume suggests that deviations are more likely due to planning assumptions or demand variation rather than operational inefficiency.

##	Q5. Find the total actual revenue generated per quarter and calculate the quarter-over-quarter growth rate (%).
```SQL
WITH q AS (
    SELECT 
        Quarter,
        SUM(ActRevenue) AS QuarterRevenue
    FROM OFRD
    GROUP BY Quarter
)
SELECT
    Quarter,
    QuarterRevenue,
    LAG(QuarterRevenue) OVER (ORDER BY Quarter) AS PrevQuarterRevenue,
    ROUND(
        (QuarterRevenue - LAG(QuarterRevenue) OVER (ORDER BY Quarter)) 
        / LAG(QuarterRevenue) OVER (ORDER BY Quarter) * 100,
    2) AS QoQ_Growth_Percent
FROM q
ORDER BY Quarter;
```
<img width="657" height="142" alt="image" src="https://github.com/user-attachments/assets/ec53bc8e-67ab-469b-bda9-095409bfd7c2" />
 
### Analysis:
- The table shows steady quarter-over-quarter revenue growth, indicating consistent business expansion across the year. This reflects effective scaling of operations and sustained demand, enabling the company to grow revenue sequentially.
- Although revenue continues to increase each quarter, the declining growth rate suggests diminishing marginal gains as scale increases. This trend signals a potential slowdown in incremental revenue expansion and warrants further diagnostic analysis to understand capacity constraints, cost pressures, or demand saturation before the next planning cycle.  

##	Q6. Determine which day of the week consistently shows the highest production volume (ActVol). Also check if the pattern changes across quarters.
```SQL
SELECT WeekDay, AVG(ActVol) AS Tot_Volume
FROM OFRD
GROUP BY WeekDay
ORDER BY Tot_Volume DESC;
```
<img width="251" height="223" alt="image" src="https://github.com/user-attachments/assets/3c11efc1-8b58-40c5-a87b-b33146bfee13" />

### Analysis: 
- Weekday 6 records the highest average production volume across the year, with an average output of approximately 2,218 units per day. This indicates that production levels tend to be higher on this weekday relative to others. While the result highlights a clear pattern in output distribution, further analysis is required to understand the operational, scheduling, or demand-related factors driving this difference before attempting replication across other days.

##	Q7. Determine which quarter of the year consistently shows the highest production volume
```SQL
SELECT 
    Quarter,
    WeekDay,
    AVG(ActVol) AS Avg_Volume
FROM OFRD
GROUP BY Quarter, WeekDay
ORDER BY Quarter, Avg_Volume DESC
```
<img width="342" height="216" alt="image" src="https://github.com/user-attachments/assets/38d96563-8833-4051-bd98-7b1fe2abf883" />
<img width="342" height="220" alt="image" src="https://github.com/user-attachments/assets/55866ba0-4937-4348-a57c-3c07fd3b0998" />
<img width="344" height="217" alt="image" src="https://github.com/user-attachments/assets/e6fd5fae-b278-4f2e-a056-00450e3fb702" />
<img width="345" height="222" alt="image" src="https://github.com/user-attachments/assets/ce9c47bf-a891-4953-b2e5-7f9720f88e3d" />

    
### Analysis: 
- The highest production weekday varies by quarter, indicating that no single day consistently dominates across the year. Production scheduling effectiveness changes with operational conditions

## Q8.	Compute variance between Planned Cost and Actual Cost for each month, and identify the month with the highest cost overrun.
```SQL
Select Month, (Sum(PlanLabCost) + SUM(PlanMatCost) + SUM(PlanEnerCost) + SUM(PlanFixCost)) AS Planned_Cost, 
(SUM(ActLabCost) + SUM(ActMatCost) + SUM(ActEnerCost) + SUM(ActFixCost)) AS Actual_Cost, ROUND(SUM(ActLabCost + ActMatCost + ActEnerCost + ActFixCost) - SUM(PlanLabCost + PlanMatCost + PlanEnerCost + PlanFixCost)) AS Cost_Variance
FROM OFRD
GROUP BY Month;
```
<img width="567" height="351" alt="image" src="https://github.com/user-attachments/assets/82de0a43-5fad-49a7-8c38-791718f25502" />

### Analysis: 
- Except for the 1st Month, positive cost variance is observed in the remaining 11 months. A positive variance here signify that the Actual Cost is more than the Planned Cost which points towards overspending than the original planned or designed budget. This can also signify cost overruns relative to budgeted expectations, with the total cost predictive way to less, amount at which production might not be feasible.
- The company need to go thoroughly analysis the estimating and production process to pinpoint the exact cause of this issue, whether it is an estimation problem or spending problem. After pinpointing the exact reason, the issue needs to be handled as quickly as possible before it causes any further damages.
- Month 12 is the month which has the highest Cost overrun with a huge gap of 21,877 between Actual and Planned Cost.  

## Q9.	Group by Shifts and calculate the average revenue per shift configuration. Which shift pattern (1 shift vs 2 shifts vs 3 shifts) yields the highest revenue per day?
```SQL
SELECT Shifts, Round(Avg(ActRevenue)) AS AVG_Revenue
FROM OFRD
GROUP BY Shifts
ORDER BY Shifts;
```
<img width="245" height="84" alt="image" src="https://github.com/user-attachments/assets/7c320660-da8f-412e-9282-aed875e46215" />
 
### Analysis:
- Shift 3 on average yields higher revenue than Shift 2 because more production lines are running in shift 3 than in shift 2 thus producing more goods  

## Q10. Identify the top 10 days with the highest profit
```SQL
Select Date, Round(ActRevenue - ActLabCost - ActMatCost - ActEnerCost - ActFixCost) AS Act_Profit
FROM OFRD
ORDER BY Act_Profit DESC
LIMIT 10;
 ```
<img width="251" height="298" alt="image" src="https://github.com/user-attachments/assets/b99cadad-4650-4271-84f2-c468ffedce5f" />


### Analysis:
- 25th October records the highest operating profit of ₹23,529. An observable pattern is that 9 of the top 10 most profitable days occur in the second half of the year, indicating a period of stronger financial performance during this phase. This clustering suggests that profitability improves later in the year, potentially due to higher production scale, revenue levels, or cost dynamics. However, further analysis is required to identify the underlying drivers before drawing operational conclusions or applying learnings to the first half of the year.

## Q11.	Calculate weekly volatility of revenue using:StdDev(ActRevenue) window function per week number.
```SQL
SELECT  WeekNum, ROUND(STDDEV(ActRevenue))
FROM OFRD
GROUP BY  WeekNum
ORDER BY WeekNum;
```
<img width="292" height="453" alt="image" src="https://github.com/user-attachments/assets/b3c3227b-742e-4b21-b3aa-26ff5e34aece" />
<img width="295" height="460" alt="image" src="https://github.com/user-attachments/assets/57ac8622-c333-4bed-bc60-bb64907f9838" />
<img width="292" height="460" alt="image" src="https://github.com/user-attachments/assets/32647d61-debd-497c-970b-a970678b6299" />
<img width="292" height="167" alt="image" src="https://github.com/user-attachments/assets/b136f48c-19a5-4643-a1fc-ceb6576683cc" />

### Analysis:
- The weekly volatility values show that revenue fluctuates significantly across the year, with standard deviations mostly ranging between 2,000 and 5,000 units. Early weeks show relatively moderate volatility, but mid-year (Weeks 35–46) exhibits pronounced spikes, indicating unstable revenue patterns. Weeks such as 40, 46, and 35 stand out as the most volatile, suggesting operational or demand-driven inconsistencies during those periods. Lower-volatility weeks (e.g., Weeks 1, 12, 20, 41) reflect more stable revenue performance. Overall, the analysis highlights clear periods of instability that may warrant deeper operational or market investigation.

##	Q12. Find the trend of energy cost per unit produced (ActEnerCost / ActVol) across months and detect the month with the highest upward deviation.
```SQL
SELECT Month, Sum(ActEnerCost) AS Monthly_Cost, Sum(ActVol) AS Monthly_Production, Sum(ActEnerCost)/Sum(ActVol) AS Ener_required_per_Unit,  Sum(ActEnerCost)/Sum(ActVol) - LAG(Sum(ActEnerCost)/Sum(ActVol)) OVER (ORDER BY Month) AS Deviation
FROM OFRD
GROUP BY Month;
```
<img width="900" height="353" alt="image" src="https://github.com/user-attachments/assets/c55628ca-5b95-4d71-addc-53afe899ec72" />

 
### Analysis:
- The energy cost per unit shows a steady upward trend across the months, increasing from roughly 1.54 in January to 1.76 by December, indicating rising energy intensity or higher energy input costs over time. Month-to-month deviations highlight where this increase accelerates or slows. The highest upward deviation occurs in Month 5, reflecting a sharp rise in energy cost per unit compared to April. Minor fluctuations appear throughout the year, but the overall pattern signals consistent cost pressure. This trend may require operational efficiency reviews or energy-saving interventions.


## Q13.	Determine the operating margin (%) per quarter.
```SQL
SELECT Quarter, ROUND(((SUM(ActRevenue) - SUM(ActLabCost) - SUM(ActMatCost) - SUM(ActEnerCost) - SUM(ActFixCost))/SUM(ActRevenue)) * 100) AS Operating_Margin
FROM OFRD
GROUP BY Quarter;
```
<img width="282" height="135" alt="image" src="https://github.com/user-attachments/assets/bbec69fc-14e2-4a89-8098-dfefca73d37e" />
 
### Analysis:
- The operating margin shows a clear downward trend across the four quarters, falling from 34% in Q1 to 23% in Q4. This indicates that costs are rising faster than revenue as the year progresses, likely driven by increases in labor, material, or energy expenses. The steepest decline occurs between Q1 and Q2, signaling early cost pressures that continue through the year. By Q4, margins are at their lowest, highlighting potential inefficiencies or weakening pricing power. Overall, the business becomes less cost-efficient over time, suggesting a need for operational or cost-control interventions.


## Q14.	Calculate the Material Cost % of Revenue monthly and rank in descending order.
```SQL
SELECT Month, (SUM(ActMatCost)/SUM(ActRevenue)) * 100 AS Material_Cost_Perc
FROM OFRD
GROUP BY Month
ORDER BY Material_Cost_Perc DESC;
```
<img width="425" height="528" alt="image" src="https://github.com/user-attachments/assets/5ee75ec4-06ff-46a3-97d2-ef48f1f50749" />
 

### Analysis:
- Material cost as a percentage of revenue shows a steady upward trend throughout the year, rising from about 36.6% in Month 1 to over 41.5% by Month 12. This indicates that material expenses are becoming a larger share of total revenue, potentially due to rising input prices or declining production efficiency. The highest-cost months—Months 10 to 12—stand out as periods of increased cost pressure. Lower percentages in the early months reflect comparatively better cost control. Overall, the ranking highlights a clear deterioration in material cost efficiency over time, signaling an area needing strategic attention.

## Q15.	Find whether production complexity changed: Compare the ratio of Small: Medium: Large volumes (ActVolS, ActVolM, ActVolL) each quarter.Which quarter shows the biggest shift in product mix?
```SQL
SELECT Quarter, SUM(ActVolS) AS Production_S, SUM(ActVolM) AS Production_M, SUM(ActVolL) AS Production_L ,SUM(ActVolS)+SUM(ActVolM)+SUM(ActVolL) AS Total_Production, SUM(ActVolS)/SUM(ActVol) AS Small_Mix, SUM(ActVolM)/SUM(ActVol) AS Medium_Mix, Sum(ActVolL)/SUM(ActVol) AS Large_Mix
FROM OFRD
GROUP BY Quarter
ORDER BY Quarter;
```
<img width="976" height="142" alt="image" src="https://github.com/user-attachments/assets/37c05c8a-4ebd-4450-8c8e-3f605bd57f4e" />

### Analysis:
- The product mix ratios for Small, Medium, and Large units remain remarkably stable across all four quarters, with variations only in the third decimal place. Small units consistently contribute around 50%, Medium units about 30%, and Large units roughly 19–20% of total output. The biggest shift appears between Quarter 1 and Quarter 2, where the Small mix slightly decreases and the Medium mix slightly increases—though the change is minimal and not operationally significant. Overall, production complexity remains highly consistent throughout the year, suggesting a steady product strategy without major mix changes.

##	Q16. Compare operational capacity of Shift 1 vs Shift 2 vs Shift 3: Which shift accounts for a higher share of total running production lines?
```SQL
SELECT Month, ROUND((SUM(LinesInShift1)/(SUM(LinesInShift1) + SUM(LinesInShift2) + Sum(LinesInShift3))) * 100) AS Operational_Capacity_1, ROUND((SUM(LinesInShift2)/(SUM(LinesInShift1) + SUM(LinesInShift2) + Sum(LinesInShift3))) * 100) AS Operational_Capacity_2, ROUND((SUM(LinesInShift3)/(SUM(LinesInShift1) + SUM(LinesInShift2) + Sum(LinesInShift3))) * 100) AS Operational_Capacity_3
FROM OFRD
GROUP BY Month
ORDER BY Month;
```
<img width="735" height="325" alt="image" src="https://github.com/user-attachments/assets/7732b41f-f4ff-48e3-9b43-e5f9d69e1091" />
 
### Analysis:
- Shift 1 consistently carries the highest operational load, starting at 57% in earlier months and gradually declining to 33% by year-end. Shift 2 mirrors Shift 1’s trend but remains slightly lower throughout, indicating it acts as secondary support rather than a primary driver. Shift 3 initially contributes 0%, then gradually increases to match Shifts 1 and 2 at 33% from Month 10 onward, reflecting expanded utilization of night operations. The biggest structural change occurs between Months 7–10, where Shift 3 ramps up significantly. Overall, production shifts from a two-shift dominant model to a balanced three-shift model by the last quarter.

## Q17.	Find the correlation (SQL approximation using window functions) between: Number of lines and Actual volume Across all week.
```SQL
WITH WeeklyData AS (
    SELECT 
        WeekNum,
        SUM(LinesInShift1 + LinesInShift2 + LinesInShift3) AS Total_Lines,
        SUM(ActVol) AS Total_ActVol
    FROM OFRD
    GROUP BY WeekNum
),

Stats AS (
    SELECT
        AVG(Total_Lines) AS avg_lines,
        AVG(Total_ActVol) AS avg_vol
    FROM WeeklyData
),

Calc AS (
    SELECT
        w.WeekNum,
        w.Total_Lines,
        w.Total_ActVol,
        (w.Total_Lines - s.avg_lines) AS diff_lines,
        (w.Total_ActVol - s.avg_vol) AS diff_vol
    FROM WeeklyData w
    CROSS JOIN Stats s
),

FinalCalc AS (
    SELECT
        SUM(diff_lines * diff_vol) AS covariance_num,
        SQRT(SUM(diff_lines * diff_lines)) AS std_lines,
        SQRT(SUM(diff_vol * diff_vol)) AS std_vol
    FROM Calc
)

SELECT 
    (covariance_num / (std_lines * std_vol)) AS Correlation
FROM FinalCalc;
```
<img width="232" height="57" alt="image" src="https://github.com/user-attachments/assets/cb414aac-d7a9-4895-9371-705dba534938" />
 
### Analysis: 
- The computed correlation coefficient of 0.9988 indicates a very strong positive linear relationship between the number of production lines running and the actual volume produced. This suggests that line utilization is a primary driver of weekly output under the current operating structure, with production scaling closely alongside active capacity. The consistency of this relationship reflects stable operational behaviour and effective utilization of installed capacity. In practical terms, increases in the number of active lines are strongly associated with higher production levels, supporting planning assumptions around line–throughput dependency.

## Q18.	Using a window function, create a 7-day rolling average of:Actual Volume, Revenue. Identify unexpected dips (days where actual is 10% below rolling average).
```SQL
WITH RollingCalc AS (
    SELECT 
        Date,
        ActVol,
        ActRevenue,

        AVG(ActVol) OVER(
            ORDER BY Date
            ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
        ) AS RollingAvgVol,

        AVG(ActRevenue) OVER(
            ORDER BY Date
            ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
        ) AS RollingAvgRev
    FROM OFRD
)

SELECT 
    Date,
    ActVol,
    ActRevenue,
    RollingAvgVol,
    RollingAvgRev,

    CASE WHEN ActVol < 0.9 * RollingAvgVol THEN 'Dip' ELSE 'Normal' END AS VolStatus,
    CASE WHEN ActRevenue < 0.9 * RollingAvgRev THEN 'Dip' ELSE 'Normal' END AS RevStatus

FROM RollingCalc
WHERE 
    (ActVol < 0.9 * RollingAvgVol)
    OR
    (ActRevenue < 0.9 * RollingAvgRev)
ORDER BY Date;
```
<img width="281" height="185" alt="image" src="https://github.com/user-attachments/assets/cdd9d18e-322f-4ab9-92e8-3f7a90bbb153" />
<img width="279" height="186" alt="image" src="https://github.com/user-attachments/assets/bbbdd718-7f80-4a73-bbc3-31222c0416f6" />
<img width="278" height="186" alt="image" src="https://github.com/user-attachments/assets/dbf478f2-9f57-4e4c-9430-ebcab0e25ffe" />
<img width="282" height="187" alt="image" src="https://github.com/user-attachments/assets/b5c997cb-932c-4c66-9a40-a9aa09e254de" />
<img width="282" height="189" alt="image" src="https://github.com/user-attachments/assets/ef50d489-226a-4357-ae65-913be1b95e52" />
<img width="277" height="186" alt="image" src="https://github.com/user-attachments/assets/9c593081-090a-40de-8c62-edc74d0f75d6" />
<img width="807" height="167" alt="image" src="https://github.com/user-attachments/assets/ae1157c0-4027-4799-97d2-ed85fd3c7a97" />

                         
### Analysis:
- The 7-day rolling window highlights several unexpected dips where both volume and revenue fall more than 10% below their recent trends. These dips occur at multiple points during the year, indicating short-term volatility rather than isolated one-off anomalies. In most flagged cases, both volume and revenue decline simultaneously, suggesting periods of operational or market-driven disruption. A smaller number of instances show divergence between volume and revenue, which may reflect pricing or product-mix effects. Overall, the results point to recurring short-term fluctuations that warrant deeper investigation to determine their underlying causes.
---
## Q19.	Create a Production Efficiency Dashboard showing:Plan vs Actual Volume, Efficiency (%), Trend over months

<img width="626" height="285" alt="image" src="https://github.com/user-attachments/assets/b60f12cc-e4a5-4c98-bb71-aecfad8a09b9" />

 
KPI Summary
- Best Efficiency Month: Month 1 — 98.34%
- Worst Efficiency Month: Month 8 — 97.58%
- Best Plan vs Actual Alignment: Month 1
- Worst Plan vs Actual Alignment: Month 6
- Overall Trend: Production volumes increase consistently month-on-month, Actuals closely follow Plan, and efficiency remains very stable around ~98% throughout the year.

### Analysis: 
- The dashboard shows that Actual production closely follows the Plan throughout the year, demonstrating strong operational discipline and stable capacity utilization. Monthly efficiency remains extremely consistent, fluctuating only between 97.5% and 98.4%, with January achieving the highest efficiency and August showing the lowest. Production volumes rise steadily month after month, indicating planned growth is being successfully executed. The largest negative deviation between Plan and Actual occurs in Month 6, suggesting a temporary shortfall, while Month 1 shows the closest alignment. Overall, the business displays high stability, strong planning accuracy, and minimal efficiency drift across the year.



## Q20.	Build a Cost Structure Breakdown Dashboard: Compare Fixed, Labour, Material, Energy costs. Show deviation between planned and actual

 <img width="1223" height="560" alt="image" src="https://github.com/user-attachments/assets/6dc9922d-1f94-46c3-bd3d-84601d579a9b" />



KPI Summary
- Largest Cost Component (Actual): Material Cost — ₹8.02M
- Second Largest Cost Component: Labour Cost — ₹5.15M
- Most Stable Cost Component: Fixed Cost — ₹0.21M
- Most Volatile Cost Component: Energy Cost — ₹1.33M
- Largest Plan vs Actual Deviation: Months 10–11 (Actual Cost significantly exceeds Plan)
- Overall Trend: Actual total costs consistently run above planned values, with cost pressure increasing in the second half of the year. Material dominates the cost structure, while Labour and Energy drive most of the monthly deviations.

### Analysis: 
- The dashboard shows that Material Cost is the dominant expense, accounting for over half of the total cost, with Labour and Energy forming the next major components. Fixed Costs remain negligible and stable throughout the period. Actual Total Cost consistently exceeds Planned Cost, with the largest deviation occurring in Months 10–11, where operational or market-driven pressures appear to have escalated expenses. Material remains structurally heavy, but Labour and Energy contribute most to the month-to-month variability that pushes Actual Cost above Plan. Overall, the cost structure reveals rising cost pressure in the second half of the year, requiring closer control of variable cost drivers.

---
##	Q21. Build a Multi-Metric Performance Dashboard:Show revenue, cost, profit, and production volume over time (monthly).

<img width="1624" height="745" alt="image" src="https://github.com/user-attachments/assets/3a6e95fb-693b-4d30-96f2-3f83fa6718f8" />
	 
KPI Summary
- Highest Production Month: Month 12 — 334,908 units
- Lowest Production Month: Month 1 — 175,604 units
- Highest Revenue Month: Month 12 — ₹83.36M
- Lowest Revenue Month: Month 1 — ₹44.03M
- Highest Operating Profit Month: Month 9 — ₹2.13M
- Lowest Operating Profit Month: Month 1 — ₹1.47M
- Overall Trend: Production, revenue, and cost all rise steadily over the months, while operating profit remains stable with moderate fluctuations. Peak profitability aligns with mid-year demand and efficiency improvements, followed by slight softening toward year-end.

### Analysis: 
- The dashboard clearly illustrates consistent growth in production and revenue across the year, with both metrics increasing sharply from Month 4 onward. Total cost also rises proportionally, driven by higher output levels, but remains well-aligned with revenue growth. Operating profit shows moderate fluctuation, peaking around Month 9 before tapering slightly toward year-end, indicating manageable cost pressures. Production volume nearly doubles from Month 1 to Month 12, reflecting strong operational scaling. Revenue growth outpaces cost in several months, supporting healthy profitability despite rising expenses. Overall, the business demonstrates steady expansion, stable profit margins, and effective scaling of production capacity throughout the year.

