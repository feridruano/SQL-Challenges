### Long Time No Sale

 **Difficulty: Intermediate**

*Changes: Using Advenure Works OLTP 2019*

The sales department will visit stores without recent sales orders. Suppose today’s date is October 7, 2008. Create a report that identifies stores in which the last order date was at least 12 months ago.

Your output should include the following columns:
* BusinessEntityID 
* CustomerID 
* StoreID 
* StoreName
* Last order date
* Number of months since last order

### My Potential Solution

> Adventure Works OLTP 2019 - Contains outdated BusinessEntityIDs and CustomerIDs for the orders listed.

```
SELECT Store.BusinessEntityID
     , Customer.CustomerID
     , Customer.StoreID
     , Store.Name                            StoreName
     , OrderDate                             LastOrderDate
     , DATEDIFF(MONTH, OrderDate, GETDATE()) LastOrderMonthsAgo
FROM Sales.SalesOrderHeader
         INNER JOIN Sales.Customer
                    ON Customer.CustomerID = SalesOrderHeader.CustomerID
         INNER JOIN Sales.Store
                    ON Store.BusinessEntityID = Customer.StoreID
WHERE NOT EXISTS(SELECT *
                 FROM Sales.SalesOrderHeader T
                 WHERE T.CustomerID = SalesOrderHeader.CustomerID
                   AND T.OrderDate > SalesOrderHeader.OrderDate)
  AND DATEDIFF(MONTH, OrderDate, GETDATE()) >= 12;
  ```

### Other Solution
```
SELECT Store.BusinessEntityID
     , Customer.CustomerID
     , Customer.StoreID
     , Store.Name                            StoreName
     , MAX(OrderDate)                        LastOrderDate
     , DATEDIFF(MONTH, MAX(OrderDate), GETDATE()) LastOrderMonthsAgo
FROM Sales.SalesOrderHeader
         INNER JOIN Sales.Customer
                    ON Customer.CustomerID = SalesOrderHeader.CustomerID
         INNER JOIN Sales.Store
                    ON Store.BusinessEntityID = Customer.StoreID
GROUP BY Store.BusinessEntityID
       , Customer.CustomerID
       , Customer.StoreID
       , Store.Name
HAVING DATEDIFF(MONTH, MAX(OrderDate), GETDATE()) >= 12;
```

  ### What I Learned
  * Adventure Works OLTP 2019 may contain outdated information in tables.