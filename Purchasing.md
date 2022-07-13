### Purchasing

 **Difficulty: Intermediate**

*Changes: Using Advenure Works OLTP 2019*

### My Solution
```
SELECT PurchaseOrderDetail.ProductID
     , Product.Name                      ProductName
     , PurchaseOrderHeader.OrderDate
     , SUM(PurchaseOrderDetail.OrderQty) QuantityOrdered
FROM Purchasing.PurchaseOrderHeader
         INNER JOIN Purchasing.PurchaseOrderDetail
                    ON PurchaseOrderDetail.PurchaseOrderID = PurchaseOrderHeader.PurchaseOrderID
         INNER JOIN Production.Product
                    ON Product.ProductID = PurchaseOrderDetail.ProductID
WHERE YEAR(PurchaseOrderHeader.OrderDate) = 2014
GROUP BY Product.Name, PurchaseOrderDetail.ProductID, PurchaseOrderHeader.OrderDate
ORDER BY QuantityOrdered DESC;
```

### What I Learned
* More practice with `GROUP BY` and better understanding of the Adventure Works OLTP database structure.