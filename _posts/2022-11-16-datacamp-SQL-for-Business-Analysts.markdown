---
layout: post
title:  SQL for Business Analysts
date:   2022-11-15 18:05:55 +0800
image:  SQL.jpg
tags:   SQL
categories: [SQL]
---

Boost your business SQL skills. Whether you’re in marketing, finance, or product, knowing how to make data-driven decisions is the key to success. The more fluently you can retrieve and analyze your data, the quicker you’ll uncover actionable insights and grow your business. In this track, you’ll learn how to quickly explore and analyze data to help you make smarter business decisions. Through hands-on practice, you’ll learn everything from creating and joining tables to writing queries, subqueries, and aggregate functions, providing you with the skills you need to excel and overcome real-world business challenges.

***

# 目錄

1. [1. Exploratory Data Analysis in SQL](#1)
2. [2. Data-Driven Decision Making in SQL](#2)
3. [3. Applying SQL to Real-World Problems](#3)
4. [4. Analyzing Business Data in SQL](#4)
5. [5. Reporting in SQL](#5)

<a name="1"/>
# 1. Exploratory Data Analysis in SQL

## What's in the database?

|Code                             |     Note                             |   
|---------------------------------|--------------------------------------|
|`NULL`                           | missing                              |
|`NULL, IS NOT NULL`              | don't use = `NULL`                   | 
|`count(*)`                       | number of rows                       |
|`count(column_name)`             | number of non-`NULL` values          |
|`count(DISTINCT column_name)`    | number of different non-`NULL` values|   
|`SELECT DISTINCT column_name ...`| distinct values, including `NULL`    | 

{% highlight SQL %}
SELECT count(*) - count(ticker) AS missing
  FROM fortune500;
{% endhighlight %}

{% highlight SQL %}
SELECT company.name 
-- Table(s) to select from
  FROM company 
       INNER JOIN fortune500 
       ON company.ticker=fortune500.ticker;
{% endhighlight %}

{% highlight SQL %}
SELECT *
  FROM price;
{% endhighlight %}

|column_1   |  column_2   |   
|-----------|-------------|
|           | 10          |
|           |             | 
|       22  |             |
|        3  | 4           |

{% highlight SQL %}
SELECT coalesce(column_1, column_2)
  FROM price;
{% endhighlight %}

|coalesce   | 
|-----------|
|       10  |
|           |             
|       22  | 
|        3  | 

{% highlight SQL %}
-- Count the number of tags with each type
SELECT type, count(*) AS count
  FROM tag_type
 -- To get the count for each type, what do you need to do?
 GROUP BY type
 -- Order the results with the most common
 -- tag types listed first
 ORDER BY count DESC;
{% endhighlight %}

{% highlight SQL %}
-- Select the 3 columns desired
SELECT name, tag_type.tag, tag_type.type
  FROM company
  	   -- Join the tag_company and company tables
       INNER JOIN tag_company 
       ON company.id = tag_company.company_id
       -- Join the tag_type and company tables
       INNER JOIN tag_type
       ON tag_company.tag = tag_type.tag
  -- Filter to most common type
  WHERE type='cloud';
{% endhighlight %}

{% highlight SQL %}
-- Select the 3 columns desired
-- Use coalesce
SELECT coalesce(fortune500.industry, fortune500.sector, 'Unknown') AS industry2,
       -- Don't forget to count!
       count(industry) 
  FROM fortune500 
-- Group by what? (What are you counting by?)
 GROUP BY industry2
-- Order results to see most common first
 ORDER BY count DESC
-- Limit results to get just the one value you want
 LIMIT 1;
{% endhighlight %}

{% highlight SQL %}
SELECT company_original.name, title, rank
  -- Start with original company information
  FROM company AS company_original
       -- Join to another copy of company with parent
       -- company information
	   LEFT JOIN company AS company_parent
       ON company_original.parent_id = company_parent.id 
       -- Join to fortune500, only keep rows that match
       INNER JOIN fortune500 
       -- Use parent ticker if there is one, 
       -- otherwise original ticker
       ON coalesce(company_original.ticker, 
                   company_parent.ticker) = 
             fortune500.ticker
 -- For clarity, order by rank
 ORDER BY rank; 
{% endhighlight %}

{% highlight SQL %}
-- Select the original value
SELECT profits_change, 
	   -- Cast profits_change
       CAST(profits_change AS integer) AS profits_change_int
  FROM fortune500;
{% endhighlight %}

* the first column will be 3; the second column will be 3.3333-

{% highlight SQL %}
-- Divide 10 by 3
SELECT 10/3, 
       -- Cast 10 as numeric and divide by 3
       10::numeric/3;
{% endhighlight %}

{% highlight SQL %}
-- Select the count of each revenues_change integer value
SELECT revenues_change::integer, COUNT(revenues_change::integer)
  FROM fortune500
 GROUP BY revenues_change::integer
 -- order by the values of revenues_change
 ORDER BY revenues_change;
{% endhighlight %}


## Summarizing and aggregating numeric data

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## Exploring categorical data and unstructured text

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## Working with dates and timestamps

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

<a name="2"/>
# 2. Data-Driven Decision Making in SQL

## Introduction to business intelligence for a online movie rental database

{% highlight SQL %}
SELECT *
FROM movies
WHERE genre <> 'Drama'; -- All genres except drama
{% endhighlight %}

## Decision Making with simple SQL queries

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## Data Driven Decision Making with advanced SQL queries

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## Data Driven Decision Making with OLAP SQL queries

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

<a name="3"/>
# 3. Applying SQL to Real-World Problems

## Use Real-World SQL

{% highlight SQL %}
EXTRACT(<part> FROM <date_column>)
{% endhighlight %}

* Use `STRING_AGG` for `GROUP BY`

{% highlight SQL %}
SELECT name, 
	STRING_AGG(title, ',') AS film_titles
FROM film AS f
INNER JOIN language AS l
  ON f.language_id = l.language_id
WHERE release_year = 2010
  AND rating = 'G'
GROUP BY name;
{% endhighlight %}

## Find Your Data

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## Manage Your Data

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## Best Practices for Writing SQL

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

<a name="4"/>
# 4. Analyzing Business Data in SQL

## Revenue, Cost, and Profit

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## User-Centric KPIs

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## ARPU, Histograms, and Percentiles

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## Generating an Executive Report

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

<a name="5"/>
# 5. Reporting in SQL

## Exploring the Olympics Dataset

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## Creating Reports

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## Cleaning & Validation

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}

## Complex Calculations

{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}


{% highlight SQL %}

{% endhighlight %}