# 🍽️ Restaurant Sales Analytics Platform

> **End-to-End Data Analysis Project**
> Transforming 1.5GB+ raw data (11M+ records) into a clean, analytics-ready dataset powering business insights.

---

## 🧭 Problem Statement

Restaurant data was distributed across multiple files and formats (7 CSV files + 2 JSON files), with inconsistent schemas and data types.

This created key challenges:
- ❌ Inability to directly merge datasets
- ❌ Schema mismatches (DATE vs TIMESTAMP, numeric inconsistencies)
- ❌ Large data volume (~1.5GB) not suitable for traditional tools

---

## 🎯 Objective

Build a scalable and reliable data pipeline to:
- Consolidate multi-source data
- Standardize schema across datasets
- Ensure data integrity (no loss / duplication)
- Prepare a single source of truth for BI reporting

---

## 🏗️ Architecture Overview

Raw Data (CSV + JSON)
        ↓
Databricks (Processing Layer)
        ↓
Delta Table (restaurant_final)
        ↓
Power BI (Visualization Layer)

---

## 🧰 Tech Stack

- Data Processing: Databricks ⚡  
- Storage: Delta Lake 🗄️  
- Transformation: SQL 🧠  
- Visualization: Power BI 📊  

---

## 📁 Data Sources

- 7 CSV Files (Structured transaction data)  
- 2 JSON Files (Semi-structured records)  

---

## 🔍 Data Exploration

DESCRIBE restaurant;
DESCRIBE restaurant_json;

Findings:
- Inconsistent column data types across sources
- Same columns with different formats
- Potential merge conflicts identified early

---

## 🔢 Data Validation (Pre-Merge)

SELECT COUNT(*) FROM restaurant;       -- 9,310,000  
SELECT COUNT(*) FROM restaurant_json;  -- 1,800,000  

Expected Total: 11,110,000 records

---

## ⚠️ Integration Challenge

Initial merge attempt:
SELECT * FROM restaurant
UNION ALL
SELECT * FROM restaurant_json;

❌ Error:
INCOMPATIBLE_COLUMN_TYPE (TIMESTAMP vs DOUBLE)

---

## 🛠️ Solution: Schema Standardization

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

---

## ✅ Post Merge Validation

SELECT COUNT(*) FROM restaurant_final;
-- 11,110,000

✔ No data loss  
✔ Schema unified  
✔ Data integrity preserved  

---

## 🧩 Feature Engineering

ALTER TABLE restaurant_final
ADD COLUMNS (
  year INT,
  month INT,
  day_name STRING
);

UPDATE restaurant_final
SET
  year = YEAR(order_date),
  month = MONTH(order_date),
  day_name = DATE_FORMAT(order_date, 'EEEE');

---

## 📊 Power BI Dashboard

An interactive Power BI dashboard was built on top of the dataset.

---

## 🏠 Home (Landing Page + Navigation Hub)

The Home page acts as the central landing page of the dashboard.

It contains:
- 🎯 KPI Summary Cards
- 📊 Quick Overview Visuals
- 🔘 Navigation Buttons to all pages

### Navigation Structure:
- Overview Page  
- Products Analysis  
- Customers Analysis  
- Time Analysis  
- Payments & Orders  

👉 The Home page is designed as a **starting hub**, allowing users to navigate seamlessly across all analytical views.

---

## 📸 Dashboard Preview

- Home Page (Landing Page + Navigation)
- Overview Page
- Products Page
- Customers Page
- Time Analysis Page
- Payments & Orders Page

<img width="1278" height="713" alt="1" src="https://github.com/user-attachments/assets/e781f4c8-6a78-4745-8c61-93e2a73d739f" />

<img width="1277" height="718" alt="2" src="https://github.com/user-attachments/assets/be56457f-b8cd-4e7e-ad8a-c8c90e341362" />

<img width="1284" height="720" alt="3" src="https://github.com/user-attachments/assets/427aae7a-d752-499e-b1a7-ec0dd691ad4a" />

<img width="1278" height="720" alt="4" src="https://github.com/user-attachments/assets/52d8ec16-1bb5-4bd2-9593-43e5e08ab415" />


<img width="1276" height="712" alt="5" src="https://github.com/user-attachments/assets/4408c507-940f-430f-9f67-960734b82090" />


<img width="1279" height="717" alt="6" src="https://github.com/user-attachments/assets/e8b045e5-56b1-4c9c-8d6f-573112f7c849" />


---

## 📊 Key KPIs

- Total Revenue: $2.9B  
- Total Orders: 11M  
- Average Order Value: $260.87  
- Total Customers: 200K  
- Avg Orders per Customer: 56  

---

## 🧠 Measures (DAX)

### Revenue & Orders
- Total Revenue = SUM(total_amount)  
- Total Orders = COUNT(order_id)  
- Avg Order Value = AVERAGE(total_amount)  

### Customers
- Total Customers = DISTINCTCOUNT(customer_id)  
- Revenue per Customer = DIVIDE([Total Revenue], [Total Customers])  
- Orders per Customer = DIVIDE([Total Orders], [Total Customers])  

### Time Analysis
- MTD Revenue = TOTALMTD([Total Revenue], order_date) 
- MTD Orders  = TOTALMTD([Total Orders], order_date)
- MoM Growth %  

### Payments
- Cash Orders  = CALCULATE([Total Orders], payment_method="Cash")
- Card Orders  = CALCULATE([Total Orders], payment_method="Card")
- Wallet Orders = CALCULATE([Total Orders], payment_method="Wallet") 

### Order Types
- Dine-in Orders  
- Delivery Orders  
- Takeaway Orders

  <img width="377" height="571" alt="measures" src="https://github.com/user-attachments/assets/98c42275-343d-4c2e-9c7f-2726dd2bf88d" />

<img width="335" height="346" alt="measures 2" src="https://github.com/user-attachments/assets/07b360e1-d393-4c77-a19e-5c64993edd0f" />


---

## 🔍 Key Insights

- Revenue is heavily concentrated in Cairo  
- Grills category is the top revenue driver  
- Strong customer loyalty with high repeat orders  
- Peak activity occurs around 8 PM  
- Weekdays slightly outperform weekends  
- Cash is still the dominant payment method  

---

## 🎯 Business Recommendations

- Reduce dependency on Cairo branch  
- Expand high-performing categories  
- Improve discount effectiveness  
- Promote digital payment adoption  
- Optimize operations during peak hours  

---

## 🧠 Lessons Learned

- Large datasets require scalable tools like Databricks  
- Schema mismatches are a real-world challenge  
- Data validation is critical before analysis  
- Insights matter more than visuals  
- Performance optimization is key for BI systems  

---

## 🚀 Author

**Mahmoud Ganash**

---

## ⭐ If you like this project, give it a star!
