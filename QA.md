What are your risk areas? Identify and describe them.

The risk areas of this dataset are the accuracy, completeness, and consistency of the data. The dataset has inaccuracy data format and values, like unit price in a very large scale, and values time columns is a long integer that unable to determine data type or format, some country and city names are mismatch or missing. There are also many missing (null) and inconsistent values (‘(not set)’).



QA Process:
Describe your QA process and include the SQL queries used to execute it.

For the QA process, I first started with data profiling, which to verify if all tables, columns, and csv files has created and imported into the database successfully and correctly.

-- This query below returns a table with information about the tables in the database.
```
SELECT 	*
FROM	information_schema.tables
WHERE 	table_schema = 'public'
```

The result shows all 5 tables are imported into the ecommerce database successfully.

-- This query returns a table with information about the columns created and imported into the table. It can be used to check all tables just by changing the table_name in the query.
```
SELECT	*
FROM	information_schema.columns
WHERE	table_schema = 'public'
	AND table_name = 'all_sessions'
```

The result shows all columns are imported correctly into each table.

By analyzing the results of these queries, I have got an insight on what is the data type for each column, and noticed there might be missing data in the nullable columns.

Then I have developed the QA process for the tables and columns I needed to answer the questions.

For question 1, 2 & 5 in the start_with_questions part, I used the columns “Country”, “City” and “ProductPrice” in the all_sessions table and the “TotalOrdered” in the sales_by_sku table.

QA Process - Data Validation:

This query checks whether there are missing values in "Country", "City", "TotalOrdered", "ProductPrice" columns.

-- JOIN both tables and filter the values that is null.
```
SELECT	"Country", "City", "TotalOrdered", "ProductPrice"
FROM	all_sessions als
JOIN	sales_by_sku sbs USING("ProductSKU")
WHERE	"Country" IS NULL 
	OR "City" IS NULL 
	OR "TotalOrdered" IS NULL 
	OR "ProductPrice" IS NULL
```

The result shows there are no missing values in these columns.

Next, check for inconsistent values in "Country", "City" columns. When exploring the dataset, I found in these columns, it contains values like (not set)' and 'not available in demo dataset' instead of a valid country name and city name. the following query check whether it is true.

-- Use WHERE clause to output rows with ‘(not set)' or 'not available in demo dataset' values.
```
SELECT	"Country", "City"
FROM	all_sessions
WHERE	"Country" IN ('(not set)', 'not available in demo dataset')
	OR "City" IN ('(not set)', 'not available in demo dataset')
```

It returns a table with "Country" and "City" columns that contains ‘(not set)' or 'not available in demo dataset' values. Cleaning might be required for this issue during the data cleaning process. Another issue of the “City” column that need to be aware of is some of the rows are containing mismatched country-city, like Canada-New York. This might cause inaccuracy of results and answers.

Next checking if there are invalid values (negative values) in "TotalOrdered" and "ProductPrice" columns with the query below.

-- First JOIN both tables to SELECT these columns from two separate tables, then use WHERE clause to output all values less than zero.
```
SELECT	"TotalOrdered", "ProductPrice"
FROM		all_sessions als
JOIN		sales_by_sku sbs USING("ProductSKU")
WHERE	"TotalOrdered" < 0 OR "ProductPrice" < 0
```

The result shows no negative values in the "TotalOrdered" and "ProductPrice" columns.

-- Then check whether "TotalOrdered" and "ProductPrice" columns are in a valid format using the following query to output all values in these two columns.
```
SELECT	"TotalOrdered", "ProductPrice"
FROM	all_sessions als
JOIN	sales_by_sku sbs USING("ProductSKU")
```

The result shows the values in "ProductPrice" column is in an unreasonable scale (ex. 18,990,000) and invalid format (no decimal places). This column needs to be cleaning during the data cleaning process to obtain accurate information.

QA Process - Data Cleansing and Testing

Data Cleansing for “City” column

As there are inconsistent values '(not set)' and 'not available in demo dataset' in the "City" column of the all_sessions table, they can be replace by the valid values in the "Country" column to make it less inconsistent and treated as a special city with the same name as the country.

-- The following query is to replace the (not set)' and 'not available in demo dataset' city name with the country name by using the UPDATE statement and CASE WHEN clause.
```
UPDATE all_sessions
SET "City" = CASE WHEN "City" NOT IN ('(not set)', 'not available in demo dataset') THEN "City"
	WHEN "City" IN ('(not set)', 'not available in demo dataset') THEN "Country" END
```

After executed the UPDATE query above, "City" rows with inconsistent values will be replace by values in the "Country" column. But the rows with inconsistent values in both columns will remain unchanged because there is not enough information to find out the valid values for these rows.

Testing for “City” column

-- This query checks whether the "City" column has been updated correctly.
```
SELECT	"Country", "City"
FROM	all_sessions
WHERE	"City" IN ('(not set)', 'not available in demo dataset')
```

It returns only rows that both "City" and "Country" are '(not set)', update has been done successfully. And only 24 out of 15134 rows are '(not set)', it should have very small impact to the result.

Data Cleansing for ProductPrice" column

-- Following query convert "ProductPrice" column to proper scale and format to 2 decimals numeric in all_sessions table, by using the UPDATE statement and ROUND clause
```
UPDATE all_sessions
	SET "ProductPrice" = ROUND("ProductPrice"/1000000, 2)
```

After update, the "ProductPrice" values should be in a reasonable range with 2 decimal places.

Testing for ProductPrice" column

-- Below query output the "ProductPrice" column and check whether it has been updated successfully.
```
SELECT	"ProductPrice"
FROM	all_sessions
```

The result shows the values has been updated to reasonable scale and 2 decimals numeric (ex. 18.99).

For question 3 & 4 in the start_with_questions part, I used the columns "ProductSKU", “V2ProductCategory” and "V2ProductName" in the all_sessions and sales_by_sku tables

QA Process - Data Validation:

This query checks whether there are missing values in "ProductSKU", "V2ProductCategory", "V2ProductName" columns in all_sessions and sales_by_sku tables.

-- JOIN both tables and filter the values that is null.
```
SELECT	"ProductSKU", "V2ProductCategory", "V2ProductName"
FROM	all_sessions als
JOIN	sales_by_sku sbs using("ProductSKU")
WHERE	"ProductSKU" IS NULL 
	OR "V2ProductCategory" IS NULL 
	OR "V2ProductName" IS NULL
```

The result shows there are no missing values in these columns.

Next, check duplicate data for sales_by_sku table.

-- This query COUNT each "ProductSKU" and output values that has count greater than 1 to determine if there is duplicate data.
```
SELECT		"ProductSKU", COUNT(*)
FROM		sales_by_sku
GROUP BY 	"ProductSKU"
HAVING 		COUNT(*) > 1
```

An empty table returned, there is no duplicate data in sales_by_sku table.

Next, check for inconsistent values in ""ProductSKU", "V2ProductCategory", "V2ProductName" columns. When exploring the dataset, I found in these columns, it contains values like (not set)' and '${escCatTitle}' instead of a valid name. the following query check whether it is true.

-- JOIN two tables and use WHERE clause to output rows with ‘(not set)' or '${escCatTitle}' values.
```
SELECT	"ProductSKU", "V2ProductCategory", "V2ProductName"
FROM	all_sessions als
JOIN	sales_report sr using("ProductSKU")
WHERE	"V2ProductCategory" IN ('(not set)', '${escCatTitle}')
	OR "ProductSKU" = '(not set)'
	OR "V2ProductName" = '(not set)'
```

It returns 485 rows in "V2ProductCategory" contains invalid values '(not set)' and '${escCatTitle}'. Because the value structure of the "V2ProductCategory” is one string contains the main category plus multiple subcategories, which creates many different combinations of category name. Therefore, it is difficult to determine a valid product category name to replace in, so no cleansing will be performed at the moment.

In the start_with_data part, I used the columns " FullVisitorId", “ChannelGrouping” and " Date" from both all_sessions and analytic tables to answer my questions.

QA Process - Data Validation:

The following queries run separately to checks whether there are missing values in FullVisitorId", “ChannelGrouping” and " Date" from both all_sessions and analytic tables.

-- FULL OUTER JOIN the two tables to include all "FullVisitorId" and output any null values in the column.
```
SELECT		"FullVisitorId"
FROM		analytics
FULL OUTER JOIN	all_sessions USING("FullVisitorId")
WHERE		"FullVisitorId" IS NULL
```

There is no missing values in "FullVisitorId" column in both tables.

-- SELECT “ChannelGrouping” and “Date” that has null values.
```
SELECT	"ChannelGrouping", "Date"
FROM	analytics
WHERE	"ChannelGrouping" IS NULL
	OR "Date" IS NULL
```

```
SELECT	"ChannelGrouping", "Date"
FROM	all_sessions
WHERE	"ChannelGrouping" IS NULL
	OR "Date" IS NULL
```

There are no missing values in "ChannelGrouping" and "Date" column in both tables.

Check total number of distinct "FullVisitorId" in analytics and all_sessions tables. 

-- First get the DISTINCT "FullVisitorId" from both tables and combine using UNION ALL to include duplicate values. Then use it as subquery and COUNT the number of all "FullVisitorId".
```
SELECT	COUNT("FullVisitorId")
FROM	
	(SELECT		DISTINCT "FullVisitorId"
	FROM		analytics
	UNION ALL
	SELECT		DISTINCT "FullVisitorId"
	FROM		all_sessions)
```

The total number of distinct "FullVisitorId" in both tables is 134,241. As a result, the total number of unique visitors (FullVisitorId) in the database should not excess 134,241, could be less if some FullVisitorId" exist in both tables and that would count as one unique value.

Check the date range of the "Date" column in analytics and all_sessions tables.

-- SELECT the earliest and latest “Date” from both tables using MIN and MAX clause and combine them using UNION ALL.
```
SELECT	MIN("Date"), MAX("Date")
FROM	analytics
UNION ALL
SELECT	MIN("Date"), MAX("Date")
FROM	all_sessions
```

The date range in analytics table is between '2017-05-01' and '2017-08-01'. The date range in all_sessions table is between '2016-08-01' and '2017-08-01'. As a result, the date range in this dataset is between '2016-08-01' and '2017-08-01'. If monthly result is needed, '2017-08-01' shouldn’t be included in the calculation because it has date of only one day for the month.
