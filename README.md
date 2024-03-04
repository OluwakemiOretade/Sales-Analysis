# Data Analysis: Sales Management

<img src="https://retaintechnologies.com/wp-content/uploads/2020/04/Project-Management-Mantenimiento-1.jpg" alt="Project Management image" />


## Project Objective

  -  Perform a detailed analysis using SQL and create interactive dashboards with PowerBI for Internet Sales to facilitate a business request.

## Languages & Tools
  -   Language: SQL
  -   Database: Microsoft SQL Server Management Studio
  -  Data Visualization: Microsoft Power BI
  -  Data Source: [Adventure Works Sample Databases 2024](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms).

## Project Outline
## 1. [Business request and Project Planning](https://github.com/OluwakemiOretade/Sales-Analysis/blob/main/1.%20Business%20Demand%20and%20Project%20Planning/Business%20Demand.md).

  -  The business request for this data analysis project was to create an executive sales report for the Sales Department. Based on the request that was made from the business, user stories were defined to fulfill delivery and ensure the acceptance criteria were maintained throughout the project.

  ### -  User Stories:
    
| # | Role | Request | User Value | Acceptance Criteria |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| 1 | Sales Manager | To get a dashboard overview of internet sales | Can follow better which customers and products sells the best | A Power BI dashboard which updates data once a day |
| 2 | Sales Representative | A detailed overview of Internet Sales per Customers | Can follow up my customers that buys the most and who we can sell more to | A Power BI dashboard which allows me to filter data for each customer |
| 3 | Sales Representative | A detailed overview of Internet Sales per Products | Can follow up my Products that sells the most | A Power BI dashboard which allows me to filter data for each Product |
| 4 | Sales Manager | A dashboard overview of internet sales | Can follow sales over time against budget | A Power Bi dashboard with graphs and KPIs comparing against budget. |

## 2. [Data Cleaning & Transformation](https://github.com/OluwakemiOretade/Sales-Analysis/tree/main/2.%20Data%20Cleaning%20%26%20Transformation)

-  To create the necessary data model for doing analysis and fulfilling the business needs defined in the user stories the following table were extracted using SQL.

-  Below are the SQL statements for cleaning and transforming necessary data.
  
     ### -  DIM_Calendar:
~~~
-- Cleaned Dim_Date Table --
SELECT 
  [DateKey], 
  [FullDateAlternateKey] AS Date, 
  --[DayNumberOfWeek],
  [EnglishDayNameOfWeek] AS Day, 
  --[SpanishDayNameOfWeek],
  --[FrenchDayNameOfWeek],
  --[DayNumberOfMonth],
  --[DayNumberOfYear],
  --[WeekNumberOfYear],
  --[EnglishMonthName], 
  LEFT([EnglishMonthName],3) AS Month,
  --[SpanishMonthName],
  --[FrenchMonthName],
  [MonthNumberOfYear] AS MonthNo, 
  [CalendarQuarter] AS Quarter, 
  [CalendarYear] AS Year 
  --[CalendarSemester],
  --[FiscalQuarter],
  --[FiscalYear],
  --[FiscalSemester],
FROM 
  [dbo].[DimDate] 
WHERE 
  CalendarYear >= 2019
  ~~~

### -  DIM_Products:

~~~
-- Cleaned Dim_Products Table --
SELECT 
  p.[ProductKey], 
  [ProductAlternateKey] AS [Product Item Code],
  --,[ProductSubcategoryKey]
  --,[WeightUnitMeasureCode]
  --,[SizeUnitMeasureCode] 
  p.[EnglishProductName] AS [Product Name],
  ps.[EnglishProductSubcategoryName] AS [Sub Category],       -- Joined in from Sub Category table
  pc.[EnglishProductCategoryName] AS [Product Category],      -- Joined in from Category table
  --,[SpanishProductName]
  --,[FrenchProductName]
  --,[StandardCost]
  --,[FinishedGoodsFlag]
  [Color] AS [Product Color],
  --,[SafetyStockLevel]
  --,[ReorderPoint]
  --,[ListPrice]
  [Size] AS [Product Size], 
  --,[SizeRange]
  --,[Weight]
  --,[DaysToManufacture] 
  [ProductLine] AS [Product Line], 
  --,[DealerPrice]
  --,[Class]
  --,[Style]
  [ModelName] AS [Product Model Name], 
  --,[LargePhoto]
  [EnglishDescription] AS [Product Description],
  --,[FrenchDescription]
  --,[ChineseDescription]
  --,[ArabicDescription]
  --,[HebrewDescription]
  --,[ThaiDescription]
  --,[GermanDescription]
  --,[JapaneseDescription]
  --,[TurkishDescription]
  --,[StartDate]
  --,[EndDate] 
  ISNULL (P.Status, 'Outdated') AS [Product Status] 
FROM 
  dbo.DimProduct as p 
  LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategoryKey = p.ProductSubcategoryKey 
  LEFT JOIN dbo.DimProductCategory AS pc ON pc.ProductCategoryKey = p.ProductSubcategoryKey 
ORDER BY 
  p.ProductKey ASC
~~~

### -  DIM_Customer:
~~~
-- Cleaned Dim_Customers Table --
SELECT 
  [CustomerKey],
  --,[GeographyKey]
  --,[CustomerAlternateKey]
  --,[Title]
  c.firstname AS [First Name], 
  --,[MiddleName]
  c.LastName AS [Last Name],
  c.firstname + ' ' + c.lastname AS [Full Name], -- Joined fristname and lastname
  --,[NameStyle]
  --,[BirthDate]
  --,[MaritalStatus]
  --,[Suffix] 
  CASE c.Gender WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender, 
  --,[EmailAddress]
  --,[YearlyIncome]
  --,[TotalChildren]
  --,[NumberChildrenAtHome]
  --,[EnglishEducation]
  --,[SpanishEducation]
  --,[FrenchEducation]
  --,[EnglishOccupation]
  --,[SpanishOccupation]
  --,[FrenchOccupation]
  --,[HouseOwnerFlag]
  --,[NumberCarsOwned]
  --,[AddressLine1]
  --,[AddressLine2]
  --,[Phone]
  c.datefirstpurchase AS DateFirstPurchase, 
  --,[CommuteDistance]
  g.city AS [Customer City],		                    -- Joined in Customer City from the Geography Table
  g.StateProvinceName AS [Customer State],		        -- Joined in Customer State from the Geography Table
  g.EnglishCountryRegionName AS [Customer Country]		-- Joined in Customer Country from the Geography Table
FROM 
  [dbo].[DimCustomer] AS c 
  LEFT JOIN [dbo].[DimGeography] AS g ON g.GeographyKey = c.GeographyKey 
ORDER BY 
  CustomerKey ASC -- Order list by CustomerKey
  ~~~


### -  FACT_Sales:
~~~
-- Cleaned Fact_InternetSales table -- 
SELECT 
  [ProductKey], 
  [OrderDateKey], 
  [DueDateKey], 
  [ShipDateKey], 
  [CustomerKey],
  --,[PromotionKey]
  --,[CurrencyKey]
  --,[SalesTerritoryKey] 
  [SalesOrderNumber],
  --,[SalesOrderLineNumber]
  --,[RevisionNumber]
  --,[OrderQuantity]
  --,[UnitPrice]
  --,[ExtendedAmount]
  --,[UnitPriceDiscountPct]
  --,[DiscountAmount]
  --,[ProductStandardCost]
  --,[TotalProductCost]
  [SalesAmount]
  --,[TaxAmt]
  --,[Freight]
  --,[CarrierTrackingNumber]
  --,[CustomerPONumber]
  --,[OrderDate]
  --,[DueDate]
  --,[ShipDate]
FROM 
  [dbo].[FactInternetSales] 
WHERE 
  LEFT(OrderDateKey, 4) >= YEAR(GETDATE())-2		-- Ensures to only return order date within two years of date 
ORDER BY 
  OrderDateKey ASC
~~~

## Data Model
- Below is a screenshot of the data model after data cleaning and tables were loaded into Power BI.
- This data model also shows how FACT_Budget is connected to FACT_InternetSales and other DIM tables.
![](https://github.com/OluwakemiOretade/Sales-Analysis/blob/main/2.%20Data%20Cleaning%20%26%20Transformation/Data_Model.png)

## 3. ![Data Visualization](https://github.com/OluwakemiOretade/Sales-Analysis/tree/main/3.%20Data%20Visualization)

To assist the decision making process, the sales managers can view the [interactive sales management dashboard](https://app.powerbi.com/view?r=eyJrIjoiN2Q1ZjhhYTktZTliZC00MWZmLTliOTQtODFlYjE3ZTlkNzgxIiwidCI6IjVkMTA4NWVkLTYyZDgtNGRhZC05MDI1LWY5YWFiNDIzNDllZSJ9&pageName=ReportSection) which consists of an overview page and two additional pages focusing on customer data and product data over time.

![](Customer_Dashboard.jpg)

