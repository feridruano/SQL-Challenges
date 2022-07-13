### Offline - Online

 **Difficulty: Beginner**

*Changes: Using Advenure Works OLTP 2019*

Create a summary table that shows, by territory, the percentage of orders placed online in comparison to orders not placed online.

Your output should include the following columns:
* TerritoryID
* Total orders
* Percentage of orders placed online
* Percentage of orders not placed online

To make the table easier to read, display percentages with a percent sign without decimals. For example, ninety five percent will be displayed as 95%.

### My Solution
```
SELECT TerritoryID
     , COUNT(*)                                                           TotalOrders
     , FORMAT(SUM(IIF(OnlineOrderFlag = 1, 1, 0)) * 1.0 / COUNT(*), 'P0') PercentOnline
     , FORMAT(SUM(IIF(OnlineOrderFlag = 0, 1, 0)) * 1.0 / COUNT(*), 'P0') PercentOffline
FROM Sales.SalesOrderHeader
GROUP BY TerritoryID;
```

### What I Learned
* Practice with `FORMAT(SUM(IFF()))` function nesting.