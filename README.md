# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
My goal has been to transform data provided into valid insights which will help with business decisions regarding ecommerce.
## Process
### Importing Data
Before I could attempt to import the data, I had to look at each table and comprehend which data types to assign each column. After the create table statements I filled out the tables through the pgadmin import process.

An important thing to check is that data values are not being misquoted. In my case fullvisitorid values was too big a number to assign as a "bigint" and date values should be assigned as string although it can be in a numeric format. (These small discrepancies do not give out errors, so better to catch them early on)
### Cleaning the Data 

I started step 2 by dividing all monetary values by one million. But found myself repeating step 1 becuase the original data type assigned dont take decimal values in consideration. 

Asside from that I gave priority to formating date/time values, deleting all columns that proved a waste of database space (so ones where every row in that column having the same value). Assigned a NULL value to improper values like in columns country, and city.


### Finding insights relating to visitor's Country and City

I attempted to answer important questions on the likes of which cities do most of our visitors shop from, and etc. However I quickly came to the realization that the data and answers are painting a wrong portrait. Using my example from before, we were actually finding out which cities do most of our visitors who offer information on their city are from. Most of the visitors did not offer information about their city.


### Wrong Questions vs Right Questions

The Next Step was finding out which questions can be asked with the data we have, without bias. One thing I quickly found out is that visitid was not a ID like with serials, it was the timestamp, perfect becuase there were no missing values. Another piece of information that had no missing values was product price in table all_sessions. You can have a look at "Starting_with_data.md" for the details. Just to summerize, I formulated questions around complete data columns.


### Quality Assurance

The Data I centered around my QA process were that of which my insights were being derived from.

## Results

The insights that were a result of analyzing this data include finding which products has been bought at a higher price point, and the potential savings on that product as a percentage difference. 

I also found out have many vistors that have visited thru paid traffic or referrals have visited again directly or thru an organic search. My findings report only 7 visitors have, which is extremly low.

Finding the Hour in a day (EST timezone) where the site has most traffic, this can help businesses make decision on when to schedule their employees, customer representatives to optimize customer satisfaction. 

Lastly, Finding the days in a year where the site has most traffic. I was surprised to learn the there most busiest days in a year are not around the holidays, this insight can be a useful benchmark for businesses. It lets them know that they do not have a competitve edge. 

All the insights above in general impower business leaders to assess the results and decide on the next course of action.



## Challenges 
It was hard to wrap my head around the data. I failed at normalizing the database, having missing values helped with that, but mainly because I did not full comprehend the dependencies each value had on others. Most of the time spent was TRYING to understand the whole database. Example of one of the ways this database stumped me, was how the analytics table had alot more sessions logged the the table all_sessions. 

## Future Goals

If I had more time & resources, I would want the role tables have on interacting with each other to be clear. To identify the dependencies each columns have on other columns. I would like for it to me normalized, and 

For the data to be complete, this can be achieved thru data reconcilation. Example, identifying the correct category for each product, and updating the tables. 
