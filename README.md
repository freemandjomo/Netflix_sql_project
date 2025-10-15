A data analysis project using SQL to explore and summarize information about Netflix’s movies and TV shows.
![Netflixbild](https://github.com/freemandjomo/Netflix_sql_project/blob/main/netflix_bild.jpg)
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
1. Count the number of Movies vs TV Shows

    SELECT type,   
	       COUNT(*) 
	       FROM netflix 
	     GROUP BY 1;

2. Find the most common rating for movies and TV shows

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
4. List all movies released in a specific year (e.g., 2020)
    SELECT * 
	FROM netflix
	WHERE type = 'Movie' and release_year = 2020
5. Find the top 5 countries with the most content on Netflix
    SELECT UNNEST(STRING_TO_ARRAY(country,',')) as countries, 
	  COUNT(*) as contents    
	  FROM  netflix
	  GROUP BY 1
	  ORDER BY contents DESC
	  LIMIT 5
	  
6. Identify the longest movie
    SELECT *, 
	       SPLIT_PART(duration,' ',1)::INT as durations 
	FROM  netflix
	     WHERE type = 'Movie' AND duration IS NOT NULL 
	     ORDER BY durations DESC
	    LIMIT 1
	
7. Find content added in the last 7 years
SELECT * 
FROM netflix 
WHERE 
    TO_DATE(date_added,'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '7 years'
8. Find all the movies/TV shows by director 'Steven Spielberg'!
SELECT * 
FROM (
SELECT title,
       UNNEST(STRING_TO_ARRAY(director,',')) as director FROM netflix
	   GROUP BY 1,2 )  
WHERE director = 'Steven Spielberg'	   
9. List all TV shows with more than 3 seasons
SELECT * 
    FROM netflix
    WHERE  type = 'TV Show'
    AND SPLIT_PART(duration,' ',1)::INT > 3 
10. Count the number of content items in each genre
SELECT
       UNNEST(STRING_TO_ARRAY(listed_in,',')) as genre ,
	   COUNT(*) as total_count 
FROM netflix
GROUP BY  1 
   
10.Find each year and the average numbers of content release in United States on netflix.
SELECT country,
       release_year,
       ROUND(count(show_id)::numeric /  (SELECT count(show_id) FROM netflix WHERE country = 'United States')::numeric * 100,2)
	   as AVG_release_year   
       FROM netflix 
	   WHERE country = 'United States'
       GROUP BY country ,release_year   
	   ORDER BY AVG_release_year DESC
	   LIMIT 5
return top 5 year with highest avg content release!
11. List all movies that are documentaries
    SELECT * 
	FROM netflix
	WHERE type = 'Movie' and listed_in LIKE '%Documentaries' 
12. Find all content without a director
    SELECT * 
	FROM netflix 
	WHERE director IS NULL  
13. Find how many movies actor 'Salman Khan' appeared in last 10 years!
      
	  SELECT * FROM netflix
	   WHERE casts LIKE '%Salman Khan'
	   AND release_year > EXTRACT( YEAR FROM CURRENT_DATE - 10  )
	   
14. Find the top 8 actors who have appeared in the highest number of movies produced in Nigeria.
       
	   SELECT UNNEST(STRING_TO_ARRAY(casts , ',')) as actors,
	          COUNT(*) FROM netflix AS content_number 
			  WHERE  country = 'Nigeria' 
			  GROUP BY 1 
	          ORDER BY 2 DESC
			  LIMIT 8
			  
15.Categorize the content based on the presence of the keywords 'kill' and 'violence' in 
the description field. Label content containing these keywords as 'not_to_recommend' and all other 
content as 'to_recommend'. Count how many items fall into each category.

   SELECT *,
	 CASE  
	 WHEN description LIKE '%kill%'
	   OR description LIKE '%violence%' THEN 'not_to_recommend'
	 ELSE 'to_recommend'
	END category 
	   FROM netflix 
	       
		   
