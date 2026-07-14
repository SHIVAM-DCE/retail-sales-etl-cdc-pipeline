# Retail Sales Data Pipeline (PySpark, Databricks, Delta Lake)

This repository contains two connected data engineering projects built on Databricks, using PySpark and Delta Lake to process real-world retail sales order data.

## Project 1: Retail Sales ETL Pipeline
**Notebook:** `sales_etl_pipeline.ipynb`

An end-to-end batch ETL pipeline that ingests raw, nested JSON sales order data and prepares it for analytics.

**What it does:**
- **Extract:** Reads nested JSON order data (Databricks' `retail-org/sales_orders` sample dataset), where each order contains an array of ordered products.
- **Transform:**
  - Removes duplicate orders and rows missing key identifiers
  - Handles malformed data (empty timestamp strings) safely using `try_cast`, avoiding pipeline failures on dirty data
  - Flattens the nested `ordered_products` array using `explode()`, turning ~4,000 orders into ~7,800 individual product-level line items
  - Adds a derived `total_revenue` column (`unit_price * quantity`)
- **Aggregate:** Groups line items by product to compute total revenue, units sold, and order count per product
- **Load:** Writes both the cleaned line-item table and the aggregated summary table as Delta Lake tables
- **Verify:** Queries the resulting tables with SQL

**Key skills demonstrated:** nested/semi-structured data handling, data quality management, PySpark transformations, Delta Lake, SQL.

---

## Project 2: CDC (Change Data Capture) Pipeline with Delta Lake
**Notebook:** `cdc_delta_pipeline.ipynb`

Builds on Project 1's output table to demonstrate incremental data processing, the pattern used in production pipelines to handle ongoing data changes rather than reprocessing full datasets.

**What it does:**
- Simulates a batch of incoming change events (`INSERT`, `UPDATE`, `DELETE`) representing new/changed source data
- Applies these changes to the existing Delta table using a single atomic `MERGE INTO` statement, using `ROW_NUMBER()` to ensure only the latest change per record is applied
- Validates the merge by checking row counts and confirming each change type produced the correct result
- Uses **Delta Lake Time Travel** (`VERSION AS OF`) to query the table's state before and after the merge, demonstrating Delta Lake's built-in audit history and rollback capability

**Key skills demonstrated:** CDC/upsert patterns, Delta Lake `MERGE INTO`, data versioning and time travel, SQL.

---

## Tech Stack
- **Compute:** Databricks (Free/Community Edition, Serverless)
- **Processing:** PySpark (DataFrame API, Spark SQL)
- **Storage format:** Delta Lake
- **Language:** Python, SQL

## How to Run
1. Import both notebooks into a Databricks workspace (Community Edition works)
2. Attach to a running cluster or serverless compute
3. Run `sales_etl_pipeline.ipynb` first (creates the base Delta tables)
4. Run `cdc_delta_pipeline.ipynb` second (reads from the tables created above)

## Author
Shivam Kumar
