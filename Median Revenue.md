### Median Revenue

 **Difficulty: Intermediate**

*Changes: Using Advenure Works OLTP 2019*

An analyst notified the Vice President of Sales that averages can be skewed by outliers. In response, he asks to see median revenue in addition to average revenue. He also asks you to add minimum and maximum revenue to your report.

Write a query of sales by year that includes the following columns:
* Order year 
* Minimum sale 
* Maximum sale 
* Average sale 
* Median sale

Notes:
* Years based on OrderDate
* Tax and freight should not be considered with revenue

### My Solution
```
SELECT DISTINCT
       YEAR(OrderDate)                                                                           Year
     , AVG(SubTotal) OVER ( PARTITION BY YEAR(OrderDate))                                        AverageRevenue
     , PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY SubTotal) OVER (PARTITION BY YEAR(OrderDate)) MedianRevenue
     , MIN(SubTotal) OVER ( PARTITION BY YEAR(OrderDate))                                        MinimumRevenue
     , MAX(SubTotal) OVER ( PARTITION BY YEAR(OrderDate))                                        MaximumRevenue
FROM Sales.SalesOrderHeader
ORDER BY YEAR(OrderDate);
```

### What I Learned
* Two functions to determine the median value of a dataset
  * `PERCENTILE_DISC()` - Returns a median value from the dataset.
  * `PERCENTILE_CONT()` - Returns an interpolated (estimated) value from the dataset whether it exists or not in the dataset.