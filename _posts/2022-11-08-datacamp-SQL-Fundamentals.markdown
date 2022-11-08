---
layout: post
title:  SQL Fundamentals
date:   2022-11-08 00:00:55 +0800
image:  SQL101.jpg
tags:   SQL
categories: [SQL]
---

學習筆記...

***

# SKILL TRACK

1. [Introduction to SQL](#1)
2. [Intermediate SQL](#2)
3. [Joining Data in SQL](#3)
4. [Data Manipulation in SQL](#4)
5. [PostgreSQL Summary Stats and Window Functions](#5)
6. [Functions for Manipulating Data in PostgreSQL](#6)

<a name="1"/>
# 1. Introduction to SQL

## Relational Databases

*   SQL, short for Structured Query Language
*   The field names should be made singular
*   The table name should not be capitalized
*   Schemas: "blueprints" of databases
*   `VARCHAR` is a flexible and popular string data type in SQL

## Querying

{% highlight SQL %}
SELECT *
FROM company;
{% endhighlight %}

{% highlight SQL %}
SELECT DISTINCT year_hired
FROM employees;
{% endhighlight %}

{% highlight SQL %}
-- Save the results of this query as a view called library_authors
CREATE VIEW library_authors AS
SELECT DISTINCT author AS unique_author
FROM books;
{% endhighlight %}

**PostgreSQL**   
*   Free and open-source relational database system
*   Created at UCB
*   "PostgreSQL" refers to both the PostgreSQL database system and its associated SQL flavor

**SQL**
*   Has free and paid versions
*   Created by Microsoft
*   T-SQL is Microsoft's SQL flavor, used with SQL Server databases

{% highlight SQL %}
-- Select the first 10 genres from books using PostgreSQL
SELECT genre
FROM books
LIMIT 10;
{% endhighlight %}

<a name="2"/>
# 2. Intermediate SQL

## Selecting Data

{% highlight SQL %}
SELECT COUNT(DISTINCT(country)) AS count_distinct_countries
FROM films;
{% endhighlight %}

[Style guide](https://www.sqlstyle.guide/)

## Filtering Records

{% highlight SQL %}
SELECT title, release_year
FROM films
WHERE (release_year = 1990 OR release_year = 1999)
	AND (language = 'English' OR language = 'Spanish')
-- Filter films with more than $2,000,000 gross
	AND gross > 2000000;
{% endhighlight %}

{% highlight SQL %}
SELECT title, release_year
FROM films
WHERE release_year BETWEEN 1990 AND 2000
	AND budget > 100000000
-- Amend the query to include Spanish or French-language films
	AND (language = 'Spanish' OR language = 'French');
{% endhighlight %}

**Filtering text**

{% highlight SQL %}
-- Select the names that start with B
SELECT name
FROM people
WHERE name LIKE 'B%';
{% endhighlight %}

{% highlight SQL %}
SELECT name
FROM people
-- Select the names that have r as the second letter
WHERE name LIKE '_r%';
{% endhighlight %}

{% highlight SQL %}
SELECT name
FROM people
-- Select names that don't start with A
WHERE NAME NOT LIKE 'A%';
{% endhighlight %}

{% highlight SQL %}
-- Count the unique titles
SELECT COUNT(DISTINCT(title)) AS nineties_english_films_for_teens
FROM films
-- Filter to release_years to between 1990 and 1999
WHERE release_year BETWEEN 1990 AND 1999
-- Filter to English-language films
	AND language = 'English'
-- Narrow it down to G, PG, and PG-13 certifications
	AND certification IN ('G', 'PG', 'PG-13');
{% endhighlight %}

**`Null`**
{% highlight SQL %}
-- List all film titles with missing budgets
SELECT title AS no_budget_info
FROM films
WHERE budget IS NULL;
{% endhighlight %}

## Aggregate Functions

*`ROUND()` with a negative parameter*

{% highlight SQL %}
-- Calculate the average budget rounded to the thousands
SELECT ROUND(AVG(budget),-3) AS avg_budget_thousands
FROM films;
{% endhighlight %}

**What is the result if you divide a `discount` of two dollars by the `paid_price` of ten dollars to get the discount percentage?**
**--> 0**

*SQL thinks we want the answer to be an integer since we are dividing two integers. 0 is the closest integer to 0.2.*

**`COUNT` won't count `NULL`.**
{% highlight SQL %}
-- Calculate the percentage of people who are no longer alive
SELECT COUNT(deathdate) * 100.0 / COUNT(*) AS percentage_dead
FROM people;
{% endhighlight %}

{% highlight SQL %}
-- Round duration_hours to two decimal places
SELECT title, ROUND(duration / 60.0, 2) AS duration_hours
FROM films;
{% endhighlight %}

## Sorting and Grouping

**Sorting multiple fields**

{% highlight SQL %}
-- Select the certification, release year, and title sorted by certification and release year
SELECT certification, release_year, title
from films
ORDER BY certification ASC, release_year DESC;
{% endhighlight %}

{% highlight SQL %}
SELECT release_year, COUNT(DISTINCT(language)) AS language_num
FROM films
GROUP BY release_year
ORDER BY language_num DESC;
{% endhighlight %}

*Filtering grouped data: `HAVING`*

* `WHERE` filters individual records
* `HAVING` filters grouped records

{% highlight SQL %}
-- Select the country and average_budget from films
SELECT country, ROUND(AVG(budget), 2) AS average_budget
FROM films
-- Group by country
GROUP BY country
-- Filter to countries with an average_budget of more than one billion
HAVING ROUND(AVG(budget), 2) > 1000000000
-- Order by descending order of the aggregated budget
ORDER BY average_budget DESC;
{% endhighlight %}

{% highlight SQL %}
SELECT release_year, AVG(budget) AS avg_budget, AVG(gross) AS avg_gross
FROM films
WHERE release_year > 1990
GROUP BY release_year
HAVING AVG(budget) > 60000000
-- Order the results from highest to lowest average gross and limit to one
ORDER BY avg_gross DESC
LIMIT 1;
{% endhighlight %}


<a name="3"/>
# 3. Joining Data in SQL

## Introducing Inner Joins

*When writing joins, many SQL users prefer to write the SELECT statement after writing the join code, in case the SELECT statement requires using table aliases.*

{% highlight SQL %}
-- Select fields with aliases
SELECT c.code AS country_code, name, year, inflation_rate
FROM countries AS c
-- Join to economies (alias e)
INNER JOIN economies AS e
-- Match on code field using table aliases
ON c.code = e.code;
{% endhighlight %}

*Recall that when both the field names being joined on are the same, you can take advantage of the `USING` clause.*

{% highlight SQL %}
SELECT c.name AS country, l.name AS language, official
FROM countries AS c
INNER JOIN languages AS l
-- Match using the code column
USING(code);
{% endhighlight %}

{% highlight SQL %}
SELECT name, e.year, fertility_rate, unemployment_rate
FROM countries AS c
INNER JOIN populations AS p
ON c.code = p.country_code
INNER JOIN economies AS e
ON c.code = e.code
-- Add an additional joining condition such that you are also joining on year
	AND e.year = p.year;
{% endhighlight %}

## Outer Joins, Cross Joins and Self Joins

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

## Set Theory for SQL Joins

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

## Subqueries

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}


<a name="4"/>
# 4. Data Manipulation in SQL

## We'll take the CASE

CASE statements contain a `WHEN`, `THEN`, and `ELSE` statement, finished with `END`. 

{% highlight SQL %}
CASE WHEN x = 1 THEN 'a'
	 WHEN x = 2 THEN 'b'
	 ELSE 'c' END AS new_column
{% endhighlight %}

{% highlight SQL %}
-- Identify the home team as Bayern Munich, Schalke 04, or neither
SELECT 
	-- Select the date of the match
	date,
	-- Identify home wins, losses, or ties
	CASE WHEN home_goal > away_goal THEN 'Home win!'
    	 WHEN home_goal < away_goal THEN 'Home loss :(' 
         ELSE 'Tie' END AS outcome
FROM matches_spain;
{% endhighlight %}

{% highlight SQL %}
SELECT 
	m.date,
	t.team_long_name AS opponent,
    -- Complete the CASE statement with an alias
	CASE WHEN m.home_goal > m.away_goal THEN 'Barcelona win!'
         WHEN m.home_goal < m.away_goal THEN 'Barcelona loss :(' 
         ELSE 'Tie' END AS outcome 
FROM matches_spain AS m
LEFT JOIN teams_spain AS t 
ON m.awayteam_id = t.team_api_id
-- Filter for Barcelona as the home team
WHERE m.hometeam_id = 8634; 
{% endhighlight %}

{% highlight SQL %}
SELECT
	date,
	CASE WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
         ELSE 'Real Madrid CF' END as home,
	CASE WHEN awayteam_id = 8634 THEN 'FC Barcelona' 
         ELSE 'Real Madrid CF' END as away,
	-- Identify all possible match outcomes
	CASE WHEN home_goal > away_goal AND hometeam_id = 8634 THEN 'Barcelona win!'
         WHEN home_goal > away_goal AND hometeam_id = 8633 THEN 'Real Madrid win!'
         WHEN home_goal < away_goal AND awayteam_id = 8634 THEN 'Barcelona win!'
         WHEN home_goal < away_goal AND awayteam_id = 8633 THEN 'Real Madrid win!'
         ELSE 'Tie!' END AS outcome
FROM matches_spain
WHERE (awayteam_id = 8634 OR hometeam_id = 8634)
      AND (awayteam_id = 8633 OR hometeam_id = 8633);
{% endhighlight %}

{% highlight SQL %}
-- Select the season, date, home_goal, and away_goal columns
SELECT 
	season,
    date,
	home_goal,
	away_goal
FROM matches_italy
WHERE 
-- Exclude games not won by Bologna
	CASE WHEN hometeam_id = 9857 AND home_goal > away_goal THEN 'Bologna Win'
		WHEN awayteam_id = 9857 AND away_goal > home_goal THEN 'Bologna Win' 
		END IS NOT NULL;
{% endhighlight %}

{% highlight SQL %}
SELECT 
	c.name AS country,
    -- Count matches in each of the 3 seasons
	COUNT(CASE WHEN m.season = '2012/2013' THEN m.id END) AS matches_2012_2013,
	COUNT(CASE WHEN m.season = '2013/2014' THEN m.id END) AS matches_2013_2014,
	COUNT(CASE WHEN m.season = '2014/2015' THEN m.id END) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
{% endhighlight %}

{% highlight SQL %}
SELECT 
	c.name AS country,
    -- Sum the total records in each season where the home team won
	SUM(CASE WHEN m.season = '2012/2013' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2012_2013,
 	SUM(CASE WHEN m.season = '2013/2014' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2013_2014,
	SUM(CASE WHEN m.season = '2014/2015' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 END) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
{% endhighlight %}

{% highlight SQL %}
SELECT 
	c.name AS country,
    -- Round the percentage of tied games to 2 decimal points
	ROUND(AVG(CASE WHEN m.season='2013/2014' AND m.home_goal = m.away_goal THEN 1
			 WHEN m.season='2013/2014' AND m.home_goal != m.away_goal THEN 0
			 END),2) AS pct_ties_2013_2014,
	ROUND(AVG(CASE WHEN m.season='2014/2015' AND m.home_goal = m.away_goal THEN 1
			 WHEN m.season='2014/2015' AND m.home_goal != m.away_goal THEN 0
			 END),2) AS pct_ties_2014_2015
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
{% endhighlight %}

## Short and Simple Subqueries

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}


## Correlated Queries, Nested Queries, and Common Table Expressions

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

## Window Functions

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}


<a name="5"/>
# 5. PostgreSQL Summary Stats and Window Functions

## Introduction to window functions

-	Numbering rows allows you to easily fetch the nth row. 

{% highlight SQL %}
SELECT
  *,
  -- Assign numbers to each row
  ROW_NUMBER() OVER () AS Row_N
FROM Summer_Medals
ORDER BY Row_N ASC;
{% endhighlight %}

-	Numbering rows reversely.

{% highlight SQL %}
SELECT
  Year,
  -- Assign the lowest numbers to the most recent years
  ROW_NUMBER() OVER (ORDER BY year DESC) AS Row_N
FROM (
  SELECT DISTINCT Year
  FROM Summer_Medals
) AS Year
ORDER BY Year;
{% endhighlight %}

{% highlight SQL %}
WITH Athlete_Medals AS (
  SELECT
    -- Count the number of medals each athlete has earned
    Athlete,
    COUNT(*) AS Medals
  FROM Summer_Medals
  GROUP BY Athlete)

SELECT
  -- Number each athlete by how many medals they've earned
  athlete,
  ROW_NUMBER() OVER (ORDER BY medals DESC) AS Row_N
FROM Athlete_Medals
ORDER BY Medals DESC;
{% endhighlight %}

-	get the previous year's champion for each year

{% highlight SQL %}
WITH Weightlifting_Gold AS (
  SELECT
    -- Return each year's champions' countries
    Year,
    Country AS champion
  FROM Summer_Medals
  WHERE
    Discipline = 'Weightlifting' AND
    Event = '69KG' AND
    Gender = 'Men' AND
    Medal = 'Gold')

SELECT
  Year, Champion,
  -- Fetch the previous year's champion
  LAG(Champion, 1) OVER
    (ORDER BY year ASC) AS Last_Champion
FROM Weightlifting_Gold
ORDER BY Year ASC;
{% endhighlight %}

{% highlight SQL %}
WITH Athletics_Gold AS (
  SELECT DISTINCT
    Gender, Year, Event, Country
  FROM Summer_Medals
  WHERE
    Year >= 2000 AND
    Discipline = 'Athletics' AND
    Event IN ('100M', '10000M') AND
    Medal = 'Gold')

SELECT
  Gender, Year, Event,
  Country AS Champion,
  -- Fetch the previous year's champion by gender and event
  LAG(country) OVER (PARTITION BY gender, event
            ORDER BY Year ASC) AS Last_Champion
FROM Athletics_Gold
ORDER BY Event ASC, Gender ASC, Year ASC;
{% endhighlight %}

## Fetching, ranking, and paging

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}


## Aggregate window functions and frames

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}


## Beyond window functions

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}


<a name="6"/>
# 6. Functions for Manipulating Data in PostgreSQL

## Overview of Common Data Types

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}


## Working with DATE/TIME Functions and Operators

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}


## Parsing and Manipulating Text

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

## Full-text Search and PostgresSQL Extensions

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}
