# 📊 Sales KPI Command Center
### A Power BI Dashboard Project

---

## Overview

An interactive sales performance dashboard built in Power BI to give sales leaders an instant read on business health across revenue, pipeline, and team performance. This was a personal project built from scratch to practice data modelling, DAX, and dashboard design in Power BI.

---

## Dashboard Features

| Visual | Description |
|---|---|
| KPI Cards | Total Revenue, Target, Attainment %, Avg Deal Size, Win Rate %, Deals Closed |
| Revenue vs Target by Month | Line and bar combo chart tracking actual vs target monthly |
| Pipeline by Stage | Horizontal bar chart showing deal count from Prospecting to Closed |
| Revenue by Product | Top 5 products ranked by total revenue |
| % Revenue Attainment by Sales Rep | Bar chart ranking rep performance against individual targets |
| Deals Closed by Month | Line chart showing sales velocity across the year |
| Win Rate by Region | Filled map of South African provinces shaded by win rate |

---

## Data Model

The project uses three related tables connected through a central DateTable following star schema best practices:

```
DateTable (centre)
    ├── Sales (one-to-many on Date)
    └── Monthly Targets (many-to-many on Month Key)
```

**Sales** — daily transactions with Date, Sales Rep, Region, Product, Revenue and Target columns

**Pipeline** — 200 deals with Stage, Deal Value, Close Date and Won status

**Monthly Targets** — monthly quota per sales rep across 5 regions for the full year

---

## DAX Measures

```dax
-- Revenue vs Target attainment
Revenue Attainment % = 
DIVIDE(SUM(Sales[Revenue]), SUM('Monthly Targets'[Monthly Target]))

-- Win rate based on closed deals only (excludes open pipeline)
Win Rate % = 
DIVIDE(
    CALCULATE(COUNTROWS(Pipeline), Pipeline[Won] <> BLANK(), VALUE(Pipeline[Won]) = 1),
    CALCULATE(COUNTROWS(Pipeline), Pipeline[Won] <> BLANK(), VALUE(Pipeline[Won]) = 1 || VALUE(Pipeline[Won]) = 0)
)

-- Monthly target that filters correctly by month and rep
Monthly Target Amount = 
SUMX(
    VALUES(Sales[Sales Rep]),
    CALCULATE(
        SUM('Monthly Targets'[Monthly Target]),
        FILTER(
            'Monthly Targets',
            'Monthly Targets'[Rep] = MAX(Sales[Sales Rep]) &&
            'Monthly Targets'[Month Key] = MAX('DateTable'[Month Key])
        )
    )
)
```

---

## Tools Used

- **Power BI Desktop** — data modelling, DAX, and visualisation
- **Power Query** — data transformation and custom column creation
- **DAX** — DIVIDE, CALCULATE, COUNTROWS, FILTER, SUMX, TREATAS, SUMX
- **Excel** — sample data source (Python/openpyxl generated)

---

## Dataset

Sample data covering:
- 5 sales reps across 5 South African provinces
- 5 product categories
- ~1,000 daily sales transactions across 2024
- 200 pipeline deals at various stages
- 60 monthly target rows (5 reps × 12 months)

All data was generated programmatically for demonstration purposes.

---

## Key Learnings

Getting the date relationships right between the Sales and Monthly Targets tables was the most challenging part of this project. The Monthly Targets table operates at month level while Sales operates at day level — bridging these correctly using a central DateTable and Month Key taught me a lot about how Power BI handles filter context and time intelligence.

---

## Files

| File | Description |
|---|---|
| `Sales_KPI_Command_Center.pbix` | Power BI report file |
| `Sales_KPI_Command_Center.xlsx` | Source data (Sales, Pipeline, Monthly Targets) |

---

## Author - Lebo

Built as a personal learning project to develop hands-on Power BI skills from data modelling through to dashboard design and publishing.
