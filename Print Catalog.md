### Print Catalog

 **Difficulty: Beginner**

*Changes: Using Advenure Works OLTP 2019*

Adventure Works will feature one product for the cover of its print catalog. Help select a list of products for consideration.

Your list should contain products which meet all of the following conditions:

* Finished goods (not products utilized to make other products)
* List price at least $1,500
* At least 150 in inventory
* Currently available for sale

Your output should contain the following columns:

* ProductID
* ProductName
* Color
* ListPrice
* Inventory quantity

### My Solution

```
WITH CTE AS (
    SELECT ProductInventory.ProductID, SUM(ProductInventory.Quantity) InventoryQuantity
    FROM Production.ProductInventory
    GROUP BY ProductID
)
SELECT DISTINCT
       Product.ProductID
     , Name ProductName
     , Color
     , ListPrice
     , InventoryQuantity
FROM Production.Product
         INNER JOIN CTE
                    ON CTE.ProductID = Product.ProductID
WHERE SellEndDate IS NULL
  AND FinishedGoodsFlag = 1
  AND ListPrice >= 1500
  AND InventoryQuantity >= 150;
```

### What I Learned
Again, it's okay to use a CTE or subquery. There's no need to try to avoid them.