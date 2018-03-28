# Exploratory Analysis of IPL Data Using SQL

#### Table of Contents
  * [Setup DB](#setup-db)
  * [Shape of Data](#shape-of-data)
  * [View first 5 rows](#view-first-5-rows)

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
psql> \dt

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

## View first 5 rows

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

  
  
