What are your risk areas? Identify and describe them.

MY primary risk areas are columns where it provides important information but also has some missing values.

Aside from columns with some missing values, values having multiple values for something they are entirly dependent on (thinking from a logical POV)

QA Process:
Describe your QA process and include the SQL queries used to execute it.

Verifying if questions surronding city can be answered without bias (is it incomplete data )

```pgsql
SELECT count(*)
FROM (SELECT distinct * 
FROM analytics
JOIN (SELECT *
FROM all_sessions) as tab2
ON tab2.fullvisitorid=analytics.fullvisitorid
WHERE city is NULL) as p1
```
```pgsql
SELECT count(*)
FROM (SELECT distinct * 
FROM analytics
JOIN (SELECT *
FROM all_sessions) as tab2
ON tab2.fullvisitorid=analytics.fullvisitorid
WHERE city is NOT NULL) as p1
```
There are more visitors that are not providing location that ones who are. So any insight with regard to cities, including top products purchased by city are questions this data cannot answer. 

Verifying that each product belongs to only one category:

```pgsql
SELECT sku, count(v2productcategory)
FROM (SELECT DISTINCT sku, v2productcategory FROM 
all_sessions) AS p1
GROUP BY sku
```
It does not. Logic dicates that a product can identify with only 1 category, when looked into further detail alot of products are miscategorized 

This QA process directly relates to Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country? and is now shown how data answering this question is not reliable. 