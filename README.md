# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals

This project initially provide 5 datasets in csv files, that contains data of an ecommerce business, tables included are all_sessions , analytics, products, sales_by_sku, and sales_report. The all_sessions tables contain 32 columns, it seems including all data on visitors/customers, products, sales, and traffics on site. The analytics table seems related to the all_sesson table, it also contains data on visitors/customers and traffics on site. The products table should be all product information. Both sales tables contain sales data which sales_by_sku include only product sku and quantity ordered and sales_report has more details information.

Goal 1: Create and import tables and columns correctly into pgAdmin database with appropriate data type for each column.
Goal 2: Clean the dataset as cleaned as possible to answer questions asked.
Goal 3: Retrieve useful information from the cleaned data to ask and answer questions.


## Process

Step 1: Explore the csv tables to get a rough idea on the columns like number of columns, data types and type of information. Create new database, tables, and columns in pgAdmin 4, and import csv tables into pgAdmin database.

Step 2: Explore each column in each table in pgAdmin by writing and execute queries, to get more information and understanding about the data like format, null values, duplicate values, etc., as well as relationship and connections between tables.

Step 3:  Read through the questions to determine what data are needed to answer the questions. Develop a QA process on the required tables and columns. Validate the data, process cleansing if necessary or state issues that cannot be clean for the moment with reasons, and test or validate again after cleaning process. Then work on queries to answer questions.

Step 4: Based on my understanding of the data, set my own set of questions, and develop a QA process as the last step and then work on answering the questions.


## Results

I found this dataset is more complicated and confusing than expected, data and information provided seems unclear and incomplete. It has a lot of missing values and unorganized structures and format. 
However, based on my understanding on the data and the questions. I was able to choose the tables and columns to use to answer to questions. Most of the questions can be answered by the all_sessions table, but extra information would be needed from other tables as well. For example, the questions asked about the transaction revenue by city and country, I found that the TransactionRevenue and Quantity columns in all_sessions table is having many null values, so I decided to join the sales_by_sku table to get the quantities data and multiply the price values in all_seesions to get the revenues. As well as my questions on the visitors and visits by date, I combine the analytics table with the all_session table to get a complete data on visitors.


## Challenges 

The most challenging part of this project is that there is not enough information about the dataset, so it is difficult to understand what each table and column is representing, as well as what is the relationship and connection between these tables. For example, what exactly the information is about in the all_session and analytics tables, and what does table name ‘all_sessions’ and ‘analytics’ mean in this dataset? How they connect with each other. Some of the columns in the tables are confusing as well, what is the meaning of the ‘Time’ and ‘TimeOnSite’ columns, are they related to each other and what format it should be in? What is the different between ‘ProductQuantity’ and ‘ItemQuantity’ in the all_sessions table? And so on.

Due to the lack of understanding, I have spent a large amount of time trying to dig into this dataset, and struggled on which tables and columns can be used to answer the questions and how should I be cleaning the data. The only way to proceed is to make assumptions on the data, and choose the required table and columns, clean the data and answer questions based on these assumptions.


## Future Goals

If I had more time, I might spend more time on studying the dataset and try to find out the answers for the questions in the Challenges part above. As well as finding out the connection between at tables, what is the primary and foreign keys. I would also spend more time on cleaning the data, like correcting the mismatch country-city rows in all_sessions and try to make the structure of the category name more organized.

