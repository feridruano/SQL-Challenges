### Product Inventory Updates

 **Difficulty: Advanced**

*Changes: Using Advenure Works OLTP 2019*

You are asked to provide frequent updates about the Adventure Works product inventory. Create a view that includes the following:
* Number of distinct products by LocationID
* Quantity of products by LocationID
* A rollup with total number of distinct products throughout all LocationIDs
* A rollup with total quantity of products throughout all LocationIDs

### My Solution
```
CREATE VIEW Production.vProductInventoryUpdates AS
SELECT IIF(GROUPING(Location.Name) = 1, 'Total', Location.Name) Location
     , COUNT(DISTINCT ProductInventory.ProductID)               DistinctProducts
     , SUM(ProductInventory.Quantity)                           RolledUpQuantity
FROM Production.ProductInventory
         INNER JOIN Production.Location
                   ON Location.LocationID = ProductInventory.LocationID
GROUP BY ROLLUP (Location.Name);
```

### What I Learned
* Using the `GROUP BY ROLLUP ()` feature and the `GROUPING()` function to determine a rolled up column value.