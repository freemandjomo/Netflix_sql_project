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
the Link to the DATASET : ![here:](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download) 
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
## 3. Find the most common rating for movies and TV shows

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
## 4. Find the top 5 countries with the most content on Netflix
```sql
    SELECT UNNEST(STRING_TO_ARRAY(country,',')) as countries, 
	  COUNT(*) as contents    
	  FROM  netflix
	  GROUP BY 1
	  ORDER BY contents DESC
	  LIMIT 5
 ```  
## 5. List all movies released in a specific year (e.g., 2020)
   ```sql
         SELECT * 
	     FROM netflix
	     WHERE type = 'Movie' and release_year = 2020
       ```
