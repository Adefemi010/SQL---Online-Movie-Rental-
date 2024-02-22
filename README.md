# SQL---Online-Movie-Rental
## Question 1:

Create a list of all the different (distinct) replacement costs of the films.
Question: What's the lowest replacement cost?

## Answer
  SELECT DISTINCT replacement_cost 
  FROM film
  ORDER BY 1;

Question 2:
Write a query that gives an overview of how many films have replacements costs in the following cost ranges
1.	low: 9.99 - 19.99
2.	medium: 20.00 - 24.99
3.	high: 25.00 - 29.99
Question: How many films have a replacement cost in the "low" group?
Answer
SELECT 
    CASE 
        WHEN replacement_cost BETWEEN 9.99 AND 19.99 THEN 'low'
        WHEN replacement_cost BETWEEN 20 AND 24.99 THEN 'medium'
        ELSE 'high'
    END as cost_range,
    COUNT(*) AS count_of_films
FROM 
    film
GROUP BY 
    cost_range;
Question 3:
Create a list of the film titles including their title, length, and category name ordered descendingly by length. Filter the results to only the movies in the category 'Drama' or 'Sports'.
Question: In which category is the longest film and how long is it?
SELECT 
    f.title,
    c.name,
    f.length
FROM 
    film f
LEFT JOIN 
    film_category fc ON f.film_id = fc.film_id
LEFT JOIN 
    category c ON c.category_id = fc.category_id
WHERE 
    c.name IN ('Sports', 'Drama')
ORDER BY 
    f.length DESC;
Question 4:
Create an overview of how many movies (titles) there are in each category (name).
Question: Which category (name) is the most common among the films?
SELECT
    c.name,
    COUNT(f.title) AS title_count
FROM 
    film f
INNER JOIN 
    film_category fc ON f.film_id = fc.film_id
INNER JOIN 
    category c ON c.category_id = fc.category_id
GROUP BY 
    c.name
ORDER BY 
    title_count DESC;

Question 5:
Task: Create an overview of the actors' first and last names and in how many movies they appear in.
Question: Which actor is part of most movies??
SELECT 
    a.first_name,
    a.last_name,
    COUNT(*) AS film_count
FROM 
    actor a
LEFT JOIN 
    film_actor fa ON fa.actor_id = a.actor_id
LEFT JOIN 
    film f ON fa.film_id = f.film_id
GROUP BY 
    a.first_name, a.last_name
ORDER BY 
    film_count DESC;

Question 6:
Create an overview of the addresses that are not associated to any customer.
Question: How many addresses are that?
SELECT *
FROM address a
LEFT JOIN customer c ON c.address_id = a.address_id
WHERE c.address_id IS NULL;
Question 7:
Task: Create the overview of the sales to determine the from which city (we are interested in the city in which the customer lives, not where the store is) most sales occur.
Question: What city is that and how much is the amount?
SELECT 
    ci.city,
    SUM(p.amount) AS total_amount
FROM 
    payment p
LEFT JOIN 
    customer c ON p.customer_id = c.customer_id
LEFT JOIN 
    address a ON c.address_id = a.address_id
LEFT JOIN 
    city ci ON a.city_id = ci.city_id
GROUP BY 
    ci.city
ORDER BY 
    ci.city DESC;

Question 8:
Task: Create an overview of the revenue (sum of amount) grouped by a column in the format "country, city".
Question: Which country, city has the least sales?
SELECT 
    co.country || ', ' || ci.city AS country_city,
    SUM(p.amount) AS total_amount
FROM 
    payment p
LEFT JOIN 
    customer c ON p.customer_id = c.customer_id
LEFT JOIN 
    address a ON c.address_id = a.address_id
LEFT JOIN 
    city ci ON a.city_id = ci.city_id
LEFT JOIN 
    country co ON ci.country_id = co.country_id
GROUP BY 
    co.country, ci.city
ORDER BY 
    total_amount ASC;

Question 9:
Level: Difficult
Topic: Uncorrelated subquery
Task: Create a list with the average of the sales amount each staff_id has per customer.
Question: Which staff_id makes on average more revenue per customer?
SELECT 
    staff_id,
    ROUND(AVG(total), 2) AS avg_amount
FROM (
    SELECT 
        SUM(amount) AS total,
        customer_id,
        staff_id
    FROM 
        payment
    GROUP BY 
        customer_id, staff_id
) AS a
GROUP BY 
    staff_id;

Question 10:
Level: Difficult to very difficult
Topic: EXTRACT + Uncorrelated subquery
Task: Create a query that shows average daily revenue of all Sundays.
SELECT 
    AVG(total) AS avg_amount,
    weekday
FROM (
    SELECT
        SUM(amount) as total,
        DATE(payment_date) AS payment_date,
        EXTRACT(dow FROM payment_date) as weekday
    FROM 
        payment
    WHERE 
        EXTRACT(dow FROM payment_date) = 0
    GROUP BY 
        DATE(payment_date), weekday
) AS daily
GROUP BY 
    weekday;

Question 11:
Task: Create a list of movies - with their length and their replacement cost - that are longer than the average length in each replacement cost group.
Question: Which two movies are the shortest on that list and how long are they?
SELECT 
    f1.title,
    f1.length
FROM 
    film f1
WHERE 
    f1.length > (
        SELECT AVG(f2.length)
        FROM film f2
        WHERE f1.replacement_cost = f2.replacement_cost
    )
ORDER BY 
    f1.length ASC;



