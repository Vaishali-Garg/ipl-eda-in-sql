# Exploratory Analysis of IPL Data Using SQL

#### Table of Contents
  * [Setup DB](#setup-db)
  * [Shape of Data](#shape-of-data)
  * [Viewing date](#viewing-data)
  * [Distinct values](#distinct-values)
  * [Order by](#order-by)
  * [Aggregation](#aggregation)
  * [Pattern Match](#pattern-match)
  

Indian Premier League (IPL) is the cricket league consisting of various Indian cities and has a brand value of around $5.3Bn. It started in 2008 and attracts large viewership.

The IPL data is available on [Kaggle](https://www.kaggle.com/manasgarg/ipl/data) as csv files. We create tables `matches` and `deliveries` using the data.

## Setup DB

Create database and switch to the same:

```sql
CREATE DATABASE ipl;
\c ipl
```

Create tables:

```sql
CREATE TABLE matches (id integer,
  season integer,
  city varchar,
  date date,
  team1 varchar, 
  team2 varchar, 
  toss_winner varchar, 
  toss_decision varchar, 
  result varchar, 
  dl_applied integer, 
  winner varchar, 
  win_by_runs integer, 
  win_by_wickets integer, 
  player_of_the_match varchar, 
  venue varchar, 
  umpire1 varchar, 
  umpire2 varchar, 
  umpire3 integer
);

CREATE TABLE deliveries (
  matchid integer, 
  inning integer, 
  batting_team varchar, 
  bowling_team varchar, 
  over integer, 
  ball integer, 
  batsman varchar, 
  non_striker varchar, 
  bowler varchar, 
  is_super_over integer, 
  wide_runs integer, 
  bye_runs integer, 
  legbye_runs integer, 
  noball_runs integer, 
  penalty_runs integer, 
  batsman_runs integer, 
  extra_runs integer, 
  total_runs integer, 
  player_dismissed varchar, 
  dismissal_kind varchar, 
  fielder varchar
);
```

Copy data from csv files:

```sql
copy matches from '<filepath>/matches.csv' with (format 'csv', header true);
copy deliveries from '<filepath>/deliveries.csv' with (format 'csv', header true);
```

See if the tables are created:

```
ipl=# \dt
           List of relations
 Schema |    Name    | Type  |  Owner   
--------+------------+-------+----------
 public | deliveries | table | vaishali
 public | matches    | table | vaishali
```

## Shape of Data

```
ipl=# SELECT COUNT(*) as no_of_rows FROM matches;
 no_of_rows 
------------
        577
  
 ipl=# SELECT COUNT(*) as no_of_columns FROM information_schema.columns WHERE table_name = 'matches';
 no_of_columns 
---------------
            18
```

## Viewing data

### View first 5 rows

```
ipl=# SELECT * FROM matches LIMIT 5;
 id | season |    city    |    date    |         team1         |            team2            |         toss_winner         | toss_decision | result | dl_applied |           winner            | win_by_runs | win_by_wickets | player_of_the_match |                   venue                    |  umpire1  |    umpire2     | umpire3 
----+--------+------------+------------+-----------------------+-----------------------------+-----------------------------+---------------+--------+------------+-----------------------------+-------------+----------------+---------------------+--------------------------------------------+-----------+----------------+---------
  1 |   2008 | Bangalore  | 2008-04-18 | Kolkata Knight Riders | Royal Challengers Bangalore | Royal Challengers Bangalore | field         | normal |          0 | Kolkata Knight Riders       |         140 |              0 | BB McCullum         | M Chinnaswamy Stadium                      | Asad Rauf | RE Koertzen    |        
  2 |   2008 | Chandigarh | 2008-04-19 | Chennai Super Kings   | Kings XI Punjab             | Chennai Super Kings         | bat           | normal |          0 | Chennai Super Kings         |          33 |              0 | MEK Hussey          | Punjab Cricket Association Stadium, Mohali | MR Benson | SL Shastri     |        
  3 |   2008 | Delhi      | 2008-04-19 | Rajasthan Royals      | Delhi Daredevils            | Rajasthan Royals            | bat           | normal |          0 | Delhi Daredevils            |           0 |              9 | MF Maharoof         | Feroz Shah Kotla                           | Aleem Dar | GA Pratapkumar |        
  4 |   2008 | Mumbai     | 2008-04-20 | Mumbai Indians        | Royal Challengers Bangalore | Mumbai Indians              | bat           | normal |          0 | Royal Challengers Bangalore |           0 |              5 | MV Boucher          | Wankhede Stadium                           | SJ Davis  | DJ Harper      |        
  5 |   2008 | Kolkata    | 2008-04-20 | Deccan Chargers       | Kolkata Knight Riders       | Deccan Chargers             | bat           | normal |          0 | Kolkata Knight Riders       |           0 |              5 | DJ Hussey           | Eden Gardens                               | BF Bowden | K Hariharan    |   
  ```
  
  ```
  ipl=# SELECT * FROM deliveries LIMIT 5;
 matchid | inning |     batting_team      |        bowling_team         | over | ball |   batsman   | non_striker | bowler  | is_super_over | wide_runs | bye_runs | legbye_runs | noball_runs | penalty_runs | batsman_runs | extra_runs | total_runs | player_dismissed | dismissal_kind | fielder 
---------+--------+-----------------------+-----------------------------+------+------+-------------+-------------+---------+---------------+-----------+----------+-------------+-------------+--------------+--------------+------------+------------+------------------+----------------+---------
       1 |      1 | Kolkata Knight Riders | Royal Challengers Bangalore |    1 |    1 | SC Ganguly  | BB McCullum | P Kumar |             0 |         0 |        0 |           1 |           0 |            0 |            0 |          1 |          1 |                  |                | 
       1 |      1 | Kolkata Knight Riders | Royal Challengers Bangalore |    1 |    2 | BB McCullum | SC Ganguly  | P Kumar |             0 |         0 |        0 |           0 |           0 |            0 |            0 |          0 |          0 |                  |                | 
       1 |      1 | Kolkata Knight Riders | Royal Challengers Bangalore |    1 |    3 | BB McCullum | SC Ganguly  | P Kumar |             0 |         1 |        0 |           0 |           0 |            0 |            0 |          1 |          1 |                  |                | 
       1 |      1 | Kolkata Knight Riders | Royal Challengers Bangalore |    1 |    4 | BB McCullum | SC Ganguly  | P Kumar |             0 |         0 |        0 |           0 |           0 |            0 |            0 |          0 |          0 |                  |                | 
       1 |      1 | Kolkata Knight Riders | Royal Challengers Bangalore |    1 |    5 | BB McCullum | SC Ganguly  | P Kumar |             0 |         0 |        0 |           0 |           0 |            0 |            0 |          0 |          0 |                  |                | 
```

### View selected columns

```
ipl=# SELECT date, team1, team2, winner FROM matches limit 5;
    date    |         team1         |            team2            |           winner            
------------+-----------------------+-----------------------------+-----------------------------
 2008-04-18 | Kolkata Knight Riders | Royal Challengers Bangalore | Kolkata Knight Riders
 2008-04-19 | Chennai Super Kings   | Kings XI Punjab             | Chennai Super Kings
 2008-04-19 | Rajasthan Royals      | Delhi Daredevils            | Delhi Daredevils
 2008-04-20 | Mumbai Indians        | Royal Challengers Bangalore | Royal Challengers Bangalore
 2008-04-20 | Deccan Chargers       | Kolkata Knight Riders       | Kolkata Knight Riders
 ```
 
## Distinct values

### How many players have won player of the match award at least once

```
ipl=# SELECT COUNT(DISTINCT(player_of_the_match)) FROM matches;
 count 
-------
   187
```

## Order by

### Get details of top 5 matches which were won by maximum number of runs

```
ipl=# SELECT * FROM matches                                                                                                                               ORDER BY win_by_runs DESC LIMIT 5;
 id  | season |    city    |    date    |            team1            |            team2            |         toss_winner         | toss_decision | result | dl_applied |           winner            | win_by_runs | win_by_wickets | player_of_the_match |                    venue                     |    umpire1     |    umpire2    | umpire3 
-----+--------+------------+------------+-----------------------------+-----------------------------+-----------------------------+---------------+--------+------------+-----------------------------+-------------+----------------+---------------------+----------------------------------------------+----------------+---------------+---------
 561 |   2016 | Bangalore  | 2016-05-14 | Royal Challengers Bangalore | Gujarat Lions               | Gujarat Lions               | field         | normal |          0 | Royal Challengers Bangalore |         144 |              0 | AB de Villiers      | M Chinnaswamy Stadium                        | AY Dandekar    | VK Sharma     |        
   1 |   2008 | Bangalore  | 2008-04-18 | Kolkata Knight Riders       | Royal Challengers Bangalore | Royal Challengers Bangalore | field         | normal |          0 | Kolkata Knight Riders       |         140 |              0 | BB McCullum         | M Chinnaswamy Stadium                        | Asad Rauf      | RE Koertzen   |        
 498 |   2015 | Bangalore  | 2015-05-06 | Royal Challengers Bangalore | Kings XI Punjab             | Kings XI Punjab             | field         | normal |          0 | Royal Challengers Bangalore |         138 |              0 | CH Gayle            | M Chinnaswamy Stadium                        | RK Illingworth | VA Kulkarni   |        
 352 |   2013 | Bangalore  | 2013-04-23 | Royal Challengers Bangalore | Pune Warriors               | Pune Warriors               | field         | normal |          0 | Royal Challengers Bangalore |         130 |              0 | CH Gayle            | M Chinnaswamy Stadium                        | Aleem Dar      | C Shamshuddin |        
 237 |   2011 | Dharamsala | 2011-05-17 | Kings XI Punjab             | Royal Challengers Bangalore | Kings XI Punjab             | bat           | normal |          0 | Kings XI Punjab             |         111 |              0 | AC Gilchrist        | Himachal Pradesh Cricket Association Stadium | Asad Rauf      | AM Saheba     |        
```

### Order the rows by city in which the match was played

```
ipl=# SELECT * FROM matches ORDER BY city LIMIT 10;
 id  | season |   city    |    date    |            team1            |         team2         |      toss_winner      | toss_decision | result | dl_applied |        winner         | win_by_runs | win_by_wickets | player_of_the_match |            venue             |     umpire1     |    umpire2     | umpire3 
-----+--------+-----------+------------+-----------------------------+-----------------------+-----------------------+---------------+--------+------------+-----------------------+-------------+----------------+---------------------+------------------------------+-----------------+----------------+---------
 413 |   2014 | Abu Dhabi | 2014-04-26 | Kings XI Punjab             | Kolkata Knight Riders | Kolkata Knight Riders | field         | normal |          0 | Kings XI Punjab       |          23 |              0 | Sandeep Sharma      | Sheikh Zayed Stadium         | HDPK Dharmasena | RK Illingworth |        
 412 |   2014 | Abu Dhabi | 2014-04-26 | Royal Challengers Bangalore | Rajasthan Royals      | Rajasthan Royals      | field         | normal |          0 | Rajasthan Royals      |           0 |              6 | PV Tambe            | Sheikh Zayed Stadium         | HDPK Dharmasena | C Shamshuddin  |        
 417 |   2014 | Abu Dhabi | 2014-04-29 | Rajasthan Royals            | Kolkata Knight Riders | Rajasthan Royals      | bat           | tie    |          0 | Rajasthan Royals      |           0 |              0 | JP Faulkner         | Sheikh Zayed Stadium         | Aleem Dar       | AK Chaudhary   |        
 399 |   2014 | Abu Dhabi | 2014-04-16 | Kolkata Knight Riders       | Mumbai Indians        | Kolkata Knight Riders | bat           | normal |          0 | Kolkata Knight Riders |          41 |              0 | JH Kallis           | Sheikh Zayed Stadium         | M Erasmus       | RK Illingworth |        
 402 |   2014 | Abu Dhabi | 2014-04-18 | Sunrisers Hyderabad         | Rajasthan Royals      | Rajasthan Royals      | field         | normal |          0 | Rajasthan Royals      |           0 |              4 | AM Rahane           | Sheikh Zayed Stadium         | BF Bowden       | RK Illingworth |        
 406 |   2014 | Abu Dhabi | 2014-04-21 | Chennai Super Kings         | Delhi Daredevils      | Chennai Super Kings   | bat           | normal |          0 | Chennai Super Kings   |          93 |              0 | SK Raina            | Sheikh Zayed Stadium         | RK Illingworth  | C Shamshuddin  |        
 401 |   2014 | Abu Dhabi | 2014-04-18 | Chennai Super Kings         | Kings XI Punjab       | Chennai Super Kings   | bat           | normal |          0 | Kings XI Punjab       |           0 |              6 | GJ Maxwell          | Sheikh Zayed Stadium         | RK Illingworth  | C Shamshuddin  |        
 139 |   2010 | Ahmedabad | 2010-03-28 | Rajasthan Royals            | Chennai Super Kings   | Rajasthan Royals      | bat           | normal |          0 | Rajasthan Royals      |          17 |              0 | NV Ojha             | Sardar Patel Stadium, Motera | SS Hazare       | SJA Taufel     |        
 128 |   2010 | Ahmedabad | 2010-03-20 | Rajasthan Royals            | Kolkata Knight Riders | Rajasthan Royals      | bat           | normal |          0 | Rajasthan Royals      |          34 |              0 | AA Jhunjhunwala     | Sardar Patel Stadium, Motera | RE Koertzen     | RB Tiffin      |        
 121 |   2010 | Ahmedabad | 2010-03-15 | Rajasthan Royals            | Delhi Daredevils      | Delhi Daredevils      | field         | normal |          0 | Delhi Daredevils      |           0 |              6 | V Sehwag            | Sardar Patel Stadium, Motera | BG Jerling      | RE Koertzen    |        
```
### Find venue of 10 most recently played matches

```
ipl=# SELECT venue 
ipl-# FROM matches 
ipl-# ORDER BY date DESC LIMIT 10;
                        venue                        
-----------------------------------------------------
 M Chinnaswamy Stadium
 Feroz Shah Kotla
 Feroz Shah Kotla
 M Chinnaswamy Stadium
 Eden Gardens
 Shaheed Veer Narayan Singh International Stadium
 Dr. Y.S. Rajasekhara Reddy ACA-VDCA Cricket Stadium
 Green Park
 Shaheed Veer Narayan Singh International Stadium
 Green Park
```
## Aggregation (where)

### What is the highest runs by which any team won a match

ipl=# SELECT MAX(win_by_runs) FROM matches;
 max 
-----
 144

### How many extra runs have been conceded in ipl

```
ipl=# SELECT SUM(extra_runs) FROM deliveries;
 sum  
------
 9519
 ```

### On an average, teams won by how many runs in ipl

```
ipl=# SELECT ROUND(AVG(win_by_runs), 2) FROM matches;
 round 
-------
 13.72
 ```
 
## Filters
### How many extra runs were conceded in ipl by SK Warne

```
ipl=# SELECT SUM(extra_runs) 
ipl-# FROM deliveries 
ipl-# WHERE bowler = 'SK Warne';
 sum 
-----
  57
```
### How many matches were played in Mumbai
```
ipl=# SELECT COUNT(*) 
ipl-# FROM matches 
ipl-# WHERE city = 'Mumbai';
 count 
-------
    77
```
### How many boundaries (4s or 6s) have been hit in ipl
```
ipl=# SELECT COUNT(total_runs) 
ipl-# FROM deliveries 
ipl-# WHERE total_runs = 4 OR total_runs = 6;
 count 
-------
 21409
 ```
 
 ### How many balls did SK Warne bowl to batsman SR Tendulkar
 ```
 ipl=# SELECT COUNT(*) 
ipl-# FROM deliveries 
ipl-# WHERE batsman = 'SR Tendulkar' AND bowler = 'SK Warne';
 count 
-------
    39
 ```
 
 ### How many matches were played in the month of April
 ```
 ipl=# SELECT COUNT(*) FROM matches WHERE EXTRACT(month FROM date) = 4;
 count 
-------
   261
   
```

### How many matches were played in the March and June
```
ipl=# SELECT COUNT(*) 
ipl-# FROM matches 
ipl-# WHERE EXTRACT(month FROM date) = 3 or EXTRACT(month FROM date) = 6;
 count 
-------
    31
 ```
 
### Total number of wickets taken in ipl (count not null values)
```
ipl=# SELECT COUNT(player_dismissed) AS total_wickets 
ipl-# FROM deliveries 
ipl-# WHERE player_dismissed IS NOT NULL;
 total_wickets 
---------------
          6727
 ```
 
## Pattern Match (like)
 
How many batsmen have names starting with S

```
ipl=# SELECT COUNT(DISTINCT(batsman)) 
ipl-# FROM deliveries 
ipl-# WHERE batsman LIKE 'S%';
 count 
-------
    69
```





  
