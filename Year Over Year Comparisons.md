## Year Over Year Comparisons

 **Difficulty: Intermediate**

*Changes: Using Advenure Works OLTP 2019*

An executive requests data concerning fiscal quarter sales by salesperson. She’d like to see comparisons from the fiscal quarters of 2008 to the same fiscal quarters of 2007.

For example, suppose sales for salesperson X totaled $1,000 during Fiscal Year 2008, Fiscal Quarter 2. If sales for salesperson X totaled $900 in Fiscal Year 2007, Fiscal Quarter 2, this reflects about 11.1% growth between the two periods for salesperson X.

Notes:
* For Adventure Works, the fiscal year spans July through June 
* Tax and freight will not be considered with revenue
* Dates are based on OrderDate
* Disregard online orders


Your output should include the following columns, corresponding to all sales people:
* LastName
* SalesPersonID
* Fiscal year
* Fiscal quarter
* Fiscal quarter sales
* Sales during the same fiscal quarter of the previous fiscal year Change in revenue between the two periods
* Percent change in revenue between the two periods

### My Solution

```
WITH CTE AS (
    SELECT SalesPersonID
         , YEAR(DATEADD(MONTH, 6, OrderDate))              [Fiscal Year]
         , DATEPART(QUARTER, DATEADD(MONTH, 6, OrderDate)) [Fiscal Quarter]
         , SUM(SubTotal)                                   [Fiscal Quarter Sales]
    FROM Sales.SalesOrderHeader
    WHERE OnlineOrderFlag = 0
    GROUP BY SalesPersonID, IIF(MONTH(OrderDate) <= 6, YEAR(OrderDate) - 1, YEAR(OrderDate))
           , DATEPART(QUARTER, DATEADD(MONTH, 6, OrderDate))
)
SELECT LastName
     , CTE.SalesPersonID
     , CTE.[Fiscal Year]
     , CTE.[Fiscal Quarter]
     , CTE.[Fiscal Quarter Sales]
     , Previous.[Fiscal Quarter Sales]                                                                        [Previous Year Fiscal Quarter Sales]
     , CTE.[Fiscal Quarter Sales] - Previous.[Fiscal Quarter Sales]                                           [Change]
     , (CTE.[Fiscal Quarter Sales] - Previous.[Fiscal Quarter Sales]) / Previous.[Fiscal Quarter Sales] * 100 [% Change]
FROM CTE
         INNER JOIN Person.Person
                    ON Person.BusinessEntityID = CTE.SalesPersonID
         LEFT JOIN CTE Previous
                   ON Previous.SalesPersonID = CTE.SalesPersonID
                       AND Previous.[Fiscal Year] = CTE.[Fiscal Year] - 1
                       AND Previous.[Fiscal Quarter] = CTE.[Fiscal Quarter]
WHERE CTE.[Fiscal Year] = 2013;
```

### What I Learned
* More practice using `DATEPART()` and `DATEADD()`.
* Remember, it's okay to use CTEs instead of trying to avoid them because of more code.