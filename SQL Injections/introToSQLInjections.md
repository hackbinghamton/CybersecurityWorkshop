
# Intro to SQL Injections
## Overview

### What You'll Learn
In this section, you'll learn
1. What SQL is
2. What an SQL Injection is
3. How to perform an SQL Injection (ON PLACES WHERE YOU ARE EXPLICITLY GIVEN PERMISSION TO DO SO)
4. How to prevent SQL Injection in applications

### Prerequisites
*Optional:* basic knowledge of HTML frameworks and web security- this is not required but might help with your understanding!

## What is SQL?
SQL is a data management language used to communicate with databases. While it is not innately a programming language, it is still an extremely useful tool. If you're familiar with assembly, you'll know that assembly languages such as MIPS or x86 are one of the most direct ways to give instructions to a computer. Think of SQL as a language that allows you to *access and give instructions to a database*.

Here are some general uses of SQL:
* Creating a new database
* Inserting data in an existing database
* Modifying data in an existing database
* Deleting data

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
```
Pants, Hoodie
```
Now that we have a basic understanding of SQL, let's jump into injections.

## What is SQL Injection?

SQL Injection can be described as a web security vulnerability that can allow for cyber attacks to occur. Generally, its vulnerability will let attackers view data that they are not normally allowed to see. An attacker can capitalize on this by modifying, deleting or extracting data from a vulnerable website.

Successful SQL Injection attacks will often result in data breaches, in which important information such as passwords, credit cards and other sensitive data is accessed by an attacker.

## The Basics of an SQL Injection Attack

Let's assume we have a basic shopping application that displays products in categories such as `Stickers`, `Hoodies`, and `Tees`.

If a user were to click on the `Stickers` tab to navigate to that category, the browser would request the following URL:
```
https://my-online-store.com/products?category=Stickers
```
This causes the web application to generate an SQL query in response, retrieving the relevant products under the sticker category from the database and displaying them to the user. That query looks something like this:
```
SELECT * FROM products WHERE category = 'Stickers' AND released=1;
```
To break it down, here's exactly what this query asks the database to return:
* All items in the products table (hence the use of the * operator)
* The above request is then *specified* with `WHERE`, making sure that all the listed products are **only** a part of the `Stickers` category.
* Finally, `AND` *adds* on to the previous request. This means only items that are marked as `released=1` will be displayed with this query.

In this case, `released=1` is being used by the developer of this web application to hide unreleased products from the average browser of the website. This means that unreleased products will have a `released` value of `0`.

This web application provides no defense against SQL Injection attacks. Because of this, an attacker can exploit the SQL query made from the URL above, and change it ever so slightly:
```
https://my-online-store.com/products?category=Stickers'--
```
This results in a different SQL query being generated:
```
SELECT * FROM products WHERE category = 'Stickers'--' AND released=1;
```
Here's why this is significant. In SQL, the double dash `--` is a comment indicator, just like `#` in Python or `/` in C. The addition of `'--'` in the URL appends it to the end of `Stickers`. This comments out the rest of the query, which specifies that `released=1` must be true. Now that is out of the equation, meaning items with a value of `released=0` can also be displayed.

### Another Example

Suppose there exists an unguarded web application with a simple set of text box forms that lets users log in with a username and password. If the user submits `hackbu` and `hack123` as a username and password, respectively, the following SQL query will be generated:
```
SELECT * FROM users WHERE username = 'hackbu' AND password = 'hack123';
```
If the username and password are correct, the login will be successful. However, an attacker can exploit this web application and login as any existing user without a password. Take for example the following query:
```
SELECT * FROM users WHERE username = 'hackbuadmin'--' AND password = ''
```
Here, the SQL comment sequence `--` is used to remove the password check by hiding the `AND` clause of the query. If there is an existing username `hackbuadmin`, the attacker will login as that user.

## Preventing SQL Injection Attacks
