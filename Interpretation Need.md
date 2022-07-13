### Interpretation Needed

 **Difficulty: Intermediate**

*Changes: Using Advenure Works OLTP 2019*

The Adventure Works Marketing department utilizes contractors to review foreign language product descriptions. To help the contractors, you are asked to prepare a list of all product descriptions written in languages other than English.

Your output should contain the following columns:
* ProductModelID
* Name of product model
* Product description
* Language

### My Solution
```
SELECT ProductModel.ProductModelID
     , ProductModel.Name              ProductModel
     , ProductDescription.Description ProductDescription
     , Culture.Name                   Language
FROM Production.ProductModel
         INNER JOIN Production.ProductModelProductDescriptionCulture
                    ON ProductModelProductDescriptionCulture.ProductModelID = ProductModel.ProductModelID
         INNER JOIN Production.ProductDescription
                    ON ProductDescription.ProductDescriptionID =
                       ProductModelProductDescriptionCulture.ProductDescriptionID
         INNER JOIN Production.Culture
                    ON Culture.CultureID = ProductModelProductDescriptionCulture.CultureID
WHERE Culture.Name != 'English';
```

### What I Learned
* Better understanding of the Adventure Works OLTP database.