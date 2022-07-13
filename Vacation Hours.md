### Vacation Hours

 **Difficulty: Intermediate**

*Changes: Using Advenure Works OLTP 2019*

Human Resources is reevaluating a policy about maximum allowable vacation rollover hours. You are asked to help by identifying the employee or group of employees with the greatest number of vacation hours. Since many Human Resources files are indexed by NationalIDNumber, please include the last four digits with your output.

In all, your output should contain the following information:
* Last four digits of NationalIDNumber FirstName
* LastName
* JobTitle
* Number of vacation hours

### My Solution
```
SELECT RIGHT(NationalIDNumber, 4) NationalID
     , FirstName
     , LastName
     , JobTitle
     , VacationHours
FROM HumanResources.Employee
         INNER JOIN Person.Person
                    ON Person.BusinessEntityID = Employee.BusinessEntityID
WHERE NOT EXISTS(SELECT * FROM HumanResources.Employee T WHERE T.VacationHours > Employee.VacationHours);
```

### What I Learned
* Better understanding of when to use `NOT EXISTS()` instead of CTE and Subqueries.