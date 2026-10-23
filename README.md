# Glo
Digital Marketing Analytics Dashboard

## Project Goal

Measure how digital marketing contributes to financial performance across Welltower's senior living portfolio.

The project connects the full marketing and resident journey:

**Marketing Spend → Leads → Tours → Move-Ins → Revenue → Length of Stay → CLTV → NOI**

Rather than measuring marketing success through leads and CPL alone, Glo connects acquisition performance to resident value, occupancy, revenue, and NOI using actual CRM, financial, occupancy, and rent roll data.

The goal is to provide a trusted, actionable dashboard that helps the team understand what happened, identify opportunities, and make better marketing investment decisions.

---

## Project Scope

- **55 senior living properties**
- **3 operators:** Monarch, Evergreen, and Sagora
- Historical lead data beginning in 2020
- Core financial, retention, and Net CLTV analysis primarily from January 2025 onward
- Snowflake for data processing, analytics, and forecasting
- Power BI for reporting and visualization

Properties are identified using their **Vantage codes** to maintain consistent reporting across operator and brand transitions.

---

## Architecture

```text
Source Systems
      ↓
Snowflake Gold Tables
      ↓
Analytics & Business Logic
      ↓
Snowflake Reporting Views
      ↓
Power BI
      ↓
Glo Digital Analytics Dashboard

      +

Snowflake ML
├── Lead Conversion Prediction
├── Occupancy Forecasting
├── CPL Forecasting
└── Lead Forecasting
```

Snowflake serves as the primary data and calculation layer, while Power BI provides the reporting and dashboard experience.

---

## Source Data

Most source data comes from `OPERATOR_GOLD.PRESENTATION`, with additional website analytics from Heap.

| Area | Primary Table |
|---|---|
| Marketing Funnel & Attribution | `FACT_PROSPECTJOURNEY` |
| Financial Move-Ins | `FACT_FINANCIALMOVEINDETAIL` |
| Move-Ins / Move-Outs | `FACT_MOVEINMOVEOUT` |
| Revenue & Rent Roll | `FACT_RENT_ROLL_MONTHLY` |
| Financials / NOI | `FACT_FINANCIALS` |
| Occupancy | `FACT_PROPERTYOCCUPANCY` |
| Lead Sources | `DIM_LEADSOURCE` |
| Property Data | `DIM_PROPERTY` |
| Website Analytics | Heap analytics tables |

**Move-in methodology:** Financial move-ins come from `FACT_FINANCIALMOVEINDETAIL`, including Permanent and Respite contracts. These records are joined to `FACT_PROSPECTJOURNEY` using resident and property identifiers to preserve marketing source attribution.

This methodology was updated in October 2026 to better align with Operator Analytics reporting.

---

## Analytics Layer

The Snowflake pipeline contains 20+ analytics and reporting views covering:

- Marketing funnel performance and conversion
- Digital marketing spend and acquisition efficiency
- Financial move-ins and move-outs
- Occupancy and property opportunity scoring
- Revenue attribution and NOI
- Length of stay and resident retention
- CLTV, Net CLTV, and CAC
- Aggregator performance (APFM and Caring.com)
- SEO, Organic, and Paid Media performance
- CPL and lead forecasting

Core views include:

```text
VW_MARKETING_FUNNEL
VW_OCCUPANCY_MONTHLY
VW_NOI_FINANCIALS
VW_REVENUE_BY_PROPERTY
VW_LOS_ANALYSIS
VW_CLTV_BY_CHANNEL
VW_NET_CLTV_BY_CHANNEL
VW_MOVEIN_MOVEOUT
VW_PBI_SOURCE_NOI
VW_PBI_AGGREGATOR_PERFORMANCE
VW_PBI_OVERVIEW
VW_PBI_SPEND_NOI_IMPACT
```

These views standardize the underlying data and business logic before it reaches Power BI.

---

## Power BI Reporting

Glo currently includes reporting areas covering:

1. Executive Overview
2. Marketing Pipeline
3. Conversion Performance
4. Spend & NOI Impact
5. Care Type Performance
6. Property Opportunity
7. Digital Trends
8. Website Traffic
9. Move-Out & Retention
10. Net CLTV
11. CPL Forecast
12. SEO & Organic
13. YoY Performance
14. Digital Performance
15. Aggregator Performance (APFM & Caring.com)

These represent the main reporting areas, not necessarily the exact number of workbook tabs.

The Digital Performance view consolidates key metrics used in monthly executive reporting.

---

## Key Metrics

| Metric | Calculation |
|---|---|
| CPL | Digital Ad Spend ÷ Digital Leads |
| CAC / CPMI | Channel Spend ÷ New Residents Acquired |
| Lead-to-Tour Rate | Tours ÷ Leads |
| Tour-to-Move-In Rate | Move-Ins ÷ Tours |
| Lead-to-Move-In Rate | Move-Ins ÷ Leads |
| ROAS | Attributed Revenue ÷ Digital Ad Spend |
| CLTV | Average Monthly Revenue × Length of Stay |
| Net CLTV | CLTV − CAC |
| CLTV:CAC | CLTV ÷ CAC |
| NOI Margin | NOI ÷ Total Revenue |
| Attributed NOI | Attributed Revenue × NOI Margin |
| Net Absorption | Move-Ins − Move-Outs |
| Occupancy | Occupied Units ÷ Available Capacity |

**CLTV methodology:**

CLTV (all-time) uses completed resident stays to measure historical lifetime value.

Net CLTV focuses on residents acquired from January 2025 onward, including those still living at the property. Their length of stay is measured through the reporting date, making this a realized-to-date value rather than a final lifetime estimate.

CAC is calculated using acquisition spend and new residents from the corresponding period and channel.

---

## Marketing Spend & Channel Definitions

Digital advertising spend is based on three GL expense categories:

- Advertising – Digital Media (i/C)
- Advertising – Digital Media
- Advertising & Media Consultants

The Digital Performance media spend view uses two advertising accounts, excluding consultant fees.

APFM and Caring.com placement commissions are tracked separately from digital advertising spend.

**Channel grouping:**

`CHANNEL_GROUP` distinguishes Paid Media from SEO & Organic, allowing the dashboard to measure acquisition efficiency and conversion performance separately.

Marketing source definitions are standardized to reduce inconsistencies between CRM source labels.

---

## Machine Learning & Forecasting

The project includes seven forecasting and predictive model configurations documented in the Snowflake notebook.

| Model | Purpose |
|---|---|
| Lead Conversion Prediction | Estimate likelihood of lead conversion |
| Occupancy Forecast | Predict property occupancy three months forward |
| Portfolio CPL Forecast | Forecast total DMM portfolio CPL |
| Operator CPL Forecast | Forecast CPL independently by operator |
| Property CPL Forecast | Forecast CPL at the individual property level |
| Lead Forecasting | Estimate future lead volume |
| Forecasting Pipeline | Refresh, evaluate, and track forecast results |

The CPL forecasting framework supports portfolio, operator, and property-level forecasts.

Forecasting is refreshed through:

`SP_REFRESH_CPL_FORECAST`

The pipeline includes historical backtesting, method selection, forecast ranges, and performance tracking.

The goal is to support future decisions around marketing budgets, lead generation, acquisition efficiency, and property-level investment opportunities.

---

## Methodology

The following principles guide the project:

- Financial move-ins are sourced from Yardi financial move-in records.
- Acquisition sources are preserved through prospect journey data.
- Funnel activity is measured using the actual month in which each event occurred.
- Revenue comes from actual resident rent roll data.
- CAC compares acquisition spend against new residents acquired during the same reporting period.
- Historical CLTV and realized-to-date Net CLTV are treated as separate measures.
- Occupancy follows the established Operator Analytics methodology.
- Aggregator commissions are tracked separately from Digital Marketing spend.
- Property reporting is standardized using Vantage codes.

### Operator Transitions

The portfolio includes historical operator and brand transitions.

Eighteen Frontier properties transitioned to Evergreen (11) and Sagora (7). Brandywine and Sunrise properties transitioned to Monarch.

Historical records are mapped to their current reporting operator using Vantage codes, including periods before the transition.

This allows consistent property-level reporting over time, although historical operator labels reflect the current reporting structure rather than necessarily the operator managing the property at that time.

---

## Known Limitations

- Some historical financial and move-out data is incomplete before 2025.
- January 2025 is the earliest period with spend coverage across all three primary channels.
- Heap currently covers approximately 50 of the 55 properties.
- Heap session counts may differ from Google Analytics because of tracking and measurement differences.
- Property-level forecasting can be less reliable where historical volume is limited.
- Realized-to-date CLTV for active residents will continue to increase as residents remain in the community.
- Some marketing source classifications require continued validation and standardization.

---

## Infrastructure

| Component | Value |
|---|---|
| Analytics Database | `SNOWFLAKE_LEARNING_DB.PRACTICE` |
| Primary Source | `OPERATOR_GOLD.PRESENTATION` |
| Web Analytics | Heap, exact source object to confirm |
| Warehouse | `FPA_WH` |
| Snowflake Role | `TEAM_FPA` |
| Project Notebook | `NOI Project.ipynb` |
| Dashboard | Power BI |
| Forecasting | Snowflake ML |

---

## Validation

The Snowflake notebook contains validation queries that reconcile dashboard metrics against the underlying source tables and financial reporting.

Key validation areas include:

- Leads, tours, and financial move-ins
- Digital Marketing spend and GL account mapping
- Revenue and NOI
- Occupancy
- CAC, CLTV, and Net CLTV
- Funnel conversion rates
- Marketing source attribution
- Operator and property mapping
- Forecast accuracy

**Financial validation:** Digital Marketing spend should reconcile to the underlying GL for the 55 in-scope Vantage codes each month, using the correct expense accounts and excluding subtotal or duplicate accounting entries.

The objective is to keep dashboard metrics traceable, consistent, and reliable as the project evolves.
