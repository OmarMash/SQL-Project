Answer the following questions and provide the SQL queries used to find the answer.

  <br/>  
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
```pgsql
SELECT distinct country,SUM(totaltransactionrevenue) 
FROM all_sessions
WHERE totaltransactionrevenue is not null
group by country
order by total desc
LIMIT 5
```
```pgsql
SELECT distinct city,SUM(totaltransactionrevenue) as total
FROM all_sessions
WHERE totaltransactionrevenue is not null and city is not null
group by city
order by total desc
LIMIT 5
```

Answer:

|  | country | total |
|---|---|---|
| 1 | United States | 13154.169999999996 |
| 2 | Israel | 602 |
| 3 | Australia | 358 |
| 4 | Canada | 150.14999999999998 |
| 5 | Switzerland | 16.99 |

|  | city | total |
|---|---|---|
| 1 | "San Francisco" | 1564.3200000000002 |
| 2 | "Sunnyvale" | 992.23 |
| 3 | "Atlanta" | 854.44 |
| 4 | "Palo Alto" | 608 |
| 5 | "Tel Aviv-Yafo" | 602 |


<br/>
**Question 2: What is the average number of products ordered from visitors in each city and country?**

SQL Queries:

```pgsql
SELECT country,part1/part2
FROM
(SELECT COUNTRY, SUM(units_sold) as part1, count(tab2.fullvisitorid) as part2
FROM analytics
JOIN (SELECT DISTINCT fullvisitorid, country, city, sku
FROM all_sessions) as tab2
ON tab2.fullvisitorid=analytics.fullvisitorid
where revenue is not null
GROUP BY COUNTRY) as tallyed

```
```pgsql
SELECT city,part1/part2 as "average" 
FROM
(SELECT city, SUM(units_sold) as part1, count(tab2.fullvisitorid) as part2
FROM analytics
JOIN (SELECT DISTINCT fullvisitorid,visitid, country, city, sku
FROM all_sessions) as tab2
ON tab2.fullvisitorid=analytics.fullvisitorid
where revenue is not null
GROUP BY city) as tallyed
WHERE city is not null
order by "average" desc
LIMIT 5

```
Answer:

|  | country | avg |
|---|---|---|
| 1 | "Canada" | 2 |
| 2 | "Germany" | 1 |
| 3 | "Japan" | 1 |
| 4 | "Switzerland" | 1 |
| 5 | "United States" | 25 |

|  | city | avg |
|---|---|---|
| 1 | "San Bruno" | 61 |
| 2 | "Mountain View" | 24 |
| 3 | "New York" | 10 |
| 4 | "Chicago" | 7 |
| 5 | "Salem" | 7 |


<br/>
**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

```pgsql
SELECT v2productcategory,country, count(*), dense_rank()over(partition by country order by count(*) desc) as ranked
FROM analytics
JOIN (SELECT DISTINCT fullvisitorid,visitid, country, v2productcategory
FROM all_sessions) as tab2
ON tab2.fullvisitorid=analytics.fullvisitorid
where revenue is not null and country is not null
GROUP BY country, v2productcategory
order by ranked , count desc
limit 5
```
```pgsql
SELECT v2productcategory,city, count(*)
FROM analytics
JOIN (SELECT DISTINCT fullvisitorid,visitid, country, city, v2productcategory
FROM all_sessions) as tab2
ON tab2.fullvisitorid=analytics.fullvisitorid
where revenue is not null and city is not null
GROUP BY city, v2productcategory
order by count(*) desc
LIMIT 3
```
Answer:

|  | productcategory | country | count | R |
|---|---|---|---|---|
| 1 | "Home/Nest/Nest-USA/" | "United States" | 78 | 1 |
| 2 | "Apparel" | "Canada" | 4 | 1 |
| 3 | "Home/Apparel/" | "Germany" | 2 | 1 |
| 4 | "Home/Shop by Brand/Google/" | "Japan" | 2 | 1 |
| 5 | "Home/Apparel/Men's/Men's-T-Shirts/" | "Switzerland" | 1 | 1 |

|  | productcategory | city | count |
|---|---|---|---|
| 1 | "Home/Bags/" | "Charlotte" | 29 |
| 2 | "Housewares" | "Sunnyvale" | 18 |
| 3 | "Home/Nest/Nest-USA/" | "Mountain View" | 16 |

<br/>
**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

```pgsql
SELECT country, sku
FROM (SELECT * , count(country) over (partition by country ORDER BY co DESC) AS ranked
		FROM (SELECT country, sku, count(*) as co
				FROM analytics
				JOIN (SELECT DISTINCT fullvisitorid,visitid, country, city, sku
						FROM all_sessions) as tab2
				ON tab2.fullvisitorid=analytics.fullvisitorid
				where revenue is not null and country is not null
				GROUP BY country, sku) as part1) AS part2
WHERE ranked = 1
```

```pgsql
SELECT city, sku
FROM (SELECT * , count(city) over (partition by city ORDER BY co DESC) AS ranked
		FROM (SELECT city, sku, count(*) as co
				FROM analytics
				JOIN (SELECT DISTINCT fullvisitorid,visitid, country, city, sku
						FROM all_sessions) as tab2
				ON tab2.fullvisitorid=analytics.fullvisitorid
				where revenue is not null and city is not null
				GROUP BY city, sku) as part1) AS part2
WHERE ranked = 1
```


Answer:

| country | sku |
|---|---|
| "Canada" | "GGOEGAEJ030715" |
| "Germany" | "GGOEGHPJ080110" |
| "Japan" | "GGOEGBRJ037399" |
| "Switzerland" | "GGOEGAAX0314" |
| "United States" | "GGOEGAYR023499" |

| city | SKU |
|---|---|
| "Ann Arbor" | "GGOEGODR017799" |
| "Atlanta" | "GGOEGAAX0613" |
| "Austin" | "GGOEYOCR077399" |
| "Cambridge" | "GGOEGOFH020299" |
| "Charlotte" | "GGOEGBCR024399" |
| "Denver" | "GGOEGHPB071610" |
| "Fremont" | "GGOENEBQ079099" |
| "Jersey City" | "GGOEGBRB073899" |
| "Los Angeles" | "GGOENEBB078899" |
| "Milpitas" | "GGOEGAAX0289" |
| "Mountain View" | "GGOENEBQ079099" |
| "New York" | "GGOEGBJC019999" |
| "Palo Alto" | "GGOENEBQ084699" |
| "Salem" | "GGOEGAAX0358" |
| "San Bruno" | "GGOEYDHJ056099" |
| "San Francisco" | "GGOEGAAX0366" |
| "San Jose" | "GGOEGBCR024399" |
| "Seattle" | "GGOEGAAX0352" |
| "South San Francisco" | "GGOEGBRJ037399" |
| "Sunnyvale" | "GGOEGCBQ016499" |
| "Toronto" | "GGOEGAEJ030715" |
| "Yokohama" | "GGOEGBRJ037399" |
| "Zurich" | "GGOEGAAX0314" |

<br/>
**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

```pgsql
SELECT country, (totalrevpercountry/totalrev)*100 AS "Accounts for % of total revenue"
	FROM (SELECT country, SUM(revenue) as totalrevpercountry, 
		  	(SELECT SUM(revenue) FROM analytics ) AS totalrev
			FROM analytics
			JOIN (SELECT DISTINCT fullvisitorid,visitid, country, city, sku
					FROM all_sessions) as tab2
			ON tab2.fullvisitorid=analytics.fullvisitorid
			where revenue is not null and country is not null
			GROUP BY country) AS part1
```
```pgsql
SELECT city, (totalrevpercity/totalrev)*100 AS "Accounts for % of total revenue"
	FROM (SELECT city, SUM(revenue) as totalrevpercity, 
		  	(SELECT SUM(revenue) FROM analytics ) AS totalrev
			FROM analytics
			JOIN (SELECT DISTINCT fullvisitorid,visitid, city, sku
					FROM all_sessions) as tab2
			ON tab2.fullvisitorid=analytics.fullvisitorid
			where revenue is not null and city is not null
			GROUP BY city) AS part1
```

Answer:

| country | % of total revenue |
|---|---|
| "Canada" | 0.0417855376416413 |
| "Germany" | 0.005996169385367274 |
| "Japan" | 0.002645923272651349 |
| "Switzerland" | 0.0014557719042210626 |
| "United States" | 9.85960121938607 |

| city | % of total revenue |
|---|---|
| "Ann Arbor" | 0.03789119776508921 |
| "Atlanta" | 0.007111775635688525 |
| "Austin" | 0.07382108759614875 |
| "Cambridge" | 0.014173854525971035 |
| "Charlotte" | 0.09951259103313094 |
| "Chicago" | 0.1185550135278038 |
| "Denver" | 0.0035970161588699313 |
| "Fremont" | 0.01062482143163105 |
| "Jersey City" | 0.08001604430587626 |
| "Kirkland" | 0.09455405601801206 |
| "Los Angeles" | 0.08080005322099403 |
| "Milpitas" | 0.042353622852058286 |
| "Mountain View" | 0.8902135159130419 |
| "New York" | 0.298676582364247 |
| "Palo Alto" | 0.06074998705666463 |
| "Salem" | 0.029648392836878836 |
| "San Bruno" | 0.3625283324920698 |
| "San Francisco" | 0.0846087088823745 |
| "San Jose" | 0.020541892661445387 |
| "Seattle" | 0.06828161424993806 |
| "South San Francisco" | 0.007062078890282509 |
| "Sunnyvale" | 0.26041608628468077 |
| "Toronto" | 0.04178553764164124 |
| "Yokohama" | 0.002645923272651345 |
| "Zurich" | 0.0014557719042210607 |




