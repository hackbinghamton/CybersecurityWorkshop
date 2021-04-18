
# Intro to SQL Injections
## Overview

### What You'll Learn
In this section, you'll learn
1. What SQL is
2. What an SQL Injection is
3. How to perform an SQL Injection (**on sites that you are explicitly given permission to do so**)
4. How to prevent SQL Injection in applications

### Prerequisites

*Optional:* Basic knowledge of HTML frameworks and web security- this is not required but might help with your understanding!

## What is SQL?
SQL is a data management language used to communicate with databases. While it is not innately a programming language, it is still an extremely useful tool. If you're familiar with assembly, you'll know that assembly languages such as MIPS or x86 are one of the most direct ways to give instructions to a computer. Think of SQL as a language that allows you to *access and give instructions to a database*.

Here are some general uses of SQL:
* Creating a new database
* Inserting data in an existing database
* Modifying data in an existing database
* Deleting data

### Quick example using basic SQL syntax

Let's assume we have a table `shop_items` containing surface level information for items being listed on an online store. Also pretend that the number values are implicitly counted as USD.

|item   |price   |
|---	|---	 |
|Shirt  |15   	 |
|Pants  |25  	 |
|Hoodie |30   	 |
|Socks  |12      |

If you wanted to get a list of all the item names, you'd do:
```
SELECT item FROM shop_items;
```
This request selects all data values under the column name `item` from the table `shop_items`.

We can zero in on this search and be even more specific. Let's only list the items that have a price greater than $20:
```
SELECT item FROM shop_items WHERE price > 20;
```
This would list the following products:
```
Pants, Hoodie
```
Now that we have a basic understanding of SQL, let's jump into injections.

## What is SQL Injection?

SQL Injection can be described as a web security vulnerability that can allow attackers to extract, modify, delete, and add data to a SQL database from a web application. Generally, its vulnerability will let attackers view data that they are not normally allowed to see.

Successful SQL Injection attacks will often result in data breaches, in which important information such as passwords, credit cards and other sensitive data is accessed by an attacker.

## The Basics of an SQL Injection Attack

Below are a few situations where attackers can exploit vulnerabilities in web applications to perform SQL Injection attacks.

### Example Scenario #1: Exposing Hidden Information

Let's assume we have a basic shopping application that displays products in categories such as `Stickers`, `Hoodies`, and `Tees`.

If a user were to click on the `Stickers` tab to navigate to that category, the browser would request the following URL:
```
https://my-online-store.com/products?category=Stickers
```
This causes the web application backend to generate and execute a SQL query, retrieving the relevant products under the `Sticker` category from the database and displaying them to the user. That query looks something like this:
```
SELECT * FROM products WHERE category = 'Stickers' AND released=1;
```
To break it down, here's exactly what this query asks the database to return:
* All items in the products table (hence the use of the * operator)
* The above request is then *specified* with `WHERE`, making sure that all the listed products are **only** a part of the `Stickers` category.
* Finally, `AND` *adds* on to the previous request. This means only items that are marked as `released=1` will be displayed with this query.

In this case, `released=1` is being used by the developer of this web application to hide unreleased products from the average browser of the website. This means that *unreleased* products will have a `released` value of `0`.

This web application provides no defense against SQL Injection attacks. Because of this, an attacker can exploit the SQL query made from the URL above, and change it ever so slightly:
```
https://my-online-store.com/products?category=Stickers'--
```
This results in a different SQL query being generated:
```
SELECT * FROM products WHERE category = 'Stickers'--' AND released=1;
```
Here's why this is significant. **In SQL, the double dash `--` is a comment indicator**, just like `#` in Python or `/` in C. The addition of `'--'` in the URL appends it to the end of `Stickers`. This comments out the rest of the query, which specifies that `released=1` must be true. Now that is out of the equation, meaning items with a value of `released=0` will also be displayed.

### Example Scenario #2: Login Bypass

Suppose there exists an unguarded web application with a simple set of text box forms that lets users log in with a username and password. If the user submits `hackbu` and `hack123` as a username and password, respectively, the following SQL query will be generated:
```
SELECT * FROM users WHERE username = 'hackbu' AND password = 'hack123';
```
If the username and password are correct, then this query will return a result, and the login will be successful. However, an attacker can exploit this web application and login as any existing user without a password. Take, for example, the following query:
```
SELECT * FROM users WHERE username = 'hackbuadmin'--' AND password = ''
```
Here, the SQL comment sequence `--` is used to remove the password check by hiding the `AND` clause of the query. If there is an existing username `hackbuadmin`, the attacker will log in as that user.

## Preventing SQL Injection Attacks

SQL Injection attacks are incredibly common and have been the driving force behind many of the large data breaches you might have heard about in recent years. In 2019, it is reported that 25% of breaches started with a SQL Injection attack.

The reason SQL Injection attacks are so common is mainly due to two factors:
* There is a significant prevalence of SQL Injection vulnerabilities throughout many web applications
* The database(s) vulnerable to SQL Injection attacks contain key information critical to the functionality of the application (meaning that a lot of the data vulnerable to SQL Injection attacks is generally very valuable)

Most critical SQL Injection flaws within applications are sourced from **dynamic database queries with unregulated user input**. One example that comes to mind is the login form above. To prevent SQL Injection vulnerabilities, developers need to do one of two things:
* Stop allowing dynamic queries to be created
* Prevent malicious user input that can affect an SQL query

### Specific Solutions
Let's zero in on the two points made above and discuss how exactly developers can better prevent SQL Injection vulnerabilities from existing in their applications.

One solution is using a whitelist of accepted user input as opposed to a blacklist or no regulation of input at all. While a blacklist can be useful in preventing certain inputs from being executed by attackers, it is far safer to use a whitelist. Using a whitelist will allow you as the developer to add accepted inputs, which means anything outside of the whitelist will not work.

Assume, for example, that the input in the login form above whitelisted the use of alphanumerical characters (A-Z, 0-9). This means the attacker is extremely limited in their means of performing an SQL Injection attack. The same would not be true if a blacklist with `--` characters was made instead, because there are far more ways to perform attacks that are not inclusive to the use of the comment operator.

## Exercises

To put what you've learned to the test, check out these websites for more interactive examples:

* [Hacksplaining's SQL Injection Tutorial](https://www.hacksplaining.com/exercises/sql-injection)
* [PortSwigger's Web Security Academy](https://portswigger.net/web-security)
