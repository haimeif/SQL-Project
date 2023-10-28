Question 1: 

What is the total number of unique visitors (fullVisitorID)?

SQL Queries:

```
-- Since some of the “FullVisitorId" exist in both analytics and all_sessions tables and the others exist only in either table. To get the total number of unique visitors (unique “FullVisitorId"), I decide to combine the “FullVisitorId" columns in both tables.

-- Use SELECT statement to get the "FullVisitorId" columns from both analytics and all_sessions tables, and combine them using UNION to eliminate the duplicate values. Then use this query as a subquery and COUNT the number of visitors (unique FullVisitorId").

SELECT	COUNT("FullVisitorId") AS "NumberOfVisitor"
FROM	
	(SELECT	"FullVisitorId"
	FROM	analytics
	UNION
	SELECT	"FullVisitorId"
	FROM	all_sessions)
```

Answer: 

The total number of unique visitors is 130,345.



Question 2: 

What is the total number of unique visitors from referring sites?

SQL Queries:

```
-- Same as last question, I combined both analytics and all_sessions tables to get the total within the dataset.

-- Use SELECT statement to get the "FullVisitorId" and "ChannelGrouping" columns from both analytics and all_sessions tables and combine them using UNION to eliminate the duplicate values. Then use this query as a subquery and GROUP the "ChannelGrouping" and COUNT the number of visitors (unique FullVisitorId") within each referring sites ("ChannelGrouping") group. ORDER BY descending number of visitors to see from the highest to lowest values.

SELECT	"ChannelGrouping", COUNT("FullVisitorId") AS "NumberOfVisitor"
FROM	
	(SELECT	"FullVisitorId", "ChannelGrouping"
	FROM	analytics
	UNION
	SELECT	"FullVisitorId", "ChannelGrouping"
	FROM	all_sessions)
GROUP BY "ChannelGrouping"
ORDER BY "NumberOfVisitor" DESC
```

Answer:

This query returns a table with number of unique visitors from each referring sites (Channel Grouping). Most of the visitors are from 'Organic Search', the lowest number is from ‘Other’ (unknown sources), and the second lowest is from ‘Display’.



Question 3: 

How many unique visitors visited each in month?

SQL Queries:

```
-- Like the last two questions, I combined the “FullVisitorId” and “Date” columns from analytics and all_sessions tables using UNION to eliminate the duplicate values and as subquery. Then EXTRACT the year and month from the “Date” columns as two separate columns, and GROUP the year and month and COUNT the DISTINCT "FullVisitorId" visited in each month. ORDER BY descending number of visitors to see from the highest to lowest values. I also filter out August 2017, because it has only the first day of the month, it will not be accurate comparing date of one day to a whole month.

SELECT		EXTRACT(YEAR FROM "Date") AS "Year",
		EXTRACT(MONTH FROM "Date") AS "Month",
		COUNT(DISTINCT "FullVisitorId") AS "NumberOfVisitors"
FROM		(SELECT	"FullVisitorId", "Date"
		 FROM	analytics
		 UNION
		 SELECT	"FullVisitorId", "Date"
		 FROM		all_sessions)
WHERE		"Date" BETWEEN '2016-08-01' AND '2017-07-31'
GROUP BY 	EXTRACT(YEAR FROM "Date"), EXTRACT(MONTH FROM "Date")
ORDER BY 	"NumberOfVisitors" DESC
```

Answer:

This Query returns a table of number of unique visitors visited in each month. It shows July 2017 has the highest number of unique visitors and October 2016 has the lowest number of unique visitors.



Question 4:

What are the total visits of each month?

SQL Queries:

```
-- Combine the “FullVisitorId” and “Date” columns from analytics and all_sessions tables using UNION to eliminate the duplicate values and as subquery. Then EXTRACT the year and month from the “Date” columns as two separate columns, and GROUP the year and month and COUNT the "FullVisitorId" visited in each month. ORDER BY descending total visits to see from the highest to lowest values. August 2017 is filtered out, because it has only the first day of the month, it will not be accurate comparing date of one day to a whole month.

SELECT		EXTRACT(YEAR FROM "Date") AS "Year",
		EXTRACT(MONTH FROM "Date") AS "Month",
		COUNT("FullVisitorId") AS "TotalVisits"
FROM		(SELECT	"FullVisitorId", "Date"
		FROM	analytics
		UNION
		SELECT	"FullVisitorId", "Date"
		FROM	all_sessions)
WHERE		"Date" BETWEEN '2016-08-01' AND '2017-07-31'
GROUP BY 	EXTRACT(YEAR FROM "Date"), EXTRACT(MONTH FROM "Date")
ORDER BY 	"TotalVisits" DESC

-- This is basically the same query as the last question, the only different is instead of counting the distinct "FullVisitorId", it counts all in the table because this question ask for total visits, and there might be same visitor visited more than once in a month.
```

Answer:

This Query returns a table of total visits in each month. It shows July 2017 has the highest visits and October 2016 has the lowest visits. 



Question 5: 

What is the total revenue for each month?

SQL Queries:

```
-- Combine the analytics and all_sessions tables using FULL OUTER JOIN to include all "FullVisitorId" and use INNER JOIN to join the sales_by_sku by the “ProductSKU” for quantity ordered for each product. Then filter out the August 1st 2017 using WHERE clause. In SELECT statement EXTRACT the year and month from the “Date” columns as two separate columns, and GROUP BY year and month, then use the SUM clause to calculate to total revenue (“TotalOrdered" * "ProductPrice") for each month. ORDER BY descending monthly revenue to see from the highest to lowest values.

SELECT		EXTRACT(YEAR FROM als."Date") AS "Year",
		EXTRACT(MONTH FROM als."Date") AS "Month",
		SUM(sbs."TotalOrdered" * als."ProductPrice") AS "MonthlyRevenue"
FROM		analytics ana
FULL OUTER JOIN	all_sessions als USING("FullVisitorId")
JOIN		sales_by_sku sbs USING("ProductSKU")
WHERE		als."Date" BETWEEN '2016-08-01' AND '2017-07-31'
GROUP BY 	EXTRACT(YEAR FROM als."Date"), EXTRACT(MONTH FROM als."Date")
ORDER BY 	"MonthlyRevenue" DESC
```

Answer:

This Query returns a table of total revenue in each month. It shows June 2017 has the highest revenue and October 2016 has the lowest revenue. By analyzing the results from this and the last question, we can see that higher visits do not mean it will have higher revenue.
