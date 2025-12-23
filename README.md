# A data analysis project using SQL to explore and summarize information about Netflix’s movies and TV shows.
![Netflixbild](https://github.com/freemandjomo/Netflix_sql_project/blob/main/netflix_bild.jpg)
## Overview  
This project shows an explicte analysis of Netflix's movies and TV shows data using SQL. The Goal is to extract valuable insights and answer some business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.


## Objective
- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations
- Explore and categorize content based on specific criteria and keywords.

## Dataset
the Link to the DATASET : [DATASET](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download) 
## SCHEMA OF THE TABLE :
```sql
-- advanced SQL Project -- NETFLIX 
   DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
    show_id      VARCHAR(25),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
``` 
## 1. Count the number of Movies vs TV Shows
```sql 
  SELECT type,   
	    COUNT(*) 
	    FROM netflix 
	 GROUP BY 1
```
**Objective:** Determine the distribution of content types on Netflix.
## 2. Find the most common rating for movies and TV shows
```sql 
   WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT 
        type,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
```
**Objective:** Identify the most frequently occurring rating for each type of content.

## 3. List all movies released in a specific year (e.g., 2020)

```sql
    SELECT * 
	FROM netflix
	WHERE type = 'Movie' and release_year = 2020
```
**Objective:** Determine all the countries that have been released in 2020.

## 4. Find the top 3 countries with the most content on Netflix
```sql
    SELECT UNNEST(STRING_TO_ARRAY(country,',')) as countries, 
	  COUNT(*) as contents    
	  FROM  netflix
	  GROUP BY 1
	  ORDER BY contents DESC
	  LIMIT 3
 ```
**Objective:** List the three country with the most content .

## 5. Identify the longest movie
    
 ``` sql
           SELECT *, 
	       SPLIT_PART(duration,' ',1)::INT as durations 
	 FROM  netflix
	     WHERE type = 'Movie' AND duration IS NOT NULL 
	     ORDER BY durations DESC
	    LIMIT 1
   ```
**Objective:** Determines the longest Movie  
## 6. Find content added in the last 7 years
```sql
SELECT * 
FROM netflix 
WHERE 
TO_DATE(date_added,'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '7 years'
```
**Objective:** Retrieve all Movies added in the last 7 years.  
   
## 7. Find all the movies/TV shows by director 'Steven Spielberg'!

```sql
 SELECT * 
 FROM (
 SELECT title,
 UNNEST(STRING_TO_ARRAY(director,',')) as director FROM netflix
 GROUP BY 1,2 )  
 WHERE director = 'Steven Spielberg'
```
**Objective:** Retrieve all Movies directed by Steven Spielberg . 
## 8. List all TV shows with more than 3 seasons
```sql 
SELECT * 
    FROM netflix
    WHERE  type = 'TV Show'
    AND SPLIT_PART(duration,' ',1)::INT > 3
```
**Objective:** Retrieve all Movies longer than 3 Seasons. 
 
## 9. Count the number of content items in each genre
```sql
SELECT
       UNNEST(STRING_TO_ARRAY(listed_in,',')) as genre ,
	   COUNT(*) as total_count 
FROM netflix
GROUP BY  1 
```
**Objective:** determine the number of content of each genre.  
## 10.Find each year and the average numbers of content release in United States on netflix.
```sql 
SELECT country,
       release_year,
       ROUND(count(show_id)::numeric /  (SELECT count(show_id) FROM netflix WHERE country = 'United States')::numeric * 100,2)
	   as AVG_release_year   
       FROM netflix 
	   WHERE country = 'United States'
       GROUP BY country ,release_year   
	   ORDER BY AVG_release_year DESC
	   LIMIT 5
```
**Objective:** determine for each Year the average number of Movies released in United states .  
 
return top 5 year with highest avg content release!
## 11. List all movies that are documentaries
 ```sql 
    SELECT * 
	FROM netflix
	WHERE type = 'Movie' and listed_in LIKE '%Documentaries'
 ```
**Objective:** determine for each Year the average number of Movies released in United states .

## 12. Find all content without a director
```sql 
    SELECT * 
	FROM netflix 
	WHERE director IS NULL
```
**Objective:** Return all content without a director. 
## 13. Find how many movies actor 'Salman Khan' appeared in last 10 years!
 ```sql     
	  SELECT * FROM netflix
	   WHERE casts LIKE '%Salman Khan'
	   AND release_year > EXTRACT( YEAR FROM CURRENT_DATE - 10  )
 ```
**Objective:** Determine the number of Movie where the Actor Salman Khan appeared . 	   
## 14. Find the top 8 actors who have appeared in the highest number of movies produced in Nigeria.
  ```sql      
	   SELECT UNNEST(STRING_TO_ARRAY(casts , ',')) as actors,
	          COUNT(*) FROM netflix AS content_number 
			  WHERE  country = 'Nigeria' 
			  GROUP BY 1 
	          ORDER BY 2 DESC
			  LIMIT 8
  ```
**Objective:** List the 8 actors who have appeeared in the highest Number of Movies.	   
			  
## 15.Categorize the content based on the presence of the keywords 'kill' and 'violence' in 
the description field. Label content containing these keywords as 'not_to_recommend' and all other 
content as 'to_recommend'. Count how many items fall into each category.
  ```sql
   SELECT *,
	 CASE  
	 WHEN description LIKE '%kill%'
	   OR description LIKE '%violence%' THEN 'not_to_recommend'
	 ELSE 'to_recommend'
	END category 
	   FROM netflix
 ``` 
**Objective:** Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.	
## Findings and Conclusion
- Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
- Geographical Insights: The top countries and the average content releases by India highlight regional content distribution.
- Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

## Author - MERVEILLES FREEMAN DJOMO TOUKA

 [My email adress](djomofreeman1776@gmail.com)  
 [My GitHub](https://github.com/freemandjomo)

 ##  Contributing

Feel free to fork this repository and submit pull requests! Any improvements to the queries or additional analyses are welcome.

---

