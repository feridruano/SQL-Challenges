### Expired Credit Cards

 **Difficulty: Intermediate**

*Changes: Using Advenure Works OLTP 2019*

The Accounting department found instances where expired credit cards were used with sales orders. You are asked examine all credit cards and report the extent of such activity.

### Part I

Based on each CreditCardID, find the following:
* CreditCardType
* ExpirationDate
* Last order date
* Number of sales orders with order dates earlier than or equal to the card’s expiration date
* Number of sales orders with order dates later than the card’s expiration date

Note:

Adventure Works stores information about a credit card’s expiration year and expiration month. Expiration dates pertain to the last day of a card’s expiration
month. For example, if the expiration year is 2007 and the expiration month is “4”, the card’s expiration date will be April 30, 2007.

### Part II

Based on CreditCardType, summarize data returned from Part I. Your output should include the following columns:

* CreditCardType
* Number of sales orders with order dates earlier than or equal to the card’s expiration date
* Number of sales orders with order dates later than the card’s expiration date

### My Solution

> AdventureWorks2019 - All credit cards expired in 2008 or below, but orders exists for all cards afterward. OrdersBeforeExpiration will always be 0 for this database.

```
WITH CTE AS (
    -- Part I
    SELECT DISTINCT
           SalesOrderHeader.CreditCardID
         , CardType
         , EOMONTH(DATEFROMPARTS(ExpYear, ExpMonth, 1))                                               ExpirationDate
         , LAST_VALUE(OrderDate) OVER (PARTITION BY SalesOrderHeader.CreditCardID ORDER BY OrderDate) LastOrderDate
         , SUM(IIF(OrderDate <= EOMONTH(DATEFROMPARTS(ExpYear, ExpMonth, 1)), 1, 0))
               OVER (PARTITION BY SalesOrderHeader.CreditCardID)                                      OrdersBeforeExpiration
         , SUM(IIF(OrderDate > EOMONTH(DATEFROMPARTS(ExpYear, ExpMonth, 1)), 1, 0))
               OVER (PARTITION BY SalesOrderHeader.CreditCardID)                                      OrdersAfterExpiration
    FROM Sales.SalesOrderHeader
             INNER JOIN Sales.CreditCard
                        ON CreditCard.CreditCardID = SalesOrderHeader.CreditCardID
)
-- Part II
SELECT CardType
     , SUM(OrdersBeforeExpiration) OrdersBeforeExpiration
     , SUM(OrdersAfterExpiration)  OrdersAfterExpiration
FROM CTE
GROUP BY CardType;
```

### What I Learned
* How to use `DATEPARTS()` and `EOMONTH()`.
* Remember `EOMONTH()` always returns a `date` type!