# AtliQ Hardware Sales Insights

## AtliQ Hardware
A company that supplies computer hardware and peripherals to many clients across India.\
The company has a head office in Delhi and regional offices throughout India.

---

## Business Issue
The sales director is facing a lot of challenges. The marketing is growing dynamically, he is struggling to keep track of the sales. He needs more accurate insights about the company sales and then makes the necessary decisions.

---

## Solution
- Create a simple and informative dashboard about the company sales.
- I used **`SQL`** queries in **`MySQL Workbench`** to look into the data and **`Tableau`** for **`ETL`** and **`Visualizations`** to create the insights dashboard.

---

## Data Overview

#### `# Tables`
    
<table>
<tr><th>1. transactions</th><th>2. customers</th><th>3. date</th><th> 4. products </th><th> 5. markets </th></tr>
<tr><td>

|Field Name|Description|
|----|---|
|Product Code|ProdXXX|
|Customer Code|CusXXX|
|Market Code|MarkXXX|
|Order Date|YYYY-MM-DD|
|Sales Qty|Quantity Sold|
|Sales Amount|Sales Amount|
|Currency|INR/USD|
|Profit Margin|(Sales Amount - Cost)/(Sales Amount)|
|Profit|(Sales Amount-Cost)|
|Cost|Total Cost of a Product|

</td><td>

|Field Name|Description|
|---|---|
|Customer Code|CusXXX|
|Custmer Name|Stores Names|
|Customer Type|Brick & Mortar/ E-Commerce|

</td><td>

|Field Name|Description|
|---|---|
|Date|YYYY-MM-DD|
|Cy Date|YYYY-MM-DD: Starting date of each month of date|
|Year|YYYY: Year of the date|
|Month Name|MMMM: Month of the date column|
|Date Yy Mmm|DD-MMM: Date and Month of the date column|
	
</td><td>
	
|Field Name|Description|
|---|---|
|Product Code|ProdXXX|
|Product Type|Own Brand/Distribution|

</td><td>

|Field Name|Description|
|---|---|
|Markets Code|MarkXXX|
|Markets Name|City Names|
|Zone|South/Central/North|


</td></tr> </table>

#  
#### `# Data Analysis Using SQL`
1. Show all customer records

    `SELECT * FROM customers;`

2. Show total number of customers

    `SELECT count(*) FROM customers;`

3. Show transactions for Chennai market (market code for chennai is Mark001

    `SELECT * FROM transactions where market_code='Mark001';`

4. Show distrinct product codes that were sold in chennai

    `SELECT distinct product_code FROM transactions where market_code='Mark001';`

5. Show transactions where currency is US dollars

    `SELECT * from transactions where currency="USD"`

6. Show transactions in 2020 join by date table

    `SELECT transactions.*, date.* FROM transactions INNER JOIN date ON transactions.order_date=date.date where date.year=2020;`

7. Show total revenue in year 2020,

    `SELECT SUM(transactions.sales_amount) FROM transactions INNER JOIN date ON transactions.order_date=date.date where date.year=2020 and transactions.currency="INR\r" or transactions.currency="USD\r";`
	
8. Show total revenue in year 2020, January Month,

    `SELECT SUM(transactions.sales_amount) FROM transactions INNER JOIN date ON transactions.order_date=date.date where date.year=2020 and and date.month_name="January" and (transactions.currency="INR\r" or transactions.currency="USD\r");`

9. Show total revenue in year 2020 in Chennai

    `SELECT SUM(transactions.sales_amount) FROM transactions INNER JOIN date ON transactions.order_date=date.date where date.year=2020
and transactions.market_code="Mark001";`

10. Show all tables and their rows in sales schema
    	 
	 ```
	 > SELECT TABLE_SCHEMA, TABLE_NAME, TABLE_ROWS  
	 > FROM INFORMATION_SCHEMA.TABLES 
	 > WHERE TABLE_SCHEMA = 'sales';
	 > ```
	 > |TABLE_SCHEMA|TABLE_NAME|TABLE_ROWS|
	 > |---|----|---|
	 > |sales|customers|38|
	 > |sales|date|1126|
	 > |sales|markets|17|
	 > |sales|products|279|
	 > |sales|ransactions|147678|


11. Show date range
    	
	> ```
	> SELECT 'First Date', MIn(Order_Date)  
	> FROM sales.transactions  
	> UNION  
	> SELECT 'Last Date', MAX(Order_Date)  
	> FROM sales.transactions;
	> ``` 
	> 
	> <table>
	> <tr>
	>     <td>First Date</td>
	>     <td>2017-10-04</td>
	> </tr>
	> <tr>
	>     <td>Last Date</td>
	>     <td>2020-06-26</td>
	> </tr>
	> </table>


12. Show Revenue in 2020 and 2019.

	> ```
	> SELECT d.year, SUM(Sales_Amount)
	> FROM sales.transactions as t
	> JOIN sales.date as d
	> ON t.Order_Date = d.date
	> WHERE d.year = '2019'
	> UNION
	> SELECT d.year, SUM(Sales_Amount)
	> FROM sales.transactions as t
	> JOIN sales.date as d
	> ON t.Order_Date = d.date
	> WHERE d.year = '2020';
	> ``` 
	> 
	> <table>
	> <tr>
	>     <td>2019</td>
	>     <td>336019102</td>
	> </tr>
	> <tr>
	>     <td>2020</td>
	>     <td>142224545</td>
	> </tr>
	> </table>



13. Show distinct currency and their count

	> ```
	> SELECT currency, COUNT(currency)
	> FROM sales.transactions
	> GROUP BY currency;
	> ``` 
	> 
	> <table>
	> <tr>
	>     <td>INR</td>
	>     <td>148393</td>
	> </tr>
	> <tr>
	>     <td>USD</td>
	>     <td>2</td>
	> </tr>
	> </table>

	
# 
#### `# Insights`
After a quick data exploration in MySQL, here are some initial findings:
- The database contains 5 tables: customers, date, markets, products, and transactions.
- There are 17 markets, 279 products, and 38 customers.
- The observation period is from OCT 2017 to JUN 2020.
- The total revenue in 2020 was ₹ 142.22 M, 57.7% less than 2019, which was ₹ 336.02 M.
- Most of the transactions data are in INR(₹) currency, but we have 2 records in US($) currency. 
- And we got some garbage values in sales amount and market column. We’re going to deal with it in the ETL process.

---

## ETL(Extract, Transform, Load)
Once I knew the basic features of the data I had to work with, I Imported the MySQL database into Tableau to do the necessary transformations and make a simple, reliable, and helpful dashboard.

# 

#### # Data Modeling Step
We have one main table and four other tables having one shared column with the main table. So we will connect the other tables to the main table using the shared columns.
<img align="right" alt="Coding" width="573" src= "https://github.com/shrpadma2/Sales_Insights/blob/main/ETL_.png" >

- Main Table: transactions

|Table|Column|Main Table Column|   
|---|---|---|
|customers|Customer_Code|Customer_Code|
|date|date|Order_Date|
|products|Market_Code|Market_Code|
|markets|Product_Code|Product_Code|


# 

#### # Filtering, Cleaning and Adding New Columns
- The company is serving only in India, So “Paris” and “New York” in the market table are garbage values, so filtering them out.
- The “currency” column (in transactions table) have 2 USD currency values, So created a new column called “Sales”, where all the sales_amount is in INR Currency.

---

## Dashboards
The two dashboards shows all the main information about the company sales.

#### # Dashboard 1: Sales Insights

    - Revenue
    - Net Profit
    - Revenue by Market
    - Profit Trend by Market
    - Revenue by Top 10 Products
    - Profit Trend by Top 10 Products



---
## Final Report
Based on the dashbaords insights, I have made some conclusions and recommendation that Sales Marketing team should/can consider making a sales strategy.

#### # Conclusions
- Sales were rapidly decreasing in 2020 compared to 2019 by around 57.7%.
- Highest revenue generated from Markets such as Delhi NCR, Mumbai, Ahmedabad, Bhopal, Nagpur, and so on.
- Highest quantities sold in the Market such as Delhi NCR, Mumbai, Nagpur, Kochi, Ahmedabad, and so on.
- Majority of the sales were takes place in the month of January followed by November and March.

#### # Recommendation
- Make a new sales strategy for lucknow since its showing lowest revenue and negative profit margin and if possible so as for Surat and Bhubhaneshwar also.
- try to increase sales quantity in Patna, Surat and Kanpur since they have lowest sales quantity.
- start target campagin for Prod047 and Prod061 since they two are the most profitable and most selling products.
- try to give special benefits to Electronics and Excel stores as they are most profitable customers.
- make campgain strategy for mid year as they are showing high sales among other months.

---

## *References*
- *Project Data: [Google Sheet](https://docs.google.com/spreadsheets/d/1cidC_V9YrS789-ZYTdAM1OgIXZVa_XfkRVy5Cyp3Qk8/edit?usp=sharing) | [MySQL Dump File](https://github.com/Laxman-Lakhan/AtliQ-Hardware-Sales-Insights/blob/a7e93603f70e2352ef3595c3717e4e7f3b352697/Data/database_dump.sql)*



