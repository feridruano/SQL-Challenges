### Toronto

 **Difficulty: Intermediate**

*Changes: Using Advenure Works OLTP 2019*

Provide address data about stores with main offices located in Toronto. 

Your output should include the following columns:
* Store name 
* AddressLine1 
* AddressLine2 
* City 
* StateProvince 
* PostalCode

### My Solution
```
SELECT Store.Name
     , AddressLine1
     , AddressLine2
     , City
     , StateProvince.Name
     , PostalCode
FROM Sales.Store
         INNER JOIN Person.BusinessEntityAddress
                    ON BusinessEntityAddress.BusinessEntityID = Store.BusinessEntityID
         INNER JOIN Person.Address
                    ON Address.AddressID = BusinessEntityAddress.AddressID
         INNER JOIN Person.AddressType
                    ON AddressType.AddressTypeID = BusinessEntityAddress.AddressTypeID
         INNER JOIN Person.StateProvince
                    ON StateProvince.StateProvinceID = Address.StateProvinceID
WHERE Address.City = 'Toronto'
  AND AddressType.Name = 'Main Office';
```

### What I Learned
* Adventure Works OLTP 2019 database structure