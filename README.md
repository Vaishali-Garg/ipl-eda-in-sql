# Exploratory Analysis of IPL Data Using SQL

#### Table of Contents
  * [Setup DB](#setup-db)
  * [Shape of Data](#shape-of-data)

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
