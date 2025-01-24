# Northwind_project
This is all about my capstone project

### project Overview
Northwind Traders is a global supplier of specialty food products and this project intails helping Northwind optimize its operations by analyzing customer orders, product performance, shipping efficiency and employee management by building a database with the provided data and quering it to answer business questions and creating an intractive dashboard using any BI tool to derive actionable insight, answer critical business questions, identify opportunities for growth and efficiency.

### Business Requirement

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

### Data Preparation
- Data Conversion: The original dataset was in xls format, so i converted it to a csv for better compactibility and efficiency in procesing.
- Data Cleaning: Duplicate were removed and I ensured the dataset was clean and ready for analysis.

### SQL
- Table Creation: Designed and created the required table structure in SQL to accomodate the dataset. 
- Data Import: Imported the cleaned dataset into SQL for further analysis.
- Querying Data: Wrote and executed SQL queries to answer bussiness questions.

### Key Performance Indicator KPIs
#### Total Orders
```Sql
SELECT
	COUNT(DISTINCT order_id) AS Total_orders
FROM orders;
```

#### Total Revenue
```Sql
SELECT
	SUM(od.quantity * p.unit_price *(1-od.discount)) AS total_revenue
FROM order_details AS od
JOIN products p
ON od.product_id = p.product_id;
```

#### Average Order Value
```Sql
SELECT
 	SUM(od.quantity * p.unit_price*(1-od.discount))/
	COUNT(DISTINCT o.order_id) AS Average_order_Value
FROM orders AS o
JOIN order_details od
ON o.order_id = od.order_id
JOIN products p
ON od.product_id = p.product_id;
```

#### Shipping Efficiency (Average Shipping Time)
```Sql
SELECT
CAST(AVG(shipped_date - order_date) AS INTEGER) AS Average_shipping_time
FROM orders;
```

### Business Questions
1. Are there any noticeable sales trends over time?
```Sql
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
	END AS month_name, total_sales
FROM (
	SELECT
	EXTRACT(MONTH FROM order_date) AS month,
	SUM(od.quantity * p.unit_price *(1-od.discount)) AS total_sales
FROM orders AS o
JOIN order_details od
ON o.order_id = od.order_id
JOIN products p
	ON od.product_id = p.product_id 
GROUP BY EXTRACT(MONTH FROM order_date)
) AS subquery
ORDER BY total_sales DESC;
```

#### Year
```Sql
SELECT
	EXTRACT(YEAR FROM order_date) AS YEAR,
	SUM(od.quantity * p.unit_price *(1-od.discount)) AS total_sales
FROM orders AS o
JOIN order_details od
ON o.order_id = od.order_id
JOIN products p
	ON od.product_id = p.product_id 
GROUP BY EXTRACT(YEAR FROM order_date)
ORDER BY total_sales DESC;
```

2. Which are the best and worst selling products?
- Best Selling Product
```Sql
SELECT
	product_name,
	SUM(od.quantity) AS total_quantity,
	SUM(od.quantity * p.unit_price *(1-od.discount)) AS total_revenue
FROM products AS p
JOIN order_details od
	ON p.product_id = od.product_id 
GROUP BY product_name
ORDER BY total_quantity DESC
LIMIT 5;
```

- Worst Selling Products
```Sql
SELECT
	product_name,
	SUM(od.quantity) AS total_quantity,
	SUM(od.quantity * p.unit_price *(1-od.discount)) AS total_revenue
FROM products AS p
JOIN order_details od
	ON p.product_id = od.product_id 
GROUP BY product_name
ORDER BY total_quantity ASC
LIMIT 5;
```

3. Can you identify any key customers?
```Sql
SELECT
	c.customer_id,
	c.company_name,
	SUM(od.quantity * p.unit_price *(1-od.discount)) AS total_revenue
FROM customers AS c
JOIN orders o
ON c.customer_id = o.customer_id
JOIN order_details od
ON o.order_id = od.order_id
JOIN products p
ON od.product_id = p.product_id
GROUP BY c.customer_id
ORDER BY total_revenue DESC
LIMIT 5;
```

4. Are shipping costs consistent across providers?
```Sql
SELECT
	s.company_name,
	ROUND(AVG(o.freight), 2) AS average_shipping_cost,
	MAX(o.freight) AS max_shipping_cost,
	MIN(o.freight) AS min_shipping_cost,
	ROUND(STDDEV_SAMP(o.freight), 2) AS stddev_shipping_cost,
	(MAX(o.freight) - MIN(o.freight)) AS range_shipping_cost
FROM shippers AS s
JOIN orders o
ON s.shipper_id = o.shipper_id
GROUP BY s.company_name 
ORDER BY stddev_shipping_cost DESC;
```

5. How fast is their delivery service, how many orders are delivered on time or late?
```Sql
SELECT
	CASE
	 WHEN shipped_date <= required_date THEN 'On time'
	 ELSE 'late'
	 END AS delivery_status,
	 COUNT(order_id) AS total_orders
FROM orders
GROUP BY delivery_status;
```

### Data Visualizations

After SQL analysis,the data was imported into Power BI for visualization and reporting.
- Data Transformation:

- After importing the dataset, the Transform option was selected to make necessary corrections.

- Column header were updated and replaced using the "Use First Row as Headers" option in the Power Query Editor

- Unnecessary columns were deleted to streamline the dataset.

- The Category and Employment tables were hidden as they were not relevant for the analysis

- Changes were applied by clicking "Close & Apply."

- Data Modeling:
 
- I switched to report view to verify the data connections and made necessary adjustments to ensure proper relationship between tables.
  
- Calendar table was created and connected to the fact table to support time-based analysis.

- Visualization

- Interactive  visualization were designed to highlight key insight, measures and calculated columns were created to enhance the dashboard.

### KPIs Measures Created

##### Total Orders
```DAX
Total Orders = DISTINCTCOUNT(orders[orderID])
``` 

##### Total Revenue
```DAX
Total Revenue = SUMX('order details', 'order details'[quantity] *
     RELATED(products[unitPrice]) * (1 - 'order details'[discount]))
``` 

##### Average Order Value
```DAX
AOV = AVERAGEX(orders, [Total Revenue])
``` 

##### Shipping Efficiency (Average Shipping Time)
```
Average Shipping Time = AVERAGEX(orders,orders[shippedDate] - orders[orderDate])
``` 
- Previous Year (PY) was calculated for all the KPIs to compare it with the current year
```DAX  
PYOrders = 
VAR PY = CALCULATE([Total Orders],PREVIOUSYEAR('Calendar'[Date]))
VAR CY = [Total Orders]
VAR PCT = FORMAT(DIVIDE(CY - PY, PY, 0), "0%")
VAR ARROW_SIGN = IF(CY > PY,"◬", "▽")
RETURN "PY:" & PCT & ARROW_SIGN
```
```DAX
PYRevenue = 
VAR PY = CALCULATE([Total Revenue],PREVIOUSYEAR('Calendar'[Date]))
VAR CY = [Total Revenue]
VAR PCT = FORMAT(DIVIDE(CY - PY, PY, 0), "0%")
VAR ARROW_SIGN = IF(CY > PY,"◬", "▽")
RETURN "PY:" & PCT & ARROW_SIGN
``` 
 
```DAX
PYAOV = 
VAR PY = CALCULATE([AOV],PREVIOUSYEAR('Calendar'[Date]))
VAR CY = [AOV]
VAR PCT = FORMAT(DIVIDE(CY - PY, PY, 0), "0%")
VAR ARROW_SIGN = IF(CY > PY,"◬", "▽")
RETURN "PY:" & PCT & ARROW_SIGN
```

```DAX
PYAST = 
VAR PY = CALCULATE([Average Shipping Time],PREVIOUSYEAR('Calendar'[Date]))
VAR CY = [Average Shipping Time]
VAR PCT = FORMAT(DIVIDE(CY - PY, PY, 0), "0%")
VAR ARROW_SIGN = IF(CY > PY,"◬", "▽")
RETURN "PY:" & PCT & ARROW_SIGN
``` 

### Business Questions:
1. Are there any noticeable sales trends over time?
- To analyze sales trends over time, the following steps were taken:

The Total Revenue calculated for the KPIs was used, the month field from the calendar table was used for grouping. A line chart was plotted to visualize the trends.
Tooltip was created in a different page showing the Total Revenue and Total Quantity per month.
#### Insights
General Insight

   - There is a noticeable sales trend overall.
   
   - From October to April, sales were high showing a peak period but from May to September, sales fluctuated and declined.
##### Yearly Breakdown
- 2013
   - Sales was recorded fro the second half of the year.
   - Peaks: November recorded the higest sales, followed by slight increase in October, December, July and August.
   - Lows: September experience a very low sales.

- 2014
   - Sales were recorded for the entire year.
   - Peaks: January, October and December show significant increase in sales.
   - Lows: Other months experienced a decline

- 2015
   - Sales were recorded for the first half of the year.
   - Peaks: January to April show increase in sales.
   - Lows: May recorded a very low increase in sales
 
#### Inference
The downward sales trend is attributed to some of these factors:

#### Recommendations
1. Stock Management:
      - Ensure that goods are adequately stocked during high sales months and reduced during low sales month.
2. Market Research:
     -  Deeper research into the downward sales trend should be conducted, esspecially April to September.
     -  Customer preference should also be investigated inorder to stock up that generated sales.  
3. Adjust Strategies:
    - Marketing strategies and promotions should be implemented during low sales months to boost demand.
    - Inventory management should be optimized to aling with sales pattern and avoid stock-outs.
   
2. Which are the best and worst selling products?
   
To determine the best and worst selling products the following steps were taken:
- Measure creation:
       - Total Quantity measure was created to calculate the sum of quantities sold:
```DAX  
TotalQuantity = SUM('order details'[quantity] )
```
      - Ranking option table was created to show any number of best/worst selling product needed to be seen:
```DAX      
Ranking option = GENERATESERIES(1, 5, 1)
```
       - TopBottom table was created to filter top/bottom product dynamically:
```DAX       
TopButtom = {"top","Buttom"}
```
       - Created a ranking measure for product based on quantity along with its integration with other measures for comprehensive analysis:
```DAX
RankBestWorstSellingProduct = 
VAR _bestproduct = RANKX( ALL(products),[TotalQuantity], ,DESC)
VAR _worstproduct = RANKX( ALL(products),[TotalQuantity], ,ASC)

VAR _ranking = IF(SELECTEDVALUE(TopButtom[Value]) = "top"
    ,_bestproduct
    ,_worstproduct
)
RETURN
IF(_ranking <= 'Ranking option'[Ranking option Value], [TotalQuantity])
```
#### Insight 
General Insight

- Best Selling Products

    - Raclette Courdavault is the best selling product with a total quantity of 1368, followed by Gorgonzola Telino with 1347 total quantity.
- Worst Selling Products

   - Genen Shouyv is the worst selling product with a total quantity of 72, followed by Mishi Kobe Niku with 79 total quantity.
  
##### Yearly Breakdown
2013
- Best Selling Products

    - Gorgonzola Telino with 444 quantity, followed by Camembert Pierrot with 343 quantity.
  
2014
- Best Selling Products
  
    - Gonocchi di nonna Alice with 846 quantity, followed by Raclette Courdavault with 706 quantity.
- Worst Selling Products
  
    - Chef Antons Gumbo Mix with 19 quantity, followed by Gravad Lax with 28 quantity.

 2015
- Best Selling Products
  
   - Konbo with 521 quantity, followed by Guarana Fantastica with 464 quantity.
  
- Worst Selling Products
  
    - Mishi Kobe Niku with 3 quantity, followed by Chef Antons Cajun Seasoning with 5 quantity.
  
#### Inference

The performance of the best/worst selling product is due to one of the following:

1. Best Selling Products
   
   - The products are well marketed and meet high customer demand.
        
   - They are consistently avaliable and competitive pricing.
        
2. Worst Selling Products
   
   - They are new products and haven't gained market traction yet.
        
   - Lack of avaliability, low quality and lack of awareness of the product.

#### Recommendation
1. Stock Management:
   
    - Increase inventory for the best selling product to meet growing demand.
   
2. Improvement for Worst Selling Products
   
      - Collaboration with suppliers or manufacturers to understand the product issue and address potential quality or supply chain issues.
        
3. Customer Analysis:
   
      - Conduct surveys and gather feedback to understand why customers are not really purchasing the worst selling products.
        
      - The insight gotten from the survey should be used to improve marketing strategies and feedback should be given to the supplliers or manufacturers to improve product features and pricing.
        
4. Marketing Campaigns:
   
      -  targeted promotions should be run for new product in stock to increase awareness and trial purchases.

3. Can you identify any key customers?

To identify key customers, the following steps were taken:
    - Chart Type: Bar Chart
    - Y - Axis: customer_id
    - X - Axis: Total Revenue
    - Legend: Company_Name
    - Tooltip: Quantity
- To show the key customer I applied a Top N filter on customer_id in the filter pane, set it to Top 5 based on Total Revenue and click Apply Filter.

#### Insights

General Insight
     - SAVEA is the overall key customer with a total revenue of $94,063, followed by ERNSH with a total revenue of $85,630.
##### Yearly Breakdown
2013
   - ERNSH was the key customer with a total revenue of $17,368, followed by FRANK with $12,191.
2014
  - SAVEA emerged as the key customer with a total revenue of $48,715, followed by FRANK with $45,945.
2015
  - SAVEA retained its position as the key customer with a total revenue of $35,549, followed by HANAR with $23,821.

#### Inference
1. Starting in 2014, SAVEA consistently became the key customer, contributing the highest revenue across 2014 and 2015. This indicates a strong and growing market strategy
2. Key customers like SAVEA and ERNSH have show strong engagement with NorthWind Traders, which includes higher order or quantity.

#### Recommendation
1. Maintain Strong Relationship with Key Customers
   - Focused on strengthening relationships with key customers through personalized discounts and exclusive offer.
2. Customer Retension Strategy 
   - Ensure continous support to key customers by offering excellent customer service and frequent check-ins to understand their evolving needs.

4. Are shipping costs consistent across providers?
I created a measure to calculate for the Range. i draged my Company_Name to the Y axis and shippingCostRange to the X axis. Then used a Bar Chart to visualize it. 
``
ShippingCostRange = 
MAX(orders[freight]) -
MIN(orders[freight])
``` Power BI

❑ How fast is their delivery service, how many orders are delivered on time or late?
In achieving this I created measures to show late delivery and on time delivery then clicked on pie chart and draged the two measures to the value.
 
``
LateDelivery = 
 CALCULATE(
    COUNTROWS(orders),
    orders[shippedDate] > orders[requiredDate])
``` Power BI

``
OnTimeDelivery = 
 CALCULATE(
    COUNTROWS(orders),
    orders[shippedDate] <= orders[requiredDate])
``` Power BI


### customize the visuals
- Use the Format page to adjust colors, fonts and labels.
- Add titles and data label to the visuals. 

 
