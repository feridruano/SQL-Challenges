### Knapsack Problem (Combinatorials)

 **Difficulty: Advanced**

*Changes: Using Advenure Works OLTP 2019*

### Part I
An Adventure Works executive asks for a list of all salespeople representing the Northwest, Southwest, and Canadian sales territories, along with their 2008 revenue. You ask about the purpose of the information. “I’m assembling a ‘special team,’ the executive responds. “I’ll tell you more soon.”

Create a list of sales people with revenue as requested. Do not consider tax and freight.

### Part II

The following day, after completing the previous challenge, the executive approaches you again.

“I’m starting a new company,” he says. “That’s why I asked for the information yesterday. I need you to recommend candidates for recruitment.

I want one salesperson from the Northwest territory, one from the Southwest territory, and one from the Canadian territory. Which three-person combination of sales people results in the highest revenue? Base your calculations on the 2008 revenue you previously found.

I want the best team, but we cannot exceed the salary constraints. My salary budget for the combined roster must be less than $210,000.”

Below are the salaries you can offer to each sales person, as provided by the executive:
* Pak: $79,500
* Vargas: $60,000 
* Campbell: $59,500 
* Mensa-Annan: $56,000 
* Ito: $68,000
* Michel: $80,000

Based on 2008 revenue, find the most valuable combination of sales people within the salary constraint.

Your output should include a single row with the following columns:

* Sum of salaries of your three person team Sum of revenue from your three person team
* Territory of the first sales person
* Last name of first sales person
* Revenue of first sales person
* Salary of first sales person
* Territory of the second sales person
* Last name of second sales person
* Revenue of second sales person
* Salary of second sales person
* Territory of the third sales person
* Last name of third sales person
* Revenue of third sales person
* Salary of third sales person

### My Solution

```
WITH CTE_SalesPersons AS (
    -- Part I
    SELECT DISTINCT
           SalesPersonID
         , Person.LastName
         , SalesTerritory.Name                             Territory
         , SUM(SubTotal) OVER (PARTITION BY SalesPersonID) Revenue
         , Salary
    FROM Sales.SalesOrderHeader
             INNER JOIN Sales.SalesTerritoryHistory
                        ON SalesTerritoryHistory.BusinessEntityID = SalesOrderHeader.SalesPersonID
             INNER JOIN Sales.SalesTerritory
                        ON SalesTerritory.TerritoryID = SalesTerritoryHistory.TerritoryID
             INNER JOIN Person.Person
                        ON Person.BusinessEntityID = SalesOrderHeader.SalesPersonID
             LEFT JOIN (
                    VALUES ('Pak', 79500)
                         , ('Vargas', 60000)
                         , ('Campbell', 59500)
                         , ('Mensa-Annan', 56000)
                         , ('Ito', 68000)
                         , ('Mitchell', 80000)
                    ) Salaries(LastName, Salary)
                       ON Salaries.LastName = Person.LastName
    WHERE EndDate IS NULL
      AND SalesTerritory.Name IN ('Northwest', 'Southwest', 'Canada')
)
-- Part II
SELECT One.Salary + Two.Salary + Three.Salary    AggregateSalary
     , One.Revenue + Two.Revenue + Three.Revenue AggregateRevenue
     , One.Territory                             FSPTerritory
     , One.LastName                              FSPerson
     , One.Revenue                               FSPRevenue
     , One.Salary                                FSPSalary
     , Two.Territory                             SSPTerritory
     , Two.LastName                              SSPerson
     , Two.Salary                                SSPSalary
     , Three.Territory                           TSPTerritory
     , Three.LastName                            TSPerson
     , Three.Salary                              TSPSalary
     , Three.Revenue                             TSPRevenue
FROM CTE_SalesPersons One
         INNER JOIN CTE_SalesPersons Two
                    ON Two.LastName < One.LastName
                        AND Two.Territory != One.Territory
         INNER JOIN CTE_SalesPersons Three
                    ON Three.LastName < One.LastName
                        AND Three.LastName < Two.LastName
                        AND Three.Territory != One.Territory
                        AND Three.Territory != Two.Territory
WHERE One.Salary + Two.Salary + Three.Salary < 210000
Order by AggregateRevenue DESC
```

### What I Learned
* Remembered, how to creating a temporary temp with custom data using `VALUES` (Table Value Constructor)
* Multiple attempts to create combinations helped understand that `< LastName` (in this example) allows us to create combinations without repetition. Where as,`!= LastName` creates repetitive rows.
* For more than 2 combinations, we have to take into account the previous combinations carefully.