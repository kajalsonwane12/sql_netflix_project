# Netflix Movies and TV Shoes Data Analysis using SQL.

![Netflix Logo](https://github.com/kajalsonwane12/sql_netflix_project/blob/main/logo%20(7).png)

## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives

- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema

```sql
CREATE DATABASE netflix_db;
USE netflix_db;

CREATE TABLE netflix (
  show_id VARCHAR(10),
  type VARCHAR(20),
  title VARCHAR(150),
  director VARCHAR(250),
  casts TEXT,
  country VARCHAR(150),
  date_added VARCHAR(50),
  release_year INT,
  rating VARCHAR(10),
  duration VARCHAR(20),
  listed_in VARCHAR(100),
  description TEXT
);

DESC netflix;

SET GLOBAL local_infile = 1;

SHOW VARIABLES LIKE 'local_infile'; # It checks the status of local_infile in MySQL.

SET GLOBAL local_infile = 1; # It allows MySQL to read local CSV files for data import.

LOAD DATA INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/netflix_titles.csv'
INTO TABLE netflix
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS;

SELECT COUNT(*) FROM netflix;

SELECT * FROM netflix;

SELECT DISTINCT type FROM netflix;

-- 15 Business Problems & Solutions

-- 1. Count the number of Movies vs TV Shows
SELECT type, COUNT(*) AS total  FROM netflix
GROUP BY type;

-- 2. Find the most common rating for movies and TV shows
SELECT type,
rating FROM 
(
SELECT
   type, rating, COUNT(*),
   RANK() OVER(PARTITION BY type ORDER BY COUNT(*) DESC) as ranking 
FROM netflix
GROUP BY 1, 2
) as t1
WHERE 
  ranking = 1;
  
-- 3. List all movies released in a specific year (e.g., 2020)
SELECT type,title,release_year FROM netflix 
WHERE type = "Movie" and release_year = 2020;

-- 4. Find the top 5 countries with the most content on Netflix
SELECT country,COUNT(*) AS total_content FROM netflix
WHERE country IS NOT NULL AND country <> '' #-- Ensure we only count rows where country is not empty or missing
GROUP BY country
ORDER BY total_content DESC
LIMIT 5;

-- 5. Identify the longest movie
SELECT type,duration as Longest_Movie FROM netflix
WHERE type = "Movie"
ORDER BY CAST(SUBSTRING_INDEX(duration,'',1) AS UNSIGNED) DESC
LIMIT 1;

-- 6. Find content added in the last 5 years
SELECT title, type, date_added FROM netflix
WHERE YEAR(STR_TO_DATE(date_added, '%M %d, %Y')) >= YEAR(CURDATE()) -5;

-- 7. Find all the movies/TV shows by director 'Rajiv Chilaka'!
SELECT title, type, director FROM netflix
WHERE director = "Rajiv Chilaka";

-- 8. List all TV shows with more than 5 seasons
SELECT title, type, duration FROM netflix
WHERE type = "TV Show" 
AND CAST(SUBSTRING_INDEX(duration, ' ',1) AS UNSIGNED) > 5;

-- 9. Count the number of content items in each genre
SELECT listed_in AS genre, COUNT(*) AS total_content FROM netflix
GROUP BY listed_in
ORDER BY total_content DESC;

-- 10.Find each year and the average numbers of content release in India on netflix. return top 5 year with highest avg content release!
SELECT release_year, COUNT(*) AS avg_content FROM netflix
WHERE country LIKE '%India%'
GROUP BY release_year
ORDER BY avg_content DESC
LIMIT 5;

-- 11. List all movies that are documentaries
SELECT title, type, listed_in FROM netflix
WHERE type = "Movie" and listed_in = "Documentaries";

-- 12. Find all content without a director
SELECT title, type FROM netflix
WHERE director IS NULL OR director = '';

-- 13. Find how many movies actor 'Salman Khan' appeared in last 10 years!
SELECT COUNT(*) AS total_movies FROM netflix
WHERE type = 'Movie'
  AND casts LIKE '%Salman Khan%'
  AND release_year >= year(CURDATE()) - 10;

-- 14. Find the top 10 actors who have appeared in the highest number of movies produced in India.
SELECT casts, COUNT(*) AS total_movies FROM netflix
WHERE country LIKE '%India%'
  AND type = 'Movie'
GROUP BY casts
ORDER BY total_movies DESC 
LIMIT 10;

-- 15. Categorize the content based on the presence of the keywords 'kill' and 'violence' in the description field.
-- Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.
SELECT 
   CASE 
      WHEN description LIKE '%Kill%'
        OR description LIKE '%Violence' THEN 'Bad'
      ELSE 'Good'
   END AS content_category,
   COUNT(*) AS total_category
FROM netflix
GROUP BY content_category;   
```

**Objective:** Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.

## Findings and Conclusion

- **Content Distribution:** The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- **Common Ratings:** Insights into the most common ratings provide an understanding of the content's target audience.
- **Geographical Insights:** The top countries and the average content releases by India highlight regional content distribution.
- **Content Categorization:** Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.
Thank you for your support, and I look forward to connecting with you!

