# Retail Sales Analysis SQL Project

# Project Overview

## Project Title

Retail Sales Analysis

## Database

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

This project was completed entirely using **MySQL Workbench**.

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
| quantity       | INT       | Quantity purchased            |
| price_per_unit | INT       | Product price per unit        |
| cogs           | INT       | Cost of goods sold            |
| total_sale     | INT       | Total transaction amount      |

---

# Project Structure

## 1. Database Setup

### Create Database

```sql
CREATE DATABASE sales_db;
USE sales_db;
```

### Create Table

```sql
CREATE TABLE sales (
    transaction_id INT,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender TEXT,
    age INT,
    category TEXT,
    quantity INT,
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

## View Dataset

```sql
SELECT *
FROM sales
LIMIT 10;
```

---

## Check Total Records

```sql
SELECT COUNT(*) AS total_records
FROM sales;
```

### Finding

* Total Records: **2001**

---

## Check NULL Values

```sql
SELECT *
FROM sales
WHERE sale_date IS NULL
   OR sale_time IS NULL
   OR customer_id IS NULL
   OR gender IS NULL
   OR age IS NULL
   OR category IS NULL
   OR quantity IS NULL
   OR total_sale IS NULL;
```

### Finding

* No NULL values found.

---

## Check Duplicate Transactions

```sql
SELECT transaction_id,
       COUNT(*) AS duplicate_count
FROM sales
GROUP BY transaction_id
HAVING COUNT(*) > 1;
```

### Finding

* No duplicate transaction IDs found.

---

## Check Invalid Values

```sql
SELECT *
FROM sales
WHERE quantity < 0
   OR total_sale < 0
   OR age < 0
   OR age > 100;
```

### Finding

* No invalid quantity, sales, or age values found.

---

## Convert Date & Time Columns

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

# Section A — Overall Business Overview

This section focuses on overall business performance metrics.

---

## Business Questions

* What is the total revenue?
* How many products were sold?
* What is the average transaction value?
* How many unique customers exist?
* What is the revenue range?

---

## Total Revenue

```sql
SELECT SUM(total_sale) AS total_revenue
FROM sales;
```

### Finding

* Total Revenue: **908,230**

---

## Total Quantity Sold

```sql
SELECT SUM(quantity) AS total_quantity_sold
FROM sales;
```

### Finding

* Total Quantity Sold: **4,995**

---

## Average Transaction Value

```sql
SELECT AVG(total_sale) AS avg_transaction_value
FROM sales;
```

### Finding

* Average Transaction Value: **457**

---

## Average Cost of Goods Sold

```sql
SELECT AVG(cogs) AS avg_cogs
FROM sales;
```

### Finding

* Average COGS: **95.17**

---

## Unique Customers

```sql
SELECT COUNT(DISTINCT customer_id) AS unique_customers
FROM sales;
```

### Finding

* Unique Customers: **155**

---

## Revenue Range

```sql
SELECT MIN(total_sale) AS minimum_sale,
       MAX(total_sale) AS maximum_sale
FROM sales;
```

### Finding

* Lowest Sale: **25**
* Highest Sale: **2000**

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

### Key Finding

* Sales increased significantly during months **9–12**, showing strong seasonal demand.

---

## Sales by Day

```sql
SELECT EXTRACT(DAY FROM sale_date) AS day,
       SUM(total_sale) AS revenue
FROM sales
GROUP BY day
ORDER BY day;
```

---

## Sales by Hour

```sql
SELECT EXTRACT(HOUR FROM sale_time) AS hour,
       SUM(total_sale) AS revenue
FROM sales
GROUP BY hour
ORDER BY hour;
```

---

## Highest Revenue Date

```sql
WITH daily_sales AS (
    SELECT sale_date,
           SUM(total_sale) AS revenue
    FROM sales
    GROUP BY sale_date
)

SELECT *
FROM daily_sales
WHERE revenue = (
    SELECT MAX(revenue)
    FROM daily_sales
);
```

### Finding

* Highest Revenue Date: **2022-10-10**
* Revenue Generated: **8,500**

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

### Finding

* Peak Sales Hour: **19:00**
* Revenue Generated: **109,460**

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

### Findings

| Category    | Revenue |
| ----------- | ------- |
| Electronics | 311,445 |
| Clothing    | 309,995 |
| Beauty      | 286,790 |

---

## Quantity Sold by Category

```sql
SELECT category,
       SUM(quantity) AS quantity_sold
FROM sales
GROUP BY category;
```

### Findings

| Category    | Quantity Sold |
| ----------- | ------------- |
| Clothing    | 1,780         |
| Electronics | 1,682         |
| Beauty      | 1,533         |

---

## Best Performing Category

```sql
WITH category_sales AS (
    SELECT category,
           SUM(total_sale) AS revenue
    FROM sales
    GROUP BY category
)

SELECT *
FROM category_sales
WHERE revenue = (
    SELECT MAX(revenue)
    FROM category_sales
);
```

### Finding

* Electronics generated the highest revenue.

---

## Lowest Performing Category

```sql
WITH category_sales AS (
    SELECT category,
           SUM(total_sale) AS revenue
    FROM sales
    GROUP BY category
)

SELECT *
FROM category_sales
WHERE revenue = (
    SELECT MIN(revenue)
    FROM category_sales
);
```

### Finding

* Beauty generated the lowest revenue.

---

## Average Transaction Value per Category

```sql
SELECT category,
       AVG(total_sale) AS avg_transaction_value
FROM sales
GROUP BY category;
```

---

## Category Contribution Percentage

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

### Key Insight

* Revenue contribution was balanced across all categories.

---

## Profit by Category

```sql
WITH category_profit AS (
    SELECT category,
           SUM(total_sale - cogs) AS profit
    FROM sales
    GROUP BY category
)

SELECT *
FROM category_profit
ORDER BY profit DESC;
```

### Finding

* Clothing generated the highest profit.

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

## Top 10 Customers by Revenue

```sql
SELECT customer_id,
       SUM(total_sale) AS total_spending
FROM sales
GROUP BY customer_id
ORDER BY total_spending DESC
LIMIT 10;
```

### Insight

* These customers can be targeted using loyalty programs and personalized offers.

---

## Repeat Customers

```sql
SELECT customer_id,
       COUNT(*) AS purchase_count
FROM sales
GROUP BY customer_id
HAVING COUNT(*) > 1
ORDER BY purchase_count DESC;
```

### Insight

* Repeat customers indicate strong customer retention.

---

## Customer Purchase Frequency

```sql
SELECT customer_id,
       COUNT(*) AS number_of_transactions
FROM sales
GROUP BY customer_id
ORDER BY number_of_transactions DESC;
```

---

## Average Spend per Customer

```sql
SELECT customer_id,
       AVG(total_sale) AS avg_customer_spending
FROM sales
GROUP BY customer_id
ORDER BY avg_customer_spending DESC;
```

---

## Gender-wise Revenue

```sql
SELECT gender,
       SUM(total_sale) AS revenue
FROM sales
GROUP BY gender;
```

### Finding

| Gender | Revenue |
| ------ | ------- |
| Male   | 445,120 |
| Female | 463,110 |

### Insight

* Revenue contribution between male and female customers is almost equal.

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

### Findings

* Gen-X and Gen-W customers generated the highest revenue.
* Gen-Z customers contributed the lowest revenue.

---

# Section E — Advanced Business Insights

This section focuses on deeper business analysis.

---

## Monthly Growth Trend

```sql
SELECT EXTRACT(YEAR FROM sale_date) AS year,
       EXTRACT(MONTH FROM sale_date) AS month,
       SUM(total_sale) AS revenue
FROM sales
GROUP BY year, month
ORDER BY year, month;
```

### Insight

* Revenue showed a strong upward trend during the final months of the year.

---

## Best-Selling Hour per Category

```sql
WITH hourly_category_sales AS (
    SELECT category,
           EXTRACT(HOUR FROM sale_time) AS hour,
           SUM(total_sale) AS revenue
    FROM sales
    GROUP BY category, hour
),

ranked_sales AS (
    SELECT *,
           DENSE_RANK() OVER(
               PARTITION BY category
               ORDER BY revenue DESC
           ) AS ranking
    FROM hourly_category_sales
)

SELECT *
FROM ranked_sales
WHERE ranking = 1;
```

### Insight

* Most categories achieved maximum sales between **19:00–20:00**.

---

## High-Value Transactions

```sql
SELECT *
FROM sales
WHERE total_sale > (
    SELECT AVG(total_sale)
    FROM sales
);
```

### Insight

* High-value transactions help identify premium purchasing behavior.

---

## Customers Spending Above Average

```sql
WITH customer_spending AS (
    SELECT customer_id,
           SUM(total_sale) AS spending
    FROM sales
    GROUP BY customer_id
),

average_spending AS (
    SELECT AVG(spending) AS avg_spending
    FROM customer_spending
)

SELECT *
FROM customer_spending
WHERE spending > (
    SELECT avg_spending
    FROM average_spending
);
```

### Insight

* These customers can be targeted as high-value buyers.

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
FROM customer_segments
ORDER BY spending DESC;
```

---

# Final Business Insights

# Top 5 Insights

## 1. Strong Seasonal Sales Pattern

Sales increased heavily during months 9–12, indicating strong seasonal demand.

## 2. Evening Hours Generate Maximum Revenue

Most purchases occur between 19:00–20:00.

## 3. Balanced Category Performance

All categories contributed almost equally to total revenue.

## 4. Older Customers Spend More

Gen-X and Gen-W customers generated significantly more revenue.

## 5. Gender Contribution is Balanced

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






