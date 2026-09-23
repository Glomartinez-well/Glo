# Glo
Digital Analytics Dashboard

# Digital Marketing NOI Impact

## Project Goal

This project measures how digital marketing activity translates into financial performance across Welltower's senior living portfolio.

The goal is to connect the full marketing and resident journey:

**Marketing Spend → Leads → Tours → Move-Ins → Revenue → Length of Stay → CLTV → NOI**

Rather than evaluating marketing only through metrics like leads and CPL, the project connects marketing performance to resident value, occupancy, revenue, and NOI using actual CRM, financial, occupancy, and rent roll data.

---

## Project Scope

* **55 senior living properties**
* **3 operators:** Monarch, Evergreen, and Sagora
* Historical marketing and resident data from **2020+**
* Snowflake as the core data and analytics platform
* Power BI as the reporting layer
* Snowflake ML for predictive modeling

---

## Architecture

```text
Source Systems
      ↓
Snowflake Gold Tables
      ↓
Analytics & Business Logic
      ↓
Power BI Reporting Views
      ↓
Digital Marketing Analytics Dashboard

          +

Snowflake ML
├── Lead Conversion Prediction
└── Occupancy Forecasting
```

---

## Source Data

Most source data comes from `OPERATOR_GOLD.PRESENTATION`, with website analytics from `HEAP.PUBLIC`.

| Area                 | Primary Tables           |
| -------------------- | ------------------------ |
| Marketing Funnel     | `FACT_PROSPECTJOURNEY`   |
| Move-Ins / Move-Outs | `FACT_MOVEINMOVEOUT`     |
| Revenue & Rent       | `FACT_RENT_ROLL_MONTHLY` |
| Financials / NOI     | `FACT_FINANCIALS`        |
| Occupancy            | `FACT_PROPERTYOCCUPANCY` |
| Lead Sources         | `DIM_LEADSOURCE`         |
| Property Data        | `DIM_PROPERTY`           |
| Web Analytics        | `HEAP.PUBLIC.SESSIONS`   |

---

## Analytics Layer

The Snowflake pipeline creates **20+ analytics and reporting views** covering:

* Marketing funnel performance
* Occupancy
* Digital marketing spend
* Move-ins and move-outs
* Revenue attribution
* Length of stay
* Customer Lifetime Value (CLTV)
* Funnel conversion and velocity
* Lost lead impact
* Source-level NOI attribution
* Resident retention
* Property opportunity scoring

Core views include:

`VW_MARKETING_FUNNEL`
`VW_OCCUPANCY_MONTHLY`
`VW_NOI_FINANCIALS`
`VW_REVENUE_BY_PROPERTY`
`VW_LOS_ANALYSIS`
`VW_CLTV_BY_CHANNEL`
`VW_SOURCE_LEVEL_NOI`
`VW_DIGITAL_NOI_SUMMARY`

---

## Power BI Reporting

Dedicated `VW_PBI_*` views provide the reporting layer for the Digital Marketing Analytics dashboard.

Current reporting areas include:

* Executive Overview
* Marketing Pipeline
* Conversion Performance
* Spend & NOI Impact
* Care Type Performance
* Property Opportunity
* Digital Trends
* Website Traffic
* Move-Out & Retention Analysis

---

## Key Metrics

| Metric          | Calculation                            |
| --------------- | -------------------------------------- |
| CPL             | Digital Ad Spend / Leads               |
| CPMI            | Digital Ad Spend / Digital Move-Ins    |
| Conversion Rate | Move-Ins / Leads                       |
| ROAS            | First-Month Revenue / Digital Ad Spend |
| Marketing ROI   | Annualized Revenue / Digital Ad Spend  |
| CLTV            | Avg Monthly Revenue × Avg LOS          |
| NOI Margin      | NOI / Total Revenue                    |
| Attributed NOI  | Revenue × NOI Margin                   |
| Net Absorption  | Move-Ins - Move-Outs                   |
| Occupancy       | Financial Occupied Units / Capacity    |

---

## Machine Learning

### Lead Conversion Prediction

Snowflake ML classification predicts the probability that an active lead will eventually move in.

Output includes:

* Conversion probability
* Lead risk tier

### Occupancy Forecast

Snowflake ML forecasting predicts property-level occupancy **3 months forward**, including confidence intervals.

Forecast results are incorporated into property opportunity analysis.

---

## Methodology

A few important principles guide the project:

* Revenue comes from **actual resident rent roll data**, not estimated revenue.
* Marketing-attributed move-ins come from `FACT_PROSPECTJOURNEY` to preserve acquisition source.
* CLTV uses completed stays and is therefore a conservative historical measure.
* Occupancy follows the existing Operator Analytics methodology.
* Funnel events are attributed to the month in which each event actually occurred.
* Aggregator commissions such as APFM and Caring.com are tracked separately from digital advertising spend.

---

## Infrastructure

| Component          | Value                            |
| ------------------ | -------------------------------- |
| Analytics Database | `SNOWFLAKE_LEARNING_DB.PRACTICE` |
| Source Database    | `OPERATOR_GOLD.PRESENTATION`     |
| Web Analytics      | `HEAP.PUBLIC`                    |
| Warehouse          | `FPA_WH`                         |
| Role               | `TEAM_FPA`                       |
| Notebook           | `NOI Project.ipynb`              |
| Dashboard          | Power BI                         |
| ML Framework       | Snowflake ML                     |

---

## Validation

The Snowflake notebook contains validation queries that reconcile dashboard metrics back to the underlying Gold tables.

Key validation areas include **move-ins, revenue, marketing spend, occupancy, NOI, CLTV, and funnel performance**.

The objective is to keep every dashboard metric traceable back to its original source data.
