# Portfolio
Stuart Brough's Portfolio - Data Analytics

**GOOGLE DOC IN DETAIL** https://docs.google.com/document/d/1WzMecgaWBDZLrT3Jf2oeh48tFggeUJd2OgXiiUtU67g/edit?usp=sharing
**TABLEAU DASHBOARD** - https://public.tableau.com/app/profile/stuart.brough/viz/GoogleDataAnalyticsCapstone-Track2GailforceClothing/GailForceDashboard

**Google Data Analytics Capstone Project - Track 2 - GailForce Clothing**

**BACKGROUND**

GailForce Clothing is an Australian clothing brand which sells outdoor clothing including jackets, coats, windproof clothing, thermals, sunglasses, hats and more at affordable prices, not designer prices. They look at selling towards children and adults.
GailForce Clothing is a relatively new company, started by a group of university friends who got together after graduating together. The company has done very well in Australia, despite its small size.
The clothing is manufactured in Australia as well due to proximity to the domestic market.
Cold weather clothing is suitable for temperatures <13 degrees and wind speed < 20km/h.
Warm weather apparel is marketed for temps >23 degrees and complete sunshine.
Sunglasses and Hats are aimed at areas with large amounts of sunshine.

The company’s strategy is to locate new markets overseas to sell both winter and summer clothing as well as sunglasses and hats. 

**My Role:** I am a junior data analyst working for a business intelligence consultant and I have been given the job of helping GailForce Clothing locate new markets. 

**Goal:** I have been tasked with identifying key regions and countries which are potential locations for GailForce Clothing to start marketing and selling their products towards.
	
**Business Question:**
‘Which regions of the world have suitable weather and climate for GailForce Clothing?’

**Expected Deliverable:**
Presentation with insights and recommendations on potential locations for summer-wear and locations for winter-wear and possible links to population and development.



**PROCESS**

**DATASETS**

Dataset Used: Global Daily Weather Data
Link: https://www.kaggle.com/datasets/guillemservera/global-daily-climate-data
Data included: Weather - Temp, climate history, Rain, Wind

**STEPS**


**CLEANING**
Used DELETE FROM to remove rows with no data. This was found to be mostly from locations which do not allow freedom of data or have accurate statistics which can be relied upon. These locations (e.g. Afghanistan) do not provide enough open data to be considered. Main focus is on temperature, precipitation/snow and wind factors among countries with extensive data over time.


These next two steps took me hours to work out before things started to click. I found it difficult to search online for assistance as I wasn’t sure how to phrase my web searches. I used Stack Overflow to try and ask questions but was quickly downvoted due to it being a repeated question - nobody would direct me to the right solution from those previous questions. YOUTUBE channel BECOMING A DATA ANALYST with Nathan Williams saved the day with his fantastic explanations of GROUP BY and subqueries.  
Used the following to collect Rainfall per year, per country. Same could be repeated for sunshine, snow cover, wind and max temperature.
Extracted Year from date to GROUP BY year for precipitation.
SELECT
	country,
	EXTRACT(year FROM date_measurement) AS year,
	SUM(precipitation_mm) AS total_rain
	INTO rainfall
FROM weather
GROUP BY EXTRACT(year from date_measurement),
	country
ORDER BY country,
	year
;

Using above, obtain average rainfall per year between 2013 and 2023 to show only recent climate history. We can focus on yearly averages instead of monthly to show stronger trends.
SELECT country, AVG(average_rain) AS total_rain FROM 
	(SELECT 
		country,
		year,
		AVG(total_rain) AS average_rain
	FROM rainfall
	WHERE year BETWEEN '2013' and '2023'
	GROUP BY 
		country,
		year
	ORDER BY country,
		year) sub
GROUP BY country;


Use Tableau to graph rainfall in millimetres, filtering out countries with less than one million people as the cost to enter the market will outweigh potential revenue. I did some digging to see if Ecuador was a data anomaly, error or just an outlier. Ecuador’s northern coastline around Oriente does receive between 3000-6000 mm precipitation per year so this is correct.

Repeat the same SQL steps for temperature. This time I wanted to compare average maximum temperatures alongside average minimum temperatures.
SELECT
	country,
	EXTRACT(year FROM date_measurement) AS year,
	AVG(max_temp_c) AS max_temperature,
	AVG(min_temp_c) AS min_temperature
	INTO temperatures
FROM weather
GROUP BY EXTRACT(year from date_measurement),
	country
ORDER BY country,
	year
;


Again, using data from 2013-2023, show the average maximum and minimum temperatures across the ten year period. 

SELECT 
	country,
	AVG(max_total) AS maximumtempavg,
	AVG(min_total) AS minimumtempavg
	FROM 
		(SELECT
	 		country,
	 		year,
	 		AVG(max_temperature) AS max_total,
		 	AVG(min_temperature) AS min_total
		 FROM temperatures
		WHERE year BETWEEN '2013' and '2023'
		GROUP BY 
			country,
			year
		ORDER BY
			country,
			year) sub
GROUP BY country;

Using Tableau, I combined Maximum and Minimum temperature yearly averages using the dual axis option. This allowed me to show circle plots for temperatures and a line which highlights the range. An average temperature line (19.9C) is included. I learned about the Barbell style graphs when combining predicted scores with actual scores in the education system and used tableau.com to find how to setup in Tableau. Example results below:





Using a similar method, I wanted to map the amount of sunshine a country had on average throughout the year and show it on a heat map (single variable on world map). I created a new table in POSTGRES to do this.
SELECT DISTINCT country,
	Sunshine_total_min,
	date_measurement
INTO sun
FROM weather2
WHERE sunshine_total_min is NOT NULL

I found that there were only 8 countries that consistently reported sunshine so this is not a reliable data point and cannot be included to make an informed decision.






Wind speed is also a piece of data that can be used to link to wind breaker, pants, hat and jacket sales in a region. Similar queries were done in SQL before but AVG used instead of SUM in the first query. Otherwise it looks like wind speeds reach impossible levels. I used this data to make a heat map instead.



Created a table to identify countries that have both high precipitation and maximum temperatures to create visualisation of correlation. I made sure to learn from earlier mistakes and use AVG instead of SUM. Overall there is a very slight positive trend as shown by the trend line. Ecuador (precipitation) is a clear outlier in this data as are Maldives, Sudan and Central African Republic (high temps but low precipitation).


Finally, I downloaded Gross National Income per capita data from the World Bank Data website (https://data.worldbank.org/indicator/NY.GNP.PCAP.CD?end=2022&start=2022) and cleaned for countries with >1,000,000 population. Used Tableau to map the GNI per capita. The World Bank did not have data on disposable income.



**FINDINGS**

**Precipitation**
Ecuador the clear leader here. Other High Income Countries with high amounts of rainfall include Taiwan, South Korea, Singapore and Chile. Brazil (not technically High Income yet, but forecast to be in the future), China (growing economy and middle class) and Indonesia (large population) are also to be noted. The USA, New Zealand and Japan are not far behind. Data for the USA is harder to interpret due to geographic size.

**Max Temps Above 23℃**
Most countries except Northern Europe experience maximum temperatures above 23℃ across the year. Mexico, South Africa, Italy and Greece have large ranges between maximum and minimum temperatures. Countries around the equator are again prominent, as expected. Singapore, Taiwan, South Korea, China, Chile and Brazil feature again.


**Min Temps Below 13℃**
Northern Europe here features alongside Canada, USA, Western Europe, Eastern Europe. Only Japan, South Korea, and China feature prominently in Asia.


**Wind Speed Averages**

Northern Europe including Scandinavia feature strong winds. UK, France as well. USA figures are increased due to impacts of Hurricanes in the region. Japan, Taiwan and South Korea are similar in Asia. New Zealand and the southern part of South America also have enough wind speed to require windbreaker clothing especially during winter. 

**Relationship between Maximum Temperature and Rainfall**

Correlation results are low. Ecuador is a clear outlier as well as some African nations prone to droughts and famines. Singapore, Malaysia, Brazil, China and Indonesia have both high maximum temperatures and above average precipitation. Of note is that New Zealand has below average temperatures and medium rainfall. 

**Gross National Income per Capita**

High Income Countries dominate here. Of those previously mentioned other variables, Singapore, USA, New Zealand, China, Taiwan, Japan and South Korea have high GNI per capita. China, Brazil, India and Indonesia are forecasted to increase over the next 20 years. 


CONCLUSION AND RECOMMENDATIONS

Due to clothing being manufactured in Australia, GailForce clothing should focus within Asia, especially Japan, South Korea, China and Taiwan. Japan, South Korea and Taiwan all have high levels of rainfall as well as high maximum and low minimum temperatures, allowing GailForce clothing to market all products there. The shipping would be much cheaper when compared with Europe and the Americas. More data should be explored to determine if production should continue in Australia in the future or if it could be outsourced to a location in East Asia.
