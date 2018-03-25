# Mysql_homework Yizhi Yin
## Question 1a. Display the first and last names of all actors from the table `actor`. 
```sql
-- switch database to sakila
USE sakila;
-- actor table is sorted by actor_id so first actor's first_name and last_name 
SELECT first_name, last_name FROM actor Order BY actor_id ASC LIMIT 1;
-- select the last row 
SELECT first_name, last_name FROM actor Order BY actor_id DESC LIMIT 1;

![q1a_output](https://github.com/yizhiyin86/Mysql_homework/blob/master/screenshot/q1a.first.png)
