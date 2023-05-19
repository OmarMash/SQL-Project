What issues will you address by cleaning the data?

All columns that include costs or prices need to be divided by 1,000,000.

TIME and date columns have to be formatted.

City values such as "(Not Set)" makes it harder to count how many city are null, therefore I will set specif strings that dont provide city values as NULL.

Columns that have either nulls for every row or the same value for every row cannot provide business insights, and I have decided to delete them to save space. (It is a safe practise to document it, ex. All sales have been in USD instead of noting down USD in every row).

<br/><br/><br/><br/><br/><br/>

Queries:
Below, provide the SQL queries you used to clean your data.

```pgsql
UPDATE all_sessions SET totaltransactionrevenue = totaltransactionrevenue/1000000;
UPDATE all_sessions SET productrevenue = productrevenue/1000000;
UPDATE all_sessions SET productprice = productprice/1000000;
UPDATE all_sessions SET transactionrevenue = transactionrevenue/1000000;
UPDATE analytics SET revenue = revenue/1000000;
UPDATE analytics SET unit_price = unit_price/1000000;
```
```pgsql
ALTER TABLE analytics ADD COLUMN "date2" DATE;
UPDATE analytics SET "date2" = CAST("date" AS DATE);
ALTER TABLE analytics drop COLUMN "date";
ALTER TABLE analytics RENAME COLUMN "date2" TO "date";

ALTER TABLE analytics ADD COLUMN visitstarttime2 timestamptz;
UPDATE analytics SET visitstarttime2 = to_timestamp(visitstarttime);
ALTER TABLE analytics drop COLUMN visitstarttime;
ALTER TABLE analytics RENAME COLUMN visitstarttime2 TO visitstarttime;

ALTER TABLE all_sessions ADD COLUMN "date2" DATE;
UPDATE all_sessions SET "date2" = CAST("date" AS DATE);
ALTER TABLE all_sessions drop COLUMN "date";
ALTER TABLE all_sessions RENAME COLUMN "date2" TO "date";
```
```pgsql
UPDATE all_sessions SET city =  
	CASE
		WHEN city LIKE '%not set%' OR city LIKE '%not available%' THEN NULL
		ELSE city
	END 
```
```pgsql
ALTER TABLE analytics DROP COLUMN userid;
ALTER TABLE analytics DROP COLUMN socialengagmenttype;
ALTER TABLE all_sessions DROP COLUMN productrefundamount;
ALTER TABLE all_sessions DROP COLUMN itemrevenue;
ALTER TABLE all_sessions DROP COLUMN itemquantity;
ALTER TABLE all_sessions DROP COLUMN searchkeyword;
ALTER TABLE all_sessions DROP COLUMN currencycode;
```