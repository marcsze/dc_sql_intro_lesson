# Introduction to SQL

## General Setup

If individuals don't want to use the mozilla firefox extension then we first need to check if the have SQLite installed.  
This can be done by typing in `which sqlite3` into the terminal.  If none comes up and then install instructions can be found
[here](https://www.tutorialspoint.com/sqlite/sqlite_installation.htm).

**Load instructions for Firefox plugin**
* Go to menu and select SQL icon button
* Select Database option and connect database
  * choose the portal_mammals.sqlite file
* Once loaded navigate to the Execute SQL tab

**Personal SQLite Instructions**

To load the data set use the command (EDIT - This causes problems downstream):  
`attach "UMichiganWorkStuff/Teaching_Courses/sql_dc_lesson/portal_mammals.sqlite" as db1;`

Better way to load data is with this commonad:  
`sqlite3 "UMichiganWorkStuff/Teaching_Courses/sql_dc_lesson/portal_mammals.sqlite"`

**To make sure no lingering VIEWS are in the database use `DROP VIEW` command.**  
**A very dangerous command would be the `DROP TABLE` command (removes everything).**

To quit use command  
`.quit`

## Basic Queries

**First Challenge Question**  
Write a query that returns The year, month, day, species_id and weight in mg

```sqlite3
SELECT year, month, day, species_id, ROUND(weight * 1000, 2)
FROM surveys;
```

**Second Challenge Question**  
Write a query that returns the day, month, year, species_id, and weight (in kg) for individuals 
caught on Plot 1 that weigh more than 75 g
  
```sqlite3
SELECT day, month, year, species_id, ROUND(weight / 1000, 4)
FROM surveys
WHERE (plot_id = 1) AND (weight > 75);
```

**Sorting**  
Write a query that returns year, species_id, and weight in kg from the surveys table, sorted with the largest weights at the top.

```sqlite3
SELECT year, species_id, ROUND(weight / 1000, 2)
FROM surveys
ORDER BY weight DESC;
```

**Order of Execution**  
Let’s try to combine what we’ve learned so far in a single query. Using the surveys table write a query to display the three date fields, species_id, and weight in kilograms (rounded to two decimal places), for individuals captured in 1999, ordered alphabetically by the species_id. Write the query as a single line, then put each clause on its own line, and see how more legible the query becomes!  

*All One Line*
```sqlite3
SELECT month, day, year, species_id, ROUND(weight / 1000, 2) FROM surveys WHERE (year = 1999) ORDER BY species_id ASC;
```
*Spaced Over Multiple Lines*  
```sqlite3
SELECT month, day, year, species_id, ROUND(weight / 1000, 2)
FROM surveys
WHERE (year = 1999)
ORDER BY species_id ASC;
```

## Aggregation

**COUNT and GROUP BY**  
Write a query that returns: total weight, average weight, and the min and max weights for all animals caught over the duration of the survey. Can you modify it so that it outputs these values only for weights between 5 and 10?  

*Base Answer*
```sqlite3
SELECT SUM(weight), AVG(weight), MIN(weight), MAX(weight)
FROM surveys;
```  

*Weights Between 5 and 10*
```sqlite3
SELECT SUM(weight), AVG(weight), MIN(weight), MAX(weight)
FROM surveys
WHERE (weight > 5) AND (weight < 10);
```

Write queries that return:
* How many individuals were counted in each year. a) in total; b) per each species.

a)
```sqlite3
SELECT species_id, COUNT(*)
FROM surveys
GROUP BY year;
```

b)
```sqlite3
SELECT species_id, COUNT(*)
FROM surveys
GROUP BY year, species_id;
```

* Average weight of each species in each year. Can you modify the above queries combining them into one?

```sqlite3
SELECT AVG(weight), species_id
FROM surveys
GROUP BY species_id, year;
```

**The "HAVING" keyword**

Write a query that returns, from the species table, the number of genus in each taxa, only for the taxa with more than 10 genus.

```sqlite3
SELECT genus, COUNT(genus) AS totals
FROM species
GROUP BY taxa
HAVING totals > 10;
```

**Saving Queries for future use**

Write a query that returns the number of each species caught in each year sorted from most often caught species to the least occurring ones within each year starting from the most recent records. Save this query as a VIEW.

```sqlite3
CREATE VIEW challenges AS
SELECT species_id, COUNT(species_id) AS totals
FROM surveys
GROUP BY year
ORDER BY totals DESC, year ASC;
```

## Joins and Aliases

Write a query that returns the genus, the species, and the weight of every individual captured at the site

```sqlite3
SELECT species.genus, species.species, surveys.weight, surveys.plot_id
FROM surveys
JOIN species
ON surveys.species_id = species.species_id
GROUP BY plot_id, genus;
```

Re-write the original query to keep all the entries present in the surveys table. How many records are returned by this query?

```sqlite3
SELECT COUNT(*)
FROM surveys
LEFT JOIN species
USING (species_id);
```
Total is the same as the surveys count (35549)

Count the number of records in the surveys table that have a NULL value in the species_id column.

```sqlite3
SELECT COUNT(*)
FROM surveys
WHERE species_id IS NULL;
```




