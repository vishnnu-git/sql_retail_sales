# sql_retail_sales
--SQL Retail Sales Analysis -P1
CREATE DATABASE sql_project_p1

--CREATE TABLE
CREATE TABLE retail_sales
		(
		transactions_id INT PRIMARY KEY,
    	sale_date DATE,	
    	sale_time TIME,
    	customer_id INT,	
    	gender VARCHAR(10),
    	age INT,
    	category VARCHAR(35),
    	quantiy INT,
    	price_per_unit FLOAT,	
    	cogs FLOAT,
    	total_sale FLOAT
		)
--AlTER Table
ALTER TABLE retail_sales RENAME COLUMN quantiy TO quantity;


--Data Exploration & Cleaning

SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;


--Data Analysing and Business key problems and answers 

--1.Write a SQL query to retrieve all columns for sales made on '2022-11-05:

SELECT * FROM retail_sales
WHERE sale_date ='2022-11-05'

--2.Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than oe equal to 4 in the month of Nov-2022:

SELECT *
FROM 
	retail_sales
WHERE
	category ='Clothing'
	AND
	TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
	AND
	quantity >= 4;

--3.Write a SQL query to calculate the total sales (total_sale) for each category.

SELECT 
	category,
	SUM(total_sale) as total_sales,
	COUNT(*) as total_orders
FROM 
	retail_sales
GROUP BY
	category;

--4.Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.:

SELECT 
	ROUND(AVG(age),2) as avg_age,
	category
FROM
	retail_sales
WHERE
	category ='Beauty'
GROUP BY
	category;

--5.Write a SQL query to find all transactions where the total_sale is greater than 1000.

SELECT * FROM retail_sales
WHERE total_sale > 1000;

--6.Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.:

SELECT
	category,
	COUNT(transactions_id) as total_transactions,
	gender
FROM 
	retail_sales
GROUP BY
	category , gender
ORDER BY
	gender



--7.Write a SQL query to calculate the average sale for each month. Find out best selling month in each year:

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


--8.Write a SQL query to find the top 5 customers based on the highest total sales.
SELECT 
	customer_id, 
	SUM(total_sale) as total_sale 
FROM 
	retail_sales
GROUP BY 
	customer_id
ORDER BY
	total_sale DESC
LIMIT 5;

--9.Write a SQL query to find the number of unique customers who purchased items from each category.:

SELECT
	COUNT(DISTINCT customer_id),
	category
FROM 
	retail_sales
GROUP BY
	category

--10.Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17):
WITH hourly_sale
AS(
SELECT *,
	CASE
		WHEN EXTRACT(HOUR FROM sale_time )<12 THEN 'Morning'
		WHEN EXTRACT(HOUR FROM sale_time ) BETWEEN 12 AND 17 THEN 'Afternoon'
		ELSE 'Evening'
	END as shift
FROM retail_sales
)
SELECT 
	shift ,
	COUNT(transactions_id) as total_transaction
FROM 
	hourly_sale
GROUP BY
	shift

--End of project
