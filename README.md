# Exploratory Analysis of IPL Data Using SQL

#### Table of Contents
  * [Setup DB](#setup-db)
  * [Shape of Data](#shape-of-data)
  * [Viewing date](#viewing-data)
  * [Distinct values](#distinct-values)
  * [Order by](#order-by)
  * [Case](#case)
  * [Data Aggregation](#data-aggregation)
  * [Pattern Match](#pattern-match)
  * [Group By](#group-by)
  * [Window Functions](#window-functions)
  * [Join](#join)

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

#### View first 5 rows

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

#### View selected columns

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

#### How many players have won player of the match award at least once

```
ipl=# SELECT COUNT(DISTINCT(player_of_the_match)) FROM matches;

 count 
-------
   187
```

#### Find season winner for each season (season winner is the winner of the last match of each season)
```
select distinct on (season) * from matches order by season, date desc;

 id  | season |     city     |    date    |        team1        |            team2            |         toss_winner         | toss_decision | result | dl_applied |        winner         | win_by_runs | win_by_wickets | player_of_the_match |              venue              |     umpire1     |    umpire2     | umpire3 
-----+--------+--------------+------------+---------------------+-----------------------------+-----------------------------+---------------+--------+------------+-----------------------+-------------+----------------+---------------------+---------------------------------+-----------------+----------------+---------
  58 |   2008 | Mumbai       | 2008-06-01 | Chennai Super Kings | Rajasthan Royals            | Rajasthan Royals            | field         | normal |          0 | Rajasthan Royals      |           0 |              3 | YK Pathan           | Dr DY Patil Sports Academy      | BF Bowden       | RE Koertzen    |        
 115 |   2009 | Johannesburg | 2009-05-24 | Deccan Chargers     | Royal Challengers Bangalore | Royal Challengers Bangalore | field         | normal |          0 | Deccan Chargers       |           6 |              0 | A Kumble            | New Wanderers Stadium           | RE Koertzen     | SJA Taufel     |        
 175 |   2010 | Mumbai       | 2010-04-25 | Chennai Super Kings | Mumbai Indians              | Chennai Super Kings         | bat           | normal |          0 | Chennai Super Kings   |          22 |              0 | SK Raina            | Dr DY Patil Sports Academy      | RE Koertzen     | SJA Taufel     |        
 248 |   2011 | Chennai      | 2011-05-28 | Chennai Super Kings | Royal Challengers Bangalore | Chennai Super Kings         | bat           | normal |          0 | Chennai Super Kings   |          58 |              0 | M Vijay             | MA Chidambaram Stadium, Chepauk | Asad Rauf       | SJA Taufel     |        
 322 |   2012 | Chennai      | 2012-05-27 | Chennai Super Kings | Kolkata Knight Riders       | Chennai Super Kings         | bat           | normal |          0 | Kolkata Knight Riders |           0 |              5 | MS Bisla            | MA Chidambaram Stadium, Chepauk | BF Bowden       | SJA Taufel     |        
 398 |   2013 | Kolkata      | 2013-05-26 | Mumbai Indians      | Chennai Super Kings         | Mumbai Indians              | bat           | normal |          0 | Mumbai Indians        |          23 |              0 | KA Pollard          | Eden Gardens                    | HDPK Dharmasena | SJA Taufel     |        
 458 |   2014 | Bangalore    | 2014-06-01 | Kings XI Punjab     | Kolkata Knight Riders       | Kolkata Knight Riders       | field         | normal |          0 | Kolkata Knight Riders |           0 |              3 | MK Pandey           | M Chinnaswamy Stadium           | HDPK Dharmasena | BNJ Oxenford   |        
 517 |   2015 | Kolkata      | 2015-05-24 | Mumbai Indians      | Chennai Super Kings         | Chennai Super Kings         | field         | normal |          0 | Mumbai Indians        |          41 |              0 | RG Sharma           | Eden Gardens                    | HDPK Dharmasena | RK Illingworth |        
 577 |   2016 | Bangalore    | 2016-05-29 | Sunrisers Hyderabad | Royal Challengers Bangalore | Sunrisers Hyderabad         | bat           | normal |          0 | Sunrisers Hyderabad   |           8 |              0 | BCJ Cutting         | M Chinnaswamy Stadium           | HDPK Dharmasena | BNJ Oxenford   |        
```

## Order by

#### Get details of top 5 matches which were won by maximum number of runs

```
ipl=# SELECT * FROM matches                                                                                                                               ipl=# ORDER BY win_by_runs DESC LIMIT 5;

 id  | season |    city    |    date    |            team1            |            team2            |         toss_winner         | toss_decision | result | dl_applied |           winner            | win_by_runs | win_by_wickets | player_of_the_match |                    venue                     |    umpire1     |    umpire2    | umpire3 
-----+--------+------------+------------+-----------------------------+-----------------------------+-----------------------------+---------------+--------+------------+-----------------------------+-------------+----------------+---------------------+----------------------------------------------+----------------+---------------+---------
 561 |   2016 | Bangalore  | 2016-05-14 | Royal Challengers Bangalore | Gujarat Lions               | Gujarat Lions               | field         | normal |          0 | Royal Challengers Bangalore |         144 |              0 | AB de Villiers      | M Chinnaswamy Stadium                        | AY Dandekar    | VK Sharma     |        
   1 |   2008 | Bangalore  | 2008-04-18 | Kolkata Knight Riders       | Royal Challengers Bangalore | Royal Challengers Bangalore | field         | normal |          0 | Kolkata Knight Riders       |         140 |              0 | BB McCullum         | M Chinnaswamy Stadium                        | Asad Rauf      | RE Koertzen   |        
 498 |   2015 | Bangalore  | 2015-05-06 | Royal Challengers Bangalore | Kings XI Punjab             | Kings XI Punjab             | field         | normal |          0 | Royal Challengers Bangalore |         138 |              0 | CH Gayle            | M Chinnaswamy Stadium                        | RK Illingworth | VA Kulkarni   |        
 352 |   2013 | Bangalore  | 2013-04-23 | Royal Challengers Bangalore | Pune Warriors               | Pune Warriors               | field         | normal |          0 | Royal Challengers Bangalore |         130 |              0 | CH Gayle            | M Chinnaswamy Stadium                        | Aleem Dar      | C Shamshuddin |        
 237 |   2011 | Dharamsala | 2011-05-17 | Kings XI Punjab             | Royal Challengers Bangalore | Kings XI Punjab             | bat           | normal |          0 | Kings XI Punjab             |         111 |              0 | AC Gilchrist        | Himachal Pradesh Cricket Association Stadium | Asad Rauf      | AM Saheba     |        
```

#### Order the rows by city in which the match was played

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
#### Find venue of 10 most recently played matches

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

## Case
#### Return a column with comment based on total_runs
```
ipl=# select batsman, total_runs, 
    CASE WHEN total_runs = 4 THEN 'Four' 
         WHEN total_runs = 6 THEN 'Six' 
         WHEN total_runs = 1 THEN 'single' 
         WHEN total_runs = 0 THEN 'duck' 
     END as howzthat 
   FROM deliveries;

      batsman        | total_runs | howzthat 
----------------------+------------+----------
 SC Ganguly           |          1 | single
 BB McCullum          |          0 | duck
 BB McCullum          |          1 | single
 BB McCullum          |          0 | duck
 BB McCullum          |          0 | duck
 BB McCullum          |          0 | duck
 BB McCullum          |          1 | single
 BB McCullum          |          0 | duck
 BB McCullum          |          4 | Four
 BB McCullum          |          4 | Four
 BB McCullum          |          6 | Six
 BB McCullum          |          4 | Four
 BB McCullum          |          0 | duck
 SC Ganguly           |          0 | duck
 ```
## Data Aggregation

#### What is the highest runs by which any team won a match

```
ipl=# SELECT MAX(win_by_runs) FROM matches;

 max 
-----
 144
```

#### How many extra runs have been conceded in ipl

```
ipl=# SELECT SUM(extra_runs) FROM deliveries;

 sum  
------
 9519
 ```

#### On an average, teams won by how many runs in ipl

```
ipl=# SELECT ROUND(AVG(win_by_runs), 2) FROM matches;

 round 
-------
 13.72
 ```
 
## Filters

#### How many extra runs were conceded in ipl by SK Warne

```
ipl=# SELECT SUM(extra_runs) 
ipl-# FROM deliveries 
ipl-# WHERE bowler = 'SK Warne';

 sum 
-----
  57
```

#### How many boundaries (4s or 6s) have been hit in ipl
```
ipl=# SELECT COUNT(total_runs) 
ipl-# FROM deliveries 
ipl-# WHERE total_runs = 4 OR total_runs = 6;

 count 
-------
 21409
 ```
 
 #### How many balls did SK Warne bowl to batsman SR Tendulkar
 ```
 ipl=# SELECT COUNT(*) 
ipl-# FROM deliveries 
ipl-# WHERE batsman = 'SR Tendulkar' AND bowler = 'SK Warne';

 count 
-------
    39
 ```
 
 #### How many matches were played in the month of April
 ```
 ipl=# SELECT COUNT(*) FROM matches WHERE EXTRACT(month FROM date) = 4;
 
 count 
-------
   261
   
```

#### How many matches were played in the March and June
```
ipl=# SELECT COUNT(*) 
ipl-# FROM matches 
ipl-# WHERE EXTRACT(month FROM date) = 3 or EXTRACT(month FROM date) = 6;

 count 
-------
    31
 ```
 
#### Total number of wickets taken in ipl (count not null values)
```
ipl=# SELECT COUNT(player_dismissed) AS total_wickets 
ipl-# FROM deliveries 
ipl-# WHERE player_dismissed IS NOT NULL;

 total_wickets 
---------------
          6727
 ```
 
## Pattern Match
 
#### How many batsmen have names starting with S

```
ipl=# SELECT COUNT(DISTINCT(batsman)) 
ipl-# FROM deliveries 
ipl-# WHERE batsman LIKE 'S%';

 count 
-------
    69
```
#### How many teams have word royal in it (could be anywhere in the team name, any case)

```
ipl=# SELECT COUNT(DISTINCT(team1))                                                                                                                       FROM matches                                                                                                                                              WHERE LOWER(team1) LIKE '%royal%';

 count 
-------
     2
 ```

## Group by

#### Maximum runs by which any team won a match per season
```
ipl=# SELECT season, MAX(win_by_runs) 
ipl-# FROM matches 
ipl-# GROUP BY season 
ipl-# ORDER BY season;

 season | max 
--------+-----
   2008 | 140
   2009 |  92
   2010 |  98
   2011 | 111
   2012 |  86
   2013 | 130
   2014 |  93
   2015 | 138
   2016 | 144
   
 ```
#### Create score card for each match Id

```
ipl=# SELECT matchid, batting_team, batsman, SUM(batsman_runs) 
ipl-# FROM deliveries 
ipl-# GROUP BY matchid, batting_team, batsman 
ipl-# ORDER BY matchid, batting_team;

 matchid |        batting_team         |       batsman        | sum 
---------+-----------------------------+----------------------+-----
       1 | Kolkata Knight Riders       | DJ Hussey            |  12
       1 | Kolkata Knight Riders       | SC Ganguly           |  10
       1 | Kolkata Knight Riders       | BB McCullum          | 158
       1 | Kolkata Knight Riders       | RT Ponting           |  20
       1 | Kolkata Knight Riders       | Mohammad Hafeez      |   5
       1 | Royal Challengers Bangalore | Z Khan               |   3
       1 | Royal Challengers Bangalore | W Jaffer             |   6
       1 | Royal Challengers Bangalore | JH Kallis            |   8
       1 | Royal Challengers Bangalore | AA Noffke            |   9
       1 | Royal Challengers Bangalore | V Kohli              |   1
       1 | Royal Challengers Bangalore | MV Boucher           |   7
       1 | Royal Challengers Bangalore | R Dravid             |   2
       1 | Royal Challengers Bangalore | CL White             |   6
       1 | Royal Challengers Bangalore | B Akhil              |   0
       1 | Royal Challengers Bangalore | P Kumar              |  18
       1 | Royal Challengers Bangalore | SB Joshi             |   3

```

#### Total boundaries hit in ipl by each team

```
ipl=# SELECT batting_team, COUNT(total_runs) as No_of_boundaries 
ipl-# FROM deliveries 
ipl-# WHERE total_runs = 4 OR total_runs = 6 
ipl-# GROUP BY batting_team 
ipl-# ORDER BY no_of_boundaries desc;

        batting_team         | no_of_boundaries 
-----------------------------+------------------
 Royal Challengers Bangalore |             2721
 Mumbai Indians              |             2696
 Kings XI Punjab             |             2604
 Chennai Super Kings         |             2532
 Delhi Daredevils            |             2384
 Kolkata Knight Riders       |             2353
 Rajasthan Royals            |             2186
 Deccan Chargers             |             1363
 Sunrisers Hyderabad         |             1079
 Pune Warriors               |              721
 Gujarat Lions               |              306
 Rising Pune Supergiants     |              241
 Kochi Tuskers Kerala        |              223
```

#### Top 10 players with max boundaries (4 or 6)
```
ipl=# SELECT batsman, COUNT(batsman_runs)                                                                                                                 FROM deliveries                                                                                                                                           WHERE batsman_runs = 6 OR batsman_runs = 4                                                                                                                GROUP BY batsman                                                                                                                                          ORDER BY COUNT(batsman_runs) desc LIMIT 10;

    batsman     | count 
----------------+-------
 CH Gayle       |   534
 SK Raina       |   521
 V Kohli        |   510
 RG Sharma      |   487
 G Gambhir      |   473
 DA Warner      |   472
 RV Uthappa     |   445
 V Sehwag       |   440
 AB de Villiers |   417
 S Dhawan       |   410
 ```
 
 #### Top 20 bowlers who conceded highest extra runs
 
 ```
 ipl=# SELECT bowler, SUM(extra_runs) as total_extra_runs 
ipl-# FROM deliveries 
ipl-# GROUP BY bowler 
ipl-# ORDER BY total_extra_runs DESC LIMIT 20;

     bowler      | total_extra_runs 
-----------------+------------------
 SL Malinga      |              239
 P Kumar         |              222
 RP Singh        |              181
 DW Steyn        |              166
 SR Watson       |              157
 DJ Bravo        |              157
 Z Khan          |              154
 Harbhajan Singh |              154
 JA Morkel       |              153
 I Sharma        |              149
 UT Yadav        |              148
 R Ashwin        |              143
 IK Pathan       |              142
 B Kumar         |              140
 PP Ojha         |              138
 M Morkel        |              131
 L Balaji        |              131
 JH Kallis       |              126
 M Muralitharan  |              123
 DS Kulkarni     |              122
```

#### Top 10 wicket takers
```
ipl=# SELECT bowler, COUNT(player_dismissed) as total_wickets                                                                                             FROM deliveries                                                                                                                                           WHERE player_dismissed IS NOT NULL                                                                                                                        GROUP BY bowler                                                                                                                                           ORDER BY total_wickets desc LIMIT 10;

     bowler      | total_wickets 
-----------------+---------------
 SL Malinga      |           159
 DJ Bravo        |           137
 A Mishra        |           132
 Harbhajan Singh |           128
 PP Chawla       |           127
 R Vinay Kumar   |           123
 A Nehra         |           111
 R Ashwin        |           110
 Z Khan          |           107
 RP Singh        |           100
 ```

#### Name and number of wickets by bowlers who have taken more than or equal to 100 wickets in ipl

```
ipl=# SELECT bowler, COUNT(player_dismissed) AS total_wickets 
ipl-# FROM deliveries 
ipl-# WHERE player_dismissed IS NOT NULL GROUP BY bowler 
ipl-# HAVING COUNT(player_dismissed) >=100 
ipl-# ORDER BY total_wickets desc;

     bowler      | total_wickets 
-----------------+---------------
 SL Malinga      |           159
 DJ Bravo        |           137
 A Mishra        |           132
 Harbhajan Singh |           128
 PP Chawla       |           127
 R Vinay Kumar   |           123
 A Nehra         |           111
 R Ashwin        |           110
 Z Khan          |           107
 RP Singh        |           100
 DW Steyn        |           100
 ```

## Window Functions

#### Top 2 player_of_the_match for each season
```
ipl=# SELECT season, player_of_the_match, count 
ipl-# FROM (SELECT row_number() OVER (partition by season) AS rn, * 
ipl(#     FROM (SELECT season, player_of_the_match,COUNT(player_of_the_match) 
ipl(#         FROM matches GROUP BY season, player_of_the_match 
ipl(#         ORDER BY season ASC, count DESC)
ipl(#     sub) 
ipl-# sub1 
ipl-# WHERE rn < 3;

 season | player_of_the_match | count 
--------+---------------------+-------
   2008 | SE Marsh            |     5
   2008 | YK Pathan           |     4
   2009 | YK Pathan           |     3
   2009 | RG Sharma           |     2
   2010 | SR Tendulkar        |     4
   2010 | JH Kallis           |     3
   2011 | CH Gayle            |     6
   2011 | MEK Hussey          |     3
   2012 | CH Gayle            |     5
   2012 | AB de Villiers      |     3
   2013 | MEK Hussey          |     5
   2013 | A Mishra            |     4
   2014 | GJ Maxwell          |     4
   2014 | RA Jadeja           |     3
   2015 | DA Warner           |     4
   2015 | A Nehra             |     3
   2016 | V Kohli             |     5
   2016 | RG Sharma           |     4
```

## Join

#### Combine column date from matches with table deliveries to get data by year

```
ipl=# select d.*, m.id, m.date from deliveries d join matches m on m.id = d.matchid limit 2;

 matchid | inning |     batting_team      |        bowling_team         | over | ball |   batsman   | non_striker | bowler  | is_super_over | wide_runs | bye_runs | legbye_runs | noball_runs | penalty_runs | batsman_runs | extra_runs | total_runs | player_dismissed | dismissal_kind | fielder | id |    date    
---------+--------+-----------------------+-----------------------------+------+------+-------------+-------------+---------+---------------+-----------+----------+-------------+-------------+--------------+--------------+------------+------------+------------------+----------------+---------+----+------------
       1 |      1 | Kolkata Knight Riders | Royal Challengers Bangalore |    1 |    1 | SC Ganguly  | BB McCullum | P Kumar |             0 |         0 |        0 |           1 |           0 |            0 |            0 |          1 |          1 |                  |                |         |  1 | 2008-04-18
       1 |      1 | Kolkata Knight Riders | Royal Challengers Bangalore |    1 |    2 | BB McCullum | SC Ganguly  | P Kumar |             0 |         0 |        0 |           0 |           0 |            0 |            0 |          0 |          0 |                  |                |         |  1 | 2008-04-18
```
