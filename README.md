# Wall-Mart-_Sales-Analysis_Sql

```sql
DROP TABLE IF EXISTS sales;

CREATE TABLE sales (
  invoice_id VARCHAR(30) NOT NULL PRIMARY KEY,
  branch VARCHAR(5) NOT NULL,
  city VARCHAR(30) NOT NULL,
  customer_type VARCHAR(30) NOT NULL,
  gender VARCHAR(10) NOT NULL,
  product_line VARCHAR(100) NOT NULL,
  unit_price DECIMAL(10,2) NOT NULL,
  quantity INT NOT NULL,
  vat DECIMAL(10,4) NOT NULL,
  total DECIMAL(12,4) NOT NULL,
  date DATETIME NOT NULL,
  time TIME NOT NULL,
  payment VARCHAR(15) NOT NULL,sales
  cogs DECIMAL(10,2) NOT NULL,
  gross_margin_pct DECIMAL(10,9),
  gross_income DECIMAL(12,4),
  rating DECIMAL(3,1)
);
```

 ## Data Import let check Once
```sql 
 select * from walmart.sales;
```sql
 # 1)Total Sales Analysis
```sql
 select sum(total) as total_Sales 
 from sales;
```sql


 # 2)Average Sales Per Transaction
```sql
 select avg(total) as avg_sales 
 from sales	;
 ```sql
 # 3) find Unique Products
```sql
 select distinct(product_line) As unique_products
 from Sales;
 ```sql
 # 4) Total Products Sold
```sql
 SELECT COUNT(Invoice_ID) AS Total_Products_Sold
FROM sales;
```sql
# 5)  find Unique citys 
select distinct(city) as Unique_city
from sales;

 # Time_of _day 
 select time,
 (case 
       when time Between  "00:00:00" And "12:00:00" Then "Morning"
       when time Between  "12:00:00" AND "17:00:00" Then "Afternon"
       Else "Evening"
 End) as time_of_day
 from sales;
 
 ALTER TABLE sales ADD column time_of_day varchar(20);
 
 update sales
 set time_of_day = (
         case 
			when time Between  "00:00:00" And "12:00:00" Then "Morning"
			when time Between  "12:00:00" AND "17:00:00" Then "Afternon"
			Else "Evening"
	     END
	);
    
  ## Dayname 
     
select date,
dayname(date) as day_name
from sales;

ALTER TABLE Sales Add column day_name Varchar(30);
update sales
set day_name = dayname(date);

## Month name 
select date,
monthname(date) as month_name 
from sales;
ALTER table Sales  add column  month_name  Varchar(30);
Update sales
set month_name = MONTHNAME(date);

select distinct City From sales;

select distinct branch, city From sales;

### What is Common Payment Method ?
select payment, count(payment) as Common_Payment_Method
from Sales
group by payment
order by Common_payment_Method Desc
limit	5;

### What is Most selling Product Line  ?
select product_line, count(product_line) as most_selling_product
from sales
group by product_line
order by most_selling_product desc
limit 5;
###  what is the total revenue by month 
select Month_name, sum(total) as total_revenue
from sales
group by month_name
order by total_revenue desc;
### which month recorded the highest cost of goods sold (coogs)
Select Month_name, sum(cogs) as total_cogs
from sales
group by Month_name
order by total_cogs desc;

### Which product line generated the highest revenue
select product_line , sum(total) as total_product_revenue
from sales
group by product_line
order by total_product_revenue desc;
Alter Table Sales  add column product_category varchar(30);
UPDATE sales
JOIN (
    SELECT AVG(total) AS avg_total FROM sales
) AS avg_table
SET product_category = 
    CASE 
        WHEN sales.total >= avg_table.avg_total THEN 'good'
        ELSE 'bad'
    END;
    
### Which branch sold more products than average product sold?
select branch, sum(quantity) as quantity 
from sales
group by branch
having sum(quantity) > avg(quantity)
order by quantity desc 
limit 5;

### What is the most common product line by gender?
select gender,product_line, count(gender) as total_count 
from sales
group by  gender ,product_line
order by total_count desc 
;



Select day_name ,time_of_day, count(invoice_id) as total_sales
from sales
group by day_name,time_of_day
having day_name not in ('sunday','saturday');
SELECT branch, time_of_day,
AVG(rating) OVER(PARTITION BY branch) AS ratings
FROM sales GROUP BY branch;
SELECT 
    branch,
    AVG(rating) AS ratings
FROM sales
GROUP BY branch;
