# Retail Sales Analysis SQL Project

# Project Overview

### Project Title:

Retail Sales Analysis

### Database:

`sales_db`

This project demonstrates practical SQL skills used in real-world retail and ecommerce data analysis. The project focuses on understanding customer behavior, analyzing sales performance, identifying business trends, and generating actionable insights using SQL.

The analysis includes:

* Database exploration
* Data cleaning
* Exploratory Data Analysis (EDA)
* Time-based sales analysis
* Customer behavior analysis
* Product/category performance analysis
* Advanced business insights
* Business recommendations

This project was completed entirely using MySQL.

---

# Objectives

The main objectives of this project are:

* Understand retail transaction data
* Clean and validate raw data
* Analyze customer purchasing behavior
* Identify top-performing product categories
* Detect seasonal sales trends
* Discover peak business hours
* Segment customers based on spending
* Generate business insights for decision-making

---

# Dataset Information

The dataset contains retail sales transaction records.

## Table Name

`sales`

## Columns Description

| Column Name    | Data Type | Description                   |
| -------------- | --------- | ----------------------------- |
| transaction_id | INT       | Unique transaction identifier |
| sale_date      | DATE      | Date of transaction           |
| sale_time      | TIME      | Time of transaction           |
| customer_id    | INT       | Unique customer identifier    |
| gender         | TEXT      | Gender of customer            |
| age            | INT       | Customer age                  |
| category       | TEXT      | Product category              |
| quantiy        | INT       | Quantity purchased            |
| price_per_unit | INT       | Product price per unit        |
| cogs           | INT       | Cost of goods sold            |
| total_sale     | INT       | Total transaction amount      |

---

# Project Structure

## 1. Database Setup

### Database Creation

```sql
CREATE DATABASE sales_db;
USE sales_db;
```

### Table Creation

```sql
CREATE TABLE sales (
    transaction_id INT,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender TEXT,
    age INT,
    category TEXT,
    quantiy INT,
    price_per_unit INT,
    cogs INT,
    total_sale INT
);
```

---

# 2. Data Understanding & Cleaning

The dataset was inspected and cleaned before analysis.

## Tasks Performed

* Checked total number of records
* Verified NULL values
* Checked duplicate transactions
* Validated negative sales and quantities
* Validated age values
* Converted date/time columns into proper SQL formats

---

## Data Cleaning Queries

### Check Total Records

```sql
SELECT COUNT(*)
FROM sales;
```

### Check NULL Values

```sql
SELECT *
FROM sales
WHERE sale_date IS NULL
   OR sale_time IS NULL
   OR customer_id IS NULL
   OR gender IS NULL
   OR age IS NULL
   OR category IS NULL
   OR quantiy IS NULL
   OR total_sale IS NULL;
```

### Check Duplicate Transactions

```sql
SELECT transaction_id, COUNT(*)
FROM sales
GROUP BY transaction_id
HAVING COUNT(*) > 1;
```

### Check Invalid Values

```sql
SELECT *
FROM sales
WHERE quantiy < 0
   OR total_sale < 0
   OR age < 0
   OR age > 100;
```

### Convert Date & Time Columns

```sql
UPDATE sales
SET sale_date = STR_TO_DATE(sale_date, '%Y-%m-%d');

UPDATE sales
SET sale_time = STR_TO_DATE(sale_time, '%H:%i:%s');

ALTER TABLE sales
MODIFY COLUMN sale_date DATE,
MODIFY COLUMN sale_time TIME;
```

---

# 3. Exploratory Data Analysis (EDA)

---

# Section A — Overall Business Overview

## Business Questions

* What is the total revenue?
* How many products were sold?
* What is the average transaction value?
* How many unique customers exist?
* What is the revenue range?

---

## Key SQL Queries

### Total Revenue

```sql
SELECT SUM(total_sale) AS total_revenue
FROM sales;
```

### Total Quantity Sold

```sql
SELECT SUM(quantiy) AS total_quantity
FROM sales;
```

### Average Transaction Value

```sql
SELECT AVG(total_sale) AS avg_transaction_value
FROM sales;
```

### Unique Customers

```sql
SELECT COUNT(DISTINCT customer_id)
FROM sales;
```

---

## Key Findings

* Total Revenue: **908,230**
* Total Quantity Sold: **4,995**
* Average Transaction Value: **457**
* Unique Customers: **155**

---

# Section B — Time-Based Analysis

This section analyzes sales trends across months, days, and hours.

---

## Business Questions

* Which month generates the highest revenue?
* Which day performs best?
* What are the peak sales hours?
* Is the business seasonal?

---

## Sales by Month

```sql
SELECT EXTRACT(MONTH FROM sale_date) AS month,
       SUM(total_sale) AS revenue
FROM sales
GROUP BY month
ORDER BY month;
```

---

## Peak Sales Hour

```sql
WITH hourly_sales AS (
    SELECT EXTRACT(HOUR FROM sale_time) AS hour,
           SUM(total_sale) AS revenue
    FROM sales
    GROUP BY hour
)
SELECT *
FROM hourly_sales
WHERE revenue = (
    SELECT MAX(revenue)
    FROM hourly_sales
);
```

---

## Key Findings

* Sales increased significantly during months **9–12**
* Peak business hours were between **19:00–20:00**
* Highest revenue date was **2022-10-10**

---

# Section C — Product & Category Analysis

This section analyzes product performance and category contribution.

---

## Business Questions

* Which category generates the highest revenue?
* Which category sells the highest quantity?
* Which category contributes most to profit?
* How much does each category contribute to total sales?

---

## Revenue by Category

```sql
SELECT category,
       SUM(total_sale) AS revenue
FROM sales
GROUP BY category;
```

---

## Category Contribution %

```sql
SET @total_revenue = (
    SELECT SUM(total_sale)
    FROM sales
);

WITH category_sales AS (
    SELECT category,
           SUM(total_sale) AS revenue
    FROM sales
    GROUP BY category
)
SELECT category,
       revenue,
       ROUND((revenue / @total_revenue) * 100, 2) AS contribution_percent
FROM category_sales;
```

---

## Key Findings

| Category    | Revenue |
| ----------- | ------- |
| Electronics | 311,445 |
| Clothing    | 309,995 |
| Beauty      | 286,790 |

### Insights

* Electronics generated the highest revenue
* Clothing generated the highest profit
* Revenue contribution was balanced across categories

---

# Section D — Customer Analysis

This section focuses on customer behavior and spending patterns.

---

## Business Questions

* Who are the top customers?
* Which customers purchase repeatedly?
* Which age groups spend the most?
* How does revenue differ by gender?

---

## Top Customers

```sql
SELECT customer_id,
       SUM(total_sale) AS total_spending
FROM sales
GROUP BY customer_id
ORDER BY total_spending DESC
LIMIT 10;
```

---

## Gender-wise Revenue

```sql
SELECT gender,
       SUM(total_sale) AS revenue
FROM sales
GROUP BY gender;
```

---

## Age Group Segmentation

```sql
WITH age_groups AS (
    SELECT *,
           CASE
               WHEN age BETWEEN 18 AND 25 THEN 'Gen-Z'
               WHEN age BETWEEN 26 AND 35 THEN 'Gen-Y'
               WHEN age BETWEEN 36 AND 50 THEN 'Gen-X'
               ELSE 'Gen-W'
           END AS age_group
    FROM sales
)
SELECT age_group,
       SUM(total_sale) AS revenue
FROM age_groups
GROUP BY age_group;
```

---

## Key Findings

* Male and female revenue contribution was nearly equal
* Gen-X and Gen-W customers generated the highest revenue
* Gen-Z customers contributed the lowest revenue

---

# Section E — Advanced Business Insights

This section focuses on deeper business analysis.

---

## Advanced Analysis Performed

* Monthly growth trend
* Best-selling hour per category
* High-value transactions
* Customer segmentation
* Spending behavior analysis

---

## Revenue Segmentation

```sql
WITH customer_segments AS (
    SELECT customer_id,
           SUM(total_sale) AS spending
    FROM sales
    GROUP BY customer_id
)
SELECT *,
       CASE
           WHEN spending >= 30000 THEN 'High Spender'
           WHEN spending BETWEEN 15000 AND 29999 THEN 'Medium Spender'
           ELSE 'Low Spender'
       END AS customer_segment
FROM customer_segments;
```

---

# Final Business Insights

## Top 5 Insights

### 1. Strong Seasonal Sales Pattern

Sales increased heavily during months 9–12, indicating strong seasonal demand.

### 2. Evening Hours Generate Maximum Revenue

Most purchases occur between 19:00–20:00.

### 3. Balanced Category Performance

All categories contributed almost equally to total revenue.

### 4. Older Customers Spend More

Gen-X and Gen-W customers generated significantly more revenue.

### 5. Gender Contribution is Balanced

Revenue contribution from male and female customers was nearly equal.

---

# Business Recommendations

## 1. Increase Inventory During Peak Months

Prepare inventory and marketing campaigns for months 9–12.

## 2. Run Evening Promotions

Focus advertisements and flash sales during 19:00–20:00.

## 3. Target High-Spending Age Groups

Create personalized campaigns for Gen-X and Gen-W customers.

## 4. Improve Customer Retention

Offer loyalty rewards to repeat and high-value customers.

## 5. Maintain Balanced Category Inventory

Since all categories perform similarly, inventory should remain balanced.

---

# Conclusion

This project demonstrates practical SQL skills used in retail and ecommerce analytics.

The project covers:

* Data cleaning
* SQL aggregations
* CTEs
* Window functions
* Business KPI analysis
* Customer segmentation
* Time-series analysis
* Business recommendation generation

This project helped transform raw sales data into meaningful business insights that can support strategic decision-making.

---

# How to Use

1. Clone the repository
2. Import dataset into MySQL
3. Run database setup queries
4. Execute analysis queries
5. Explore and modify queries for deeper analysis

---

# Author

## Anish Tiwari



