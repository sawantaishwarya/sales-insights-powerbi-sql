# sales-insights-powerbi-sql

End-to-end portfolio project: SQL exploration ➜ data cleaning (Power Query) ➜ data model (star schema) ➜ interactive Power BI dashboard.

# Overview

This project simulates a real-world sales analytics engagement for a computer hardware supplier. I built a repeatable pipeline and an interactive dashboard to help leadership answer:

How are revenue and quantity trending by year and by month?

Which regions, products, and customers drive performance?

Where are sales declining and where should we focus?

Stack: Power BI Desktop, MySQL, SQL Workbench, Power Query (M), DAX.

# Project Planning — AIMS Grid

Purpose: Build a single source of truth for sales, reduce ad-hoc Excel work, and enable data-driven decisions.

Stakeholders: Sales Director, Regional Managers, Data/IT teams.

End Result: A Power BI dashboard with drill-downs for year, market, product, and customer.

Success Criteria: Lower manual reporting time/cost, accurate and timely KPIs, and measurable uplift in sales after adoption.

# Data Sources

Database: sales (MySQL)

Tables: transactions, customers, products, markets, date

Record volumes: ~150k transaction rows; customers ~38

# Data Model (Star Schema)

Fact: transactions

Dimensions:

customers (customer_code → customers.customer_code)

products (product_code → products.product_code)

markets (market_code → markets.market_code)

date (order_date → date.date)

Relationships are one-to-many from each dimension into the fact table.

# Data Cleaning & Transformation (Power Query)

Key steps performed in Transform data (Power Query):

Filter irrelevant markets: Remove non-Indian markets (e.g., New York, Paris) and blank zones.

Remove invalid amounts: Exclude sales_amount <= 0 and known error codes (e.g., -1).

Normalize currency: Handle messy currency values (e.g., "INR", "INR\r", "USD", "USD\r"). Create a new column normalized_sales_amount to convert USD to INR (example conversion: 1 USD = 88.18 INR).

Deduplicate anomalies: Remove duplicated transaction rows created by bad currency variants.

Modeling: Build relationships (see star schema) and create base measures:

Revenue = SUM( transactions[normalized_sales_amount] )

Sales Quantity = SUM( transactions[sales_qty] )

# Dashboard Highlights

KPI cards: Total Revenue, Total Quantity.

Bar charts: Revenue & Quantity by Market and by Customer.

Line chart: Revenue trend by Month/Year.

Top-N visuals: Top 5 Customers and Top 5 Products by revenue.

Slicers: Year, Market, Product for quick filtering.

# Reproducibility — How to Run
1) MySQL

Install MySQL & Workbench.

Import the provided dump (db_dump.sql) via Server → Data Import.

Confirm tables exist in a schema named sales.

2) Power BI Desktop

Get Data → MySQL. Server: localhost (or your host). Database: sales.

Load tables: transactions, customers, products, markets, date.

Open Transform data and apply the cleaning steps listed above.

Build visuals & measures, then save as sales_insights.pbix.

(Optional) Lighter template: File → Export → Power BI template (.pbit).

# Example SQL Query (Monthly Revenue Check)

-- Total sales amount for January 2020
SELECT 
    SUM(t.sales_amount) AS jan_revenue
FROM sales.transactions t
INNER JOIN sales.date d
    ON t.order_date = d.date
WHERE d.year = 2020
  AND d.month_name = 'January';
