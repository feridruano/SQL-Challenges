### Thermoform Temperature

 **Difficulty: Intermediate**

*Changes: Using Advenure Works OLTP 2019*

You are asked to report the most common reasons why products were scrapped through the manufacturing process.

Create a query by ProductID that includes the following:
* ProductID
* ProductName
* Number of work orders affected
* Most common scrap reason

### My Solution
```
WITH CTE AS (
    SELECT Product.ProductID
         , Product.Name
         , COUNT(*)         WorkOrderCount
         , ScrapReason.Name ScrapReason
    FROM Production.Product
             INNER JOIN Production.WorkOrder
                        ON WorkOrder.ProductID = Product.ProductID
             INNER JOIN Production.ScrapReason
                        ON ScrapReason.ScrapReasonID = WorkOrder.ScrapReasonID
    GROUP BY Product.ProductID, Product.Name, ScrapReason.Name
)
SELECT *
FROM CTE
WHERE NOT EXISTS(SELECT *
                 FROM CTE T
                 WHERE T.ProductID = CTE.ProductID
                   AND T.WorkOrderCount > CTE.WorkOrderCount)
```

### What I Learned
* More practice with `NOT EXISTS()` for returning the highest count in a column.