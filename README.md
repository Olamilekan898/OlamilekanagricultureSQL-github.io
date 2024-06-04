
                                    Agricultural Data Analysis

In This document, I provides SQL queries for analyzing agricultural sales data. Below are various queries and their explanations.

## Initial Data Selection

-- Select all records from the agro table

SELECT * FROM agricultural.agro;

-- Select all records from the agro table

SELECT * FROM agro;

-- Update date format to standard date format

UPDATE agro
SET date = STR_TO_DATE(date, "%m/%d/%Y");

-- Alter table to modify date column to DATE type

ALTER TABLE agro
MODIFY date DATE;


## Sales Analysis by Product

-- Calculate total sales by product name

SELECT product_name, ROUND(SUM(unit_sold * unit_price), 2) AS total_sales
FROM agro
GROUP BY product_name
ORDER BY total_sales DESC;


## Sales Analysis by Company

-- Calculate total sales by company

SELECT company, ROUND(SUM(unit_sold * unit_price), 2) AS total_sales
FROM agro
GROUP BY company
ORDER BY company DESC;

-- Get the company with the highest total sales

SELECT company, ROUND(SUM(unit_sold * unit_price), 2) AS total_sales
FROM agro
GROUP BY company
ORDER BY total_sales DESC
LIMIT 1;


## Sales Analysis by Export Country and Product

-- Calculate total sales by export country and product name

SELECT export_country, product_name, ROUND(SUM(unit_sold * unit_price), 2) AS total_sales
FROM agro
GROUP BY export_country, product_name
ORDER BY export_country ASC, total_sales DESC;

-- Calculate total sales by export country

SELECT export_country, ROUND(SUM(unit_sold * unit_price), 2) AS total_sales
FROM agro
GROUP BY export_country
ORDER BY total_sales DESC;

-- Calculate average sales by export country

SELECT export_country, ROUND(AVG(unit_sold * unit_price), 2) AS AVG_sales
FROM agro
GROUP BY export_country
ORDER BY AVG_sales DESC;


## Order and Quantity Analysis

-- Calculate the total number of orders

SELECT SUM(unit_sold) AS Total_order
FROM agro;

-- Calculate the total number of units sold by export country

SELECT export_country, SUM(unit_sold)
FROM agro
GROUP BY export_country;

-- Calculate the total quantity sold and total profit by export country

SELECT export_country, SUM(unit_sold) AS Qty_Sold, ROUND(SUM(unit_sold * profit_per_unit), 2) AS total_profit
FROM agro
GROUP BY export_country;


## Revenue Analysis by Year, Month, and Quarter

-- Calculate yearly revenue

SELECT YEAR(date) AS year_, ROUND(SUM(unit_sold * unit_price), 2) AS revenue
FROM agro
GROUP BY year_
ORDER BY year_ DESC;

-- Calculate monthly revenue

SELECT YEAR(date) AS year_, MONTHNAME(date) AS month_, SUM(unit_sold * unit_price) AS revenue
FROM agro
GROUP BY year_, month_
ORDER BY year_, MONTH(date);

-- Calculate quarterly revenue

SELECT YEAR(date) AS year_, CONCAT('Qtr', QUARTER(date)) AS Qtr, ROUND(SUM(unit_sold * unit_price), 2) AS revenue
FROM agro
GROUP BY year_, Qtr
ORDER BY year_, Qtr ASC;


## Cost and Profit Analysis

-- Calculate total cost by product name

SELECT product_name, revenue, profit_, ROUND((revenue - profit_), 2) AS total_cost
FROM (
    SELECT product_name, ROUND(SUM(unit_sold * unit_price), 2) AS revenue, ROUND(SUM(unit_sold * profit_per_unit), 2) AS profit_
    FROM agro
    GROUP BY product_name
) AS sub;

-- Calculate percentage of total sales by product name

SELECT product_name, revenue, total_sales, ROUND((revenue / total_sales) * 100, 2) AS perc
FROM (
    SELECT product_name, ROUND(SUM(unit_sold * unit_price), 2) AS revenue, (SELECT ROUND(SUM(unit_sold * unit_price), 2) FROM agro) AS total_sales
    FROM agro
    GROUP BY product_name
) AS sub;


## Window Functions for Ranking and Sales Comparison

-- Rank products by total sales using window function

SELECT product_name, total_sales, Rank_
FROM (
    SELECT product_name, SUM(unit_sold * unit_price) AS total_sales, RANK() OVER (PARTITION BY product_name ORDER BY SUM(unit_sold * unit_price) DESC) AS Rank_
    FROM agro
    GROUP BY product_name
) AS sub
WHERE rank_ = 1;

-- Calculate total sales and previous year's sales using LAG

SELECT YEAR(date) AS year_, SUM(unit_sold * unit_price) AS total_sales, LAG(SUM(unit_sold * unit_price)) OVER (ORDER BY YEAR(date) ASC) AS prev_year
FROM agro
GROUP BY year_;

-- Calculate sales difference by product using LEAD function

SELECT year_, total_sales, next_year, ROUND((total_sales - next_year), 2)
FROM (
    SELECT YEAR(date) AS year_, SUM(unit_sold * unit_price) AS total_sales, LEAD(SUM(unit_sold * unit_price)) OVER (ORDER BY YEAR(date) ASC) AS next_year
    FROM agro
    GROUP BY year_
) AS sub;

-- Compare sales using both LAG and LEAD functions

SELECT year_, total_sales, prev_year, next_year, ROUND((total_sales - next_year), 2), ROUND((total_sales - prev_year), 2)
FROM (
    SELECT YEAR(date) AS year_, ROUND(SUM(unit_sold * unit_price), 2) AS total_sales, LEAD(SUM(unit_sold * unit_price)) OVER (ORDER BY YEAR(date) ASC) AS next_year, LAG(SUM(unit_sold * unit_price)) OVER (ORDER BY YEAR(date) ASC) AS prev_year
    FROM agro
    GROUP BY year_
) AS sub;


## Detailed Sales Comparison Using LAG and LEAD

-- Sales comparison using LAG and LEAD functions by product and date

SELECT 
    product_name,
    date,
    unit_sold,
    unit_price,
    ROUND(unit_sold * unit_price, 2) AS total_sales,
    LAG(ROUND(unit_sold * unit_price, 2)) OVER (PARTITION BY product_name ORDER BY date) AS previous_sales,
    LEAD(ROUND(unit_sold * unit_price, 2)) OVER (PARTITION BY product_name ORDER BY date) AS next_sales,
    ROUND(ROUND(unit_sold * unit_price, 2) - LAG(ROUND(unit_sold * unit_price, 2)) OVER (PARTITION BY product_name ORDER BY date), 2) AS diff_with_previous,
    ROUND(LEAD(ROUND(unit_sold * unit_price, 2)) OVER (PARTITION BY product_name ORDER BY date) - ROUND(unit_sold * unit_price, 2), 2) AS diff_with_next
FROM agro;


## Conclusion

This repository contains a comprehensive set of SQL queries designed to perform in-depth analysis of agricultural sales data. These queries cover various aspects of data analysis, including sales, profit, order quantities, and revenue across different dimensions like product, company, and export country. Additionally, advanced SQL techniques such as window functions, LAG, and LEAD are used to provide detailed insights and comparisons.

## Contact Information

For any questions, feedback, or contributions, please feel free to reach out to me:

- **Email**: toheebrasaq999@gmail.com
- **LinkedIn**: [Toheeb Rasaq](https://www.linkedin.com/in/toheeb-rasaq-292aaa265/)

## Contributions

I welcome feedback and contributions to enhance this project. Please feel free to open issues or submit pull requests.
