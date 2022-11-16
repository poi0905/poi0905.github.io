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

1. [Exploratory Data Analysis in SQL](#1)
2. [Data-Driven Decision Making in SQL](#2)
3. [Applying SQL to Real-World Problems](#3)
4. [Analyzing Business Data in SQL](#4)
5. [Reporting in SQL](#5)

<a name="1"/>
# Exploratory Data Analysis in SQL

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


## Summarizing and aggregating numeric data
## Exploring categorical data and unstructured text
## Working with dates and timestamps

<a name="2"/>
# Data-Driven Decision Making in SQL

## Introduction to business intelligence for a online movie rental database
## Decision Making with simple SQL queries
## Data Driven Decision Making with advanced SQL queries
## Data Driven Decision Making with OLAP SQL queries

<a name="3"/>
# Applying SQL to Real-World Problems

## Use Real-World SQL
## Find Your Data
## Manage Your Data
## Best Practices for Writing SQL

<a name="4"/>
# Analyzing Business Data in SQL

## Revenue, Cost, and Profit
## User-Centric KPIs
## ARPU, Histograms, and Percentiles
## Generating an Executive Report

<a name="5"/>
# Reporting in SQL

## Exploring the Olympics Dataset
## Creating Reports
## Cleaning & Validation
## Complex Calculations
