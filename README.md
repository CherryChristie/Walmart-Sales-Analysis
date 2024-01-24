# Walmart-Sales-Analysis


## Project Overview
This project aims to explore the Walmart Sales data to understand top performing 
branches and products, sales trend of different products, customer behavior. The aims is 
to study how sales strategies can be improved and optimized. 
Analyst was provided with historical sales data for 45 
Walmart stores located in different regions. Each store contains many departments, and 
participants must project the sales for each department in each store. To add to the 
challenge, selected holiday markdown events are included in the dataset. These 
markdowns are known to affect sales, but it is challenging to predict which departments 
are affected and the extent of the impact.

## Data Source
Walmart Sales Data : The primary dataset used for this analysis is the "WalmartSalesDAta.csv.csv" file which contains detailed information about each sales transaction.

## Tools
Excel (This is used for data cleaning)
Microsoft SQL server (This is used for data analysis)
Power Bi (is used for Data visualisation)

##  Data Cleaning and Preparation
The following steps breaks down the cleaning process taken in ensuring the dataset was clean, valid and ready for analysis.

Dropped Irrelevant columns
Changed  Datatype
Removed duplicates
Filled missing values with 'N/A' for string/text variables

## Exploratory Data Analysis
EDA involves exploring the datasets to find out and answer key questions such as :

1. How many unique cities does the data have?
2. In which city is each branch?
3. How many unique product lines does the data have?
4. What is the most selling product line?
5. What is the total revenue by month?
6. What month had the largest COGS (Cost of Goods Sold)?
7. What product line had the largest revenue?
8. What is the city with the largest revenue?
9. What product line had the largest VAT (Value Added Tax)?
10. Fetch each product line and add a column showing "Good" or "Bad" based on whether it's greater 
than average sales.
11. Which branch sold more products than the average product sold?
12. What is the most common product line by gender?
13. What is the average rating of each product line?
14. How many unique customer types does the data have?
15. How many unique payment methods does the data have?
16. What is the most common customer type?
17. Which customer type buys the most?
18. What is the gender of most customers?
19. What is the gender distribution per branch?

## Solutions
-- What is the total revenue by month?
```` sql
SELECT DATEPART(MONTH, Date) as Monthly_Revenue, ROUND(SUM(Total),0) Revenue
FROM walmartsales
GROUP BY DATEPART(MONTH, Date) 
ORDER BY 2 DESC;
````

--  What month had the largest COGS (Cost of Goods Sold)?
```` sql
SELECT TOP 1  DATEPART(MONTH, Date) as Monthly_Revenue, ROUND(SUM(cogs),0) Total_cogs
FROM walmartsales
GROUP BY DATEPART(MONTH, Date) 
ORDER BY 2 DESC;
````


-- What product line had the largest revenue?
```` sql
SELECT TOP 1 product_line,  ROUND(SUM(Total),0) Total_Revenue
FROM walmartsales
GROUP BY product_line
ORDER BY 2 DESC;
````

-- What is the city with the largest revenue?
```` sql
SELECT TOP 1 city,  ROUND(SUM(Total),0) Total_Revenue
FROM walmartsales
GROUP BY city
ORDER BY 2 DESC;
````

--  What product line had the largest VAT (Value Added Tax)?
```` sql
SELECT TOP 1 product_line,  ROUND(SUM(Tax_5),0) Total_Tax
FROM walmartsales
GROUP BY product_line
ORDER BY 2 DESC;
````
-  During which time of day do customers give the most ratings?
```` sql
SELECT TOP 1 DATETRUNC(HOUR, Time)as Hourly_Rating, COUNT(Rating) Num_of_Ratings
FROM   walmartsales
GROUP BY DATETRUNC(HOUR, Time)
ORDER BY 2 DESC;
````


-- 2 During which time of day do customers give the most ratings per branch?
```` sql
WITH RatingsPerHour AS(
	SELECT  Branch,DATETRUNC(HOUR, Time)as Hourly_Rating,  COUNT(Rating) Num_of_Ratings
	FROM   walmartsales
	GROUP BY  Branch, DATETRUNC(HOUR, Time)),
MaxHourlyRating AS 
	(SELECT Branch,  MAX(Num_of_Ratings) Max_Hourly_Ratings
	FROM RatingsPerHour
	GROUP BY Branch)

SELECT 
    rp.branch, 
    rp.Hourly_Rating, 
    rp.Num_of_Ratings
FROM 
    RatingsPerHour rp
JOIN MaxHourlyRating t2 ON t2.Branch= rp.branch AND t2.Max_Hourly_Ratings = rp.Num_of_Ratings
ORDER BY 3 DESC;
````


--  Which day of the week has the best average ratings?
```` sql
SET DATEFIRST 7;

SELECT TOP 1 DATENAME(weekday, Date) AS Weekday, 
			DATEPART(dw, Date) DW_Ratings, 
			ROUND(AVG(Rating),1) as Num_of_Ratings
FROM walmartsales
GROUP BY DATENAME(weekday, Date), DATEPART(dw, Date)
ORDER BY 3 DESC;
````


--  Which day of the week has the best average ratings per branch?
```` sql
WITH AvgRatings_PerWeek  AS 
   (SELECT DATENAME(weekday, Date) AS Weekday, DATEPART(dw, Date) DW_Ratings, Branch, ROUND(AVG(Rating),1) as Avg_Ratings
    FROM walmartsales
     GROUP BY DATENAME(weekday, Date), DATEPART(dw, Date), Branch),
MaxAvgRating AS 
	(SELECT  Branch, Max(Avg_Ratings) Max_Rating
	 FROM AvgRatings_PerWeek
	 GROUP BY Branch)

SELECT av.Weekday, av.DW_Ratings, av.Branch, Avg_Ratings
FROM AvgRatings_PerWeek av
JOIN MaxAvgRating mx ON av.Avg_Ratings = mx.Max_Rating AND av.Branch = mx.Branch
ORDER BY 4 DESC;
````
## Findings
- Geographical Insights:

The dataset covers sales data from 3 unique cities.
An examination of each city's branches revealed distinct sales patterns, although specific results for each branch were not commented on in the data.
Product Line Analysis:

There are 6 unique product lines in the dataset.
The most selling product line is Electronic Accessories, with a count of 178.
The product line generating the largest revenue is Food and Beverages.
- Sales and Revenue:

The first month of the year recorded the highest revenue, followed by the third month.
January had the largest Cost of Goods Sold (COGS).
Naypytaw city is identified as generating the largest revenue among the cities.
- Customer Demographics and Preferences:

For female customers, the most popular product lines are Fashion Accessories and Food and Beverages. For male customers, the preferred lines are Health and Beauty and Electronic Accessories.
The female gender is the most common among customers.
The most common customer type is identified as members, and they also buy the most, indicating strong loyalty and frequent purchasing patterns.
- Taxation and VAT Analysis:

Food and Beverages also lead in generating the largest VAT (Value Added Tax).
Yangon city has the largest tax/VAT percent.
- Sales Performance by Time and Day:

The highest number of customer ratings occurs at 7 pm.
Monday is the day with the best average customer ratings.
Sales performance varies significantly across different times of the day and weekdays, suggesting varying customer behaviors.
- Branch Performance:

Branch A sold more products than the average, with a total product count of 1859.
- Customer Type Analysis:

The member customer type brings in the most revenue and pays the most in VAT, highlighting their significant contribution to Walmart's sales.
## Dashboard
![6e4299d790b75b0db4d1795cfcbe287](https://github.com/CherryChristie/Walmart-Sales-Analysis/assets/148567375/091a2858-9992-4778-8df4-35c29771cca5)










