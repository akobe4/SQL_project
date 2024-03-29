Question 1: Determine the average amount of time spent on site for those who made purchases and those who did not. 

SQL Queries:
```sql
--average amount  of time spent on site for transactions with a purchase vs without a purchase 
WITH T1 AS (
SELECT timeonsite 
    ,CASE
	   	WHEN productquantity IS NULL THEN 'noPurchase'
		ELSE 'purchaseMade'
    END AS ifPurchase
FROM all_sessions 
	),

--remove null values from time 
T2 AS (
SELECT ifpurchase
	   ,timeonsite
FROM T1
WHERE timeonsite IS NOT NULL
GROUP BY ifpurchase, timeonsite
	)

SELECT ifpurchase
      ,AVG(timeonsite) AS avgTimeOnSite
FROM T2
GROUP BY ifpurchase

```

Answer: 
Those who made a purchase spent an average of 6 minutes and 37 seconds, while those who did not make a purchase spent more than double that on the site - 13 minutess and 26 seconds. 


Question 2: What are the top ten selling products based on units sold?  

SQL Queries:
```sql 
WITH T1 AS (
SELECT units_sold
	  ,v2productname AS productName
FROM analytics an
RIGHT JOIN all_sessions al 
ON an.visitid = al.visitid
	WHERE units_sold IS NOT NULL
GROUP BY an.visitid, units_sold, productName
ORDER BY productName
	)
	
SELECT  productname
	   ,SUM(units_sold) AS totalUnitsSold
FROM T1
GROUP BY productname
ORDER BY totalUnitsSold DESC
LIMIT 10
```

Answer: The top 10 selling products based on units sold are: 

    productname	                                        totalunitssold

    Grip Highlighter Pen 3 Pack	                        50
    Google Alpine Style Backpack	                    41
    Google Twill Cap	                                28
    Google Toddler Raglan Shirt Blue Heather/Navy	    20
    Nest® Protect Smoke + CO White Wired Alarm-USA	    17
    SPF-15 Slim & Slender Lip Balm	                    16
    Nest® Learning Thermostat 3rd Gen-USA - White	    12
    Waze Pack of 9 Decal Set	                        10
    Google Women's Scoop Neck Tee White	                8
    Nest® Cam Indoor Security Camera - USA	            8



Question 3: 
Which month has the highest revnue? 

SQL Queries:
```sql
WITH T1 AS (
SELECT DATE_TRUNC('month', sessiondate)  AS revenueMonth
	   ,sum(revenue) AS totalrevenue
FROM all_sessions al
LEFT JOIN analytics an
ON al.visitid = an.visitid
GROUP BY revenueMonth
ORDER BY revenueMonth
  )
 
SELECT  revenuemonth
	   ,totalrevenue
FROM T1
WHERE totalrevenue IS NOT NULL
GROUP BY revenuemonth, totalrevenue
ORDER BY totalrevenue DESC
```

Answer:
July 2017 is the month with the highest revenue at $1348.

