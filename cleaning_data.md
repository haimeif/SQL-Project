What issues will you address by cleaning the data?

By cleaning the data, the incorrect unit price values will become proper format, the city values will be less inconsistent, and the tables and columns will be in more organize and clear view.



Queries:
Below, provide the SQL queries you used to clean your data.

1. While exploring the dataset, I found the “ProductPrice" column from the all_sessions table is in an unreasonable scale (ex. 18,990,000) and invalid format (no decimals). In order to retrieve accurate pricing from the data, values in these columns has to be divide by 1,000,000 and round to 2 decimals place.

-- First I used the SELECT statement to try if the ROUND clause in below query would returns the result I expected, and it did. The “ProductPrice" is now in a reasonable scale and format (ex. 18.99).
```
SELECT	ROUND("ProductPrice"/1000000, 2) AS "ProductPrice"
FROM	all_sessions;
```

-- Then I used UPDATE statement to change the dataset as following.
```
UPDATE all_sessions
	SET "ProductPrice" = ROUND("ProductPrice"/1000000, 2)
```

2. When working on the questions that uses the “Country” and “City” columns in the all_sessions table, I found that although there is no null values in these columns, but there are inconsistent values like '(not set)' and 'not available in demo dataset'. In order to make the values less inconsistent, I tried to replace the inconsistent values of the “City” column with the valid values in the “Country” column. That means when Country is ‘Canada’ and City id ‘(not set)’, the ‘(not set)’ will be replaced by ‘Canada’. In this way, the replaced values can be treated as one special city in that country. But for the rows that both values are ‘(not set)’ will remain unchanged because there is not enough information to determine what the values should be.

-- Same in here, before I use the UPDATE statement, I first used the SELECT statement with CASE WHEN clause to check if the query returns what it is expected.
```
SELECT	“Country”, 
	CASE WHEN "City" NOT IN ('(not set)', 'not available in demo dataset') THEN "City" 
	WHEN "City" IN ('(not set)', 'not available in demo dataset') THEN "Country" END AS “City”
FROM	all_sessions
```

-- Then I used UPDATE statement to change the dataset as following.
```
UPDATE all_sessions
	SET "City" = CASE WHEN "City" NOT IN ('(not set)', 'not available in demo dataset') THEN 	"City" 
	WHEN "City" IN ('(not set)', 'not available in demo dataset') THEN "Country" END
```

Beside the ‘(not set)’ values, the “City” column has another issue, that is some of the rows are having incorrect Country-City matches, like Canada-New York, or United States-Hong Kong, etc. These are inaccurate values that might causes incorrect information to the results and answers. But at this point, I still haven’t figure out the way to correct the issue, but this should be something to be aware of.

3. I also checked if there are columns with null values in the entire column using the following query on different table names and column names.

-- SELECT and returns non-null values within the table, it will return an empty table if it is empty column.
```
SELECT	*
FROM 	all_sessions
WHERE 	"SearchKeyword" IS NOT NULL
```

After checking, there are a couple of empty columns, and these columns can be removed if a clear view of the table is required, because it does not have any data values can be used. Removing uses the following query:

```
ALTER TABLE all_sessions
DROP COLUMN "SearchKeyword"
```

I did not remove any if these columns for this project because they do not affect the columns I needed to answer the questions, and just in case I might need them in the future.

4. I also did some extra cleaning on the “Name” columns in the sales_report and products tables, even though I didn’t need to use these columns at the end. Some of “Name” values has white spaces at the beginning of the word.

-- Use the SELECT statement with LTRIM and RTRIM clause to try first,
```
SELECT LTRIM(RTRIM("Name"))
FROM sales_report;
```

-- and then the UPDATE statement to remove the leading and trailing spaces.
```
UPDATE sales_report
SET "Name" = LTRIM(RTRIM("Name"))
```
