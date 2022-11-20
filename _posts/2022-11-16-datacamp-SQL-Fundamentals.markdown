---
layout: post
title:  SQL Fundamentals
date:   2022-11-15 00:00:55 +0800
image:  SQL101.jpg
tags:   SQL
categories: [SQL]
---

Gain the fundamental skills you need to interact with and query your data in SQL—a powerful language used by data-driven businesses large and small to explore and manipulate their data to extract meaningful insights.

In this track, you'll learn the skills you need to level up your data skills and leave Excel behind you. Through hands-on exercises, you’ll discover how to quickly summarize, join tables, and use window functions and built-in PostgreSQL functions to analyze your data.

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
* **`HAVING` filters grouped records**

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

*Quick Overview*

![image](https://raw.githubusercontent.com/poi0905/poi0905.github.io/master/images/SQL_JOINS.jpg)

[Scource](https://zhuanlan.zhihu.com/p/29234064)

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

* `RIGHT JOIN` is less commonly used than `LEFT JOIN`
* Any `RIGHT JOIN` can be re-written as a `LEFT JOIN`

{% highlight SQL %}
SELECT region, AVG(gdp_percapita) AS avg_gdp
FROM countries AS c
LEFT JOIN economies AS e
USING(code)
WHERE year = 2010
GROUP BY region
ORDER BY avg_gdp DESC
LIMIT 10;
{% endhighlight %}

{% highlight SQL %}
SELECT 
	c1.name AS country, 
  region, 
  l.name AS language,
	basic_unit, 
  frac_unit
FROM countries as c1 
FULL JOIN languages AS l
USING(code)
FULL JOIN currencies AS c2
USING(code)
WHERE region LIKE 'M%esia';
{% endhighlight %}

`CROSS JOIN` creates all possible combinations of two tables.

{% highlight SQL %}
SELECT c.name AS country, l.name AS language
FROM countries AS c        
-- Perform a cross join to languages (alias as l)
CROSS JOIN languages as l
WHERE c.code in ('PAK','IND')
	AND l.code in ('PAK','IND');
{% endhighlight %}

* `SELF JOIN` are tables joined with themselves
* Can be used to **compare** parts of the smae table

{% highlight SQL %}
SELECT 
    p1.country_code, 
    p1.size AS size2010, 
    p2.size AS size2015
FROM populations AS p1
INNER JOIN populations AS p2
ON p1.country_code = p2.country_code
WHERE p1.year = 2010
-- Filter such that p1.year is always five years before p2.year
    AND p2.year = 2015
{% endhighlight %}

## Set Theory for SQL Joins

`UNION` can be helpful for consolidating data from multiple tables into one result, which as you have seen, can then be ordered in meaningful ways.

{% highlight SQL %}
-- Select all fields from economies2015
SELECT *
FROM economies2015 
-- Set operation
UNION
-- Select all fields from economies2019
SELECT *
FROM economies2019 
ORDER BY code, year;
{% endhighlight %}

`UNION` returned 434 records, whereas `UNION ALL` returned 814. There are duplicates in the `UNION ALL`.

{% highlight SQL %}
SELECT code, year
FROM economies
-- Set theory clause
UNION ALL
SELECT country_code, year
FROM populations
ORDER BY code, year;
{% endhighlight %}

*`INTERSECT`*

{% highlight SQL %}
-- Return all cities with the same name as a country
SELECT name
FROM cities
INTERSECT
SELECT name
FROM countries;
{% endhighlight %}

*`EXCEPT`*

{% highlight SQL %}
-- Return all cities that do not have the same name as a country
SELECT name
FROM cities
EXCEPT
SELECT name
FROM countries
ORDER BY name;
{% endhighlight %}

## Subqueries

{% highlight SQL %}
SELECT DISTINCT name
FROM languages
-- Add syntax to use bracketed subquery below as a filter
WHERE code IN
    (SELECT code
    FROM countries
    WHERE region = 'Middle East')
ORDER BY name;
{% endhighlight %}

{% highlight SQL %}
SELECT code, name
FROM countries
WHERE continent = 'Oceania'
-- Filter for countries not included in the bracketed subquery
  AND code NOT IN
    (SELECT code
    FROM currencies);
{% endhighlight %}

* Subqueries inside `WHERE` and `SELECT`

{% highlight SQL %}
SELECT *
FROM populations
-- Filter for only those populations where life expectancy is 1.15 times higher than average
WHERE life_expectancy > 1.15 *
  (SELECT AVG(life_expectancy)
   FROM populations
   WHERE year = 2015) 
     AND year = 2015;
{% endhighlight %}

{% highlight SQL %}
-- Select relevant fields from cities table
SELECT name, country_code, urbanarea_pop
-- Filter using a subquery on the countries table
FROM cities
WHERE name IN
    (SELECT capital
    FROM countries)
ORDER BY urbanarea_pop DESC;
{% endhighlight %}

{% highlight SQL %}
SELECT countries.name AS country,
-- Subquery that provides the count of cities   
  (SELECT COUNT(*)
   FROM cities
   WHERE countries.code = cities.country_code) AS cities_num
FROM countries
ORDER BY cities_num DESC, country
LIMIT 9;
{% endhighlight %}

* Subqueries inside FROM

{% highlight SQL %}
-- Select local_name and lang_num from appropriate tables
SELECT local_name, sub.lang_num
FROM countries,
    (SELECT code, COUNT(*) AS lang_num
     FROM languages
     GROUP BY code) AS sub
-- Where codes match    
WHERE countries.code = sub.code
ORDER BY lang_num DESC;
{% endhighlight %}

{% highlight SQL %}
-- Select relevant fields
SELECT code, inflation_rate, unemployment_rate
FROM economies
WHERE year = 2015 
  AND code NOT IN
-- Subquery returning country codes filtered on gov_form
	(SELECT code
  FROM countries
  WHERE gov_form LIKE '%Republic%' OR gov_form LIKE '%Monarchy%')
ORDER BY inflation_rate;
{% endhighlight %}

{% highlight SQL %}
-- Select fields from cities
SELECT name, 
    country_code, 
    city_proper_pop, 
    metroarea_pop,
    city_proper_pop / metroarea_pop * 100 AS city_perc
FROM cities
-- Use subquery to filter city name
WHERE name IN 
    (SELECT capital
    FROM countries
    WHERE continent = 'Europe' OR continent LIKE '%America')
-- Add filter condition such that metroarea_pop does not have null values
    AND metroarea_pop IS NOT NULL
-- Sort and limit the result
ORDER BY city_perc DESC
LIMIT 10;
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

* Can be in *any* part of a query, like `SELECT`, `FROM`, `WHERE`, `GROUP BY`

`Where`
{% highlight SQL %}
SELECT
	-- Select the team long and short names
	team_long_name,
	team_short_name
FROM team
-- Filter for teams with 8 or more home goals
WHERE team_api_id IN
	  (SELECT hometeam_ID 
       FROM match
       WHERE home_goal >= 8);
{% endhighlight %}

`FROM`
{% highlight SQL %}
SELECT
	-- Select country, date, home, and away goals from the subquery
    country,
    date,
    home_goal,
    away_goal
FROM 
	-- Select country name, date, home_goal, away_goal, and total goals in the subquery
	(SELECT c.name AS country, 
     	    m.date, 
     		m.home_goal, 
     		m.away_goal,
           (m.home_goal + m.away_goal) AS total_goals
    FROM match AS m
    LEFT JOIN country AS c
    ON m.country_id = c.id) AS subquery
-- Filter by total goals scored in the main query
WHERE total_goals >= 10;
{% endhighlight %}

`SELECT`
{% highlight SQL %}
SELECT
	-- Select the league name and average goals scored
	l.name AS league,
	ROUND(AVG(m.home_goal + m.away_goal),2) AS avg_goals,
    -- Subtract the overall average from the league average
	ROUND(AVG(m.home_goal + m.away_goal) - 
		(SELECT AVG(home_goal + away_goal)
		 FROM match 
         WHERE season = '2013/2014'),2) AS diff
FROM league AS l
LEFT JOIN match AS m
ON l.country_id = m.country_id
-- Only include 2013/2014 results
WHERE season = '2013/2014'
GROUP BY l.name;
{% endhighlight %}

`EVERYWHERE`
{% highlight SQL %}
SELECT 
	-- Select the stage and average goals from s
	s.stage,
    ROUND(s.avg_goals,2) AS avg_goal,
    -- Select the overall average for 2012/2013
    (SELECT AVG(home_goal + away_goal) FROM match WHERE season = '2012/2013') AS overall_avg
FROM 
	-- Select the stage and average goals in 2012/2013 from match
	(SELECT
		 stage,
         AVG(home_goal + away_goal) AS avg_goals
	 FROM match
	 WHERE season = '2012/2013'
	 GROUP BY stage) AS s
WHERE 
	-- Filter the main query using the subquery
	s.avg_goals > (SELECT AVG(home_goal+ away_goal) 
                    FROM match WHERE season = '2012/2013');
{% endhighlight %}


## Correlated Queries, Nested Queries, and Common Table Expressions

**Simple Subquery**:
* Can be run independently from the main query
* Evaluated once in the whole query

**Correlated Subquery**:
* Dependent on the main query to execute
* Evaluated in loops: Significantly slows down query runtime

{% highlight SQL %}
SELECT 
	-- Select country ID, date, home, and away goals from match
	main.country_id,
    main.date,
    main.home_goal,
    main.away_goal
FROM match AS main
WHERE 
	-- Filter for matches with the highest number of goals scored
	(home_goal + away_goal) = 
        (SELECT MAX(sub.home_goal + sub.away_goal)
         FROM match AS sub
         WHERE main.country_id = sub.country_id
               AND main.season = sub.season);
{% endhighlight %}

{% highlight SQL %}
SELECT
	-- Select the season and max goals scored in a match
	season,
    MAX(home_goal + away_goal) AS max_goals,
    -- Select the overall max goals scored in a match
   (SELECT MAX(home_goal + away_goal) FROM match) AS overall_max_goals,
   -- Select the max number of goals scored in any match in July
   (SELECT MAX(home_goal + away_goal) 
    FROM match
    WHERE id IN (
          SELECT id FROM match WHERE EXTRACT(MONTH FROM date) = 07)) AS july_max_goals
FROM match
GROUP BY season;
{% endhighlight %}

**Common Table Expressions(CTEs)**

* Table *declared* before the main query
* *Named* and *referenced* later in `FROM` statement

{% highlight SQL %}
-- Set up your CTE
WITH match_list AS (
  -- Select the league, date, home, and away goals
    SELECT 
  		l.name AS league, 
     	m.date, 
  		m.home_goal, 
  		m.away_goal,
      (m.home_goal + m.away_goal) AS total_goals
    FROM match AS m
    LEFT JOIN league as l ON m.country_id = l.id)

-- Select the league, date, home, and away goals from the CTE
SELECT league, date, home_goal, away_goal
FROM match_list
-- Filter by total goals
WHERE total_goals >= 10;
{% endhighlight %}

{% highlight SQL %}
-- Set up your CTE
WITH match_list AS (
    SELECT 
  		country_id,
  	   (home_goal + away_goal) AS goals
    FROM match
  	-- Create a list of match IDs to filter data in the CTE
    WHERE id IN (
       SELECT id
       FROM match
       WHERE season = '2013/2014' AND EXTRACT(MONTH FROM date) = 8))
-- Select the league name and average of goals in the CTE
SELECT 
	l.name,
  AVG(match_list.goals)
FROM league AS l
-- Join the CTE onto the league table
LEFT JOIN match_list ON l.id = match_list.country_id
GROUP BY l.name;
{% endhighlight %}

{% highlight SQL %}
SELECT
	m.date,
    -- Get the home and away team names
    hometeam,
    awayteam,
    m.home_goal,
    m.away_goal
FROM match AS m

-- Join the home subquery to the match table
LEFT JOIN (
  SELECT match.id, team.team_long_name AS hometeam
  FROM match
  LEFT JOIN team
  ON match.hometeam_id = team.team_api_id) AS home
ON home.id = m.id

-- Join the away subquery to the match table
LEFT JOIN (
  SELECT match.id, team.team_long_name AS awayteam
  FROM match
  LEFT JOIN team
  -- Get the away team ID in the subquery
  ON match.awayteam_id = team.team_api_id) AS away
ON away.id = m.id;
{% endhighlight %}

{% highlight SQL %}
WITH home AS (
  SELECT m.id, m.date, 
  		 t.team_long_name AS hometeam, m.home_goal
  FROM match AS m
  LEFT JOIN team AS t 
  ON m.hometeam_id = t.team_api_id),

-- Declare and set up the away CTE
     away AS (
  SELECT m.id, m.date, 
  		 t.team_long_name AS awayteam, m.away_goal
  FROM match AS m
  LEFT JOIN team AS t 
  ON m.awayteam_id = t.team_api_id)

-- Select date, home_goal, and away_goal
SELECT 
	home.date,
    home.hometeam,
    away.awayteam,
    home.home_goal,
    away.away_goal
-- Join away and home on the id column
FROM home
INNER JOIN away
ON home.id = away.id;
{% endhighlight %}

{% highlight SQL %}

{% endhighlight %}

*Key takeaway*

![image](https://raw.githubusercontent.com/poi0905/poi0905.github.io/master/images/SQL_different_technique.jpg)

## Window Functions

The `OVER()` clause allows you to pass an aggregate function down a data set, similar to subqueries in `SELECT`. The `OVER()` clause offers significant benefits over subqueries in select -- namely, your queries will run faster, and the `OVER()` clause has a wide range of additional functions and clauses you can include with it that we will cover later on in this chapter.

**Same Result**

{% highlight SQL %}
SELECT
  date,
  (home_goal + away_goal) AS goals,
  (SELECT AVG(home_goal + away_goal)
    FROM match
    WHERE season = '2011/2012') AS overall_avg
FROM match
WHERE season = '2011/2012';
{% endhighlight %}

**Same Result**

{% highlight SQL %}
SELECT 
  date,
  (home_goal + away_goal) AS goals,
  AVG(home_goal + away_goal) OVER() AS overall_avg
FROM match
WHERE season = '2011/2012'
{% endhighlight %}

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

* Overview of all table

{% highlight SQL %}
 -- Select all columns from the TABLES system database
 SELECT * 
 FROM INFORMATION_SCHEMA.TABLES
 -- Filter by schema
 WHERE table_schema = 'public';
{% endhighlight %}

* Overview of particular table

{% highlight SQL %}
 -- Select all columns from the COLUMNS system database
 SELECT * 
 FROM INFORMATION_SCHEMA.COLUMNS
 WHERE table_name = 'actor';
{% endhighlight %}

* Get the column name and data type

{% highlight SQL %}
SELECT
 	column_name, 
    data_type
-- From the system database information schema
FROM INFORMATION_SCHEMA.COLUMNS 
-- For the customer table
WHERE table_name = 'customer';
{% endhighlight %}

* `DATE` data types use an `yyyy-mm-dd` format
* `TIMESTAMP`s contain both a date value and a time value with microsecond precision
* `INTERVAL`s can do arithmetic on date and time columns

{% highlight SQL %}
SELECT
 	-- Select the rental and return dates
	rental_date,
	return_date,
 	-- Calculate the expected_return_date
	rental_date + INTERVAL '3 days' AS expected_return_date
FROM rental;
{% endhighlight %}

* Note that PostgreSQL array indexes start with one and not zero

{% highlight SQL %}
-- Select the title and special features column 
SELECT 
  title, 
  special_features 
FROM film
-- Use the array index of the special_features column
WHERE special_features[2] = 'Deleted Scenes';
{% endhighlight %}

{% highlight SQL %}
SELECT
  title, 
  special_features 
FROM film 
-- Modify the query to use the ANY function 
WHERE 'Trailers' = ANY(special_features);
{% endhighlight %}

* The contains operator `@>` operator is alternative syntax to the `ANY` function

{% highlight SQL %}
SELECT 
  title, 
  special_features 
FROM film 
-- Filter where special_features contains 'Deleted Scenes'
WHERE special_features @> ARRAY['Deleted Scenes'];
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
