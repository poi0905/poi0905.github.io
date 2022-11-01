---
layout: post
title:  SQL Fundamentals
date:   2022-11-01 18:00:55 +0800
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
# Introduction to SQL

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
# Intermediate SQL

## Selecting Data

{% highlight SQL %}
SELECT COUNT(DISTINCT(country)) AS count_distinct_countries
FROM films;
{% endhighlight %}

[Style guide](https://www.sqlstyle.guide/)

## Filtering Records
## Aggregate Functions
## Sorting and Grouping

<a name="3"/>
# Joining Data in SQL

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

## Outer Joins, Cross Joins and Self Joins
## Set Theory for SQL Joins
## Subqueries

<a name="4"/>
# Data Manipulation in SQL

## We'll take the CASE
## Short and Simple Subqueries
## Correlated Queries, Nested Queries, and Common Table Expressions
## Window Functions

<a name="5"/>
# PostgreSQL Summary Stats and Window Functions

## Introduction to window functions
## Fetching, ranking, and paging
## Aggregate window functions and frames
## Beyond window functions

<a name="6"/>
# Functions for Manipulating Data in PostgreSQL

## Overview of Common Data Types
## Working with DATE/TIME Functions and Operators
## Parsing and Manipulating Text
## Full-text Search and PostgresSQL Extensions
