### Upsell Tuesdays

 **Difficulty: Beginner**

*Changes: Using Advenure Works OLTP 2019*

Tuesday’s are “upsell” days for sales people at Adventure Works. Management wants to compare sales from Tuesday to other days of the week to see if the initiative is working. Help monitor the upsell initiative by creating a query to calculate average revenue per order by day of week in 2008.

Include the following columns with your output:
* Day of week 
* Revenue
* Orders
* Revenue per order

Notes:
* Dates based on OrderDate
* Tax and freight should not be considered Exclude online orders

### My Solution
```
SELECT DATENAME(WEEKDAY, OrderDate) Day
     , SUM(SubTotal)                Revenue
     , COUNT(*)                     Orders
     , SUM(SubTotal) / COUNT(*)     RevenuePerOrder
FROM Sales.SalesOrderHeader
WHERE OnlineOrderFlag = 0
  AND YEAR(OrderDate) = 2014
GROUP BY DATENAME(WEEKDAY, OrderDate);
```

### What I Learned
More practice using `DATENAME()`.