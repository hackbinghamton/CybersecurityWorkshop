
# Intro to SQL Injections
## Overview

### What You'll Learn
In this section, you'll learn
1. What SQL is
2. What an SQL Injection is
3. How to perform an SQL Injection (ON PLACES WHERE YOU ARE EXPLICITLY GIVEN PERMISSION TO DO SO)
4. How to prevent SQL Injections in applications

### Prerequisites
*Optional:* basic knowledge of HTML frameworks and web security- this is not required but might help with your understanding!

## What is SQL?
SQL is a data management language used to communicate with databases. While it is not innately a programming language, it is still an extremely useful tool. If you're familiar with assembly, you'll know that assembly languages such as MIPS or x86 are one of the most direct ways to give instructions to a computer. Think of SQL as a language that allows you to *access and give instructions to a database*.

Here are some general uses of SQL:
-Creating a new database
-Inserting data in an existing database
-Modifying data in an existing database
-Deleting data

### Quick example using basic SQL syntax

Lets assume we have a table `shop_items` containing surface level information for items being listed on an online store. Also pretend the number values are implicitly counted as USD.

|item   |price   |
|---	|---	 |
|Shirt  |15   	 |
|Pants  |25  	 |
|Hoodie |30   	 |
|Socks  |12      |

If you wanted to get a list of all the item names, you'd do
```
SELECT item FROM shop_items;
```
This request selects all data values under the column name `item` from the table `shop_items`.

We can zero in on this search and be even more specific. Let's only list the items that have a price greater than $20:
```
SELECT item FROM shop_items WHERE price > 20;
```
This would list the following products:
`Pants, Hoodie`

Now that we have a basic understanding of SQL, let's jump into injections.

## What are SQL Injections?
