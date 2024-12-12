# Intregated Business Analytics

## Analyzed sales, product, customer, and operational efficiency using PostgreSQL, Power BI, and Python

### Project Overview

This project provides insights into sales performance, product performance, customer behavior, and operational efficiency for a retail business. It analyzes key metrics such as revenue, profit, product-level trends, customer retention, and store performance. The project uncovers actionable insights, evaluates business strategies, and identifies opportunities to improve profitability, product optimization, customer satisfaction, and operational efficiency.

### Data Sources

1. Customers Data: Contains detailed information about customers name, including demographics, membership levels, and contact details.
2. Products Data: Includes product details such as names, categories, retail prices, and costs.
3. Stores Data: Provides store-level information, including store names, locations, and total square footage.
4. Regions Data: Lists regional details, such as region names and districts.
5. Calendar Data: Serves as a date dimension table for time-based analysis.
6. Returns Data: Tracks informations on return date, including quantities and it is connected to Products and Stores Data through their IDs.
7. Orders 1997 Data: Includes orders details for 1997, such as order date, stock date, quantities, and it links to Customers, Products, and Stores Data through thei IDs.
8. Orders 1998 Data: Includes orders details for 1998, structured the same way as Orders 1997.

### Tools

- PostgreSQL
  - Data Preparation
- Power BI
  - EDA Process with DAX 
  - Data Visualization
- Python
  - Find the Correlation

### Data Preparation Process

1. Data Import and Inspection
2. Data Cleaning
3. Data Transformation
4. Defining Relationships

#### 1. Data Import and Inspection

In this phase, i import the data and check it for any issues. This includes:

- Importing Data: Bringing the data into the system.
- Inspecting Data: Looking for errors like missing values or incorrect formats.

I found that the date column in several datasets had mixed formats, with some values not being actual dates. To fix this, I imported the date columns as **VARCHAR** (text) to keep all the values intact. Later, during the data cleaning & type conversion phase, I will convert them to proper date formats for consistency and analysis.

Below are the SQL queries and results for each Iport and Inspection step.
```sql
/* ================================================================================
   STEP 1: DATA IMPORT AND INSPECTION
   ================================================================================ */


/* --------------------------------------------------------------------------------
   1.1. Create Table: mavenmarket_customers
   -------------------------------------------------------------------------------- */
   
CREATE TABLE mavenmarket_customers(
	customer_id SERIAL PRIMARY KEY,
	customer_acct_num VARCHAR(150),
	first_name VARCHAR(20),
	last_name VARCHAR (20),
	customer_address TEXT,
	customer_city VARCHAR (20),
	customer_state_province VARCHAR(20),
	customer_postal_code VARCHAR (50),
	customer_country VARCHAR (20),
	birthdate VARCHAR (20),
	marital_status VARCHAR (10),
	yearly_income VARCHAR (50),
	gender VARCHAR (10),
	total_children SMALLINT,
	num_children_at_home SMALLINT,
	education VARCHAR (50),
	acct_open_date VARCHAR (20),
	member_card VARCHAR (50),
	occupation VARCHAR (50),
	homeowner VARCHAR (10)
);

-- Import data from csv files into mavenmarket_customers table
COPY mavenmarket_customers FROM 'D:\Downloads\MAVEN\Maven\MavenMarket_Customers.csv' delimiter ',' csv header;  



/* --------------------------------------------------------------------------------
   1.2. Create Table: mavenmarket_products
   -------------------------------------------------------------------------------- */
   
CREATE TABLE mavenmarket_products(
	product_id SERIAL PRIMARY KEY,
	product_brand VARCHAR (20),
	product_name VARCHAR (50),
	product_sku VARCHAR (100),
	product_retail_price NUMERIC,
	product_cost NUMERIC,
	product_weight NUMERIC,
	recyclable TEXT,
	low_fat TEXT
);

-- Import data from csv files into mavenmarket_products table
COPY mavenmarket_products FROM 'D:\Downloads\MAVEN\Maven\MavenMarket_Products.csv' delimiter ',' csv header;



/* --------------------------------------------------------------------------------
   1.3. Create Table: mavenmarket_stores
   -------------------------------------------------------------------------------- */
   
CREATE TABLE mavenmarket_stores(
	store_id SERIAL PRIMARY KEY,
	region_id INT,
	store_type VARCHAR (50),
	store_name VARCHAR (20),
	store_street_address VARCHAR (100),
	store_city VARCHAR (20),
	store_state VARCHAR (50),
	store_country VARCHAR (20),
	store_phone VARCHAR (50),
	first_opened_date VARCHAR (20),
	last_remodel_date VARCHAR (20),
	total_sqft INT,
	grocery_sqft INT
);

-- Import data from csv files into mavenmarket_stores table
COPY mavenmarket_stores FROM 'D:\Downloads\MAVEN\Maven\MavenMarket_Stores.csv' delimiter ',' csv header;



/* --------------------------------------------------------------------------------
   1.4. Create Table: mavenmarket_regions
   -------------------------------------------------------------------------------- */

CREATE TABLE mavenmarket_regions(
	region_id SERIAL PRIMARY KEY,
	sales_district VARCHAR (50),
	sales_region VARCHAR (50)
);

-- Import data from csv files into mavenmarket_regions table
COPY mavenmarket_regions FROM 'D:\Downloads\MAVEN\Maven\MavenMarket_Regions.csv' delimiter ',' csv header;



/* --------------------------------------------------------------------------------
   1.5. Create Table: mavenmarket_calendar
   -------------------------------------------------------------------------------- */

CREATE TABLE mavenMarket_calendar(
	date VARCHAR (20)
);

-- Import data from csv files into mavenmarket_calendar table
COPY mavenmarket_calendar FROM 'D:\Downloads\MAVEN\Maven\MavenMarket_Calendar.csv' delimiter ',' csv header;



/* --------------------------------------------------------------------------------
   1.6. Create Table: mavenmarket_returns
   -------------------------------------------------------------------------------- */
SELECT * FROM mavenmarket_returns
CREATE TABLE mavenmarket_returns(
	return_date VARCHAR (50),
	product_id INT,
	store_id INT,
	quantity INT
);

-- Import data from csv files into mavenmarket_returns table
COPY mavenmarket_returns FROM 'D:\Downloads\MAVEN\Maven\MavenMarket_Returns.csv' delimiter ',' csv header;



/* --------------------------------------------------------------------------------
   1.7. Create Table: mavenmarket_orders_1997
   -------------------------------------------------------------------------------- */

CREATE TABLE mavenmarket_orders_1997(
	transaction_date VARCHAR (20),
	stock_date VARCHAR (20),
	product_id INT,
	customer_id INT,
	store_id INT,
	quantity INT
);

-- Import data from csv files into mavenmarket_orders_1997 table
COPY mavenmarket_orders_1997 FROM 'D:\Downloads\MAVEN\Maven\MavenMarket_Orders_1997.csv' delimiter ',' csv header;



/* --------------------------------------------------------------------------------
   1.8. Create Table: mavenmarket_orders_1998
   -------------------------------------------------------------------------------- */

CREATE TABLE mavenmarket_orders_1998(
	transaction_date VARCHAR (20),
	stock_date VARCHAR (20),
	product_id INT,
	customer_id INT,
	store_id INT,
	quantity INT
);

-- Import data from csv files into mavenmarket_orders_1998 table
COPY mavenmarket_orders_1998 FROM 'D:\Downloads\MAVEN\Maven\MavenMarket_Orders_1998.csv' delimiter ',' csv header;
```

#### 2. Data Cleaning

In this phase,i clean the data to make sure it's accurate, consistent, and reliable. The process includes:

- Type Conversion: Changing data to the correct format (e.g., text to dates or numbers).
- Handling Null Values: Fixing missing data by filling or removing it.
- Removing Duplicates: Removing duplicate entries to keep the data accurate.

Below are the SQL queries and results for each Cleaning step.
```sql
/* ================================================================================
   STEP 2: DATA CLEANING
   ================================================================================ */


/* --------------------------------------------------------------------------------
   2.1. Data Cleaning for mavenmarket_customers
   -------------------------------------------------------------------------------- */


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.1.1 Cleaning and Type Conversion for 'birthdate' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Convert values to the valid 'MM/DD/YYYY' format
UPDATE mavenmarket_customers
SET birthdate = TO_DATE(birthdate, 'MM/DD/YYYY')
WHERE birthdate ~ '^\d{1,2}/\d{1,2}/\d{4}$'; --Ensures valid date format

-- Change the data type to DATE for consistent data handling
ALTER TABLE mavenmarket_customers
ALTER COLUMN birthdate TYPE DATE USING birthdate ::DATE;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.1.2 Cleaning and Type Conversion for 'acct_open_date' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Convert values to the valid 'MM/DD/YYYY' format
UPDATE mavenmarket_customers
SET acct_open_date = TO_DATE(acct_open_date, 'MM/DD/YYYY')
WHERE acct_open_date ~ '^\d{1,2}/\d{1,2}/\d{4}$'; --Ensures valid date format

-- Change the data type to DATE for consistent data handling
ALTER TABLE mavenmarket_customers
ALTER COLUMN acct_open_date TYPE DATE USING acct_open_date ::DATE;



/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 
   2.1.3 Cleaning and Type Conversion for 'yearly_income' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Remove unwanted symbols ($ and +) from the column
UPDATE mavenmarket_customers
SET yearly_income = REGEXP_REPLACE(yearly_income, '[\$\+]', '', 'g');

-- Replace 'K' with '000' to convert income values to a full numeric scale
UPDATE mavenmarket_customers
SET yearly_income = REGEXP_REPLACE(yearly_income, 'K', '000', 'g');

-- Handle income ranges (e.g., '50000 - 60000') by averaging the values if a range is present,
-- otherwise, keep the single income value as is
UPDATE mavenmarket_customers
SET yearly_income = 
    CASE
        WHEN yearly_income LIKE '% - %' THEN
            (CAST(SPLIT_PART(yearly_income, ' - ', 1) AS NUMERIC) +
             CAST(SPLIT_PART(yearly_income, ' - ', 2) AS NUMERIC)) / 2
        ELSE CAST(yearly_income AS NUMERIC)  -- Keep single value as it is
    END;

-- Change the data type to NUMERIC for proper calculations
ALTER TABLE mavenmarket_customers
ALTER COLUMN yearly_income TYPE NUMERIC USING yearly_income::NUMERIC;

-- Round yearly_income values to nearest whole number (remove decimals)
UPDATE mavenmarket_customers
SET yearly_income = ROUND(yearly_income, 0);

-- Ensure all yearly_income values are valid numbers before type conversion
UPDATE mavenmarket_customers
SET yearly_income = NULL
WHERE yearly_income IS NULL OR yearly_income < 0;



/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    2.1.4 Null Handling for mavenmarket_customers
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */
	
SELECT COUNT(*) FILTER (WHERE customer_id IS NULL) AS customer_id_nulls,
       COUNT(*) FILTER (WHERE customer_acct_num IS NULL) AS customer_acct_num_nulls,
       COUNT(*) FILTER (WHERE first_name IS NULL) AS first_name_nulls,
       COUNT(*) FILTER (WHERE last_name IS NULL) AS last_name_nulls,
       COUNT(*) FILTER (WHERE customer_address IS NULL) AS customer_address_nulls,
       COUNT(*) FILTER (WHERE customer_city IS NULL) AS customer_city_nulls,
       COUNT(*) FILTER (WHERE customer_state_province IS NULL) AS customer_state_province_nulls,
       COUNT(*) FILTER (WHERE customer_postal_code IS NULL) AS customer_postal_code_nulls,
       COUNT(*) FILTER (WHERE customer_country IS NULL) AS customer_country,
       COUNT(*) FILTER (WHERE marital_status IS NULL) AS marital_status_nulls,
       COUNT(*) FILTER (WHERE yearly_income IS NULL) AS yearly_income_nulls,
       COUNT(*) FILTER (WHERE gender IS NULL) AS gender_nulls,
       COUNT(*) FILTER (WHERE total_children IS NULL) AS total_children_nulls,
       COUNT(*) FILTER (WHERE num_children_at_home IS NULL) AS num_children_at_home_nulls,
       COUNT(*) FILTER (WHERE education IS NULL) AS education_nulls,
       COUNT(*) FILTER (WHERE member_card IS NULL) AS member_card_nulls,
       COUNT(*) FILTER (WHERE occupation IS NULL) AS occupation_nulls,
       COUNT(*) FILTER (WHERE homeowner IS NULL) AS homeowner_nulls,
       COUNT(*) FILTER (WHERE birthdate IS NULL) AS birthdate_country,
       COUNT(*) FILTER (WHERE acct_open_date IS NULL) AS acct_open_date_nulls,
       COUNT(*) FILTER (WHERE yearly_income IS NULL) AS yearly_income_nulls
FROM mavenmarket_customers;

-- No NULL values found in the table



/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    2.1.5 Duplicate Check for mavenmarket_customers
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT customer_id, COUNT(*)
FROM mavenmarket_customers
GROUP BY customer_id
HAVING COUNT(*) > 1
ORDER BY customer_id DESC;

-- No duplicate records found based on customer_id



/* --------------------------------------------------------------------------------
   2.2. Data Cleaning for mavenmarket_products
   -------------------------------------------------------------------------------- */

-- No cleaning or type conversion needed for mavenmarket_products


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    2.2.1 Null Handling for mavenmarket_products
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT COUNT(*) FILTER (WHERE product_id IS NULL) AS product_id_nulls,
       COUNT(*) FILTER (WHERE product_brand IS NULL) AS product_brand_nulls,
       COUNT(*) FILTER (WHERE product_name IS NULL) AS product_name_nulls,
       COUNT(*) FILTER (WHERE product_sku IS NULL) AS product_sku_nulls,
       COUNT(*) FILTER (WHERE product_retail_price IS NULL) AS product_retail_price_nulls,
       COUNT(*) FILTER (WHERE product_cost IS NULL) AS product_cost_nulls,
       COUNT(*) FILTER (WHERE product_weight IS NULL) AS product_weight_nulls,
       COUNT(*) FILTER (WHERE recyclable IS NULL) AS recyclable_nulls,
       COUNT(*) FILTER (WHERE low_fat IS NULL) AS low_fat_nulls
FROM mavenmarket_products;
 
-- 'recyclable' (687 NULLs) and 'low_fat' (1008 NULLs) were set to 0

-- Assumed NULL means "not recyclable" or "not low-fat" based on context
UPDATE mavenmarket_products
SET recyclable = 0
WHERE recyclable IS NULL;

UPDATE mavenmarket_products
SET low_fat = 0
WHERE low_fat IS NULL;

-- Later in the Data Transformation Section, 1 & 0 will be changed to 'Yes' & 'No'


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    2.2.2 Duplicate Check for mavenmarket_products
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT product_id, COUNT(*)
FROM mavenmarket_products
GROUP BY product_id
HAVING COUNT(*) > 1
ORDER BY product_id DESC;

-- No duplicate records found based on product_id



/* --------------------------------------------------------------------------------
   2.3 Data Cleaning for mavenmarket_stores
   -------------------------------------------------------------------------------- */


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.3.1 Cleaning and Type Conversion for 'first_opened_date' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Convert values to the valid 'MM/DD/YYYY' format
UPDATE mavenmarket_stores
SET first_opened_date = TO_DATE(first_opened_date, 'MM/DD/YYYY')
WHERE first_opened_date ~ '^\d{1,2}/\d{1,2}/\d{4}$'; -- Ensures valid date format 

-- Change the data type to DATE for consistent data handling
ALTER TABLE mavenmarket_stores
ALTER COLUMN first_opened_date TYPE DATE USING first_opened_date::DATE;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.3.2 Cleaning and Type Conversion for 'last_remodel_date' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */
   
-- Convert values to the valid 'MM/DD/YYYY' format
UPDATE mavenmarket_stores
SET last_remodel_date = TO_DATE(last_remodel_date, 'MM/DD/YYYY')
WHERE last_remodel_date ~ '^\d{1,2}/\d{1,2}/\d{4}$'; -- Ensures valid date format 

-- Change the data type to DATE for consistent data handling
ALTER TABLE mavenmarket_stores
ALTER COLUMN last_remodel_date TYPE DATE USING last_remodel_date::DATE;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    2.3.3 Null Handling for mavenmarket_stores
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT COUNT(*) FILTER (WHERE store_id IS NULL) AS store_id_nulls,
       COUNT(*) FILTER (WHERE region_id IS NULL) AS region_id_nulls,
       COUNT(*) FILTER (WHERE store_type IS NULL) AS store_type_nulls,
       COUNT(*) FILTER (WHERE store_name IS NULL) AS store_name_nulls,
       COUNT(*) FILTER (WHERE store_street_address IS NULL) AS store_street_address_nulls,
       COUNT(*) FILTER (WHERE store_city IS NULL) AS store_city_nulls,
       COUNT(*) FILTER (WHERE store_state IS NULL) AS store_state_nulls,
       COUNT(*) FILTER (WHERE store_phone IS NULL) AS store_phone_nulls,
       COUNT(*) FILTER (WHERE first_opened_date IS NULL) AS first_opened_date_nulls,
       COUNT(*) FILTER (WHERE last_remodel_date IS NULL) AS last_remodel_date_nulls,
       COUNT(*) FILTER (WHERE total_sqft IS NULL) AS total_sqft_nulls,
       COUNT(*) FILTER (WHERE grocery_sqft IS NULL) AS grocery_sqft_nulls
FROM mavenmarket_stores;

-- No NULL values found in the table


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    2.3.4 Duplicate Check for mavenmarket_stores
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT store_id, COUNT(*)
FROM mavenmarket_stores
GROUP BY store_id
HAVING COUNT(*) > 1
ORDER BY store_id DESC;

-- No duplicate records found based on store_id



/* --------------------------------------------------------------------------------
   2.4. Data Cleaning for mavenmarket_regions
   -------------------------------------------------------------------------------- */

-- No cleaning or type conversion needed for mavenmarket_regions


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    2.4.1 Null Handling for mavenmarket_stores
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT COUNT(*) FILTER (WHERE region_id IS NULL) AS region_id_nulls,
       COUNT(*) FILTER (WHERE sales_district IS NULL) AS sales_district_nulls,
       COUNT(*) FILTER (WHERE sales_region IS NULL) AS sales_region_nulls
FROM mavenmarket_regions;

-- No NULL values found in the table


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    2.4.2 Duplicate Check for mavenmarket_products
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT region_id, COUNT(*)
FROM mavenmarket_regions
GROUP BY region_id
HAVING COUNT(*) > 1
ORDER BY region_id DESC;

-- No duplicate records found based on region_id



/* --------------------------------------------------------------------------------
   2.5 Data Cleaning for mavenmarket_calendar
   -------------------------------------------------------------------------------- */


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.5.1 Cleaning and Type Conversion for 'date' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */
   
-- Convert values to the valid 'MM/DD/YYYY' format
UPDATE mavenmarket_calendar
SET date = TO_DATE(date, 'MM/DD/YYYY')
WHERE date ~ '^\d{1,2}/\d{1,2}/\d{4}$'; -- Ensures valid date format

-- Change the data type to DATE for consistent data handling
ALTER TABLE mavenmarket_calendar
ALTER COLUMN date TYPE DATE USING date::DATE;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.5.2 Null Handling for mavenmarket_calendar
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT COUNT(*) FILTER (WHERE date IS NULL) AS date_nulls
FROM mavenmarket_calendar;

-- No NULL values found in the table


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.5.3 Duplicate Check for mavenmarket_products
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */\

SELECT date, COUNT(*)
FROM mavenmarket_calendar
GROUP BY date
HAVING COUNT(*) > 1
ORDER BY date;

-- No duplicate records found based on date



/* --------------------------------------------------------------------------------
   2.6 Data Cleaning for mavenmarket_returns
   -------------------------------------------------------------------------------- */


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.6.1 Cleaning and Type Conversion for 'return_date' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */
   
-- Convert values to the valid 'MM/DD/YYYY' format
UPDATE mavenmarket_returns
SET return_date = TO_DATE(return_date, 'MM/DD/YYYY')
WHERE return_date ~ '^\d{1,2}/\d{1,2}/\d{4}$'; -- Ensures valid date format

-- Change the data type to DATE for consistent data handling
ALTER TABLE mavenmarket_returns
ALTER COLUMN return_date TYPE DATE USING return_date::DATE;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.6.2 Null Handling for mavenmarket_returns
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT COUNT(*) FILTER (WHERE return_date IS NULL) AS return_date_nulls,
       COUNT(*) FILTER (WHERE product_id IS NULL) AS product_id_nulls,
       COUNT(*) FILTER (WHERE store_id IS NULL) AS store_id_nulls,
       COUNT(*) FILTER (WHERE quantity IS NULL) AS quantity_nulls
FROM mavenmarket_returns;

-- No NULL values found in the table

-- No duplicate check needed for mavenmarket_returns as it is a foreign key (FK) table



/* --------------------------------------------------------------------------------
   2.7 Data Cleaning for mavenmarket_orders_1997
   -------------------------------------------------------------------------------- */


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.7.1 Cleaning and Type Conversion for 'transaction_date' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */
   
-- Convert values to the valid 'MM/DD/YYYY' format
UPDATE mavenmarket_orders_1997
SET transaction_date = TO_DATE(transaction_date, 'MM/DD/YYYY')
WHERE transaction_date ~ '^\d{1,2}/\d{1,2}/\d{4}$'; -- Ensures valid date format

-- Change the data type to DATE for consistent data handling
ALTER TABLE mavenmarket_orders_1997
ALTER COLUMN transaction_date TYPE DATE USING transaction_date::DATE;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.7.2 Cleaning and Type Conversion for 'stock_date' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Convert values to the valid 'MM/DD/YYYY' format 
UPDATE mavenmarket_orders_1997
SET stock_date = TO_DATE(stock_date, 'MM/DD/YYYY')
WHERE stock_date ~ '^\d{1,2}/\d{1,2}/\d{4}$'; -- Ensures valid date format

-- Change the data type to DATE for consistent data handling
ALTER TABLE mavenmarket_orders_1997
ALTER COLUMN stock_date TYPE DATE USING stock_date::DATE;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    2.7.3 Null Handling for mavenmarket_orders_1997
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT COUNT(*) FILTER (WHERE transaction_date IS NULL) AS transaction_date_nulls,
       COUNT(*) FILTER (WHERE stock_date IS NULL) AS stock_date_nulls,
       COUNT(*) FILTER (WHERE product_id IS NULL) AS product_id_nulls,
       COUNT(*) FILTER (WHERE customer_id IS NULL) AS customer_id_nulls,
       COUNT(*) FILTER (WHERE store_id IS NULL) AS store_id_nulls,
       COUNT(*) FILTER (WHERE quantity IS NULL) AS quantity_nulls
FROM mavenmarket_orders_1997;

-- No NULL values found in the table

-- No duplicate check needed for mavenmarket_orders_1997 as it is a foreign key (FK) table



/* --------------------------------------------------------------------------------
   2.8. Data Cleaning for mavenmarket_orders_1998
   -------------------------------------------------------------------------------- */


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.8.1 Cleaning and Type Conversion for 'transaction_date' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */
   
-- Convert values to the valid 'MM/DD/YYYY' format
UPDATE mavenmarket_orders_1998
SET transaction_date = TO_DATE(transaction_date, 'MM/DD/YYYY')
WHERE transaction_date ~ '^\d{1,2}/\d{1,2}/\d{4}$'; -- Ensures valid date format

-- Change the data type to DATE for consistent data handling
ALTER TABLE mavenmarket_orders_1998
ALTER COLUMN transaction_date TYPE DATE USING transaction_date::DATE;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   2.8.2 Cleaning and Type Conversion for 'stock_date' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Convert values to the valid 'MM/DD/YYYY' format 
UPDATE mavenmarket_orders_1998
SET stock_date = TO_DATE(stock_date, 'MM/DD/YYYY')
WHERE stock_date ~ '^\d{1,2}/\d{1,2}/\d{4}$'; -- Ensures valid date format

-- Change the data type to DATE for consistent data handling
ALTER TABLE mavenmarket_orders_1998
ALTER COLUMN stock_date TYPE DATE USING stock_date::DATE;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    2.8.3 Null Handling for mavenmarket_orders_1998
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

SELECT COUNT(*) FILTER (WHERE transaction_date IS NULL) AS transaction_date_nulls,
       COUNT(*) FILTER (WHERE stock_date IS NULL) AS stock_date_nulls,
       COUNT(*) FILTER (WHERE product_id IS NULL) AS product_id_nulls,
       COUNT(*) FILTER (WHERE customer_id IS NULL) AS customer_id_nulls,
       COUNT(*) FILTER (WHERE store_id IS NULL) AS store_id_nulls,
       COUNT(*) FILTER (WHERE quantity IS NULL) AS quantity_nulls
FROM mavenmarket_orders_1998;

-- No NULL values found in the table

-- No duplicate check needed for mavenmarket_orders_1998 as it is a foreign key (FK) table
```

#### 3. Data Transformation

This section focuses on transforming the data to make it enriched. Key steps include:

- Combining Data: Merging datasets into a single, unified table.
- Standardizing Values: Updating column values to ensure consistency and readability.
- Creating New Columns: Adding and populating new columns to enrich the dataset with additional information.

Below are the SQL queries and results for each transformation step.
```sql
/* ================================================================================
   STEP 3: DATA TRANSFORMATION 
   ================================================================================ */


/* --------------------------------------------------------------------------------
   3.1. Data Transformation for mavenmarket_orders 
   -------------------------------------------------------------------------------- */


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   3.1.1 Combine Orders from 1997 and 1998
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

CREATE TABLE mavenmarket_orders AS 
SELECT * FROM mavenmarket_orders_1997
UNION ALL
SELECT * FROM mavenmarket_orders_1998;

-- Result: Combined 269,720 rows



/* --------------------------------------------------------------------------------
   3.2. Data Transformation for mavenmarket_customers
   -------------------------------------------------------------------------------- */


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   3.2.1 Update 'marital_status' Column Values
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Change 'S' to 'Single' and 'M' to 'Married'
UPDATE mavenmarket_customers
SET marital_status = 
	CASE WHEN marital_status = 'S' THEN 'Single'
	ELSE 'Married'
	END;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   3.2.2 Update 'gender' Column Values
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Change 'M' to 'Male' and 'F' to 'Female'
UPDATE mavenmarket_customers
SET gender =
	CASE WHEN gender = 'M' THEN 'Male'
	ELSE 'Female'
	END;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   3.2.3 Update 'homeowner' Column Values
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Change 'Y' to 'Yes' and 'N' to 'No'
UPDATE mavenmarket_customers
SET homeowner =
	CASE WHEN homeowner = 'Y' THEN 'Yes'
	ELSE 'No'
	END;
	

/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   3.2.4 Add and Populate 'full_name' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Add a 'full_name' column
ALTER TABLE mavenmarket_customers
ADD COLUMN full_name VARCHAR(200);

-- Populate 'full_name' by combining first and last names
UPDATE mavenmarket_customers
SET full_name = first_name || ' ' || last_name;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   3.2.5 Add and Populate 'income_level' Column
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Add a 'income_level' column
ALTER TABLE mavenmarket_customers
ADD COLUMN income_level VARCHAR(10);

-- Populate 'income_level' based on membership and homeownership
UPDATE mavenmarket_customers
SET income_level = 
	CASE WHEN yearly_income BETWEEN 20000 AND 49999 THEN 'Low'
		 WHEN yearly_income BETWEEN 50000 AND 100000 THEN 'Middle'
	ELSE 'High'
	END;



/* --------------------------------------------------------------------------------
   3.3. Data Transformation for mavenmarket_products
   -------------------------------------------------------------------------------- */


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   3.3.1 Update 'recyclable' Column Values
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Change '1' to 'Yes' and '0' to 'No'
UPDATE mavenmarket_products
SET recyclable =
	CASE WHEN recyclable = '1' THEN 'Yes'
	ELSE 'No'
	END;


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   3.3.2 Update 'low_fat' Column Values
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */	

-- Change '1' to 'Yes' and '0' to 'No'
UPDATE mavenmarket_products
SET low_fat =
	CASE WHEN low_fat = '1' THEN 'Yes'
	ELSE 'No'
	END;



/* --------------------------------------------------------------------------------
   3.4. Data Transformation for mavenmarket_calendar
   -------------------------------------------------------------------------------- */


/* ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   3.4.1 Add and Populate Date-Related Columns
   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ */

-- Add new columns for date-related 
ALTER TABLE mavenmarket_calendar
ADD COLUMN week_end DATE,
ADD COLUMN day_name VARCHAR(20),
ADD COLUMN month_end DATE,
ADD COLUMN month_name VARCHAR(20),
ADD COLUMN quarter INT,
ADD COLUMN year INT;

-- Populate the date-related columns with calculated values
UPDATE mavenmarket_calendar
SET week_end = DATE_TRUNC('WEEK', date) + INTERVAL '6 days',
	day_name = TO_CHAR(date, 'DAY'),
	month_end = DATE_TRUNC('MONTH', date) + INTERVAL '1 month' - INTERVAL '1 day',
	month_name = TO_CHAR(date, 'MONTH'),
	quarter = EXTRACT(QUARTER FROM date),
	year = EXTRACT(YEAR FROM date);
```

#### 4. Defining Relationships

In this phase, i define relationships between tables to ensure data integrity and proper connections. 

Since the date values were imported as **VARCHAR** but were changed to actual **DATE** during data cleaning, the first step is to set the primary key (PK) for the date in the calendar table.

Below are the SQL queries and results for each table relationships step.
```sql
/* ================================================================================
   STEP 4: DEFINING RELATIONSHIPS 
   ================================================================================ */


/* --------------------------------------------------------------------------------
   4.1 Set Primary Key Constraints for 'date' in the mavenmarket_calendar
   -------------------------------------------------------------------------------- */
   
-- This ensures that each date in the calendar table is unique and serves as a 
-- central point for linking other tables to time-related data
ALTER TABLE mavenmarket_calendar
ADD CONSTRAINT pk_date PRIMARY KEY (date);



/* --------------------------------------------------------------------------------
   4.2: Define Foreign Key Relationships in the mavenmarket_returns
   -------------------------------------------------------------------------------- */

-- Link the returns table to other tables for data integrity:
-- 1. 'product_id' must match a record in the products table
-- 2. 'store_id' must match a record in the stores table
-- 3. 'return_date' must match a date in the calendar table
ALTER TABLE mavenmarket_returns
ADD CONSTRAINT fk_product_id FOREIGN KEY (product_id) REFERENCES mavenmarket_products (product_id),
ADD CONSTRAINT fk_store_id FOREIGN KEY (store_id) REFERENCES mavenmarket_stores (store_id),
ADD CONSTRAINT fk_return_date FOREIGN KEY (return_date) REFERENCES mavenmarket_calendar (date); 



/* --------------------------------------------------------------------------------
   4.3: Define Foreign Key Relationships in the mavenmarket_orders
   -------------------------------------------------------------------------------- */

-- Link the orders table to other tables for data integrity:
-- 1. 'customer_id' must match a record in the customers table
-- 2. 'product_id' must match a record in the products table
-- 3. 'store_id' must match a record in the stores table
-- 4. 'transaction_date' must match a date in the calendar table
ALTER TABLE mavenmarket_orders
ADD CONSTRAINT fk_customer_id FOREIGN KEY (customer_id) REFERENCES mavenmarket_customers (customer_id),
ADD CONSTRAINT fk_product_id FOREIGN KEY (product_id) REFERENCES mavenmarket_products (product_id),
ADD CONSTRAINT fk_store_id FOREIGN KEY (store_id) REFERENCES mavenmarket_stores (store_id),
ADD CONSTRAINT fk_transaction_date FOREIGN KEY(transaction_date) REFERENCES mavenmarket_calendar (date);



/* --------------------------------------------------------------------------------
   4.4: Define Foreign Key Relationships to Link Stores and Regions
   -------------------------------------------------------------------------------- */

-- Link the stores table to the regions table:
-- 'region_id' must match a record in the regions table to assign a store to a region
ALTER TABLE mavenmarket_stores
ADD CONSTRAINT fk_region_id FOREIGN KEY (region_id) REFERENCES mavenmarket_regions (region_id);
```

