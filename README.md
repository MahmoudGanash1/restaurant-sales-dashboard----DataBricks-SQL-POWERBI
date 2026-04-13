

# 🍽️ Restaurant Sales Analytics Platform

> **End-to-End Data Analysis Project**
> Transforming **1.5GB+ raw data (11M+ records)** into a clean, analytics-ready dataset powering business insights.

---

## 🧭 Problem Statement

Restaurant data was distributed across multiple files and formats (**CSV & JSON**), with inconsistent schemas and data types.

This created key challenges:

* ❌ Inability to directly merge datasets
* ❌ Schema mismatches (DATE vs TIMESTAMP, numeric inconsistencies)
* ❌ Large data volume (~1.5GB) not suitable for traditional tools

---

## 🎯 Objective

Build a **scalable and reliable data pipeline** to:

* Consolidate multi-source data
* Standardize schema across datasets
* Ensure data integrity (no loss / duplication)
* Prepare a single source of truth for BI reporting

---

## 🏗️ Architecture Overview

```
Raw Data (CSV + JSON)
        ↓
Databricks (Processing Layer)
        ↓
Delta Table (restaurant_final)
        ↓
Power BI (Visualization Layer)
```

---

## 🧰 Tech Stack

| Layer           | Tool           |
| --------------- | -------------- |
| Data Processing | Databricks ⚡   |
| Storage         | Delta Lake 🗄️ |
| Transformation  | SQL 🧠         |
| Visualization   | Power BI 📊    |

---

## 📁 Data Sources

| Type | Files Count | Description                 |
| ---- | ----------- | --------------------------- |
| CSV  | 7           | Structured transaction data |
| JSON | 2           | Semi-structured records     |

---

## 🔍 Data Exploration

Initial schema inspection:

```sql
DESCRIBE restaurant;
DESCRIBE restaurant_json;
```

### Findings:

* Inconsistent column data types across sources
* Same columns with different formats
* Potential merge conflicts identified early

---

## 🔢 Data Validation (Pre-Merge)

```sql
SELECT COUNT(*) FROM restaurant;       -- 9,310,000
SELECT COUNT(*) FROM restaurant_json;  -- 1,800,000
```

✔ Defined expected total: **11,110,000 records**

---

## ⚠️ Integration Failure

Initial merge attempt:

```sql
SELECT * FROM restaurant
UNION ALL
SELECT * FROM restaurant_json;
```

### ❌ Error:

```
INCOMPATIBLE_COLUMN_TYPE
(TIMESTAMP vs DOUBLE)
```

### Root Cause:

* Schema mismatch across datasets
* Critical column conflicts (`order_date`, numeric fields)

---

## 🛠️ Solution: Schema Standardization

Implemented controlled transformation during merge:

```sql
CREATE TABLE restaurant_final 
USING DELTA
AS 
SELECT
    order_id,
    CAST(order_date AS TIMESTAMP) AS order_date,
    hour,
    category,
    item_name,
    price,
    quantity,
    discount,
    total_amount,
    branch,
    payment_method,
    order_type,
    customer_id,
    rating,  
    is_weekend
FROM restaurant

UNION ALL    

SELECT
    order_id,
    order_date,
    hour,
    category,
    item_name,
    price,
    quantity,
    discount,
    total_amount,
    branch,
    payment_method,
    order_type,
    customer_id,
    rating,  
    is_weekend 
FROM restaurant_json;
```

---

## ✅ Data Validation (Post-Merge)

```sql
SELECT COUNT(*) FROM restaurant_final;
-- 11,110,000
```

✔ Full data successfully merged
✔ No data loss
✔ Data integrity preserved

---

## 🧩 Feature Engineering

Enhanced dataset with time-based attributes:

```sql
ALTER TABLE restaurant_final
ADD COLUMNS (
  year INT,
  month INT,
  day_name STRING
);
```

```sql
UPDATE restaurant_final
SET
  year = YEAR(order_date),
  month = MONTH(order_date),
  day_name = DATE_FORMAT(order_date, 'EEEE');
```

### Impact:

* Enables time-series analysis
* Supports business reporting (monthly / weekly trends)

---

## 📊 Output Dataset

**Final Table:** `restaurant_final`

✔ Clean
✔ Unified schema
✔ Analytics-ready
✔ Optimized for BI tools

---

## 🚀 Business Impact

* 📈 Reliable single source of truth
* ⚡ Faster analytics & reporting
* 🧠 Better decision-making support
* 📊 Seamless integration with Power BI

---

## 🔗 Next Step

Building an interactive **Power BI Dashboard** including:

* Revenue Performance
* Order Trends
* Category Insights
* Branch Analysis
* Time-based KPIs

---

## 👨‍💻 Author

**Mahmoud Ganash**

---

## ⭐ Support

If you found this project useful, consider giving it a ⭐
