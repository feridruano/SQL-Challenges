### Product Combinations

 **Difficulty: Advanced**

*Changes: Using Advenure Works OLTP 2019*

### Part I
The executive management team wants to analyze the buying behavior of customers.

Provide the following calculations:
* Percentage of sales orders containing at least one bike and at least one accessory item
* Percentage of sales orders containing at least one bike and at least two different clothing products

### Part II
Count sales orders by product type. For example, if 500 sales orders included the product types Bikes and Clothing, with no accessories and components purchased, that output row would appear as follows:

| Bikes | Accessories | Clothing | Components | Orders |
| ----- | ----------- | -------- | ---------- | ------ |
| 1     | 0           | 1        | 0          | 500    |

### Part III
Count customers by product line. For example, if 100 customers purchased products from product lines M and T, but not S and R, that output row would appear as follows:

| M   | S   | T   | R   | Customers |
| --- | --- | --- | --- | --------- |
| 1   | 0   | 1   | 0   | 100       |


### My Solution

```
-- Part I
WITH CTE_Aggregates AS (
    SELECT SalesOrderID, ProductCategory.Name CategoryName, SUM(OrderQty) Quantity
    FROM Sales.SalesOrderDetail
             INNER JOIN Production.Product
                        ON Product.ProductID = SalesOrderDetail.ProductID
             INNER JOIN Production.ProductSubcategory
                        ON ProductSubcategory.ProductSubcategoryID = Product.ProductSubcategoryID
             INNER JOIN Production.ProductCategory
                        ON ProductCategory.ProductCategoryID = ProductSubcategory.ProductCategoryID
    GROUP BY SalesOrderID, ProductCategory.Name
)
SELECT SUM(IIF(Bikes >= 1 AND Accessories >= 1, 1, 0)) * 100 / COUNT(*) PercentBikeWithAccessories
     , SUM(IIF(Bikes >= 1 AND Clothing >= 2, 1, 0)) * 100 / COUNT(*)    PercentBikeWithClothing
FROM CTE_Aggregates
         PIVOT ( MAX(Quantity) FOR CategoryName IN (Bikes,Accessories,Clothing,Components) ) P;

-- Part II
WITH CTE_Aggregates AS (
    SELECT SalesOrderID, ProductCategory.Name CategoryName
    FROM Sales.SalesOrderDetail
             INNER JOIN Production.Product
                        ON Product.ProductID = SalesOrderDetail.ProductID
             INNER JOIN Production.ProductSubcategory
                        ON ProductSubcategory.ProductSubcategoryID = Product.ProductSubcategoryID
             INNER JOIN Production.ProductCategory
                        ON ProductCategory.ProductCategoryID = ProductSubcategory.ProductCategoryID
    GROUP BY SalesOrderID, ProductCategory.Name
)
SELECT Bikes, Accessories, Clothing, Components, COUNT(*) Orders
FROM CTE_Aggregates
         PIVOT ( COUNT(CategoryName) FOR CategoryName IN (Bikes,Accessories,Clothing,Components) ) P
GROUP BY Bikes, Accessories, Clothing, Components
ORDER BY Bikes, Accessories, Clothing, Components;

-- Part III
WITH CTE_Aggregates AS (
    SELECT CustomerID, ProductLine
    FROM Sales.SalesOrderDetail
             INNER JOIN Production.Product
                        ON Product.ProductID = SalesOrderDetail.ProductID
             INNER JOIN Sales.SalesOrderHeader
                        ON SalesOrderDetail.SalesOrderID = SalesOrderHeader.SalesOrderID
    GROUP BY CustomerID, ProductLine
)
SELECT M, S, T, R, COUNT(*) Customers
FROM CTE_Aggregates
         PIVOT ( COUNT(ProductLine) FOR ProductLine IN (M, S, T, R) ) P
GROUP BY M, S, T, R
ORDER BY M, S, T, R;
```

### What I Learned
* Remembered, I still know how to use CTE and Pivots for aggregating and transforming data.
* The solutions provided by the book can sometimes be completely different, but produce the same datasets.