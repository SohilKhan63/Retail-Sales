# Retail-Sales
-- CREATE TABLE
DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales
	(
	transactions_id	INT NOT NULL PRIMARY KEY,
	sale_date	DATE,
	sale_time	TIME,
	customer_id	INT NOT NULL,
	gender	VARCHAR (50),
	age	INT,
	category VARCHAR (50),	
	quantiy INT,
	price_per_unit	FLOAT,
	cogs	FLOAT,
	total_sale FLOAT
	);

-- Q.1 Write a SQL query to retrieve all columns for sales made on '2022-11-05
	SELECT * FROM retail_sales
	WHERE sale_date = '2022-11-05';

-- Q.2 Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 10 in the month of Nov-2022
	SELECT * FROM retail_sales
	WHERE 
		category = 'Clothing'
	AND 
		TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
	AND
		quantiy >= 4;

-- Q.3 Write a SQL query to calculate the total sales (total_sale) for each category.
	SELECT 
		category,
		SUM(total_sale) AS Revenue
	FROM retail_sales
	GROUP BY 1;

-- Q.4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.
	SELECT 
		category,
		AVG(age)
	FROM retail_sales
	WHERE category = 'Beauty'
	GROUP BY 1;
	
-- Q.5 Write a SQL query to find all transactions where the total_sale is greater than 1000.
	SELECT * FROM retail_sales 
	WHERE total_sale > 1000;

-- Q.6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.
	SELECT 
		COUNT(transactions_id) AS Total, 
		gender,
		category
	FROM retail_sales
	GROUP BY 2,3 
	ORDER BY 1;

-- Q.7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year
	SELECT 
       Years,
       Months,
       AvgSale
	FROM 
		(
		SELECT 
			EXTRACT(MONTH FROM sale_date) AS Months,
			EXTRACT(YEAR FROM sale_date) AS Years,
			AVG(total_sale) AS AvgSale,
			RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS Ranks
		FROM retail_sales
		GROUP BY 1, 2
		) t1
	WHERE Ranks = 1
	ORDER BY 1,2 ;
	
-- Q.8 Write a SQL query to find the top 5 customers based on the highest total sales 
	SELECT 
		customer_id,
		SUM(total_sale) AS Revenue
	FROM retail_sales
	GROUP BY 1
	ORDER BY 2 DESC
	LIMIT 5;

-- Q.9 Write a SQL query to find the number of unique customers who purchased items from each category.
	SELECT 
    	category,    
    	COUNT(DISTINCT customer_id) as Single_sale_cust
	FROM retail_sales
	GROUP BY category;

-- Q.10 Write a SQL query to create each shift and number of orders (Example Morning <=12, Afternoon Between 12 & 17, Evening >17)
	WITH Hourly_table
	AS
		(
		SELECT *,
		CASE 
			WHEN EXTRACT ( HOUR FROM sale_time) < 12 THEN 'Morning'
			WHEN EXTRACT ( HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
			ELSE 'Evening'
		END AS Time_table
		FROM retail_sales
		)
	SELECT
		Time_table,
		COUNT(*) AS total_orders
	FROM Hourly_table
	GROUP BY Time_table
	ORDER BY total_orders DESC;
