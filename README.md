# Northwind_project
This is all about my capstone project

### project Overview
Northwind Traders is a global supplier of specialty food products and this project intails helping Northwind optimize its operations by analyzing customer orders, product performance, shipping efficiency and employee management by building a database with the provided data and quering itto answer business questions and creating an intractive dashboard using an BI tool to derive actionable insight, answer critical business questions, identify opportunities for growth and efficiency.

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


---view table
SELECT * 
FROM customers;

--Key Performance Indicator KPIs
--Total Orders
SELECT
	COUNT(DISTINCT order_id) AS Total_orders
FROM orders; 

--Total Revenue
SELECT
	SUM(od.quantity * p.unit_price) AS total_revenue
FROM order_details AS od
JOIN products p
ON od.product_id = p.product_id;

--Average Order Value
SELECT
 	SUM(od.quantity * p.unit_price)/
	COUNT(DISTINCT o.order_id) AS Average_order_Value
FROM orders AS o
JOIN order_details od
ON o.order_id = od.order_id
JOIN products p
ON od.product_id = p.product_id;

--Shipping Efficiency (Average Shipping Time)
SELECT
CAST(AVG(shipped_date - order_date) AS INTEGER) AS Average_shipping_time
FROM orders;

SELECT
ROUND(AVG(shipped_date - order_date)) AS Average_shipping_time
FROM orders;

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

--Business Questions:
1.--Are there any noticeable sales trends over time?
here any noticeable sales trends over time?
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

--Year--
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

2.--Which are the best and worst selling products?
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

--Worst selling products---
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
3.--Can you identify any key customers?
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
4.--Are shipping costs consistent across providers?
SELECT
	s.shipper_id,
	s.company_name,
	SUM(o.freight) AS shipping_cost
FROM shippers AS s
JOIN orders o
ON s.shipper_id = o.shipper_id
GROUP BY s.shipper_id

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
5.--How fast is their delivery service, how many orders are delivered on time or late?
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
-------
SELECT
	CASE
	 WHEN shipped_date <= required_date THEN 'On time'
	 ELSE 'late'
	 END AS delivery_status,
	 COUNT(order_id) AS total_orders
FROM orders
GROUP BY delivery_status;

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
