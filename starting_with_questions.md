Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

```
-- First create a CTE table JOIN both all_sessions and sales_by_sku tables, SELECT the “Country” and “City” columns and GROUP them for calculation of the total revenue (quantity * price) by city. Also use window function NTILE to divide the revenues into 3 levels and order by descending order.

WITH RevLevel AS
	(SELECT als."Country", als."City", SUM(sbs."TotalOrdered" * als."ProductPrice") AS 			"TransRev",
		NTILE(3) OVER(ORDER BY SUM(sbs."TotalOrdered" * als."ProductPrice") DESC) AS "NTile"
	FROM	all_sessions als
	JOIN	sales_by_sku sbs USING("ProductSKU")
	GROUP BY als."Country", als."City"
	ORDER BY "TransRev" DESC, als."Country", als."City")

-- Then from the CTE table, SELECT the "Country", "City", "TransRev" columns and use CASE WHEN clause to change the ntile values 1-3 to text ‘High’, ‘Medium’, ‘Low’, and filter to output the rows with ntile = 1.

SELECT	"Country", "City", "TransRev",
	CASE WHEN "NTile" = 1 THEN 'High'
	WHEN "NTile" = 2 THEN 'Medium'
	ELSE 'Low' END AS "Level"
FROM 	RevLevel
WHERE 	"NTile" = 1 
```

Answer:

The result shows 122 cities classified as 'High' level, and among the top 10 highest revenue, 8 of the cities are from the US, and the other 2 cities are from the UK. 



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

```
-- Begin with JOIN both tables all_sessions and sales_by_sku, then SELECT the "Country" and "City" columns and GROUP them for calculation of the average quantities ordered (“TotalOrdered”) by each city and country. And ORDER it in descending order to see the highest to lowest average.

SELECT		als."Country", als."City", AVG(sbs."TotalOrdered") AS "AvgOrdered"
FROM 		all_sessions als
JOIN 		sales_by_sku sbs USING("ProductSKU")
GROUP BY 	als."Country", als."City"
ORDER BY 	"AvgOrdered" DESC, als."Country", als."City" 
```

Answer:

From the result, Brno of Czechia and Riyadh of Saudi Arabia has the highest average number of products ordered. Compared to the highest revenue from cities of US from the last question, we can explain that visitors from Brno of Czechia and Riyadh of Saudi Arabia ordered more products with lower prices and the US visitors ordered less quantity of products with higher prices.



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

```
-- This query COUNT the number of categories ordered by each city and country GROUP. And use the window function to RANK the count by city. Lastly ORDER BY descending order to view from highest to lowest count.

SELECT		"Country", "City", "V2ProductCategory", 
		COUNT("V2ProductCategory") AS "ProductCategoryCount",
		RANK() OVER(PARTITION BY "City" ORDER BY COUNT("V2ProductCategory") DESC)
FROM		all_sessions als
GROUP BY	"Country", "City", "V2ProductCategory"
ORDER BY	COUNT("V2ProductCategory") DESC, "Country", "City"
```

Answer:

The value structure of the "V2ProductCategory" column is a string containing the main categories with multiple subcategories, that makes many different combination category names. Due to the complication structure of the category names, it is hard to see any pattern in the category ordered. But from the ranking, it seems like the top selling Brand is YouTube in most of the cities across countries. 



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

```
-- Begin with creating a subquery to calculate the SUM of total quantity of products ordered by each city and country GROUP, from the JOIN tables all_sessions and sales_by_sku. Then use the window function to RANK the sum by country and city with descending order.

-- From the subquery, SELECT all columns except the rank and filter to output the 1st rank of each city and country. And ORDER BY the sum of total quantity of products ordered in descending order to see from the highest to lowest.

SELECT		"Country", "City", "ProductSKU", "V2ProductName", "TotalSold"
FROM		(SELECT als."Country", als."City", als."ProductSKU", als."V2ProductName",
		SUM(sbs."TotalOrdered") AS "TotalSold",
		RANK() OVER(PARTITION BY als."Country", als."City" ORDER BY SUM(sbs."TotalOrdered") 		DESC)
		FROM	all_sessions als
		JOIN	sales_by_sku sbs using("ProductSKU")
		GROUP BY 	als."Country", als."City", als."ProductSKU", als."V2ProductName") 
		AS "TotalSoldRank"
WHERE		RANK = 1
ORDER BY 	"TotalSold" DESC
```

Answer:

From the top 10 rows of the table, the top selling product from cities in the US and Canada is the '17oz Stainless Steel Sport Bottle', from cities in UK is the 'Hard Cover Journal', and Germany and Japan are the 'Ballpoint LED Light Pen'. 



**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

```
-- JOIN both all_sessions and sales_by_sku tables, SELECT the “Country” and “City” columns and GROUP them for calculation of the total revenue (quantity * price) by city. ORDER the revenue in descending order.

SELECT		als."Country", als."City", SUM(sbs."TotalOrdered" * als."ProductPrice") AS "TransRev"
FROM		all_sessions als
JOIN 		sales_by_sku sbs USING("ProductSKU")
GROUP BY 	als."Country", als."City"
ORDER BY 	"TransRev" DESC, als."Country", als."City"
```

Answer:

From this result, it shows the highest revenues are generated by the cities of US and followed by cities in UK. It also shows at the bottom of the table, some cities in US, Japan, Canada, etc. generated zero revenue. The company could make regional marketing strategies and decisions based on this result.


