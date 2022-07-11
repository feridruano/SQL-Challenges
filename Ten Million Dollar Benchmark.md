### Ten Million Dollar Benchmark

 **Difficulty: Intermediate**

*Changes: Using Advenure Works OLTP 2019*

Ten million dollars of revenue is a common benchmark for Adventure Works. For each fiscal year (2007 and 2008), find the first dates when the cumulative running revenue total hit $10 million.

Notes:
* For Adventure Works, the fiscal year spans July through June
* Do not consider tax and freight with revenue

Your output should include the following columns:
* Fiscal year (2007 or 2008)
* Order date in which $10 million was reached or exceeded
* Order number within the fiscal year in which $10 million was reached or exceeded. Note, this is a count of orders. For example, if the $10 million goal was reached on the 50th order, then the appropriate value to report is 50.
* Running total revenue in which $10 million was reached or exceeded

### My Solution

```
WITH CTE AS (
    SELECT DISTINCT
           YEAR(DATEADD(MONTH, 6, OrderDate))                                                      FiscalYear
         , OrderDate
         , SUM(SubTotal) OVER (PARTITION BY YEAR(DATEADD(MONTH, 6, OrderDate)) ORDER BY OrderDate) RunningTotal
         , DENSE_RANK() OVER (PARTITION BY YEAR(DATEADD(MONTH, 6, OrderDate)) ORDER BY OrderDate)  OrderNumber
    FROM Sales.SalesOrderHeader
)
SELECT *
FROM CTE
WHERE RunningTotal >= 10000000
  AND NOT EXISTS(SELECT *
                 FROM CTE T
                 WHERE T.FiscalYear = CTE.FiscalYear
                   AND T.OrderDate < CTE.OrderDate
                   AND T.RunningTotal >= 10000000)
```

### What I Learned
Remembered, FIRST/LAST Row NOT EXISTS() requires the same outer and inner 'literal value' where conditions.

* Without the NOT EXISTS(), we produce a dataset of all orders with a running total greater than $10 million.
* With the NOT EXISTS(), we limit the outer dataset to be the first order with a running total greater than $10 million.
* Without the outer `>= 10 Million`, our dataset will **always** contain running totals of less than 10 Million.
* Without the inner `EXISTS()` `>= 10 Million`, we ***end up*** searching for the **lowest (first)** order date in a dataset with running totals of less than **AND** greater than 10 Million. Where the less than 10 Million data don't exist in the outer dataset because of outer `>= 10 Million` removing anything less than. Resulting an an empty dataset.
* `NOT EXISTS()` are very tricky but incredibly useful.
