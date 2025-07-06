# My-DSA-Data-Analysis-Project-Documentation-2

## MY DSA DATA ANALYSIS PROJECT WORK SUMMARY  USING SQL SERVER
### KULTRA MEGA STORES INVENTORY

### Project Overview

This analysis project is aimed to generate insight into the sales performance of the Kultra Mega Stores in these trade years (2009-2012). By analyzing various parameters in the data received, we seek to provide some business intelligence to guide product improvement, marketing strategy, and share key insights and findings.

### Data Source
The primary sources of data used for this work the “KMS SQL Case Study.csv” and Order_Status.csv” excel files shared to my Canvas LMS by DSA Incubator hub containing data from 2009-2012.

### Data Cleaning and Preparation
In this phase, I performed the following processes;
- Data loading and inspection to ensure data quality.
- Uploading the files on SQL server
- Reformatting column type in data set
- Joining the two tables

### ANALYSIS TOOLS
 1. Microsoft Excel 2019 [Download Here](https://www.microsoft.com/en-us/microsoft-365/download-office) for data inspection.
 2. SQL Server [Download Here](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) for querying and Analysis

### Exploratory Data Analysis
By employing the use of SQL queries, I was able to answer the following questions:
1. Which product category has the highest number of sales?
2. What are the top 3 and bottom 3 regions in terms of sales?
3. What were the total sales of appliances in Ontario?
4. Advise the management of KMS on what to do to increase the revenue from the bottom 10 customers?
5. KMS uncured the most shipping cost using which shipping method?
6. Who are the most valuable Customers, and what products or services do they typically purchase?
7. Which small business customer had the highest sales?
8. Which corporate customer placed the most number of orders in 2009-2012?
9. Which consumer customer was the most profitable one?
10. Which customer returned items and what segment do they belong to?
11. If the delivery truck is the most economical but the slowest, and express air is the faster and the most expensive one, do you think the shipping company appropriately spent shipping cost based on the order priority? Explain your answer.

### DATA ANALYSIS AND FINDINGS
Below are some DAX Expressions used during my analysis:
``` sql
CREATE DATABASE DSA_PROJECT

SELECT * FROM [KMS Sql Case Study 1]

------CLEAN MY TABLE--------

alter table [KMS Sql Case Study 1]
alter Column ship_Date date 

alter table [KMS Sql Case Study 1]
alter Column order_Date date 

alter table [KMS Sql Case Study 1]
alter Column sales decimal (10,3) 

alter table [KMS Sql Case Study 1]
alter Column discount decimal (10,3) 

alter table [KMS Sql Case Study 1]
alter Column profit decimal (10,3)

alter table [KMS Sql Case Study 1]
alter Column unit_price decimal (10,3)

alter table [KMS Sql Case Study 1]
alter Column Shipping_cost decimal (10,3) 

alter table [KMS Sql Case Study 1]
alter Column Product_Base_Margin decimal (10,3) 


select * from Order_Status
SELECT * FROM [KMS Sql Case Study 1]

----Join the tables 
create view VW_Complete1
as
Select [KMS Sql Case Study 1].Row_ID,
		Order_Status.Order_ID,
		[KMS Sql Case Study 1].Customer_Name,
		[KMS Sql Case Study 1].Province,
		[KMS Sql Case Study 1].Region,
		[KMS Sql Case Study 1].Customer_Segment,
		Order_Status.status,
		[KMS Sql Case Study 1].Order_Date,
		[KMS Sql Case Study 1].Order_Priority,
		[KMS Sql Case Study 1].Order_Quantity,
		[KMS Sql Case Study 1].Sales,
		[KMS Sql Case Study 1].Discount,
		[KMS Sql Case Study 1].Ship_Mode,
		[KMS Sql Case Study 1].Profit,
		[KMS Sql Case Study 1].Unit_Price,
		[KMS Sql Case Study 1].Shipping_Cost,
		[KMS Sql Case Study 1].Product_Category,
		[KMS Sql Case Study 1].Product_Sub_Category,
		[KMS Sql Case Study 1].Product_Name,
		[KMS Sql Case Study 1].Product_Container,
		[KMS Sql Case Study 1].Product_Base_Margin,
		[KMS Sql Case Study 1].Ship_Date
from Order_Status
right JOIN [KMS Sql Case Study 1]
on [KMS Sql Case Study 1].Order_ID = Order_Status.Order_ID
  
  
 select * from VW_Complete1

-----------CASE SCENERIO 1
-----1) Which product category had the highest number of sales? ANS: OFFICE SUPPLIES (4610)

select top 1 Product_Category, count(Sales) as [Total no of sales]
from (
select Product_Category, sales
from VW_Complete1) as total
group by Product_Category
order by [Total no of sales] desc


-----2) what are the top 3 and bottom 3 regions in terms of sales?
---TOP 3: WEST, ONTARIO, AND PRARIE
select top 3 Region, count(Sales) as [Total no of sales]
from VW_Complete1
group by Region
order by [Total no of sales] desc
---BOTTOM 3: NUNAVUT, NORTHWEST TERRITORIES, AND YUKON
select top 3 Region, count(Sales) as [Total no of sales]
from VW_Complete1
group by Region
order by [Total no of sales] asc


-----3) What were the total sales of appliances in Ontario? ANS: 3,063,212.527
Select Region, sum(Sales) as [Total no of sales] 
from VW_Complete1
group by Region 
order by Region desc
 

 -----4) Advise the management of KMS on what to do to increase the revenue from the bottoms 10 customers?
 ---First, who are the bottom 10 customers?
select top 10 Customer_Name, sum(Sales) as [Total Revenue]
from VW_Complete1
group by Customer_Name
order by [Total Revenue] asc
---ANS: 'Jeremy Farry', 'Natalie DeCherney', 'Nicole Fjeld', 'Katrina Edelman', 'Dorothy Dickinson', 'Christine Kargatis', 'Eric Murdock', 'Chris McAfee', 'Rick Huthwaite', 'Mark Hamilton' 

---Second, look at their data in the complete SQL view table
select * from VW_Complete1 
where Customer_Name IN ('Jeremy Farry', 'Natalie DeCherney', 'Nicole Fjeld', 'Katrina Edelman', 'Dorothy Dickinson', 'Christine Kargatis', 'Eric Murdock', 'Chris McAfee', 'Rick Huthwaite', 'Mark Hamilton')
order by Customer_Name

---ADVICE: Firstly, Reduce excessive discounts that eat so much into profits despite the bulk of sales. Secondly, shift focus to product subcategories with high margin like Furniture (office furnishing).


-----5) KMS incurred the most shipping cost from which shipping method? ANS: Delivery Truck (51,971.940)
select top 1 Ship_Mode, sum(Shipping_Cost) as [Total shipping cost]
from vw_KMS_Complete
group by Ship_Mode
order by [Total shipping cost] desc



-----------CASE  SCENERIO 2
-----6) Who are the most valuable Customers and what products or services do they typically purchase?
select top 5 Customer_Name, Product_Sub_Category, sum(sales) as total_sales, sum(profit) as total_profit, count(*) as total_orders
from VW_Complete1
group by Customer_Name, Product_Sub_Category
order by total_sales desc, total_profit desc


-----7) Which small business customer had the highest sales?
select top 1 Customer_Name, sum(Sales) as total_sales, Customer_Segment
from VW_Complete1
where Customer_Segment in ('Small business')
group by Customer_Name, Customer_Segment
order by total_sales desc
---ANS: Dennis Kane

-----8) Which corporate customer placed the most number of orders in 2009-2012?
select top 1 Customer_Name, sum(Order_Quantity) as number_of_orders, Customer_Segment
from VW_Complete1
where Customer_Segment in ('Corporate')
group by Customer_Name, Customer_Segment
order by number_of_orders desc
---ANS: Roy Skaria


-----9) Which Consumer customer was the most profitable one?
select top 1 Customer_Name, sum(Profit) as total_profit, Customer_Segment
from VW_Complete1
where Customer_Segment in ('Consumer')
group by Customer_Name, Customer_Segment
order by total_profit desc
----ANS: Emily Phan


-----10) Which customers returned items, and what segment do they belong to?
select Customer_Name, Customer_Segment, status
from VW_Complete1
where status in ('Returned')


-----11) If the the delivery truck is the most economical but the slowest shipping method and Express air is the fastest but the most expensive one, do you think the company appropraitely spent shipping cost based on the order priority?
select Order_Priority, Ship_Mode, sum(Shipping_Cost) as total_shipping_cost
from VW_Complete1
group by Order_Priority, Ship_Mode
order by Order_Priority desc
----ANS: Based on my data, YES. Because, express air being the most expensive ship mode was used least in all the orde priorities of the customers except 'CRITICAL' as shown in the table by the Total_Shipping_cost of each order_priority scenario.
```








