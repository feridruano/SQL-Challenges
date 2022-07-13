### Needy Accountant

 **Difficulty: Beginner**

*Changes: Using Advenure Works OLTP 2019*

An accountant needs to add assumptions about sales tax rates to his Excel worksheet. He asks you to provide one sales tax rate, conservatively, for each of the countries in which tax rates are known.

To fulfill his request, report the maximum sales tax rate of each country.

### My Solution
```
SELECT CountryRegion.Name
     , MAX(SalesTaxRate.TaxRate) MaxTaxRate
FROM Sales.SalesTaxRate
         INNER JOIN Person.StateProvince
                    ON StateProvince.StateProvinceID = SalesTaxRate.StateProvinceID
         INNER JOIN Person.CountryRegion
                    ON CountryRegion.CountryRegionCode = StateProvince.CountryRegionCode
GROUP BY CountryRegion.Name
```

### What I Learned
* Read the question carefully, it shouldn't be more complicated than an intermediate or advance question haha.