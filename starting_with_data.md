Question 1: Which products had multiple prices, and the percent differences between the highest and lowest price

SQL Queries:

```pgsql
SELECT sku, ((min1-max1)/max1)*100 as "% difference"
FROM (SELECT as1.sku ,max(as1.productprice) as max1,min (as2.productprice) as min1
		FROM all_sessions as as1
		LEFT JOIN all_sessions as as2
		ON as1.sku =as2.sku
		WHERE as1.productprice > as2.productprice and as1.productquantity is not NULL
		GROUP BY as1.sku) AS details
```
Answer: 
| SKU | % difference |
|---|---|
| "GGOENEBQ079199" | -33.61344537815126 |
| "GGOENEBJ079499" | -40.16064257028113 |
| "GGOEGOAB016099" | -20.011117287381868 |
| "GGOENEBQ079099" | -33.61344537815126 |
| "GGOEGAAX0604" | -20.002667022269627 |
| "GGOENEBQ078999" | -40.20100502512563 |
| "GGOEGAFB035814" | -20.00357206644044 |
| "GGOEGAAX0338" | -20.010531858873087 |
| "GGOEGAAX0317" | -20.011771630370802 |
| "GGOEGAAX0037" | -20.000000000000004 |
| "GGOEGAAX0318" | -20.011771630370802 |
| "GGOEGEVB070899" | -20.003333888981494 |
| "GGOEGOCB017499" | -20.018198362147416 |
| "GGOEGBRB013899" | -20.002000200020003 |
| "GGOEYHPB072210" | -20.018198362147416 |
| "GGOEGBJR018199" | -68.75 |
| "GGOENEBB078899" | -40.20100502512563 |
| "GGOEGAAX0104" | -20.011771630370802 |
| "GGOEGOCT019199" | -87.50000000000001 |
| "GGOEGBRJ037299" | -20.002000200020003 |
| "GGOEGHPB071610" | -20.018198362147416 |
| "GGOEGAAX0331" | -20.008003201280513 |
| "GGOEAHPA004110" | -30.01200480192077 |
| "GGOEGOAQ020099" | -20.066889632107024 |
| "GGOEWAEA083899" | -20.022246941045605 |
| "GGOEGAAX0362" | -20.003846893633387 |
| "GGOEGFKQ020399" | -20.10050251256281 |


Question 2: How many shoppers returned to the site organically(direct or organic search) from advertising activities (measures returning visitor from advertisements)

SQL Queries:
```pgsql
SELECT count(DISTINCT aftertb.fullvisitorid) AS "NUM of returning visitors from ads "
FROM 
	(SELECT *
	FROM ALL_SESSIONS
	WHERE channelgrouping IN ('Direct', 'Organic Search'))AS aftertb
JOIN
	(SELECT *
	FROM ALL_SESSIONS
	WHERE channelgrouping IN ('Referral', 'Affiliates', 'Paid Search','Display'))AS beforetb
ON aftertb.fullvisitorid=beforetb.fullvisitorid
WHERE aftertb.channelgrouping IN ('Direct', 'Organic Search')
AND beforetb.channelgrouping IN ('Referral', 'Affiliates', 'Paid Search','Display')
AND aftertb.visitid>beforetb.visitid AND aftertb.time>0

```
Answer:
| NUM of returning visitors |
|---|
| 7 |

Question 3: What hour in EST is our peak time in terms of traffic ( Helps CEO make choices like increasing workers for online chats etc)


SQL Queries:
```pgsql
SELECT EXTRACT(HOUR from ((to_timestamp(startshoppingtime)) AT TIME ZONE 'EST')) AS esthour, count(*)
FROM (SELECT CASE WHEN time1 IS NULL THEN time2
					ELSE time1
				END AS startshoppingtime
		FROM 
			(SELECT DISTINCT an.fullvisitorid, an.visitid AS time1, al.fullvisitorid, al.visitid AS time2
			FROM analytics as an
			FULL OUTER JOIN
				all_sessions as al
			ON an.fullvisitorid= al.fullvisitorid AND an.visitid=al.visitid) AS data1) AS timeforallvisitors
GROUP BY esthour
ORDER BY count(*) desc
LIMIT 5


```

Answer:
| EST hour | # of visitors |
|---|---|
| 12 | 9862 |
| 13 | 9837 |
| 15 | 9627 |
| 11 | 9067 |
| 16 | 9004 |


Question 4: 
What days are out peak time in terms of traffic ( Helps business choice like days to offer up reward to better retain visitors)


SQL Queries:
```pgsql
SELECT EXTRACT(DOY from (to_timestamp(startshoppingtime))) AS dayofyear, count(*)
FROM (SELECT CASE WHEN time1 IS NULL THEN time2
					ELSE time1
				END AS startshoppingtime
		FROM 
			(SELECT DISTINCT an.fullvisitorid, an.visitid AS time1, al.fullvisitorid, al.visitid AS time2
			FROM analytics as an
			FULL OUTER JOIN
				all_sessions as al
			ON an.fullvisitorid= al.fullvisitorid AND an.visitid=al.visitid) AS data1) AS timeforallvisitors
GROUP BY dayofyear
ORDER BY count(*) desc
LIMIT 5
```

Answer:
| Day of Year | # of visitors |
|---|---|
| 136 | 2350 |
| 137 | 2225 |
| 152 | 2182 |
| 180 | 2100 |
| 194 | 2071 |

