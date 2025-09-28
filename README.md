/* Load the dataset into SAS */
proc import datafile="/mnt/data/sales_data/sales_data.csv"
    out=salesdata
    dbms=csv
    replace;
    getnames=yes;
run;

/* Create another sample table (e.g., country region mapping) for JOIN */
data country_region;
    input Country $ Region $;
    datalines;
Canada NorthAmerica
Australia APAC
Germany Europe
France Europe
UnitedStates NorthAmerica
;
run;

/* Example SQL queries in one step */
proc sql;

/* a. SELECT with WHERE, ORDER BY, GROUP BY */
create table sales_summary as
select Country, Product_Category,
       sum(Revenue) as Total_Revenue,
       avg(Profit) as Avg_Profit
from salesdata
where Year >= 2014
group by Country, Product_Category
order by Total_Revenue desc;

/* b. JOINS: INNER, LEFT, RIGHT */
create table join_inner as
select a.Country, a.Total_Revenue, b.Region
from sales_summary a
inner join country_region b
on a.Country = b.Country;

create table join_left as
select a.Country, a.Total_Revenue, b.Region
from sales_summary a
left join country_region b
on a.Country = b.Country;

create table join_right as
select a.Country, a.Total_Revenue, b.Region
from sales_summary a
right join country_region b
on a.Country = b.Country;

/* c. Subquery: find countries above avg revenue */
create table high_revenue_countries as
select Country, Total_Revenue
from sales_summary
where Total_Revenue > (
      select avg(Total_Revenue)
      from sales_summary);

/* e. Create a VIEW for analysis */
create view top_products as
select Product, sum(Revenue) as Revenue
from salesdata
group by Product
having Revenue > 10000;

/* f. Optimize queries with an index */
create index idx_country on salesdata(Country);
create index idx_year on salesdata(Year);

quit;# Sales-Data-using-sql
This dataset is designed to represent a retail sales system,where customers purchase products over time.It can be used for practicing SQL queries such as filtering, aggregation,join,and reporting 
