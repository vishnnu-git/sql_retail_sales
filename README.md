# SQL Retail Sales Analysis - P1

## Project Overview
This project involves analyzing retail sales data using SQL. The dataset includes transactional details such as sales date, time, customer demographics, product categories, and sales amounts. The analysis covers data cleaning, exploration, and business insights to derive meaningful conclusions from the sales data.

## Database and Table Creation
```sql
CREATE DATABASE sql_project_p1;

CREATE TABLE retail_sales (
    transactions_id INT PRIMARY KEY,
    sale_date DATE,    
    sale_time TIME,
    customer_id INT,    
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,    
    cogs FLOAT,
    total_sale FLOAT
);
```

### Table Alteration
```sql
ALTER TABLE retail_sales RENAME COLUMN quantiy TO quantity;
```

## Data Exploration & Cleaning
```sql
-- Record Count: Determine the total number of records in the dataset
SELECT COUNT(*) AS total_records FROM retail_sales;

-- Customer Count: Find out how many unique customers are in the dataset
SELECT COUNT(DISTINCT customer_id) AS unique_customers FROM retail_sales;

-- Category Count: Identify all unique product categories in the dataset
SELECT DISTINCT category FROM retail_sales;

-- Null Value Check: Check for any null values in the dataset
SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

-- Delete records with missing data
DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

## Data Analysis & Business Insights
### 1. Retrieve all sales made on '2022-11-05'
```sql
SELECT * FROM retail_sales
WHERE sale_date = '2022-11-05';
```

### 2. Retrieve all transactions where the category is 'Clothing' and quantity sold is ≥ 4 in Nov 2022
```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
    AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND quantity >= 4;
```

### 3. Calculate total sales for each category
```sql
SELECT 
    category,
    SUM(total_sale) AS total_sales,
    COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

### 4. Find the average age of customers who purchased 'Beauty' category items
```sql
SELECT 
    ROUND(AVG(age), 2) AS avg_age,
    category
FROM retail_sales
WHERE category = 'Beauty'
GROUP BY category;
```

### 5. Find transactions where total_sale > 1000
```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000;
```

### 6. Total transactions by gender for each category
```sql
SELECT
    category,
    COUNT(transactions_id) AS total_transactions,
    gender
FROM retail_sales
GROUP BY category, gender
ORDER BY gender;
```

### 7. Calculate average sale for each month and find best-selling month
```sql
SELECT DISTINCT ON (year) 
    year, 
    month, 
    avg_sale
FROM (
    SELECT 
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale
    FROM retail_sales
    GROUP BY 1, 2
    ORDER BY 1, 3 DESC
) subquery
ORDER BY year;
```

### 8. Find the top 5 customers based on total sales
```sql
SELECT 
    customer_id, 
    SUM(total_sale) AS total_sale 
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sale DESC
LIMIT 5;
```

### 9. Count of unique customers per category
```sql
SELECT
    COUNT(DISTINCT customer_id) AS unique_customers,
    category
FROM retail_sales
GROUP BY category;
```

### 10. Sales shift analysis: Morning, Afternoon, Evening
```sql
WITH hourly_sale AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT 
    shift,
    COUNT(transactions_id) AS total_transactions
FROM hourly_sale
GROUP BY shift;
```

## Findings
- **Customer Demographics:** The dataset includes customers from various age groups, with sales distributed across different categories such as Clothing and Beauty.
- **High-Value Transactions:** Several transactions had a total sale amount greater than 1000, indicating premium purchases.
- **Sales Trends:** Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights:** The analysis identifies the top-spending customers and the most popular product categories.

## Reports
- **Sales Summary:** A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis:** Insights into sales trends across different months and shifts.
- **Customer Insights:** Reports on top customers and unique customer counts per category.

## Conclusion
This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance.

---
**End of Project**
