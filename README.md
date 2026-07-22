# AtliQ-Hardware-Supply-Chain-Finance-Analytics
An end-to-end relational database management, ETL, and data analytics project built for  AtliQ Hardware, a global consumer electronics manufacturer. 
## 📌 Executive Summary

AtliQ Hardware manufactures and distributes hardware products (mice, keyboards, laptops, printers) across global markets via multiple channels (Retailers, Direct Outlets, and Distributors). 

This project establishes a robust relational data model in **MS SQL Server** to optimize financial tracking and supply chain planning. It analyzes the journey from **Gross Price** to **Net Sales (Revenue)** and **Gross Margin (Profitability)** while evaluating demand forecast precision.

---

## 📐 Financial & P&L Framework

The financial model accounts for both pre-invoice and post-invoice deductions to determine true profitability:

$$\text{Gross Price} \xrightarrow{-\text{Pre-Invoice Discounts}} \text{Net Invoice Sales} \xrightarrow{-\text{Post-Invoice Deductions}} \text{Net Sales (Revenue)}$$

* **Pre-Invoice Deductions:** Annual volume discounts negotiated at the beginning of each fiscal year.
* **Post-Invoice Deductions:** Variable adjustments made after sales, including **Promotional Offers**, **Placement Fees** (prime store layout), and **Performance Rebates**.
* **Cost of Goods Sold (COGS):** Sum of Manufacturing Costs, Freight/Transportation, and Operational Costs.
* **Gross Margin:** $\text{Net Sales} - \text{COGS}$.

---

## 🗄️ Database Architecture & Schema

The database `SupplyChainFinanceManagement` utilizes a **Star/Snowflake Schema** consisting of **9 core tables**:

              ┌────────────────────────┐
              │      dim_customer      │
              └───────────┬────────────┘
                          │
┌──────────────────┐        │        ┌─────────────────────────┐
│   dim_product    ├────────┼────────┤  fact_sales_monthly     │
└────────┬─────────┘        │        └─────────────────────────┘
│                  │                     │
├──────────────────┼─────────────────────┼──────────────────┐
│                  │                     │                  │
┌──────────┴───────────┐ ┌────┴─────────────────┐ ┌─┴──────────────┐ ┌──┴─────────────────┐
│  fact_gross_price    │ │fact_pre_invoice_ded. │ │fact_forecast_m.│ │fact_mfg_cost       │
└──────────────────────┘ └──────────────────────┘ └────────────────┘ └────────────────────┘


### Table Definitions:
1. **`dim_customer`**: Master data for customers, platforms, channels, and geographical regions.
2. **`dim_product`**: Product catalog details (divisions, segments, categories, variants).
3. **`fact_sales_monthly`**: Monthly sales transaction quantities.
4. **`fact_forecast_monthly`**: Monthly forecasted demand quantities.
5. **`fact_gross_price`**: Product list prices per fiscal year.
6. **`fact_manufacturing_cost`**: Per-unit production costs.
7. **`fact_freight_cost`**: Market-level freight and logistics cost percentages.
8. **`fact_pre_invoice_deductions`**: Agreed yearly customer discount percentages.
9. **`fact_post_invoice_deductions`**: Monthly promotional, placement, and performance rebate rates.

> **Note:** AtliQ's Fiscal Year runs from **September 1 to August 31** (e.g., September 2022 belongs to FY 2023).

---

## 🛠️ Key Technical Implementations

This project covers advanced SQL features and database concepts:

* **User-Defined Functions (UDFs):**
  * `dbo.get_total_forecast`: Calculates cumulative forecasted quantity per product and fiscal year.
  * `dbo.get_total_mfg_cost`: Computes multi-year manufacturing expenses.
  * `dbo.get_avg_discount`: Aggregates customer discount averages.
* **Stored Procedures:**
  * `sp_update_gross_price`: Updates base product prices dynamically.
  * `sp_product_mom_growth`: Reports month-over-month growth rates using window functions.
  * `sp_get_total_period_revenue`: Aggregates total period sales revenue.
* **Triggers & Integrity Control:**
  * `trg_sales_audit`: Audits new transaction insertions into an audit log table.
  * `trg_check_inventory` & `trg_update_inventory_on_sale`: Enforces inventory limits and auto-deducts stock upon sale.
  * `trg_flag_sales_outliers`: Automatically flags abnormal sales spikes.
* **Advanced Analytical Queries:**
  * Product rankings via `DENSE_RANK() OVER (PARTITION BY ...)`
  * MoM variance analysis using `LAG()` / `LEAD()`
  * Market customer aggregation using `STRING_AGG()`
  * Forecast Accuracy modeling using `FULL OUTER JOIN` and pivoting.

---

├── Database/
│   ├── 01_schema_creation.sql       # Table definitions & FK constraints
│   └── 02_data_insertion.sql        # Baseline transactional & dimension data
├── Queries/
│   ├── task3_monthly_reports.sql    # Transaction & Gross Sales SQL scripts
│   ├── task5_advanced_objects.sql   # UDFs, Stored Procedures, Triggers
│   └── task6_forecast_accuracy.sql # Forecast vs Actuals modeling & Pivot logic



💡 Key Business Insights
Peak Sales Seasonality: Demand consistently spikes between September and December, validating the fiscal calendar design and highlighting key stock-up periods.

Pricing Optimization: Strategic post-invoice deductions (placement fees & performance rebates) drive higher retailer loyalty in high-volume regions without sacrificing gross margin.

Forecast Accuracy: Identifying low-accuracy months helps inventory planning teams mitigate overstocking and stockout scenarios across global fulfillment centers.

🛠️ Tools & Technologies Used
Database Engine: Microsoft SQL Server (Sql management studio 360)

Modeling Tool: Entity-Relationship Diagrams (ERD), Star/Snowflake Schema Design

Analytics Techniques: Window Functions, Pivot Tables, CTEs, Data Auditing, Outlier Detection
