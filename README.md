
#  Energy Consumption and Pricing Trends Analysis (2020–2023)

This project provides a data driven exploration of **U.S. state-level energy consumption and pricing patterns** from 2020 to 2023. By leveraging SQL for data wrangling and **Power BI** for visualization, the analysis identifies key trends, energy source behaviors, pricing shifts, and regional insights.

---

## Background

As global energy demands rise and prices fluctuate, understanding how energy is consumed and priced at a regional level becomes increasingly important. This project focuses on **state-level energy usage** in the U.S., along with the **price dynamics of different energy types (MSNs)** over time.

The goal is to extract actionable insights from raw data on energy consumption and pricing, reveal the most consumed and expensive energy types, highlight growth patterns, and explore changes over time all to support **policy planning, infrastructure development, and economic forecasting**.

---

## Tools & Technologies Used

* **PostgreSQL** – for querying and transforming energy datasets
* **Power BI** – for interactive dashboards and visualization
* **SQL (CTEs, Joins, Aggregations, Window Functions)** – for deep analytical queries

---

##  Key Analyses Conducted

### 1. **Top 5 States by Total Consumption (2020–2023)**

This analysis identified the five states with the highest energy consumption across each year. Texas, California, and Louisiana consistently appeared at the top, reflecting their large populations and industrial energy needs.

**Query Insight**:

```sql
SELECT State, SUM(Year_2020 + Year_2021 + Year_2022 + Year_2023) AS total_consumption
FROM energy
WHERE State <> 'US'
GROUP BY State
ORDER BY total_consumption DESC
LIMIT 5;
```

---

### 2. **Top 5 States by Energy Prices per Year (2020–2023)**

This analysis highlighted the states with the highest energy prices for each year. Texas and California often led due to high transportation and regulatory costs.

**Query Insight**:

```sql
SELECT State, price_2020, price_2021, price_2022, price_2023
FROM energy_price
WHERE State <> 'US'
ORDER BY price_2023 DESC
LIMIT 5;
```

---

### 3. **Year-over-Year Change in National Energy Consumption & Price**

We tracked percentage changes in both energy usage and price between 2022 and 2023, uncovering **growth patterns** and possible influences like policy shifts or global energy events.

**Query Insight**:

```sql
-- Shows % change in both consumption and price
SELECT 
  ROUND(100.0 * (c_2023 - c_2022) / NULLIF(c_2022, 0), 2) AS consumption_change_pct,
  ROUND(100.0 * (p_2023 - p_2022) / NULLIF(p_2022, 0), 2) AS price_change_pct
FROM (
  SELECT
    SUM(e.Year_2022) AS c_2022,
    SUM(e.Year_2023) AS c_2023,
    SUM(p.price_2022) AS p_2022,
    SUM(p.price_2023) AS p_2023
  FROM energy e
  JOIN energy_price p ON e.State = p.State AND e.MSN = p.MSN
  WHERE e.State <> 'US'
) totals;
```
This breakdown shows how selected states' total energy consumption changed annually:
| State             | 2020–2021 | 2021–2022 | 2022–2023 |
| ----------------- | --------- | --------- | --------- |
| **Hawaii**        | 12.34%    | 6.25%     | 3.21%     |
| **California**    | 8.01%     | 2.24%     | 8.01%     |
| **Oregon**        | 7.22%     | 2.06%     | 5.14%     |
| **West Virginia** | 6.75%     | 1.91%     | 4.88%     |
| **Texas**         | 5.38%     | 2.98%     | 4.39%     |
| **Oklahoma**      | -2.34%    | 3.21%     | 5.21%     |

---

### 4. **Top 10 MSNs by Consumption Growth (2020 vs 2023)**

This section ranks the top 10 energy types by absolute consumption growth. It helps uncover which energy sources (like solar, wind, or natural gas) have gained the most traction.

**Query Insight**:

```sql
SELECT
  MSN,
  SUM(Year_2023) - SUM(Year_2020) AS growth
FROM energy
WHERE State <> 'US'
GROUP BY MSN
ORDER BY growth DESC
LIMIT 10;
```
This table highlights the top 10 MSNs with the largest increase in national consumption over the 4-year period:
| MSN       | 2020      | 2023      | Growth (%) | Description                                        |
| --------- | --------- | --------- | ---------- | -------------------------------------------------- |
| **BTGBP** | 1,508.00  | 15,999.00 | 960.94     | **Biomass Total Gross Production**                 |
| **B1ACP** | 68,445.00 | 68,445.00 | 249.85     | **Coal – Electric Power Sector**                   |
| **B1TCP** | 19,564.00 | 68,445.00 | 249.85     | **Coal – Total Consumption**                       |
| **BTVHN** | 973.70    | 3,403.40  | 249.53     | **Biomass – Transportation – Non-Electric**        |
| **ELVHS** | 22.00     | 66.60     | 202.73     | **Electric Vehicle Charging (Highway Sector)**     |
| **BTVHP** | 1,824.00  | 5,447.00  | 198.63     | **Biomass – Transportation – Electric Power**      |
| **ELVHN** | 1,587.00  | 4,554.50  | 186.97     | **Electric Vehicle Charging (Non-Highway Sector)** |
| **EYICP** | 226.00    | 180.97    | 180.97     | **Electricity – Industrial Combined Heat & Power** |
| **EYTCP** | 226.00    | 635.00    | 180.97     | **Electricity – Total Consumption**                |
| **ESVHP** | 2,901.00  | 7,595.00  | 161.81     | **Solar – Transportation – Electric Power**        |

---

### 5. **Top 10 Energy Types (MSNs) by National Consumption in 2023**

This reveals which energy types dominate the national energy profile. It serves as a snapshot of the **current energy mix** in the U.S.

**Query Insight**:

```sql
SELECT
  MSN,
  SUM(Year_2023) AS total_consumption
FROM energy
WHERE State <> 'US'
GROUP BY MSN
ORDER BY total_consumption DESC
LIMIT 10;
```
| MSN       | Description                                        |
| --------- | -------------------------------------------------- |
| **NGTCP** | **Natural Gas – Total Consumption**                |
| **GDPHV** | **Distillate Fuel Oil – Electric Power**           |
| **NGTHP** | **Natural Gas – Electric Power Sector**            |
| **NGREP** | **Natural Gas – Residential Sector**               |
| **NGICP** | **Natural Gas – Industrial Combined Heat & Power** |
| **PATCP** | **Petroleum – Total Consumption**                  |
| **PAACP** | **Petroleum – Electric Power Sector**              |
| **NGGRP** | **Natural Gas – Gross Production**                 |

---

## What I Learned

* Mastered SQL aggregation techniques to filter, rank, and transform multi-year energy data

* Gained hands-on skills in Power BI report design, including slicers, filters, bar charts, and matrix tables

* Learned how to link energy consumption trends with economic and environmental implications

* Identified how specific states or sectors drive energy usage spikes, and how energy price correlates with geography and demand

---

## Conclusion

This project successfully identifies national and state-level patterns in energy consumption and pricing. Key takeaways:

* This project offers a comprehensive view of U.S. energy consumption trends and pricing shifts from 2020–2023. The use of MSNs helped dissect data into energy types and sectors, allowing for targeted insights. Major takeaways:

* Texas and California remain energy giants in both consumption and price.

* Natural gas and petroleum-based energy sources dominate consumption nationally.

* Rapid growth in biomass and electric vehicle charging indicates future-facing energy shifts.

* States like Hawaii and Oregon show pronounced year-over-year changes worth policy review.


The combination of PostgreSQL and Power BI proved to be a powerful stack for this type of analytical work, enabling both depth and clarity.

