---
layout: post
title:      "SQL: Communicating with Databases"
date:       2018-02-17 21:29:20 +0000
permalink:  sql_communicating_with_databases
---


Howdy! Welcome to SQL! I'd be happy to be your guide :)

## Ess-Cue-El? Esquell? Skwull?
Well, it's actually more commonly pronounced as **"Sequel"**, nice try though!

SQL stands for Structured Query Language, and it's really good at pulling information from databases.

Basically, A SQL statement queries a database table to recieve columns from records. Doesn't make sense? That's ok! let's take a closer look!

## Databases, and Tables, and Records, and Columns? Sounds confusing.
It's not. I like to think of databases like a supermarket. Some are large and store a whole lot of things, like a BJ's or a Costco. Some are small and store a whole lot less, like the quick-stop mart at a gas station. But no matter how many things you're storing, you need a building to keep it all in. That's where programmers are at an advantage! If you wanted to open your own supermarket, you would need to know how many items you are planning to store before building the building. But online, space isn't as constricting as in the real world. Databases can grow as you need them too! And databases are *way* easier to build, too. The SQL syntax for making a brand new database is `CREATE DATABASE database_name;`. If I wanted to build a database to store information about dinosaurs, I might do something like: `CREATE DATABASE dinosaurs;`.

Inside a supermarket, there are aisles. Hopefully, the owner of the supermarket did a good job organizing their shop, because you can usually expect to find similar things in the same aisle. A supermarket aisle is like a table. A database can have many tables, just like a supermarket can have many aisles. Another way to think of a table is like an excell spreadsheet, full of rows and columns, that stores information. In fact, a table looks just like a excel spreadsheet. Making a new table is slightly more challenging than making a new database, so lets move on with our metaphore before we get into the nitty-gritty details!

Within each aisle, there are many products. Just like an excel spreadsheet has many rows. In SQL, this is called a record. Aisles have products, spreadsheets have rows, tables have records.

Each product has nutritional information, and you are likely to find the same information on every box, can, and package in the supermarket on that product's Nutrition Information sticker. Each of these pieces of information could be stored in a column on our table.

## How do I make a new table?
The general syntax for making a new table is as follows:

```
CREATE TABLE table_name(
  column_name TYPE,
  column_name TYPE
);
```

Before you make a new table, you should already know all the columns and the type of information that each column will be storing. Adding more columns is relatively easy after the table has been made, but deleting or renaming columns is tricky, so it's best not to try it. 

SQL pretty universally recognizes four different data types:
* **TEXT** - any alphanumeric characters to be displayed as text
* **INTEGER** - whole numbers only; no letters, no special characters, and no decimals
* **REAL** - this one allows decimals, but only 15 characters
* **BLOB** - generally used for storing binary data. I mention it because it exists, not because it's common.

Some SQL engines recognize more data types, like *DATE*, but the above four are the standard set.

One of the best ways to ensure that each record is unique, is to give each one a unique id number. The easies way to do this is to let SQL do it for you! Utilizing the "Primary Key" technique has SQL automatically assign the next integer to each new record created. The syntax would look like this: `id INTEGER PRIMARY KEY`

So lets go with my dinosaur example. I already created a database for storing information about dinosaurs like this: `CREATE DATABASE dinosaurs;`. Now, let's make a table to represent the carnivorous dinosaurs!

```
CREATE TABLE carnivores(
  id INTEGER PRIMARY KEY,
  name TEXT,
  name_meaning TEXT,
  weight_in_lbs INTEGER
);
```

Woah! Now I have a table called "carnivores" that has 4 columns: id, name, name_meaning, and weight_in_tons. Notice how my column names do a good job of describing what kind of information you would expect to find about each dinosaur. SQL doesn't care what name you give it, but other programmers looking to work with your tables are much happier if they don't have to do a lot of work figuring out what column names mean!

## Don't you want more columns than that?
Oh man! You're right! I forgot that I wanted my carnivores table to also keep track of the dinos' length and height!

No worries! The syntax for adding a column looks like this:

```
ALTER TABLE table_name ADD COLUMN column_name DATA TYPE;
```

So I would craft the following SQL statements:
```
ALTER TABLE carnivores ADD COLUMN length_in_ft REAL;
ALTER TABLE carnivores ADD COLUMN height_in_ft REAL;
```

*While weight for dinosaurs tends to be big numbers like 32000, length and height stay under 100 ft; I figured I could safely use integers for weight, but I wanted more specificity for length and height so I'm saying those columns can use decimals by using the REAL data-type.*

## How do we put information in one of these tables?
The syntax for inserting info into a table:

```
INSERT INTO table_name(column_name, another_column)
VALUES('a value', 'the next value');
```

It is worth noting that we don't need to insert the id number, the table does that for us!

So lets put in everyone's favorite dinosaur!

```
INSERT INTO carnivores(name, name_meaning, weight_in_lbs, length_in_ft, height_in_ft)
VALUES ('Tyrannosaurus Rex', 'Tyrant Lizard', 15000, 40, 15);
```

Now, let's say I want to put in a few more dinosaurs, but I don't want to type out the whole thing each time! Don't worry, there's a shortcut! Look:

```
INSERT INTO carnivores(name, name_meaning, weight_in_lbs, length_in_ft, height_in_ft)
VALUES ('Allosaurus', 'Different Lizard', 3300, 28, NULL),
       ('Velociraptor', 'swift seizer', 33, 7, 0.5);
```

I added two dinosaurs for the price of one SQL statement! And, because my research returned inconclusive about an Allosaurus' height, I put in NULL as a placeholder for the missing values.

## So my table is all set, how do I get information?
Queries are statements used to retrieve information from a table. They generally look like this:

```
SELECT column_name, or_multiple_columns FROM table_name WHERE one_of_the_columns = value;
```

The big words here are: 
* `SELECT`, which tells the program we want it to find some info for us; 
* `FROM`, which tells the program which table we want it to look in; 
* `WHERE`, which states the condition that the program needs to match. 
 
Here are a few situations I might want to craft a select statement for:

1. I want to know the weight of a T-rex?
```
SELECT weight_in_lbs FROM carnivores WHERE name = "Tyrannosaurus Rex";
-> 15000
```

2. Or maybe I want to know which dinosaurs are longer than 10 feet? We can have SELECT give us multiple columns of information by separating them with a comma (`,`).
```
SELECT name, lenght_in_ft FROM carnivores WHERE lenght_in_ft > 10;
-> "Tyrannosaurus Rex", 40  -> "Allosaurus", 28
```

3. Or how about the name of the first dinosaur I inserted?
```
SELECT name FROM carnivores WHERE id =1;
-> "Tyrannosaurus Rex"
```

4. Or what if I want to see all of the information for the dinosaurs I didn't know the height of?
```
SELECT * FROM carnivores WHERE height_in_ft IS NULL;
-> 2, "Allosaurus", "DIfferent Lizard", 3300, 28, NULL"
```

*The asterisk selects all columns! It's a good shortcut to know!*

## Oh, wait! I know how tall Allosaurus is!

You do? Why didn't you say so? Woah, 16.5 feet! Awesome! Let's update the data in our table :) A basic updating statement might look like:

```
UPDATE table_name SET column = value WHERE column = value;
```

The `SET` is where you enter your new info, the `WHERE` tells SQL which record you are looking to update.

Let's update our carnivores table:

```
UPDATE carnivores SET height_in_ft = 16.5 WHERE name = "Allosaurus";
```

## I didn't know we could update, so I just made a brand new record with the complete information... Is that ok?

Well, technically it works, but now you have two records, one with the id `2` and a `NULL` height and the other with the id `4` and `16.5` height. Other than that, those records are identical and - conceptually - are referencing the same thing. So we really only want one record with the correct information. Why don't we delete that new one you made and just update the old one instead. Heres the syntax for a SQL delete statement:

```
DELETE FROM table_name WHERE column = value;
```

The `WHERE` clause is the most important one here. It tells the program which record to delete. If I crafted the clause as `WHERE name = "Allosaurus"` both of our Allosaurus records would be deleted. Better to use something that you know is unique to identify the record you want deleted. Luckily, we set up our table with `id INTEGER PRIMARY KEY`, which makes sure each row has a unique id number. Our duplicate has the id `4`.

```
DELETE FROM carnivores WHERE id = 4;
```

## Is that all SQL can do?
No way! SQL has many tricks to help you find, process, and control your information. Lets look at a few examples:

#### `LIMIT`

We have two dinosaurs longer than 10 feet, but what if I only wanted SQL to tell me the name of the first one it finds? That's a good place for us to use the `LIMIT` keyword. It limits the number of responses the computer will give me.

```
SELECT name FROM carnivores WHERE length_in_ft > 10 LIMIT 1;
-> "Tyrannosaurus Rex"
```

#### `ORDER BY`

Or, maybe I want to get the dinosaurs in height order for a photoshoot? SQL has an `ORDER BY` keyword for us. We can order them `ASC` for ascending or `DESC` for descending. This will put them in alphabetical or numerical order.

```
SELECT name FROM carnivores ORDER BY height_in_ft ASC;
-> "Velociraptor"  -> "Allosaurus"  -> "Tyrannosaurus Rex"
```

*Fun tip: To find the shortest dinosaur, try ordering them by height and then limiting the number of results to 1*

#### `BETWEEN ... AND`

SQL can also recognize ranges using the `BETWEEN ... AND` keywords. Here's a convoluted way to return Allosaurus' name's meaning:

```
SELECT name_meaning FROM carnivores WHERE weight_in_lbs BETWEEN 40 AND 5000;
```

#### `GROUP BY`

I just discovered a new dinosaur:

```
INSERT INTO carnivores(name, name_meaning, weight_in_lbs, length_in_ft, height_in_ft)
VALUES ('Johnasaurus', 'Coolest Dino', 13000, 35, 15);
```

It's not quite as heavy or as long as the T-rex, but it is the same height! Johnasaurus has the id of 4 because it was the 4th dino entered to the table, but I can use the `GROUP BY` keyword to put like things together, watch:

```
SELECT id, name FROM carnivores GROUP BY height_in_ft;
-> 1, "Tyrannosaurus Rex"  -> 4, "Johnasaurus"  -> 2, "Allosaurus"  -> 3, "Velociraptor"
```

## What about Aggregate Functions? What are those?
Aggregate functions are SQL statements that let you perform calculations on the data. There are 5 main aggregate functions. Let's look at them:

#### `AVG`

The AVG function finds the average value of a column. You pass the column name of the column you want averaged to the AVG function in parentheses like this: `AVG(column_name)`. Here's an example where I select the average dinosaur weight from my carnivores table: *remember I added a 4th dino, if you're checking the math!*

```
SELECT AVG(weight_in_lbs) FROM carnivores;
-> 7833.25
```

*PRO TIP! There are situations where SQL also displays the column names for your selected data. The column name when you are using the AVG function is AVG(column_name) which looks pretty ugly... the `AS` keyword lets you rename the column for display purposes only. So it might be more beneficial for me to display my average as such: `SELECT AVG(weight_in_lbs) AS average_weight FROM carnivores;`

#### `SUM`

This function adds up all the values of the column you select. Pass the SUM function the column name the same way you do for AVG. Heres how I would find the total weight for all of the dinos over 12000 lbs:

```
SELECT SUM(weight_in_lbs) FROM carnivores WHERE weight_in_lbs > 12000;
```

**NOPE, this doesn't work!** Aggregate functions don't work with the WHERE clause. Instead, use `HAVING`. It works the same way as where, but is used with aggregate functions. Here's a working statement:

```
SELECT SUM(weight_in_lbs) FROM carnivores HAVING weight_in_lbs > 12000;
->28000
```


#### `MIN`

MIN finds the smallest value in the column you pass it. 

*One note: All these aggregate functions only return a number, and no other information*. 

So if I wanted to find out which dinosaur is the shortest, and I put only `MIN(height_in_ft)` into my select statement, it would just return 0.5... not very useful if we want to know *which* dino is that height. Here's a select statement that solves that problem:

```
SELECT name, MIN(height_in_ft) FROM carnivores;
-"Velociraptor", 0.5
```

#### `MAX`

MAX is the opposite of MIN.

```
SELECT name, MAX(height_in_ft) FROM carnivores;
-> "Tyrannosaurus Rex",  15
```

#### `COUNT`

The COUNT function returns the number of rows that meet a condition. Unlike the other aggregate functions, COUNT uses a WHERE clause. 

How many carnivores are longer than 30 ft?

```
SELECT COUNT(name) FROM carnivores WHERE length > 30;
-> 2
```

*Many times, the column name you pass COUNT is irrelevant. In the example, the return value would be the same if I used `SELECT COUNT(name_meaning)`*

Also, that WHERE clause is optional. Leaving it out just counts all the rows in the table.

```
SELECT COUNT(*) FROM carnivores
-> 4
```

## Great, thanks for your help! I get it now :)

Awesome! Thank's for reading!





	

