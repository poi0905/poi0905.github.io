---
layout: post
title:  SQL for Business Analysts
date:   2022-10-31 18:05:55 +0800
image:  SQL.jpg
tags:   SQL
categories: [SQL]
---

施工中...

# 目錄

***

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
SELECT *
    FROM company;
LIMIT 5
{% endhighlight %}

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
