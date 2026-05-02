# CRM Sales Analytics Dashboard
### Python · MySQL · Power BI — End-to-End Sales Pipeline Analysis

![Dashboard Preview](dashboard/crm_dashboard.PNG)

> Cleaned and analyzed **8,800+ B2B CRM deals** worth **$10M in total revenue** using Python for data cleaning, MySQL for business queries, and Power BI for interactive dashboards — uncovering performance gaps across agents, products, and accounts.

---

## Table of Contents
- [Project Overview](#project-overview)
- [Business Problem](#business-problem)
- [Dataset](#dataset)
- [Tools & Technologies](#tools--technologies)
- [Data Cleaning with Python](#step-1--data-cleaning-with-python)
- [SQL Analysis](#step-2--sql-analysis)
- [Key Findings & Insights](#key-findings--insights)
- [Business Recommendations](#business-recommendations)
- [Dashboard](#dashboard)
- [Connect With Me](#connect-with-me)

---

## Project Overview

This project simulates the role of a **Data Analyst embedded in a B2B sales team**. I followed a complete end-to-end workflow:

1. **Python (pandas)** — inspected raw CSVs, handled missing values, standardised formats, and validated data before loading into SQL
2. **MySQL** — wrote business-focused queries to answer stakeholder questions about pipeline health, agent performance, and revenue trends
3. **Power BI** — built an interactive dashboard to communicate findings to a non-technical audience

The goal was not just to build charts — but to extract **actionable business intelligence** that a Sales Manager could act on immediately.

---

## Business Problem

A sales organisation wants answers to:

1. How healthy is our pipeline? What is our true conversion rate?
2. Which agents are top performers — and who needs coaching?
3. Which products and accounts drive the most revenue?
4. Are deals stuck in the pipeline that we should be chasing?
5. Is revenue trending up or down over time?

---

## Dataset

Four relational CSV tables exported from a CRM system:

| Table | Rows | Key Columns |
|-------|------|-------------|
| `sales_pipeline` | 8,800 | opportunity_id, sales_agent, product, account, deal_stage, engage_date, close_date, close_value |
| `accounts` | 85 | account, sector, revenue, employees, office_location |
| `products` | 7 | product, series, sales_price |
| `sales_teams` | 35 | sales_agent, manager, regional_office |

**Deal stages:**

| Stage | Meaning |
|-------|---------|
| `Won` | Deal successfully closed |
| `Lost` | Deal did not convert |
| `Engaging` | Active discussion — stale risk |
| `Prospecting` | Early stage |

---

## Tools & Technologies

| Tool | Purpose |
|------|---------|
| **Python (pandas)** | Data cleaning, null handling, type conversion, validation |
| **MySQL** | Data extraction, aggregation, business queries |
| **Power BI** | Interactive dashboard & data visualisation |
| **Excel / CSV** | Raw data storage and inspection |

---

## Step 1 — Data Cleaning with Python

Before loading into MySQL, I inspected all four CSVs using **pandas** to identify and resolve quality issues.

### Issues Found in Raw Data

| Table | Issue | Fix Applied |
|-------|-------|-------------|
| `sales_pipeline` | Missing `account` values on open deals | Filled with `'Unknown'` |
| `sales_pipeline` | Missing `close_date` for Engaging/Prospecting deals | Left as `NaT` — excluded from date queries |
| `sales_pipeline` | Missing `close_value` for open deals | Filled with `0` |
| `sales_pipeline` | `close_value = 0` for Lost deals (correct business logic) | Kept as-is |
| `sales_pipeline` | `engage_date` and `close_date` stored as plain strings | Converted to `datetime` |
| `accounts` | Whitespace variations in account names | Stripped and standardised |
| `products` | No nulls found | No action needed |
| `sales_teams` | No nulls found | No action needed |

---

### Python Cleaning

Used Python to clean missing values, remove duplicates, and standardize formats.

---

### Key Cleaning Decisions Explained

**Why fill missing `account` with `'Unknown'` instead of dropping rows?**
Rows with missing accounts are all Engaging or Prospecting deals — real pipeline activity. Dropping them would undercount the pipeline size and artificially inflate the conversion rate.

**Why convert date columns before loading into MySQL?**
Ensuring a consistent `YYYY-MM-DD` format prevents MySQL from silently misinterpreting dates, which would break all `DATEDIFF` and `MONTHNAME` based queries.

---

## SQL Analysis

All queries are in [`queries/CRM_SALES_QUERIES.sql`](queries/CRM_SALES_QUERIES.sql)

---

## Key Findings & Insights

### Revenue Performance
| Metric | Value |
|--------|-------|
| Total Revenue | $10.0M |
| Total Deals | 8,800+ |
| Peak Monthly Revenue | $1.34M (June 2017) |
| Lowest Monthly Revenue | $697K (July 2017) |
| Revenue Decline Jun → Jul | -48% |

> Revenue declined consistently from June 2017 onward — a 48% drop over 13 months. This signals either seasonal demand, pipeline quality issues, or reduced prospecting activity upstream.

---

### Product Revenue Breakdown
| Product | Revenue |
|---------|---------|
| GTXPro | $3,510,578 |
| GTX Plus Pro | $2,629,651 |
| MG Advanced | $2,216,387 |
| GTX Plus Basic | $705,275 |
| GTX Basic | $499,263 |
| GTK 500 | $400,612 | 
| MG Special | $43,768 |

> GTXPro contributes 35% of total revenue alone. MG Special at $43K is a clear underperformer — a candidate for repricing or discontinuation.

---

### Agent Performance — Volume vs. Efficiency

| Agent | Revenue | Conv. Rate | Total Deals | Lost Deals |
|-------|---------|-----------|-------------|------------|
| Darcel Schlecht | $1,153,214 | 46.7% | 747 | 204 |
| Vicki Laflamme | $478,396 | 49.0% | 451 | 126 |
| Kary Hendrixson | $454,298 | 47.7% | 438 | 126 |
| Donn Cantrell | $445,860 | 57.5% | 275 | 117 |
| Reed Clapper | $438,336 | **65.4%** | 237 | 82 |

> The highest-revenue agent (Darcel Schlecht) also has the most lost deals (204). Reed Clapper has the best conversion rate (65.4%) but handles 3× fewer deals. Applying Reed's approach to higher-volume agents is the clearest performance lever available.

---

### Pipeline Health
| Stage | Count |
|-------|-------|
| Won | 4,238 |
| Lost | 1,589 |
| Engaging | ~2,000 |
| Prospecting | 500 |

> ~2,000 stale "Engaging" deals sit idle. At average won deal value (~$775), converting just 20% adds ~$310K in revenue without any new prospecting spend.

---

### Deal Velocity
- Slowest closed deals: **138 days** to finish
- Most slow deals cluster between **135–138 days**
- B2B industry benchmark: 60–90 days

> Deals taking 135+ days suggest a bottleneck after the engage stage — likely in procurement or approvals. A structured follow-up protocol at day 60 could meaningfully shorten cycles.

---

## Business Recommendations

**1. Re-engage the stale pipeline**
Run a structured 2-week campaign targeting the top 300 "Engaging" deals by estimated value. No new prospecting cost — high-probability near-term revenue.

**2. Scale GTXPro, review MG Special**
GTXPro generates 35% of all revenue. Increase quota allocation and training focus here. MG Special at under $44K should be repriced or retired.

**3. Build a coaching programme from Reed Clapper's playbook**
Reed converts 65.4% of deals — 17 percentage points above the team average (48.2%). Document his qualification and objection-handling approach and roll it out to the bottom-quartile agents first.

---

## Dashboard

The Power BI dashboard covers:
- KPI cards: Total Revenue · Conversion Rate · Loss Rate · Engage Rate
- Monthly Revenue Trend (line chart)
- Sales Funnel Conversion Analysis
- Top Agents by Revenue (bar chart)
- Revenue by Product (bar chart)
- Revenue by Region (pie chart)

![Dashboard](dashboard/crm_dashboard.PNG)

---

## Connect With Me

- LinkedIn: [[Your LinkedIn URL](https://www.linkedin.com/in/muskan-mujawar-231a04323/)]
- Email: [mujawar.muskan.aziz@gmail.com]


---

*Built as part of my data analytics portfolio to demonstrate end-to-end skills across Python data cleaning, SQL business analysis, and Power BI visualisation.*
