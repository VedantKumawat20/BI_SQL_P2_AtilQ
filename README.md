# BI_SQL_P2_AtilQ
Data Analysis Project 2  (Power BI) (SQL)

## sales_inghts_data_analysis

GitHub Guide – 

# SQL_P2_sales_insights_data_analysis
## Data Analysis Project 2 (Analysis – SQL, Power BI)
This study uses SQL and Power BI to…
<br>
The goal is to predict which….

### . Table of Contents
[Background and Overview](#1.Background-and-Overview-)

### 1. Background and Overview- 
#### Project Background--
AtilQ, computer hardware & peripheral Manufacturer, has headquarter in delhi, and Bhavin patel is sales director successfully sold its products online and received an overwhelmingly positive respone in several cities.
<br>
The company has significant amounts of data on its product offerings, market reach, customer details, and transaction history. This project throughly analyzes and synthesizes this data in order to uncover critical insights that will help AtilQ to solve computer hardware manufactruer sales issue.
This project aims to thoroughly analyze and synthesize data to uncover key insights, which will support AtilQ in addressing its sales challenges within the computer hardware manufacturing sector.




#### Overview--
##### Insights and recommendations are provided on the following key areas:

##### Sales Trends Analysis: 

##### Product level performance:

##### Regional Comparisons:

________________________________________

#### Objective--
The goal of this project is to analyze the sales data of Monday Coffee, and to recommend the top three major cities in India for opening new coffee shop locations based on consumer demand and sales performance.



### 2. Problem Statement--
The company operates in a dynamic and rapidly changing market, with sales teams spread across North, South, and Central India. Currently, the process of obtaining sales insights is inefficient and unreliable. Regional managers provide verbal updates when contacted, which often leads to inconsistent, sugar-coated, or misleading information, preventing leadership from getting a true picture of market performance.
Although sales data is shared in Excel files, they are often large, unstructured, and difficult to interpret, making it challenging for decision-makers to extract clear, actionable insights. As a result, there is a lack of real-time, transparent, and easy-to-understand sales reporting, hindering timely and informed strategic decisions.


### 3. AIMS Grid – Sales Insights Automation Project for AtilQ--
4 Components-
#### Purpose:
The primary goal of this project is to unlock previously hidden sales insights for the sales team, enabling better and faster decision-making. By automating data consolidation and analysis, the project aims to significantly reduce the manual effort spent on gathering and preparing sales data.
#### Stakeholders:
•	Sales Director
•	Marketing Team
•	Customer Service Team
•	Data & Analytics Team
•	IT Department
#### End Result:
A fully automated sales dashboard delivering real-time, actionable insights, empowering stakeholders to make informed, data-driven decisions with minimal delay.
#### Success Criteria:
•	✅ Reduction in manual effort and associated costs related to data gathering
•	✅ Dashboards delivering up-to-date sales order insights and performance metrics
•	✅ Sales team empowered to make more effective decisions, achieving a 10% cost saving on overall spend
•	✅ Sales analysts save 20% of their time by eliminating manual data tasks and redirect it to higher-value strategic activities


### 4. Project Team & Technical Setup
1. IT Team – "Falcons"
The IT team, known as Falcons, manages the existing Sales Management System, which includes responsibilities like printing invoices and maintaining the MySQL database that stores all transactional sales data.
2. Data Analyst Team – "Data Masters"
The Data Masters team, responsible for generating insights, aims to use the existing MySQL database as a data source in a Business Intelligence (BI) tool, such as Power BI, to create dynamic dashboards.
3. Data Warehouse Consideration
Typically, to prevent BI queries from putting load on the MySQL OLTP (Online Transaction Processing) system, a data warehouse would be created. This would involve extracting data from MySQL, transforming it, and loading it into a separate system optimized for reporting and analytics.
However, for simplicity and project scope, the existing MySQL database will be used directly, without setting up a separate data warehouse. Data will be accessed from MySQL with caution to ensure the live transactional system remains unaffected.


### 2. Data Sources-
(#### Sales Data--  The primary dataset used for this analysis is the "sales_sql.csv" file, containing detailed information about each sale made by the company.)
#### Transactions Data – (The primary dataset used for this analysis is the "sales_sql.csv" file) Sales transactions including codes, order dates, quantity, amounts, currencies, profit margin %, profit margin and cost price.
#### Customers Data – Customer name with associated market codes and customer type.
#### Markets Data – Market zone-level information (e.g., market name, code, and region).
#### Date Table – Used to extract time-based trends (e.g., year, quarter).
#### Database Source – MySQL (live OLTP data system).


### 3. Tools-
•	Power BI – Used for ETL, data modeling, transformation, visualization, and dashboard creation.
•	MySQL Workbench – Used to inspect, clean, and validate data before loading into Power BI.

### 4. Data Cleaning/Preparation-
#### In the initial data preparation phase, we performed the following tasks:
- Data loading and inspection.
- Data cleaning and formating.





✅ ETL Project Summary – AtilQ Sales Data Analysis (via Power BI & SQL)

📊 Primary Analysis Goals
•	Generate automated dashboards with up-to-date insights.
•	Normalize sales across currencies (USD to INR).
•	Enable quick filtering by year, market, and currency.
•	Eliminate duplicate or invalid records for accurate KPIs.


📥 Data Loading
•	Loaded all relevant tables (e.g., transactions, customers, sales_markets, date) into Power BI using MySQL as the data source.
•	Used Power Query Editor for data transformation and cleaning.
•	Tables include:
o	transactions (150,283 records)
o	customers (38 records)
________________________________________
🧼 Data Cleaning
1.	Removing Invalid Sales Amounts
o	SQL Check: SELECT * FROM transactions WHERE sales_amount <= 0
o	In Power BI: Filtered out rows where sales_amount is -1 or 0
powerquery
= Table.SelectRows(sales_transactions, each ([sales_amount] <> -1 and [sales_amount] <> 0))
2.	Currency Field Clean-up
o	Problem: Duplicate currencies with extra characters like 'INR\r', 'USD\r', 'USD', 'INR'
o	SQL Check:
sql
SELECT DISTINCT currency FROM transactions;
	'INR' → 279 (bad/extra)
	'INR\r' → 150,000+ (valid)
	'USD' → 2
	'USD\r' → 2 (valid)
o	Fix in Power BI: Retain only valid formats
powerquery
= Table.SelectRows(#"Previous Step", each ([currency] = "INR#(cr)" or [currency] = "USD#(cr)"))
3.	Removing Blank Zones
o	In sales_markets table, removed rows with blank zone values (e.g., New York, Paris)
________________________________________
🛠 Data Preparation
•	Joined transactions with date table to extract year-wise insights
sql
SELECT *
FROM transactions tr
JOIN date da ON tr.order_date = da.date
•	Created custom column for currency normalization:
o	If currency = USD → multiply sales_amount by 75
powerquery
= Table.AddColumn(#"cleanup currency", "normalise_sales_amount", each if [currency] = "USD#(cr)" then [sales_amount]*75 else [sales_amount])
________________________________________
🔍 Data Inspection
•	Counted records to verify table size:
sql
SELECT COUNT(*) FROM transactions;        -- 150,283
SELECT COUNT(*) FROM customers;           -- 38
SELECT COUNT(*) FROM customers WHERE market_code = "Mark001";  -- 1035
SELECT COUNT(*) FROM transactions WHERE currency = "USD";      -- 2
•	Verified sales amount for Chennai (Mark001) in 2020:
sql
SELECT SUM(tr.sales_amount)
FROM transactions tr
JOIN sales.date da ON tr.order_date = da.date
WHERE da.year = 2020 AND tr.market_code = "Mark001"
-- Result: 2,463,024
________________________________________
🧾 Data Formatting
•	Created a star schema by establishing relationships manually between tables (Data Modeling).
•	Used Power BI's Applied Steps feature in Power Query to track and manage transformation logic.
•	Ensured that formatting and data types were consistent (e.g., cleaned currency field for consistency).







4. Data Cleaning & Preparation
Performed the following:
•	Data Loading from MySQL into Power BI via Power Query.
•	Data Inspection:
o	Total transactions: 150,283
o	Total customers: 38
o	Chennai (Mark001) Customers: 1035
o	Currency inconsistencies identified via SQL.
Cleaning & Transformation Tasks:
•	Removed transactions where sales_amount <= 0.
•	Filtered out rows in sales_markets table with blank zone values (e.g., New York, Paris).
•	Cleaned currency values such as 'INR', 'INR\r', 'USD', and 'USD\r':
o	Retained standardized formats: INR#(cr) and USD#(cr)
o	Removed duplicates and misformatted entries.
•	Created a new column:
powerquery
Copy code
normalise_sales_amount = if [currency] = "USD#(cr)" then [sales_amount]*75 else [sales_amount]
•	Changed normalise_sales_amount from text to number for proper aggregations.









### 5. Data Analysis-
include some code you worked with

### 6. Results/Findings-
The analysis results are summarized as follows:

### 7. Recommendations-
Based on the analysis, we recommend the following actions:

### 8. Limitations-
remove or exclude recordes to do analysis

•  Data warehouse not implemented; data pulled directly from live OLTP (MySQL), which may affect performance under load.
•  Bad records were removed instead of corrected due to scope and simplicity constraints.
•  Static USD conversion rate used (₹75); dynamic rate API not integrated.



### 9. References-

### 10.	Data Structure Overview-
EER Diagram
<br>
 data structure as seen below consists of four tables: sales, customers, products, and city with a total row count of …

(Prior to begnning the analysis, a variety of checks were conducted for quality control and famaliarization with the dataset. The SQL quaries utilized to instep and perform quality check can be found here.)

### 11. Executive Summary –  summary about main findings (BI)


### 12.	Insights Deep Dive – 

-	Quantified value
-	Business metric
-	Simple story about historical trend

### 6.	Recommendations- 

-	Usefulness of project



