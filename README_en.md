# Olist Analytics — Brazilian E-Commerce Marketplace Analysis

Analysis of a Brazilian e-commerce marketplace (Olist) — from raw data to a simple forecast. Tools: SQL (SQLite), Google Sheets, Tableau Public, Python.

A portfolio project analyzing the Brazilian E-Commerce Public Dataset by Olist: from raw data to four interactive dashboards in Tableau Public.

**Live dashboards:** [Tableau Public — Olist_main_sales](https://public.tableau.com/views/Olist_main_sales/CustomerSatisfactionStory?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

---

## Data

- **Source:** Brazilian E-Commerce Public Dataset by Olist (Kaggle): https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce
- **Scope:** 9 CSV files, roughly 100K orders (2016–2018), relational structure.

The **Brazilian E-Commerce Public Dataset by Olist** (Kaggle) consists of 9 raw CSV tables:

- `olist_customers_dataset` — customers
- `olist_geolocation_dataset` — geolocation
- `olist_order_items_dataset` — order line items
- `olist_order_payments_dataset` — payments
- `olist_order_reviews_dataset` — reviews
- `olist_orders_dataset` — orders (statuses, dates)
- `olist_products_dataset` — products
- `olist_sellers_dataset` — sellers
- `product_category_name_translation` — category name translation

Plus a pre-aggregated flat file `main_sales_reg` (the result of a SQL join of delivered order items with category, region, price, payment method, and review score context), which serves as the main data source for most of the Tableau dashboards.

**Scope:** 98,666 order items (main_sales_reg) / 99,441 orders overall (olist_orders_dataset), 72 unique product categories, period 2016–2018.

---

## Goal

Analyze the Olist marketplace's performance across four key areas:
1. Overall commercial performance (revenue, orders, regions, categories)
2. Order fulfillment funnel (from purchase to delivery)
3. Delivery operational performance (lead times, lateness, seasonal peaks)
4. Customer satisfaction (ratings, distribution, gap between categories)

and present the results as a professional data analyst portfolio piece.

---

## Steps

**SQL** — raw data cleaning and aggregation (`sql/queries.sql`), run in SQLite online:
- Main join query: delivered order items + region + category + price + freight + payment method + review score
- Category cleanup via `COALESCE(t.product_category_1, 'unknown')` — resolved 1,437 rows with a missing category translation (replaced with `"unknown"`)
- Exploratory queries: monthly revenue, top-10 categories, revenue by state, average review score by category, average delivery time, payment method distribution

**Google Sheets** — final data cleanup (Find & Replace `"null"` → `"unknown"` in the category column, 1,437 replacements), preparing `main_sales_reg` for upload to Tableau.

**Tableau** — building 4 dashboards (details below), including:
- calculated fields (`Delivery Days`, `Is Late`, `Late Delivery Rate`, `Review Score Label`, and others)
- table calculations (moving average, percent of total)
- reference lines (thresholds), annotations, dual-axis charts

**Python** — two draft notebooks (`olist_predict.ipynb`, `olist_predict2.ipynb`) with a forecasting component; **not yet evaluated or finalized** — remains the next step for the project.

---

## Dashboards

### 1. Executive Overview
KPI cards (Total Revenue R$13,591.6K, Orders 98,666, AOV — Average Order Value — R$137.8) + monthly revenue trend + revenue-by-region map (São Paulo alone drives 38% of total revenue) + top 7 categories by revenue (health_beauty leads at R$1,257.6K) + average review score by category.

### 2. Order Fulfillment Funnel
Order progression funnel: Purchased → Approved → Handed to carrier → Delivered, with a percent-of-start figure at each stage and 4 KPIs on top (Total Purchased, Delivered, Lost Orders, Delivery Rate).

### 3. Delivery Performance Monitor
Operational monitor: weekly order volume (with a Black Friday spike annotation — 3,008 orders in a single week) + average delivery time trend with a 4-week moving average and a threshold reference line + KPIs (Late Delivery Rate, Avg Delivery Days).

### 4. Customer Satisfaction Story
Narrative dashboard: a text panel with key findings (bolded figures) + rating distribution by star + average review score by region + overall average score (KPI).

---

## Key Findings

- **São Paulo dominates:** a single state drives 38% of total revenue (R$5.2M out of R$13.6M) — the next state, Rio de Janeiro, brings in just R$1.76M.
- **health_beauty is the top category** by revenue (R$1,257.6K), ahead of watches_gifts and bed_bath_table.
- **97% of orders reach delivery** — of 99,441 purchased, 96,476 were delivered, 2,965 were lost along the way. The biggest drop-off happens at the handoff-to-carrier stage.
- **Average delivery time is 12–13 days**, with 8.11% of orders arriving later than the promised date. A sharp demand spike is visible around Black Friday (3,008 orders in a single week, roughly three times the period's average of ~985 orders per week).
- **Overall customer satisfaction is strong** — an average rating of 4.11 out of 5, with most orders receiving 4–5 stars. However, the **security_and_services** category is a clear outlier at just 2.50 — more than 2 points below the top performers (cds_dvds_musicals at 4.67).
- **Regional variation in satisfaction is minor** (4.15 in the South vs. 3.91 in the Northeast) — product category affects satisfaction far more than geography does.

---

## Forecast and Recommendations

**Recommendations based on the analysis:**
1. Investigate the causes of low ratings in the `security_and_services` category — the issue is more likely product quality than logistics, since regional variation is minimal.
2. Reduce dependence on São Paulo by growing sales in other regions — 38% revenue concentration in a single state is a business risk.
3. Prepare in advance for seasonal demand spikes (like Black Friday) — current data shows delivery time doesn't degrade critically during peak weeks, but it's worth monitoring.
4. Continue working to reduce the Late Delivery Rate (8.11%) — even a small improvement here is directly tied to customer satisfaction.

**Forecasting component (Python):** the notebooks `olist_predict.ipynb` and `olist_predict2.ipynb` contain draft forecasting models; evaluating and finalizing this part is the project's next stage.

---

## Repository Structure

```
olist-analytics/
├── data/              # raw and intermediate CSV files
├── notebook/          # olist_predict.ipynb, olist_predict2.ipynb
├── sql/               # queries.sql — SQL cleaning and aggregation logic
├── README_en.md
└── README_ua.md
```

---

## How to Reproduce

1. Download the raw Olist dataset CSV tables (Kaggle) into `data/`.
2. Run `sql/queries.sql` (SQLite/PostgreSQL) to get the cleaned and aggregated dataset.
3. If needed, do a final category cleanup in Google Sheets (Find & Replace `null` → `unknown`).
4. View the finished dashboards online — the Tableau workbook is published on Tableau Public; no local `.twb` file is kept in this repository: [Tableau Public — Olist_main_sales](https://public.tableau.com/views/Olist_main_sales/CustomerSatisfactionStory?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)
5. To rebuild the dashboards yourself, connect the updated, cleaned `main_sales_reg` file as a data source in a new Tableau workbook.
