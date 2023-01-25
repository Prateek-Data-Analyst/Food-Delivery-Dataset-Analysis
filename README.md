# INTRODUCTION
In this project, we will be epxlorinf the food delivery dataset to uncover insights for a better decision making.

# PROJECT TAKS
- [Creating the dataset](#creating-the-dataset)
- [Creating the Dataset](#creating-the-dataset)
- [Checking the tables](#checking-the-tables)
- [How many rolls were ordered?](#how-many-rolls-were-ordered)
- [How many unique customers ordered rolls?](#how-many-unique-customers-ordered-rolls)
- [How many successful orders?](#how-many-successful-orders)
- [How many each type of rolls were delivered?](#how-many-each-type-of-rolls-were-delivered)
- [Classyfing cancellation and customer cancellation](#classyfing-cancellation-and-customer-cancellation)
- [How many veg and non veg rolls were ordered by each customer?](#how-many-veg-and-non-veg-rolls-were-ordered-by-each-customer)
- [Max number of rolls delivered in a single order](#max-number-of-rolls-delivered-in-a-single-order)
- [How many delivered roll had at least 1 change or no change?](#how-many-delivered-roll-had-at-least-1-change-or-no-change)
- [How many rolls were there that had both exclusions and extras?](#how-many-rolls-were-there-that-had-both-exclusions-and-extras)
- [What was the total number of rolls delivered at each hour of the day?](#what-was-the-total-number-of-rolls-delivered-at-each-hour-of-the-day)
- [What was the number of orders for each day of the week?](#what-was-the-number-of-orders-for-each-day-of-the-week)
- [What was the average time it took to the driver to reach HQ for the order?](#what-was-the-average-time-it-took-to-the-driver-to-reach-hq-for-the-order)

# Creating the Dataset

### Driver Table
```
drop table if exists driver;
CREATE TABLE driver(driver_id integer,reg_date date); 

INSERT INTO driver(driver_id,reg_date) 
 VALUES (1,'01-01-2021'),
(2,'01-03-2021'),
(3,'01-08-2021'),
(4,'01-15-2021');
```

### Ingredient Table
```
drop table if exists ingredients;
CREATE TABLE ingredients(ingredients_id integer,ingredients_name varchar(60)); 

INSERT INTO ingredients(ingredients_id ,ingredients_name) 
 VALUES (1,'BBQ Chicken'),
(2,'Chilli Sauce'),
(3,'Chicken'),
(4,'Cheese'),
(5,'Kebab'),
(6,'Mushrooms'),
(7,'Onions'),
(8,'Egg'),
(9,'Peppers'),
(10,'schezwan sauce'),
(11,'Tomatoes'),
(12,'Tomato Sauce');
```

### Rolls Table
```
drop table if exists rolls;
CREATE TABLE rolls(roll_id integer,roll_name varchar(30)); 

INSERT INTO rolls(roll_id ,roll_name) 
 VALUES (1	,'Non Veg Roll'),
(2	,'Veg Roll');
```

### Rolls_Recipe Table
```
drop table if exists rolls_recipes;
CREATE TABLE rolls_recipes(roll_id integer,ingredients varchar(24)); 

INSERT INTO rolls_recipes(roll_id ,ingredients) 
 VALUES (1,'1,2,3,4,5,6,8,10'),
(2,'4,6,7,9,11,12');
```

### Driver_Order
```
drop table if exists driver_order;
CREATE TABLE driver_order(order_id integer,driver_id integer,pickup_time datetime,distance VARCHAR(7),duration VARCHAR(10),cancellation VARCHAR(23));
INSERT INTO driver_order(order_id,driver_id,pickup_time,distance,duration,cancellation) 
 VALUES(1,1,'01-01-2021 18:15:34','20km','32 minutes',''),
(2,1,'01-01-2021 19:10:54','20km','27 minutes',''),
(3,1,'01-03-2021 00:12:37','13.4km','20 mins','NaN'),
(4,2,'01-04-2021 13:53:03','23.4','40','NaN'),
(5,3,'01-08-2021 21:10:57','10','15','NaN'),
(6,3,null,null,null,'Cancellation'),
(7,2,'01-08-2020 21:30:45','25km','25mins',null),
(8,2,'01-10-2020 00:15:02','23.4 km','15 minute',null),
(9,2,null,null,null,'Customer Cancellation'),
(10,1,'01-11-2020 18:50:20','10km','10minutes',null);
```

### Customer_Orders Table

```
drop table if exists customer_orders;
CREATE TABLE customer_orders(order_id integer,customer_id integer,roll_id integer,not_include_items VARCHAR(4),extra_items_included VARCHAR(4),order_date datetime);
INSERT INTO customer_orders(order_id,customer_id,roll_id,not_include_items,extra_items_included,order_date)
values (1,101,1,'','','01-01-2021  18:05:02'),
(2,101,1,'','','01-01-2021 19:00:52'),
(3,102,1,'','','01-02-2021 23:51:23'),
(3,102,2,'','NaN','01-02-2021 23:51:23'),
(4,103,1,'4','','01-04-2021 13:23:46'),
(4,103,1,'4','','01-04-2021 13:23:46'),
(4,103,2,'4','','01-04-2021 13:23:46'),
(5,104,1,null,'1','01-08-2021 21:00:29'),
(6,101,2,null,null,'01-08-2021 21:03:13'),
(7,105,2,null,'1','01-08-2021 21:20:29'),
(8,102,1,null,null,'01-09-2021 23:54:33'),
(9,103,1,'4','1,5','01-10-2021 11:22:59'),
(10,104,1,null,null,'01-11-2021 18:34:49'),
(10,104,1,'2,6','1,4','01-11-2021 18:34:49');
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# Checking the tables

```
select * from customer_orders;
select * from driver_order;
select * from ingredients;
select * from driver;
select * from rolls;
select * from rolls_recipes;
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# How many rolls were ordered?
```
select count(roll_id) as total_rolls_ordered from customer_orders
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# How many unique customers ordered rolls
```
select count(distinct customer_id) as Unique_customers from customer_orders
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# How many successful orders?
```
select driver_id, count(distinct order_id) as number_of_orders from driver_order 
where cancellation not in ('Cancellation','Customer Cancellation')
group by driver_id;
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# How many each type of rolls were delivered?
```
select*from driver_order where cancellation not in ('Cancellation','Customer Cancellation') --not reading the null values
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# Classyfing cancellation and customer cancellation
```
-- select *, is used when case is used
-- for classyfing cancellation and customer cancellation
select roll_id, count(roll_id) as Successful_Orders from customer_orders where order_id in(
select D.order_id from(
select *,
case
when cancellation in ('Cancellation','Customer Cancellation') then 'C'
else 'NC'
end as order_cancel_details
from driver_order) as D
where order_cancel_details='NC' ) 
group by roll_id
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# How many veg and non veg rolls were ordered by each customer?

```
select Z.*, Y.roll_name from (
select customer_id, roll_id, count(roll_id) as number_of_orders 
from customer_orders 
group by customer_id, roll_id ) as Z 
inner join rolls as Y
on Z.roll_id=Y.roll_id
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------

# Max number of rolls delivered in a single order
```
select *from (
select*, rank() over(order by num_of_rolls desc) as rnk from
(
select order_id, count(roll_id) as num_of_rolls from (
select*from customer_orders where order_id in (
select order_id from (
select *,
case
when cancellation in ('Cancellation','Customer Cancellation') then 'C'
else 'NC'
end as order_cancel_details
from driver_order ) as P
where order_cancel_details='NC')) as L
group by order_id ) O ) I 
where rnk=1
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# How many delivered roll had at least 1 change or no change?
```
select*from customer_orders

with temp_customer_order ([order_id],customer_id,roll_id,not_include_items,extra_items_included,order_date)as
(
select order_id, customer_id, roll_id, 
case 
when not_include_items is null or not_include_items=' ' then '0' else not_include_items end as new_not_included_items,
case when extra_items_included is null or extra_items_included='NaN' or extra_items_included=' ' then '0' else extra_items_included
end as new_extra_items_included,
order_date from customer_orders
) 
,
temp_driver_order ([order_id],driver_id,pickup_time,distance,duraiton,new_cancellation) as
(
select order_id,driver_id,pickup_time,distance,duration, 
case 
when cancellation in ('Cancellation','Customer Cancellation') then 0 else 1 end as new_cancellation
from driver_order
)

select customer_id, change_status, count(order_id) as atleast_one_change from (
select *, case 
when not_include_items ='0' and extra_items_included='0' then 'No Change' else 'Changes' end as change_status
from temp_customer_order where order_id in (
select order_id from temp_driver_order where new_cancellation !=0 ) )K
group by customer_id, change_status 
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# How many rolls were there that had both exclusions and extras?
```
with temp_customer_order ([order_id],customer_id,roll_id,not_include_items,extra_items_included,order_date)as
(
select order_id, customer_id, roll_id, 
case 
when not_include_items is null or not_include_items=' ' then '0' else not_include_items end as new_not_included_items,
case when extra_items_included is null or extra_items_included='NaN' or extra_items_included=' ' then '0' else extra_items_included
end as new_extra_items_included,
order_date from customer_orders
) 
,
temp_driver_order ([order_id],driver_id,pickup_time,distance,duraiton,new_cancellation) as
(
select order_id,driver_id,pickup_time,distance,duration, 
case 
when cancellation in ('Cancellation','Customer Cancellation') then '0' else '1' end as new_cancellation
from driver_order
)
select order_edit_status, count(order_edit_status) from (
select *, case 
when not_include_items !='0' and extra_items_included!='0' then 'both_inc_exc' else 'either no change or one change' end as order_edit_status
from temp_customer_order where order_id in (
select order_id from temp_driver_order where new_cancellation !=0 ) ) M
group by order_edit_status
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# What was the total number of rolls delivered at each hour of the day?
```
select hour_bucket, count(hour_bucket) from
(
select*, concat( cast(datepart(hour,order_date) as Varchar) ,'-', 
cast(datepart(hour,order_date)+1 as varchar)) as hour_bucket
from customer_orders ) as J
group by hour_bucket
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# What was the number of orders for each day of the week?
```
select dow, count(distinct order_id) from (
select*, datename(dw,order_date) as dow from customer_orders )I
group by dow
```
---------------------------------------------------------------------------------------------------------------------------------------------------------------
# What was the average time it took to the driver to reach HQ for the order?
```
select*from customer_orders
select*from driver_order

select a.* , b.*, datediff(MINUTE, a.order_date , b.pickup_time) as diff_time 
from customer_orders a
inner join driver_order b
on a.order_id=b.order_id
where b.pickup_time is not null
```



