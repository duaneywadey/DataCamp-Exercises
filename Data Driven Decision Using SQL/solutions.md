# Data Driven Decision Using SQL

## First account for each country.
Conduct an analysis to see when the first customer accounts were created for each country.


100 XP
Create a table with a row for each country and columns for the country name and the date when the first customer account was created.
Use the alias first_account for the column with the dates.
Order by date in ascending order.

```SQL
SELECT country, min(date_account_start) AS first_account
FROM customers
GROUP BY country
ORDER BY first_account;
```

## Average movie ratings
For each movie the average rating, the number of ratings and the number of views has to be reported. Generate a table with meaningful column names.

 1/4
25 XP
Group the data in the table renting by movie_id and report the ID and the average rating.

```SQL
SELECT movie_id, avg(rating)    -- Calculate average rating per movie
FROM renting
group by movie_id;
```

Add two columns for the number of ratings and the number of movie rentals to the results table.
Use alias names avg_rating, number_rating and number_renting for the corresponding columns.

```SQL
SELECT movie_id, 
       AVG(rating) AS avg_rating, -- Use as alias avg_rating
       count(rating) AS number_rating, -- Add column for number of ratings with alias number_rating
       count(renting_id) as number_renting -- Add column for number of movie rentals with alias number_renting
FROM renting
GROUP BY movie_id;
```

Order the rows of the table by the average rating such that it is in decreasing order.
Observe what happens to NULL values.

```SQL
SELECT movie_id, 
       AVG(rating) AS avg_rating,
       COUNT(rating) AS number_ratings,
       COUNT(*) AS number_renting
FROM renting
GROUP BY movie_id
order by avg_rating desc; -- Order by average rating in decreasing order
```

## Average rating per customer
Similar to what you just did, you will now look at the average movie ratings, this time for customers. So you will obtain a table with the average rating given by each customer. Further, you will include the number of ratings and the number of movie rentals per customer. You will report these summary statistics only for customers with more than 7 movie rentals and order them in ascending order by the average rating.


0 XP
Group the data in the table renting by customer_id and report the customer_id, the average rating, the number of ratings and the number of movie rentals.
Select only customers with more than 7 movie rentals.
Order the resulting table by the average rating in ascending order.

```sql
SELECT customer_id,  -- Report the customer_id
       AVG(rating), -- Report the average rating per customer
       COUNT(rating), -- Report the number of ratings per customer
       COUNT(*) -- Report the number of movie rentals per customer
FROM renting
GROUP BY customer_id
HAVING COUNT(*) > 7 -- Select only customers with more than 7 movie rentals
ORDER BY AVG(rating); -- Order by the average rating in ascending order
```


## Join renting and customers
For many analyses it is necessary to add customer information to the data in the table renting.

 1/3
35 XP
Augment the table renting with all columns from the table customers with a LEFT JOIN.
Use as alias' for the tables r and c respectively.

```sql
SELECT * -- Join renting with customers
FROM renting r
LEFT JOIN customers c
ON r.customer_id = c.customer_id;
```

Select only records from customers coming from Belgium.

```sql
SELECT *
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
WHERE country = 'Belgium'; -- Select only records from customers coming from Belgium
```

Average ratings of customers from Belgium.

```sql
SELECT avg(rating) -- Average ratings of customers from Belgium
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
WHERE c.country='Belgium';
```

## Aggregating revenue, rentals and active customers
The management of MovieNow wants to report key performance indicators (KPIs) for the performance of the company in 2018. They are interested in measuring the financial successes as well as user engagement. Important KPIs are, therefore, the profit coming from movie rentals, the number of movie rentals and the number of active customers.

 1/3
35 XP
First, you need to join movies on renting to include the renting_price from the movies table for each renting record.
Use as alias' for the tables m and r respectively.

```SQL
SELECT *
FROM renting AS r
left join movies AS m -- Choose the correct join statment
ON r.movie_id = m.movie_id;
```

Calculate the revenue coming from movie rentals, the number of movie rentals and the number of customers who rented a movie.

```SQL
SELECT 
	SUM(m.renting_price), -- Get the revenue from movie rentals
	COUNT(*), -- Count the number of rentals
	COUNT(DISTINCT r.customer_id) -- Count the number of customers
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id;
```

Now, you can report these values for the year 2018. Calculate the revenue in 2018, the number of movie rentals and the number of active customers in 2018. An active customer is a customer who rented at least one movie in 2018.

```SQL
SELECT 
	SUM(m.renting_price), 
	COUNT(*), 
	COUNT(DISTINCT r.customer_id)
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id
-- Only look at movie rentals in 2018
WHERE date_renting BETWEEN '2018-01-01' AND '2018-12-31';
```

## Movies and actors
You are asked to give an overview of which actors play in which movie.


100 XP
Create a list of actor names and movie titles in which they act. Make sure that each combination of actor and movie appears only once.
Use as an alias for the table actsin the two letters ai.

```sql
SELECT m.title, -- Create a list of movie titles and actor names
       a.name
FROM actsin ac
LEFT JOIN movies AS m
ON m.movie_id = ac.movie_id
LEFT JOIN actors AS a
ON a.actor_id = ac.actor_id;
```

## Income from movies
How much income did each movie generate? To answer this question subsequent SELECT statements can be used.

 1/3
35 XP
3
Use a join to get the movie title and price for each movie rental.

```sql
SELECT m.title, m.renting_price
-- Use a join to get the movie title and price for each movie rental
       
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id;
```

Report the total income for each movie.
Order the result by decreasing income.

```sql
SELECT rm.title, -- Report the income from movie rentals for each movie 
       SUM(rm.renting_price) AS income_movie
FROM
       (SELECT m.title, 
               m.renting_price
       FROM renting AS r
       LEFT JOIN movies AS m
       ON r.movie_id=m.movie_id) AS rm
GROUP BY rm.title
ORDER BY income_movie DESC; -- Order the result by decreasing income
```
## Age of actors from the USA
Now you will explore the age of American actors and actresses. Report the date of birth of the oldest and youngest US actor and actress.


100 XP
Create a subsequent SELECT statements in the FROM clause to get all information about actors from the USA.
Give the subsequent SELECT statement the alias a.
Report for actors from the USA the year of birth of the oldest and the year of birth of the youngest actor and actress.

```sql
SELECT a.gender, -- Report for male and female actors from the USA 
       min(a.year_of_birth), -- The year of birth of the oldest actor
       max(a.year_of_birth) -- The year of birth of the youngest actor
FROM
   (SELECT * FROM actors WHERE nationality = 'USA') as a -- Use a subsequen SELECT to get all information about actors from the USA
  
    -- Give the table the name a
GROUP BY a.gender;
```
## Identify favorite movies for a group of customers
Which is the favorite movie on MovieNow? Answer this question for a specific group of customers: for all customers born in the 70s.

 1/4
25 XP
Augment the table renting with customer information and information about the movies.
For each join use the first letter of the table name as alias.

```SQL
SELECT *
FROM renting AS r
LEFT JOIN customers c   -- Add customer information
on r.customer_id = c.customer_id
LEFT JOIN movies m   -- Add movie information
on r.movie_id = m.movie_id;
```

Select only those records of customers born in the 70s.

```SQL
SELECT *
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01'  AND '1979-12-31'; -- Select customers born in the 70s
```

For each movie, report the number of times it was rented, as well as the average rating. Limit your results to customers born in the 1970s.

```sql
SELECT m.title, 
count(*), -- Report number of views per movie
avg(rating) -- Report the average rating per movie
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'
group by m.title;
```

Remove those movies from the table with only one rental.
Order the result table such that movies with highest rating come first.

```sql
SELECT m.title, 
COUNT(*),
AVG(r.rating)
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'
GROUP BY m.title
HAVING count(r.renting_id) > 1 -- Remove movies with only one rental
ORDER BY AVG(r.rating); -- Order with highest rating first
```

## Identify favorite actors for Spain
You're now going to explore actor popularity in Spain. Use as alias the first letter of the table, except for the table actsin use ai instead.

 1/3
1 XP
2
3
Augment the table renting with information about customers and actors.

```sql
SELECT *
FROM renting as r
LEFT JOIN customers AS c         -- Augment table renting with information about customers
ON r.customer_id = c.customer_id 
LEFT JOIN actsin as ai           -- Join the table renting with the table actsin
ON r.movie_id = ai.movie_id
LEFT JOIN actors as a            -- Augment table renting with information about actors
ON ai.actor_id = a.actor_id;
```

Report the number of movie rentals and the average rating for each actor, separately for male and female customers.
Report only actors with more than 5 movie rentals.

```sql
SELECT a.name,  c.gender,
       COUNT(*) AS number_views, 
       AVG(r.rating) AS avg_rating
FROM renting as r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
LEFT JOIN actsin as ai
ON r.movie_id = ai.movie_id
LEFT JOIN actors as a
ON ai.actor_id = a.actor_id

GROUP BY a.name, c.gender -- For each actor, separately for male and female customers
HAVING AVG(r.rating) IS NOT NULL 
  AND COUNT(*) > 5 -- Report only actors with more than 5 movie rentals
ORDER BY avg_rating DESC, number_views DESC;
```

Now, report the favorite actors only for customers from Spain.

```sql
SELECT a.name,  c.gender,
       COUNT(*) AS number_views, 
       AVG(r.rating) AS avg_rating
FROM renting as r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
LEFT JOIN actsin as ai
ON r.movie_id = ai.movie_id
LEFT JOIN actors as a
ON ai.actor_id = a.actor_id
WHERE c.country = 'Spain' -- Select only customers from Spain
GROUP BY a.name, c.gender
HAVING AVG(r.rating) IS NOT NULL 
  AND COUNT(*) > 5 
ORDER BY avg_rating DESC, number_views DESC;
```

## KPIs per country
In chapter 1 you were asked to provide a report about the development of the company. This time you have to prepare a similar report with KPIs for each country separately. Your manager is interested in the total number of movie rentals, the average rating of all movies and the total revenue for each country since the beginning of 2019.

 1/2
50 XP
Augment the table renting with information about customers and movies.
Use as alias the first latter of the table name.
Select only records about rentals since beginning 

```SQL
SELECT *
FROM renting r -- Augment the table renting with information about customers
LEFT JOIN customers c
on r.customer_id = c.customer_id
LEFT JOIN movies m -- Augment the table renting with information about movies
on r.movie_id = m.movie_id
WHERE r.date_renting >= '2019-01-01'; -- Select only records about rentals since the beginning of 2019
```

Calculate the number of movie rentals.
Calculate the average rating.
Calculate the revenue from movie rentals.
Report these KPIs for each country.

```SQL
SELECT 
       c.country,                    -- For each country report
       COUNT(renting_id) AS number_renting, -- The number of movie rentals
       AVG(rating) AS average_rating, -- The average rating
       SUM(renting_price) AS revenue         -- The revenue from movie rentals
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE date_renting >= '2019-01-01'
GROUP BY c.country;
```

## Often rented movies
Your manager wants you to make a list of movies excluding those which are hardly ever watched. This list of movies will be used for advertising. List all movies with more than 5 views using a nested query which is a powerful tool to implement selection conditions.

 1/2
0 XP
Select all movie IDs which have more than 5 views.

```SQL
SELECT movie_id -- Select movie IDs with more than 5 views
FROM renting
GROUP BY movie_id
HAVING COUNT(*) > 5
```

Select all information about movies with more than 5 views.

```SQL
SELECT *
FROM movies
WHERE movie_id IN    -- Select movie IDs from the inner query
  (SELECT movie_id
  FROM renting
  GROUP BY movie_id
  HAVING COUNT(*) > 5)
```

## Frequent customers
Report a list of customers who frequently rent movies on MovieNow.


70 XP
List all customer information for customers who rented more than 10 movies.

```SQL
SELECT *
FROM customers
WHERE customer_id IN            -- Select all customers with more than 10 movie rentals
  (SELECT customer_id
  FROM renting
  GROUP BY customer_id
  HAVING COUNT(renting_id) > 10);
```
## Movies with rating above average
For the advertising campaign your manager also needs a list of popular movies with high ratings. Report a list of movies with rating above average.

 2/3
1 XP
2
3
Select movie IDs and calculate the average rating of movies with rating above average.

```sql
SELECT movie_id,  -- Select movie IDs and calculate the average rating 
       AVG(rating)
FROM renting
GROUP BY movie_id
HAVING AVG(rating) >   -- Of movies with rating above average
  (SELECT AVG(rating)
  FROM renting);
```

## Movies with rating above average
For the advertising campaign your manager also needs a list of popular movies with high ratings. Report a list of movies with rating above average.

 3/3
30 XP
3
The advertising team only wants a list of movie titles. Report the movie titles of all movies with average rating higher than the total average.

```sql
SELECT title -- Report the movie titles of all movies with average rating higher than the total average
FROM movies
WHERE movie_id IN
  (SELECT movie_id
   FROM renting
     GROUP BY movie_id
     HAVING AVG(rating) > 
    (SELECT AVG(rating)
     FROM renting));
```

