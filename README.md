# BI_SQL_P2_AtilQ
### Data Analysis Project 2  (Power BI) (SQL)

https://github.com/VedantKumawat20/BI_SQL_P2_AtilQ/blob/main/BI_Dashboard.pbit

### Table of Contents
[Background and Overview](#1.Background-and-Overview-)<br>
[Problem Statement](#2.Problem-Statement-)<br>
[AIMS Grid](#3.AIMS-Grid–Sales-Insights-Automation-Project-for-AtilQ-)<br>
[Project Team & Technical Setup](#4.Project-Team-&-Technical-Setup-)<br>
[Data Sources](#5.Data-Sources-)<br>
[Tools](#6.Tools-)<br>
[ELT](#7.ELT-(Extract,-Load,-Transform)-)<br>
[Data Structure Overview](#8.Data-Structure-Overview-)<br>
[Limitations](#9.Limitations-)<br>
[Executive Summary](#10.Executive-Summary–summary-about-main-findings-(BI)-)<br>

---
### 1. Background and Overview- 

#### Project Background--
- AtilQ, computer hardware & peripheral Manufacturer, has headquarter in delhi, and Bhavin patel is sales director successfully sold its products online and in-person and received an overwhelmingly positive respone in several cities.
- The company has significant amounts of data on its product offerings, market reach, customer details, and transaction history. 

#### Overview--
#### Insights and recommendations are provided on the following key areas:
• Sales Trends Analysis 
<br>
• Product level performance
<br>
• Regional Comparisons

#### Objective--
The goal of this project is to analyze and synthesizes this data in order to uncover critical insights that will help AtilQ to solve computer hardware manufactruer sales issue.

---
### 2. Problem Statement-
The company operates in a dynamic and rapidly changing market, with sales teams spread across North, South, and Central India. Currently, the process of obtaining sales insights is inefficient and unreliable. Regional managers provide verbal updates when contacted, which often leads to inconsistent, sugar-coated, or misleading information, preventing leadership from getting a true picture of market performance.
<br>
Although sales data is shared in Excel files, they are often large, unstructured, and difficult to interpret, making it challenging for decision-makers to extract clear, actionable insights. As a result, there is a lack of real-time, transparent, and easy-to-understand sales reporting, hindering timely and informed strategic decisions.

---
### 3. AIMS Grid – Sales Insights Automation Project for AtilQ-
#### [4 Components]
- #### Purpose:
The primary goal of this project is to unlock previously hidden sales insights for the sales team, enabling better and faster decision-making. By automating data consolidation and analysis, the project aims to significantly reduce the manual effort spent on gathering and preparing sales data.
- #### Stakeholders:
•	Sales Director
<br>
•	Marketing Team
<br>
•	Customer Service Team
<br>
• Data & Analytics Team
<br>
•	IT Department
- #### End Result:
A fully automated sales dashboard delivering real-time, actionable insights, empowering stakeholders to make informed, data-driven decisions with minimal delay.
- #### Success Criteria:
✅ Reduction in manual effort and associated costs related to data gathering
<br>
✅ Dashboards delivering up-to-date sales order insights and performance metrics
<br>
✅ Sales team empowered to make more effective decisions, achieving a 10% cost saving on overall spend
<br>
✅ Sales analysts save 20% of their time by eliminating manual data tasks and redirect it to higher-value strategic activities

---
### 4. Project Team & Technical Setup-

1. IT Team – "Falcons"
The IT team, known as Falcons, manages the existing Sales Management System, which includes responsibilities like printing invoices and maintaining the MySQL database that stores all transactional sales data.

2. Data Analyst Team – "Data Masters"
The Data Masters team, responsible for generating insights, aims to use the existing MySQL database as a data source in a Business Intelligence (BI) tool, such as Power BI, to create dynamic dashboards.

3. Data Warehouse Consideration
Typically, to prevent BI queries from putting load on the MySQL OLTP (Online Transaction Processing) system, a data warehouse would be created. This would involve extracting data from MySQL, transforming it, and loading it into a separate system optimized for reporting and analytics.
However, for simplicity and project scope, the existing MySQL database will be used directly, without setting up a separate data warehouse. Data will be accessed from MySQL with caution to ensure the live transactional system remains unaffected.

---
### 5. Data Sources-
#### Transactions Data –  Contains detailed information about each sale made by the company including codes, order dates, quantity, amounts, currencies, profit margin %, profit margin and cost price.
#### Customers Data – Customer name with associated market codes and customer type.
#### Markets Data – Market zone-level information (e.g., market name, code, and region).
#### Date Table – Used to extract time-based trends (e.g., year, quarter).
#### Database Source – MySQL (live OLTP data system).

---
### 6. Tools-
•	Power BI – Used for ELT, data modeling, transformation, visualization, and dashboard creation.
<br>
•	MySQL Workbench – Used to inspect, clean, and validate data before loading into Power BI.

---
### 7. ELT (Extract, Load, Transform)-
The project uses an ELT workflow, where raw data is extracted from a MySQL OLTP source, loaded into Power BI, then transformed inside Power BI (Power Query) for analysis.
#### we performed the following tasks:
- Data Loading
- Data Inspection
- Data Cleaning
- Data Formating
- Data Preparation
- Data Modeling

#### 📥 Data Loading

-	Loaded all relevant tables into Power BI using MySQL as the data source.<br>
-	Used Power Query Editor for data transformation and cleaning.<br>
- Tables include:<br>
• transactions (150,283 records)<br>
• customers (38 records)<br>
• markets (17 records)<br>
• products (279 records)<br>
• date (1129 records)<br>


#### 🔍 Data Inspection

- Counted records to verify table size:

```sql
SELECT COUNT(*) FROM transactions;        -- 150,283
SELECT COUNT(*) FROM customers;           -- 38
SELECT COUNT(*) FROM customers WHERE market_code = "Mark001";  -- 1035
SELECT COUNT(*) FROM transactions WHERE currency = "USD";      -- 2
```

- Verified sales amount for Chennai (Mark001) in 2020:
```sql
SELECT SUM(tr.sales_amount)
FROM transactions tr
JOIN sales.date da ON tr.order_date = da.date
WHERE da.year = 2020 AND tr.market_code = "Mark001"
-- Result: 2,463,024
```

#### 🧼 Data Cleaning

1.	Removing Invalid Sales Amounts

- SQL Check:

```sql 
SELECT
 *
FROM transactions
WHERE sales_amount <= 0
```

- In Power BI: Filtered out rows where sales_amount is -1 or 0

[powerquery]

```vb
= Table.SelectRows(sales_transactions, each ([sales_amount] <> -1 and [sales_amount] <> 0))
```

2.	Currency Field Clean-up

Problem: Duplicate currencies with extra characters like 'INR\r', 'USD\r', 'USD', 'INR'

- SQL Check:

```sql
SELECT DISTINCT currency FROM transactions;
```

```sql
SELECT
COUNT(currency)
FROM transactions
WHERE currency = 'USD' 
```

• 'INR' → 279 (bad/extra)

• 'INR\r' → 150,000+ (valid)

• 'USD' → 2

• 'USD\r' → 2 (valid)

- Fix in Power BI: Retain only valid formats

[powerquery]

```vb
= Table.SelectRows(#"Previous Step", each ([currency] = "INR#(cr)" or [currency] = "USD#(cr)"))
```

3.	Removing Blank Zones

- In sales_markets table, removed rows with blank zone values (e.g., New York, Paris)

#### 🛠 Data Preparation

- Joined transactions with date table to extract year-wise insights
```sql
SELECT *
FROM transactions tr
JOIN date da ON tr.order_date = da.date
```

- Created custom column (norm_sales_amount) for currency normalization:

• If currency = USD → multiply sales_amount by 75

[powerquery]

```vb
= Table.AddColumn(#"cleanup currency", "norma_sales_amount", each if [currency] = "USD#(cr)" then [sales_amount]*75 else [sales_amount])
```

#### 🧾 Data Formatting

•	Ensured that formatting and data types were consistent (e.g., cleaned currency field for consistency).
<br>
•	Used Power BI's Applied Steps feature in Power Query to track and manage transformation logic.

#### 🔗 Data Modeling

•	Created a star schema by establishing relationships manually between tables

---
### 8.	Data Structure Overview-
ERD  Entity Relationship Diagram
<br>
Data Structure as seen below consists of four tables: transactions, customers, products, markets, and date.
![Screenshot (119)](https://github.com/user-attachments/assets/4504fea6-31ba-4053-b256-152bb77e8438)

(Prior to begnning the analysis, a variety of checks were conducted for quality control and famaliarization with the dataset. The SQL quaries utilized to instep and perform quality check)

---
### 9. Limitations-
[remove or exclude recordes to do analysis]
<br>
•  Data warehouse not implemented; data pulled directly from live OLTP (MySQL), which may affect performance under load.
<br>
•  Bad records were removed instead of corrected due to scope and simplicity constraints.
<br>
•  Static USD conversion rate used (₹75); dynamic rate API not integrated.
<br>
• The dataset contains a product named `(blank)` due to a source data issue. In a real project, this would be resolved with the relevant department. For simplicity, the original data is unchanged and not manually corrected. It may slightly affect aggregate insights, category-level analyses, or visualizations where product names are displayed.

---
### 10. Executive Summary – summary about main findings (BI)

#### ✅ 1️⃣ Dashboard: Key Insights
![Screenshot (127)](https://github.com/user-attachments/assets/b1e9965a-b5ab-425c-b9f5-8e67c9d7b397)

#### What this shows:
• Card Visual: Total Revenue and Sales Quantity 
<br>
• Revenue by Markets: Breakdown of revenue across different cities/markets, highlighting top performers.
<br>
• Sales Quantity by Markets: Sales volume by city, showing where the highest demand exists.
<br>
• Revenue Trend: Month-by-month trend line, useful for spotting seasonality, growth, or drops.
<br>
• Top Products: Revenue generated by top-selling products.
<br>
• Top 5 Customers: Major revenue-contributing customers.
<br>
• Year and Month Slicers : To navigate between years and months/ Filter performance by any year and month. Analyze trends during specific periods (e.g., festive seasons).

#### Key insights & value for sales director :
✔️ Identify best-performing markets and regions.
<br>
✔️ Recognize which cities are driving the most sales and revenue.
<br>
✔️ Spot dependency on specific customers or products.
<br>
✔️ Detect seasonal trends for production and inventory planning.

#### ✅ 2️⃣ Dashboard: Key Insights with Customer Type
![Screenshot (128)](https://github.com/user-attachments/assets/619a54f0-ed11-4971-b408-a49a08b71553)

#### What this shows:
• Same overall metrics — Revenue and Sales Quantity.
<br>
• Split by Customer Type: Clear comparison of Brick & Mortar vs E-Commerce contribution.
<br>
• Revenue by Customer Type (Donut Chart): Overall share of each channel.

#### Key insights & value for sales director:
✔️ Understand the balance between E-Commerce and traditional retail.
<br>
✔️ Find which cities are strong in E-Commerce — identify new digital opportunities.
<br>
✔️ Optimize marketing spend by channel and region.
<br>
✔️ Strategize expansion in weaker channels/markets to diversify risk.

#### ✅ 3️⃣ Dashboard: Profit Analysis
![Screenshot (129)](https://github.com/user-attachments/assets/6060bb99-0d33-45fa-940e-0eeb33f53209)

#### What this shows:
• Total Revenue, Sales Qty, and Total Profit Margin
<br>
• Revenue Contribution % by Market: Which markets bring the most revenue share.
<br>
• Profit Contribution % by Market: Which markets contribute most to profit.
<br>
• Profit Margin % by Market: Actual margin rates for each market — identifies high or low margin zones.
<br>
• Customer Table: Detailed customer-wise Revenue, Revenue Contribution %, Profit Margin Contribution %, and Profit Margin %.

#### Key insights & value for sales director:
✔️ Compare revenue contribution with actual profit — revenue leaders may not be profit leaders.
<br>
✔️ Identify low-profit or loss-making markets.
<br>
✔️ Discover hidden high-margin markets that may deserve more focus.
<br>
✔️ Take action to improve margins: renegotiate with low-margin customers or reduce operational costs.
<br>
✔️ Develop targeted pricing and discounting strategies.

#### ✅ 4️⃣ Dashboard: Performance Insights

![Screenshot (130)](https://github.com/user-attachments/assets/8d2ed905-4f83-4b31-b28a-f7489839579f)

#### What this shows:
• Same KPIs — Revenue, Sales Qty, Profit Margin.
<br>
• Adds a Profit Target slicer — lets user adjust target margin and compare.
<br>
• Combined Revenue Trend with Profit Margin %: Visualizes how profit margin aligns with revenue over time.

#### Key insights & value for sales director:
✔️ Track whether profit margins meet targets month by month.
<br>
✔️ Visual correlation between revenue growth and margin fluctuation — e.g., high sales might not mean high profit.
<br>
✔️ Prioritize high-margin regions while improving or exiting poor-performing zones.
<br>
✔️ Use the profit target slider for scenario planning — test “what if” for better planning. 
<br>
✔️ Support leadership in making informed decisions about pricing, promotions, and market focus.







