# Northwind_project
This is all about my capstone project

### project Overview
Northwind Traders is a global supplier of specialty food products and this project intails helping Northwind optimize its operations by analyzing customer orders, product performance, shipping efficiency and employee management by building a database with the provided data and quering it to answer business questions and creating an intractive dashboard using any BI tool to derive actionable insight, answer critical business questions, identify opportunities for growth and efficiency.

### Business Requirement
Dashboard Requirement

❑ Key Metrics:

❑ Total Orders

❑ Total Revenue

❑ Average Order Value

❑ Shipping Efficiency (Average Shipping Time)

❑ Business Questions:

❑ Are there any noticeable sales trends over time?

❑ Which are the best and worst selling products?

❑ Can you identify any key customers?

❑ Are shipping costs consistent across providers?

❑ How fast is their delivery service, how many orders are delivered on time or late?

### Tools Used
- Excel: for data cleaning
- Structure Query Language(SQL): for querying the data
- Power Bi: for data visualizations

### Steps Followed
The dataset was in xls file so i converted it to a csv file first to enable me work on it effectively. Duplicate were removed then i created the needed table in SQL and imported the dataset into SQL.

After importing the data into SQL, I started to query it by answering the business questions. When done with that I imported the data in power Bi to turn it into a finished product(visualization). A lot of steps and measures were followed and created to  ensure a good dashboard will be achived and the processes will be explained better in the visualization section.

### SQL
Building a database 

First a database was created then I created table to import the data into the database created.


---view table
SELECT * 
FROM customers;

--Key Performance Indicator KPIs
```
--Total Orders
SELECT
	COUNT(DISTINCT order_id) AS Total_orders
FROM orders;
```SQL

```
--Total Revenue
SELECT
	SUM(od.quantity * p.unit_price) AS total_revenue
FROM order_details AS od
JOIN products p
ON od.product_id = p.product_id;
```SQL

```
--Average Order Value
SELECT
 	SUM(od.quantity * p.unit_price)/
	COUNT(DISTINCT o.order_id) AS Average_order_Value
FROM orders AS o
JOIN order_details od
ON o.order_id = od.order_id
JOIN products p
ON od.product_id = p.product_id;
``` SQL

```
--Shipping Efficiency (Average Shipping Time)
SELECT
CAST(AVG(shipped_date - order_date) AS INTEGER) AS Average_shipping_time
FROM orders;
```SQL

```
SELECT
ROUND(AVG(shipped_date - order_date)) AS Average_shipping_time
FROM orders;
```SQL

```
SELECT 
	order_id,
	order_date,
	required_date,
	shipped_date,
	ROUND(AVG(shipped_date - order_date)) AS average_shipping_time,
	MIN(shipped_date - order_date) AS  min_time,
	MAX(shipped_date - order_date) AS max_time
FROM orders
GROUP BY order_id
ORDER BY order_date;
```SQL

--Business Questions:
1.--Are there any noticeable sales trends over time?
here any noticeable sales trends over time?
```SQL
SELECT
	CASE
	WHEN month = 1 THEN 'January'
	WHEN month = 2 THEN 'February'
	WHEN month = 3 THEN 'March'
	WHEN month = 4 THEN 'April'
	WHEN month = 5 THEN 'May'
	WHEN month = 6 THEN 'June'
	WHEN month = 7 THEN 'July'
	WHEN month = 8 THEN 'August'
	WHEN month = 9 THEN 'September'
	WHEN month = 10 THEN 'October'
	WHEN month = 11 THEN 'November'
	WHEN month = 12 THEN 'December'
	END AS month_name, year, total_sales
FROM (
	SELECT
	EXTRACT(MONTH FROM order_date) AS month,
	EXTRACT(YEAR FROM order_date) AS year,
	SUM(od.quantity * p.unit_price) AS total_sales
FROM orders AS o
JOIN order_details od
ON o.order_id = od.order_id
JOIN products p
	ON od.product_id = p.product_id 
GROUP BY EXTRACT(MONTH FROM order_date),EXTRACT(YEAR FROM order_date)
) AS subquery
ORDER BY total_sales DESC;
```SQL

--Year--
```
SELECT
	EXTRACT(YEAR FROM order_date) AS YEAR,
	SUM(od.quantity * p.unit_price) AS total_sales
FROM orders AS o
JOIN order_details od
ON o.order_id = od.order_id
JOIN products p
	ON od.product_id = p.product_id 
GROUP BY EXTRACT(YEAR FROM order_date)
ORDER BY total_sales DESC;
```SQL

2.--Which are the best and worst selling products?
```
SELECT
	product_name,
	SUM(od.quantity) AS total_quantity,
	COUNT(p.product_id) AS total_id,
	SUM(od.quantity * p.unit_price) AS total_revenue,
	COUNT(DISTINCT o.order_id) AS total_orders
FROM products AS p
JOIN order_details od
	ON p.product_id = od.product_id 
JOIN orders o
ON od.order_id = o.order_id
GROUP BY product_name
ORDER BY total_quantity DESC
LIMIT 5;
```SQL

--Worst selling products---
```
SELECT
	product_name,
	SUM(od.quantity) AS total_quantity,
	SUM(od.quantity * p.unit_price) AS total_revenue
FROM products AS p
JOIN order_details od
	ON p.product_id = od.product_id 
GROUP BY product_name
ORDER BY total_quantity ASC
LIMIT 5;
```SQL

3.--Can you identify any key customers?
```
SELECT
	o.customer_id,
	SUM(od.quantity) AS total_quantity,
	SUM(od.quantity * p.unit_price) AS total_revenue
FROM orders AS o
JOIN order_details od
ON o.order_id = od.order_id
JOIN products p
ON od.product_id = p.product_id
GROUP BY o.customer_id
ORDER BY total_quantity DESC
LIMIT 5;
```SQL

4.--Are shipping costs consistent across providers?
```
SELECT
	s.shipper_id,
	s.company_name,
	SUM(o.freight) AS shipping_cost
FROM shippers AS s
JOIN orders o
ON s.shipper_id = o.shipper_id
GROUP BY s.shipper_id
```SQL

```
SELECT
	s.shipper_id,
	s.company_name,
	AVG(o.freight) AS Average_shipping_cost,
	SUM(o.freight) AS total_shipping_cost,
	STDDEV(o.freight) AS shipping_cost
FROM shippers AS s
JOIN orders o
ON s.shipper_id = o.shipper_id
GROUP BY s.shipper_id,o.freight 
```SQL

5.--How fast is their delivery service, how many orders are delivered on time or late?
```
SELECT
	order_id,
	order_date,
	shipped_date,
	required_date,
CASE
	 WHEN shipped_date <= required_date THEN 'On time'
	 ELSE 'late'
	 END AS delivery_status
FROM orders
GROUP BY order_date,order_id
```SQL
-------
```
SELECT
	CASE
	 WHEN shipped_date <= required_date THEN 'On time'
	 ELSE 'late'
	 END AS delivery_status,
	 COUNT(order_id) AS total_orders
FROM orders
GROUP BY delivery_status;
```SQL

```
SELECT 
	order_id,
	order_date,
	required_date,
	shipped_date,
	ROUND(AVG(shipped_date - order_date)) AS average_shipping_time,
	MIN(shipped_date - order_date) AS  min_time,
	MAX(shipped_date - order_date) AS max_time
FROM orders
GROUP BY order_id
ORDER BY order_date;
```SQL

### Visualizations
The dataset was imported into power Bi from excel and click on transform in order to work on it and insure its uniqneness and accurate analysis after that i checked for duplicated. colums header names were changed and headers were replaced using the change header name using use first colum as header in the tab button. Colums that were not necessary for my analysis were deleted and the category and employment table were hidden because they were not necessary for my analysis. After which i closed and apply.

Once it has closeand apply successfully, i clicked on my report view to check my connections and make necessary connections where needed. i clicked back on my reportview to start working on my visuals. I created calendar date table and i connected the table to the fact table.

I created Dax measures to calculate for my KPIs which include the following

❑ Total Orders
```
Total Orders = DISTINCTCOUNT(orders[orderID])
``` Power Bi

❑ Total Revenue
```
Total Revenue = SUMX('order details', 'order details'[quantity] * RELATED(products[unitPrice]) * (1 - 'order details'[discount]))
``` Power Bi

❑ Average Order Value
```
AOV = AVERAGEX(orders, [Total Revenue])
``` Power Bi

❑ Shipping Efficiency (Average Shipping Time)
```
Average Shipping Time = AVERAGEX(orders,orders[shippedDate] - orders[orderDate])
``` Power Bi



 
