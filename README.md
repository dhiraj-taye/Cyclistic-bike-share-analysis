# Cyclistic bike share analysis

## Author: Dhiraj Taye
### Date: 30-3-2024
__Tableau Visualization [Link](https://public.tableau.com/app/profile/dhiraj.taye/viz/Cyclistic_Analysis_17117994201650/Sheet5#2)__

----

# INTRODUCTION

Cyclistic, a bike-sharing company headquartered in Chicago, boasts a fleet of over 5,800 bicycles and operates 600 docking stations. While a significant portion of its user base utilizes the bikes for leisurely activities, approximately 30% rely on them for daily commuting. Historically, Cyclistic's marketing efforts have prioritized broad consumer outreach and brand awareness. The company provides single-day and full-day passes for occasional riders and offers annual subscriptions for dedicated members.

Through data analysis, Cyclistic has determined that annual members yield greater profitability compared to casual riders. As a result, the company aims to focus on increasing the number of annual memberships to drive future growth. Rather than targeting entirely new customers, Cyclistic plans to launch a targeted marketing campaign aimed at converting casual users into annual members. To facilitate this transition, Cyclistic seeks to better understand the distinctions between casual users and members through an in-depth analysis of historical trip data. This strategic approach will inform the development of tailored marketing strategies aimed at achieving the objective of boosting annual memberships.

----

## Problem Statement

How do annual members and casual riders use Cyclistic bikes differently?

----


## Preparing Data for Analysis

For this project, I've used the 13 trip-data datasets dated from April 2020 to April 2021. Click on this link to access the website and download the datasets provided as .zip files. The data provided in this website is made available to access to the public.

Or you could access and download the data from this repository named as "Raw Data".

I am using Microsoft SQL Server Management Studio in this part of the project to help process and analyze the datasets.

First make sure to import all of the dataset as a .csv file to the database server. Check and verify if the data types of each of the columns in each dataset is same to merge them all together

```sql
-- Creating a table to merge 13 datasets into one table for better usability


CREATE TABLE all_data_202004_202104 (
ride_id nvarchar(255),
rideable_type nvarchar(50),
started_at datetime2,
ended_at datetime2,
start_lat float,
start_lng float,
end_lat float,
end_lng float,
member_casual nvarchar(50) )
```

```sql

 Creating a table to merge 12 datasets into one table for better usability

CREATE TABLE all_data_202301_202312 (
ride_id nvarchar(255),
rideable_type nvarchar(50),
started_at datetime2,
ended_at datetime2,
start_station_name varchar(MAX),
end_station_name varchar(MAX),
start_lat float,
start_lng float,
end_lat float,
end_lng float,
member_casual nvarchar(50)
)

  Insert the information from 13 tables to one table using UNION

 INSERT INTO [dbo].[all_data_202301_202312] (ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual)
 (SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202301])
 UNION ALL
 (SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202302])
 UNION ALL(SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202303])
 UNION ALL(SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202304])
 UNION ALL(SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202305])
 UNION ALL(SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202306])
 UNION ALL(SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202307])
 UNION ALL(SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202308])
 UNION ALL(SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202309])
 UNION ALL(SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202310])
 UNION ALL(SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202311])
 UNION ALL(SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, end_station_name,
 start_lat, start_lng, end_lat, end_lng, member_casual
 FROM dbo.[202312])
```

----

## Processing and Analysis of Data

Here, I will be transforming and organizing data by adding new columns, extracting information and removing bad data and duplicates.

```sql
 -- Adding a new column to calculate the ride length from datetime2

ALTER TABLE [dbo].[all_data_202004_202104]
ADD ride_length int

UPDATE [dbo].[all_data_202004_202104]
SET ride_length = DATEDIFF(MINUTE, started_at, ended_at)


-- Extracting month and year from datetime2 format and adding them as new columns


ALTER TABLE [dbo].[all_data_202004_202104]
ADD day_of_week nvarchar(50),
month_m nvarchar(50),
year_y nvarchar(50)

UPDATE [dbo].[all_data_202004_202104]
SET day_of_week = DATENAME(WEEKDAY, started_at),
month_m = DATENAME(MONTH, started_at),
year_y = year(started_at)


ALTER TABLE [dbo].[all_data_202004_202104]       
ADD month_int int

UPDATE [dbo].[all_data_202004_202104]             -- Extracting month num from datetime2 format
SET month_int = DATEPART(MONTH, started_at)


ALTER TABLE [dbo].[all_data_202004_202104]       
ADD date_yyyy_mm_dd date

UPDATE [dbo].[all_data_202004_202104]             -- Casting datetime2 format to date
SET date_yyyy_mm_dd = CAST(started_at AS date)
```
In order to get accurate analysis, validate and make sure the dataset does not include any bias, incorrect data, and duplicates.

```sql

/* Deleted rows where (NULL values), (ride length = 0), (ride length < 0), (ride_length > 1440 mins)
   for accurate analysis */


DELETE FROM [dbo].[all_data_202004_202104]
Where ride_id IS NULL OR
start_station_name IS NULL OR
ride_length IS NULL OR
ride_length = 0 OR
ride_length < 0 OR
ride_length > 1440


-- Checking for any duplicates by checking count

Select Count(DISTINCT(ride_id)) AS uniq,
Count(ride_id) AS total
From [dbo].[all_data_202004_202104]
```

```sql
/* Calculating Number of Riders Each Day by User Type and Creating View to store date
   for Further Visualization */


Create View users_per_day AS
Select 
Count(case when member_casual = 'member' then 1 else NULL END) AS num_of_members,
Count(case when member_casual = 'casual' then 1 else NULL END) AS num_of_casual,
Count(*) AS num_of_users,
day_of_week
From [dbo].[all_data_202004_202104]
Group BY day_of_week
```
Result:

|num_of_member|	num_of_casuals | num_of_users |	day_of_week |
|---------| -------- | ------- | ---|
|387082	|342061| 729143 |	Saturday|
|495331	|206152| 701483	|Wednesday|
|419502	|195642| 615144	|Monday|
|337633	|280182| 617815	|Sunday|
|441106	|256421| 697527	|Friday|
|495696	|224189| 719885	|Thursday|
|489219	|203959| 693178	|Tuesday|


```sql

/* Calculating Average Ride Length for Each User Type and Creating View to store data 
   for further Data Visualization */


Create View avg_ride_length AS
SELECT member_casual AS user_type, AVG(ride_length)AS avg_ride_length
From [dbo].[all_data_202004_202104]
Group BY member_casual
```

Result:
 |user_type|avg_ride_length|
 |---|---|
 |member |	12|
|casual	|22|

```sql

-- Creating temporary tables exclusively for Casual Users and Members


CREATE TABLE #member_table (
ride_id nvarchar(50),
rideable_type nvarchar(50),
member_casual nvarchar(50),
ride_length int,
day_of_week nvarchar(50),
month_m nvarchar(50),
year_y int )

INSERT INTO #member_table (ride_id, rideable_type, member_casual, ride_length,
 day_of_week, month_m, year_y)

(Select ride_id, rideable_type, member_casual, ride_length, day_of_week, month_m, year_y
From [dbo].[all_data_202004_202104]
Where member_casual = 'member')

CREATE TABLE #casual_table (
ride_id nvarchar(50),
rideable_type nvarchar(50),
member_casual nvarchar(50),
ride_length int,
day_of_week nvarchar(50),
month_m nvarchar(50),
year_y int )

INSERT INTO #casual_table (ride_id, rideable_type, member_casual, ride_length, day_of_week,
 month_m, year_y)

(Select ride_id, rideable_type, member_casual, ride_length, day_of_week, month_m, year_y
From [dbo].[all_data_202004_202104]
Where member_casual = 'casual')

Select *
From #casual_table

Select *
From #member_table


-- Calculating User Traffic Every Month Since Startup


Select month_int AS Month_Num,
month_m AS Month_Name, 
year_y AS Year_Y,
Count(case when member_casual = 'member' then 1 else NULL END) AS num_of_member,
Count(case when member_casual = 'casual' then 1 else NULL END) AS num_of_casual,
Count(member_casual) AS total_num_of_users
From [dbo].[all_data_202004_202104]
Group BY year_y, month_int, month_m
ORDER BY year_y, month_int, month_m
```

Result:
|Month_Num|	Month_Name|	Year_y|	num_of_members|	num_of_casuals|	total_num_of_users|
|----|----|----|----|----|-----|
|1	|January |	2023|127738|	32962	|160700|
|2	|February|2023|125834	|36179	|162013|
|3	|March	|2023|166149	|52022	|218171|
|4	|April	|2023|232663	|123093	|355756|
|5	|May	|2023|311327	|196244	|507571|
|6	|June	|2023|347312	|247628	|594940|
|7	|July	|2023|361789	|273646	|635435|
|8	|August|	2023|	384918|	259089	|644007|
|9	|September|	2023|	339834|	218384	|558218|
|10	|October|	2023|	301170|	146068	|447238|
|11	|November|	2023|	222636|	81334	|303970|
|12	|December|	2023|	144199|	41957|186156|

```sql
-- Calculating Daily Traffic Since Startup 


Select 
Count(case when member_casual = 'member' then 1 else NULL END) AS num_of_members,
Count(case when member_casual = 'casual' then 1 else NULL END) AS num_of_casual,
Count(*) AS num_of_users,
date_yyyy_mm_dd AS date_d
From [dbo].[all_data_202004_202104]
Group BY date_yyyy_mm_dd
ORDER BY date_yyyy_mm_dd
```

```sql
-- Calculating User Traffic Hour Wise


Alter Table [dbo].[all_data_202004_202104]
ADD hour_of_day int

UPDATE [dbo].[all_data_202004_202104]
SET hour_of_day = DATEPART(hour, started_at)

Select
hour_of_day AS Hour_of_day,
Count(case when member_casual = 'member' then 1 else NULL END) AS num_of_members,
Count(case when member_casual = 'casual' then 1 else NULL END) AS num_of_casual,
Count(*) AS num_of_users
From [dbo].[all_data_202004_202104]
Group By Hour_Of_Day
Order By Hour_Of_Day
```
Result:
|Hour_of_day|	num_of_members|	num_of_casuals|	num_of_users|
|---|---|---|---|
|0|	27365|	29040|	56405|
|1|	15961|	18631	|34592|
|2|	9053|	10984	|20037|
|3|	5819|	5753	|11572|
|4|	6803|	4360	|11163|
|5|	28780|	9298	|38078|
|6|	90387|	24982	|115369|
|7|	168271|	43517	|211788|
|8|	210830|	59388	|270218|
|9|	139825|	59179	|199004|
|10|	125053|	74141	|199194|
|11|147384|	94106	|241490|
|12|166880|	111144	|278024|
|13|	165594|	115001	|280595|
|14|	167027|	119137	|286164|
|15|	206168|	132126	|338294|
|16|	280400|	152869	|433269|
|17|	331313|	168442	|499755|
|18|	260008|	143629	|403637|
|19|	180854|	104014	|284868|
|20|	123863|	74171	|198034|
|21|	94527|	61092	|155619|
|22|	69303|	54080	|123383|
|23|	44101|	39522	|83623|

```sql
--Calculating Most Popular Stations for Casual Users, (limiting results to top 20 station)


Select
TOP 20 start_station_name AS Station_name,
Count(case when member_casual = 'casual' then 1 else NULL END) AS num_of_casual
From [dbo].[all_data_202004_202104]
Group By start_station_name
Order By num_of_casual DESC
```
Result:
|station_name	|num_of_casuals|
|2112 W Peterson Ave|	455|
|410	|5|
|63rd St Beach	|354|
|900 W Harrison St	|9368|
|Aberdeen St & Jackson Blvd	|10989|
|Aberdeen St & Monroe St	|7172|
|Aberdeen St & Randolph St	|7598|
|Ada St & 113th St	|8|
|Ada St & Washington Blvd	|4747|
|Adler Planetarium	|4929|
|Albany Ave & 16th St	|31|
|Albany Ave & 26th St	|98|
|Albany Ave & Belmont Ave	|410|
|Albany Ave & Bloomingdale Ave	|3423|
|Albany Ave & Douglas Blvd	|21|
|Albany Ave & Montrose Ave	|1380|
|Altgeld Gardens	|4|
|Archer (Damen) Ave & 37th St|	399|
|Archer Ave & 43rd St	|17|
|Artesian Ave & 55th St	|65|

----
## Visualizing Data
In this phase, we will be visualizing the data analyzed and tables created using Tableau Public.

### Average Ride Duration:

From inferring to the figure shown below. We can conclude that casual members on average tend to ride bikes for a longer duration of time than annual members.
![Alt text](image link)

