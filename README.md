# Retail Customer Segmentation

## Table of Content

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Preparation](#data-preparation)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)

### Project Overview

Customer Segmentation is a typical strategy used by organizations to categorize clients based on their demographics, shopping patterns, or other traits. This practice is essential for understanding the diverse needs and preferences of the customer base, allowing businesses to tailor their products, services, and marketing efforts more effectively.

### Data Sources

Retail Dataset: The primary dataset used for this analysis is the 'Retail.csv' file, containing detailed information about each sale made by the company. This dataset contains the following variables:
InvoiceNo: The invoice number for each transaction
StockCode: The unique code for each product sold
Description: The description of each product sold
Quantity: The quantity of each product sold in each transaction
InvoiceDate: The date and time of each transaction
UnitPrice: The price of each product sold
CustomerID: The unique identifier for each customer
Country: The country where each transaction occurred

### Tools

- PostgreSQL - Data Cleaning & Analysis [download_here](https://www.postgresql.org/)

### Data Preparation

In the initial data preparation phase, I performed the following tasks:
1. Data loading and inspection
2. Handling null values
3. Data cleaning and formating

### Exploratory Data Analysis

I explored the retail dataset to answer key questions such as;

- What is the distribution of order values across all customers in the dataset?
- How many unique products has each customer purchased?
- Which customers have only made a single purchase from the company?
- Which products are most commonly purchased together by customers in thedataset?

### Data Analysis

```sql
WITH
	firstcte AS (
	/* Here I created a new calculated metric called order value by mnultiplying quantity and unitprice*/
	SELECT
	customerid,
	stockcode,
	description,
	quantity,
	unitprice,
	ROUND(CAST(SUM(quantity * unitprice) AS DECIMAL), 2) AS ordervalue   
FROM
	retail
	GROUP BY customerid,
	stockcode,
	description,
	quantity,
	unitprice
HAVING
	ROUND(CAST(SUM(quantity * unitprice) AS DECIMAL), 2) > 0
	AND customerid IS NOT NULL
),
	
secondcte AS (
-- Here I classified order values into 4 classes
	SELECT
		*,
		CASE 
			WHEN ordervalue BETWEEN 0 AND 500 THEN 'a, 0 - 500'
			WHEN ordervalue BETWEEN 501 AND 5000 THEN 'b, 501 - 5k'
			WHEN ordervalue BETWEEN 5000 AND 100000 THEN 'c, 5k - 100k'
			ELSE 'd - above 100k'
		END AS orderclass
	FROM firstcte
)
SELECT orderclass,
	COUNT(*)AS orderno
FROM secondcte
GROUP BY orderclass
ORDER BY 1;
```


