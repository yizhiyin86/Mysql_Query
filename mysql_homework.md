# Mysql Yizhi Yin
Here I practiced SQL queries to retrieve data from sakila database.

## Question 1a. Display the first and last names of all actors from the table `actor`. 
```sql
-- switch database to sakila
USE sakila;
SELECT first_name,last_name FROM actor;

 <img src="https://github.com/yizhiyin86/Mysql_homework/blob/master/screenshot/q1a_actor_names.png" alt="screenshot of q1a">
```
## Question 1b. Display the first and last name of each actor in a single column in upper case letters. Name the column `Actor Name`
```sql
-- i am going to concat first_name and last_name with a separator ',' they are already in upper case
SELECT CONCAT_WS (',', first_name,last_name) AS Actor_Name
FROM actor;
![q1b](screenshot/q1b_Actor_Name.png)
```

## Question 2a. You need to find the ID number, first name, and last name of an actor, of whom you know only the first name, "Joe." What is one query would you use to obtain this information?
```sql
SELECT first_name, last_name, actor_id 
FROM actor
WHERE first_name='Joe';
![q2a](screenshot/q2a_Joe.png)
```

## Question 2b. Find all actors whose last name contain the letters `GEN`:
```sql
SELECT * FROM actor
WHERE last_name LIKE "%GEN%";
![q2b](screenshot/q2b_GEN.png)
```

## Question 2c. Find all actors whose last names contain the letters `LI`. This time, order the rows by last name and first name, in that order:
```sql
-- the question does not specify if we order ascedning or descending so I go with ASC
SELECT * FROM actor
WHERE last_name LIKE"%LI%"
ORDER BY last_name ASC, first_name ASC;
![q2c](screenshot/q2c_LI_order.png)
```

## Question 2d. Using `IN`, display the `country_id` and `country` columns of the following countries: Afghanistan, Bangladesh, and China:
```sql
SELECT country_id, country
FROM country
WHERE country IN ('Afghanistan', 'Bangladesh', 'China');

![q2D](screenshot/q2D_IN.png)
```
## Question 3a. Add a `middle_name` column to the table `actor`. Position it between `first_name` and `last_name`. Hint: you will need to specify the data type.
```sql
ALTER TABLE actor ADD middle_name VARCHAR(50) AFTER first_name;
-- uncomment the line below to see the changed table
-- SELECT * FROM actor LIMIT 5;
![q3a](screenshot/q3a_middle_name.png)
```

## Question 3b. You realize that some of these actors have tremendously long last names. Change the data type of the `middle_name` column to `blobs`.
```sql
-- I do not understand we change the type of middle_name if the last name is too long, but whatever
ALTER TABLE actor MODIFY middle_name BLOB;
-- uncomment the line below to see the field type has been changed
-- DESCRIBE actor;
![q3b](screenshot/q3b_BLOB.png)
```

## Question 3c. Now delete the `middle_name` column.
```sql
ALTER TABLE actor DROP middle_name;
-- uncomment the line below to see the field type has been changed
-- DESCRIBE actor;
![q3c](screenshot/q3c_DROP_mid_name.png)
```

## Question 4a. List the last names of actors, as well as how many actors have that last name.
```sql
SELECT 
    last_name, 
    COUNT(*) AS Last_Name_Count
FROM 
	actor
GROUP BY
	last_name;

![q4a](screenshot/q4a_unique_count.png)
```
## Question 4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors.
```sql
SELECT 
    last_name, 
    COUNT(*) AS Last_Name_Count
FROM 
	actor
GROUP BY
	last_name
HAVING COUNT(*)>=2;

![q4b](screenshot/q4b_shared_unique_count.png)
```

## Question 4c. Oh, no! The actor `HARPO WILLIAMS` was accidentally entered in the `actor` table as `GROUCHO WILLIAMS`, the name of Harpo's second cousin's husband's yoga teacher. Write a query to fix the record.
```sql
UPDATE actor
SET first_name='HARPO'
WHERE first_name='GROUCHO' and last_name='WILLIAMS';
-- uncomment lines below to see the change
-- SELECT * FROM actor
-- WHERE first_name='HARPO' and last_name='WILLIAMS';
![q4c](screenshot/q4c_name_change.png)
```

## Question 4d. Perhaps we were too hasty in changing `GROUCHO` to `HARPO`. It turns out that `GROUCHO` was the correct name after all! In a single query, if the first name of the actor is currently `HARPO`, change it to `GROUCHO`. Otherwise, change the first name to `MUCHO GROUCHO`, as that is exactly what the actor will be with the grievous error. BE CAREFUL NOT TO CHANGE THE FIRST NAME OF EVERY ACTOR TO `MUCHO GROUCHO`, HOWEVER! (Hint: update the record using a unique identifier.)
```sql
UPDATE actor
SET 
    -- IF first_name='HARPO' change into 'GROUCHO' else 'MUCHO GROUCHO'
	first_name= IF(first_name= `HARPO`, 'GROUCHO', 'MUCHO GROUCHO')
WHERE 
	actor_id=172;

-- uncomment lines below to see the change   
-- SELECT * FROM actor
-- WHERE actor_id=172;
![q4d](screenshot/q4d_if_change.png)
```

## Question 5. You cannot locate the schema of the `address` table. Which query would you use to re-create it?
```sql
SHOW CREATE TABLE address;
![q5](screenshot/q5_show_table.png)
```

## Question 6a. Use `JOIN` to display the first and last names, as well as the address, of each staff member. Use the tables `staff` and `address`:
```sql
SELECT staff.first_name, staff.last_name, address. address
FROM staff 
INNER JOIN address
ON staff.address_id=address.address_id;
![q6a](screenshot/q6a_inner_join.png)
```

## Question 6b. Use `JOIN` to display the total amount rung up by each staff member in August of 2005. Use tables `staff` and `payment`.
```sql
SELECT s.first_name,s.last_name,SUM(p.amount) AS Sum_Aug
FROM staff AS s
INNER JOIN
	payment AS p
ON s.staff_id=p.staff_id
WHERE DATE(p.payment_date) BETWEEN '2005-08-01' AND '2005-08-31'
GROUP BY p.staff_id;
![q6b](screenshot/q6b_Aug_Payment.png)
```

## Question 6c. List each film and the number of actors who are listed for that film. Use tables `film_actor` and `film`. Use inner join.
```sql
SELECT f.title, COUNT(fa.actor_id) AS Actor_Count
FROM film AS f
INNER JOIN film_actor AS fa
ON f.film_id=fa.film_id
GROUP BY f.film_id;
![q6c](screenshot/q6c_actor_counts.png)
```

## Question 6d. How many copies of the film `Hunchback Impossible` exist in the inventory system?
```sql
SELECT COUNT(*) AS copy
FROM 
	inventory
WHERE 
	film_id=(SELECT film_id 
					FROM film
                    WHERE title='Hunchback Impossible'
                    );

![q6d](screenshot/q6d_suq_select.png)
```

## Question 6e. Using the tables `payment` and `customer` and the `JOIN` command, list the total paid by each customer. List the customers alphabetically by last name:
```sql
SELECT c.first_name,c.last_name,SUM(p.amount) AS Total_Payment_Customer
FROM customer AS c
INNER JOIN
	payment AS p
ON c.customer_id=p.customer_id
GROUP BY 
	p.customer_id
ORDER BY 
	c.last_name ASC;

![q6e](screenshot/q6e_customer_payment.png)
```

## Question 7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters `K` and `Q` have also soared in popularity. Use subqueries to display the titles of movies starting with the letters `K` and `Q` whose language is English.
```sql
SELECT title
FROM film
WHERE 
	(title LIKE "K%"  OR title LIKE "Q%") 
AND 
	(language_id=
				(SELECT language_id 
				FROM language
				WHERE name='English')
				);

![q7a](screenshot/q7a_K_Q.png)
```

## Question 7b. Use subqueries to display all actors who appear in the film `Alone Trip`.
```sql
SELECT first_name, last_name
FROM actor
WHERE actor_id 
IN 
	(SELECT actor_id 
	FROM film_actor
	WHERE film_id 
	IN 
		(SELECT film_id
		FROM film
		WHERE title='Alone Trip')
        );

![q7b](screenshot/q7b_sub_sub.png)
```

## Question 7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.
```sql
-- this problem ask to use join but it can also be solved by using where I did both ways
-- USE join to solve this problem
SELECT c.first_name, c.last_name,c.email
FROM customer AS c
INNER JOIN
	address as a
ON 
	c.address_id=a.address_id
INNER JOIN city 
ON 
	city.city_id=a.city_id
INNER JOIN country
ON
	city.country_id=country.country_id
WHERE 
	country='Canada';

-- USE SELECT WHERE
SELECT first_name, last_name,email
FROM customer
WHERE address_id
IN 
	(SELECT address_id 
	FROM address
	WHERE city_id 
	IN 
		(SELECT city_id 
					FROM city
					WHERE country_id 
					IN 
						(SELECT country_id
						FROM country
						WHERE country='Canada')
						) 
						);

![q7c](screenshot/q7c_use_join.png)
![q7c](screenshot/q7c_use_where.png)
```

## Question 7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as famiy films.
```sql
-- seems using join is faster than using where IN 
-- USE join to solve this problem
SELECT f.title
FROM film AS f
INNER JOIN
	film_category as fc
ON 
	fc.film_id=f.film_id
INNER JOIN
	category AS c
ON
	fc.category_id=c.category_id
WHERE c.name='Family';

-- USE SELECT WHERE IN
SELECT title
FROM film
WHERE film_id 
IN 
	(
		(SELECT film_id
		FROM film_category 
		WHERE category_id 
		IN 
			(SELECT category_id
			 FROM category
			 WHERE name='Family')
			)
	);

![q7d](screenshot/q7d_use_join.png)
![q7d](screenshot/q7d_use_where_in.png)
```

## Question 7e. Display the most frequently rented movies in descending order..
```sql
SELECT f.title, COUNT(r.rental_id) AS Counts
FROM film AS f
INNER JOIN inventory AS i
ON f.film_id=i.film_id
INNER JOIN rental AS r
ON r.inventory_id=i.inventory_id
GROUP BY f.film_id
ORDER BY Counts DESC;
![q7e](screenshot/q7e_most_rented.png)
```

## Question 7f. Write a query to display how much business, in dollars, each store brought in.
```sql
SELECT 
	a.address, s.store_id, SUM(p.amount) AS Store_Revenue
FROM 
	address as a
INNER JOIN 
	store as s
ON 
	a.address_id=s.address_id
INNER JOIN
	payment as p
ON 
	s.manager_staff_id=p.staff_id
GROUP BY
	s.store_id;

![q7f](screenshot/q7f_revenue_store.png)
```

## Question 7g. Write a query to display for each store its store ID, city, and country.
```sql
SELECT 
	s.store_id, c.city, co.country
FROM 
	store AS s
INNER JOIN
	address
ON 
	s.address_id=address.address_id
INNER JOIN
	city AS c
ON
	c.city_id=address.city_id
INNER JOIN
	country AS co
ON
	co.country_id=c.country_id;
![q7g](screenshot/q7g_store_city_country.png)
```

## Question 7h. List the top five genres in gross revenue in descending order. (**Hint**: you may need to use the following tables: category, film_category, inventory, payment, and rental.)
```sql
SELECT 
	c.name, SUM(p.amount) AS Sum_Category
FROM 
	category AS c
INNER JOIN 
	film_category AS fc
ON 
	c.category_id=fc.category_id
INNER JOIN 
	inventory AS i
ON 
	fc.film_id=i.film_id
INNER JOIN
	rental AS r
ON 
	i.inventory_id=r.inventory_id
INNER JOIN
	payment AS p
ON
	r.rental_id=p.rental_id
GROUP BY
	c.name
ORDER BY Sum_Category DESC
LIMIT 5;
![q7h](screenshot/q7h_top5_genre.png)
```

## Question 8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. If you haven't solved 7h, you can substitute another query to create a view.
```sql
CREATE VIEW 
	TOP_5_GENRE
AS SELECT * FROM  
	(SELECT 
		c.name, SUM(p.amount) AS Sum_Category
	FROM 
		category AS c
	INNER JOIN 
		film_category AS fc
	ON 
		c.category_id=fc.category_id
	INNER JOIN 
		inventory AS i
	ON 
		fc.film_id=i.film_id
	INNER JOIN
		rental AS r
	ON 
		i.inventory_id=r.inventory_id
	INNER JOIN
		payment AS p
	ON
		r.rental_id=p.rental_id
	GROUP BY
		c.name
	ORDER BY Sum_Category DESC
	LIMIT 5) as t1;  

![q8a](screenshot/q8a_create_view.png)
```
## Question 8b. How would you display the view that you created in 8a?
```sql
SELECT * FROM  top_5_genre;

![q8b](screenshot/q8b_show_view.png)
```
## Question 8c. You find that you no longer need the view `top_five_genres`. Write a query to delete it.
```sql
DROP VIEW top_5_genre;

![q8c](screenshot/q8c_drop_view.png)
```
