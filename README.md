
# WEBSITE ANALYTICS FOR A DIGITAL SONG DISTRIBUTOR USING SQL 

<p align="center">
<img src="https://github.com/AnalyticsAndOm/digital-music-distributor/blob/main/website%20analytics.jpg">
</p>


- **Github repository at**: https://github.com/AnalyticsAndOm/digital-music-distributor
- **File name**: PROJECT_QUERIES_WEBSITE_ANALYTICS.sql
- **Dataset Name**: Events and Songs (dummy data)
- **Dataset Link**: <a name="dataset_link"></a> https://github.com/AnalyticsAndOm/digital-music-distributor/tree/main/Data%20Preparation/CSV%20files

## Project Description
The marketing team of a digital music distributor wish to know how they can increase the traffic on the website and increase revenue. The team has reached out to the analytics team to analyse the website data to increase the website traffic, strengthen revenues and provide insights to help develop custom offers for the customers.

## Table of Contents
1. [Introduction](#introduction)
    1. [Problem Statement](#sec1p1)
    2. [Questions to answer](#sec1p2)
2. [Description of the dataset](#section2)
    1. [Table of dataset and contents](#sec2p1)
3. [Procedure and Code Snippets](#section3)
4. [Key Suggestions](#key_suggestions)

## 1. Introduction <a name="introduction"></a>
- The analysis has been carried out in MySQL workbench with file name given above. To view this file, download it from [this]( https://github.com/AnalyticsAndOm/digital-music-distributor/tree/main/Project%20Queries) repository and open the PROJECT_QUERIES_WEBSITE_ANALYTICS.sql file in relational database management tool such as MySQL Workspace.
- Alternatively, copy the scripts and paste in [myCompiler]( https://www.mycompiler.io/new/sql) website to run the queries in the [queries folder]( https://github.com/AnalyticsAndOm/digital-music-distributor/tree/main/Project%20Queries).

### 1.1. Problem Statement <a name="sec1p1"></a>
Analyse the website data and come up with suggestions to increase the website traffic, strengthen revenues and provide insights to help develop custom offers for customers.

### 1.2. Questions to ask <a name="sec1p2"></a>
- 1. Which artists have greater number of streamers listening to artists’ songs?
- 2. Which are the most popular songs on the website?
- 3. Which songs are being played frequently (songs that are appearing in multiple sessions)?
- 4. Which songs are being played in loop or on repeat?
- 5. Which artists’ songs are appearing in multiple sessions?
- 6. Who are the most active customers on the website? 
- 7. What is the percentage of paid and free songs for each customer?
- 8. What is the time spent by each customer on the website ?

## 2. Description of the dataset <a name="section2"></a>
The dataset consists of total 2 .csv files that provide details regarding songs and Events. Two tables provide details such as song titles, artist names, user names, album name, duration of the song, session id for each song per customer, etc.  

### 2.1. Table of datasets and contents <a name="sec2p1"></a>
- Below table consists of details of table name and its features. For better understanding of features of each table please refer the data dictionary.


| Table Name | Features | Description |
|------------|----------|-------------|
| Events     | ARTIST_NAME | Name of the artist |
| Events     | USER_AUTHENTICATION | Whether the user logged in or not |
| Events     | USER_FIRST_NAME | Users' first name |
| Events     | USER_GENDER | Users' gender |
| Events     | NO_ITEMS_IN_SESSION | Tells the number of songs in each session |
| Events     | USER_LAST_NAME | Users' last name |
| Events     | SONG_LENGTH_IN_SECONDS | Total duration of the song in seconds |
| Events     | SONG_LEVEL | Tells whether the song is free or paid song |
| Events     | USER_LOCATION | Users city |
| Events     | SONG_METHOD | N.A. |
| Events     | SONG_PLAYED | N.A. |
| Events     | USER_REGISTRATION_TIME_IN_SECONDS | Time user spent on the website |
| Events     | SESSION_ID | Unique identifier for each session |
| Events     | SONG_NAME | Name of the song |
| Events     | SONG_STATUS | NA |
| Events     | TIME_IN_SECONDS_OF_PLAYING_SONG | How long the song was played for |
| Events     | USER_AGENT | Browser user to login |
| Events     | USER_ID | Unique identifier to identify each user |
| Songs      | artist_id | Unique Identifier for each Artist |
| Songs      | artist_latitude | Geographical latitude of the artist's city |
| Songs      | artist_location | |
| Songs      | artist_longitude | Geographical longitude of the artist's city |
| Songs      | artist_name | Name of the artist |
| Songs      | duration | Duration of the song in seconds |
| Songs      | num_songs | Number of songs by the artist |
| Songs      | song_id | Unique identifier for each song |
| Songs      | title | Title of the song |
| Songs      | year | Year of song release |


## 3. Procedure and Code Snippets <a name="section3"></a>
Note: The queries to [create the tables]( https://github.com/AnalyticsAndOm/digital-music-distributor/tree/main/Data%20Preparation/Create%20Tables) and [insert values]( https://github.com/AnalyticsAndOm/digital-music-distributor/tree/main/Data%20Preparation/Insert%20Data%20into%20tables) into the table are provided in the data preparation folder. Separately events.csv and songs.csv file has also been provided in the [folder]( https://github.com/AnalyticsAndOm/digital-music-distributor/tree/main/Data%20Preparation/CSV%20files).

**1. Which artists have greater number of streamers listening to artists’ songs?**
   
- **Purpose**: To add more songs of top artists on the platform.

- **Impact** : Increased Traffic and Increased revenue.

>Code Snippet:
```SQL
SELECT DISTINCT * FROM
(
	SELECT 
E.ARTIST_NAME, S.ARTIST_ID, ARTIST_LOCATION, ARTIST_LATITUDE, ARTIST_LONGITUDE,
		COUNT(E.USER_ID) OVER(PARTITION BY E.ARTIST_NAME) AS USERS_NUMBER
   	 FROM EVENTS E, SONGS s
    WHERE E.ARTIST_NAME = S.ARTIST_NAME 
) AS SUB_Q1
ORDER BY USERS_NUMBER DESC;
```

<img src="https://github.com/AnalyticsAndOm/digital-music-distributor/blob/main/QUERY%20OUTPUTS/Query%201.png">

**2. Which are the most popular songs on the website?**

-- **Purpose**: To promote and suggest popular(most listened to) songs on the website to the customers.

-- **Impact**: Improved customer experience on the platform by suggesting popular songs.
>Code Snippet:
```SQL
SELECT * FROM
(
	SELECT 
SONG_ID, E.SONG_NAME, S.ARTIST_ID, E.ARTIST_NAME, E.SONG_LENGTH_IN_SECONDS, 
		COUNT(USER_ID) OVER(PARTITION BY SONG_ID) USERS_NUMBER
	FROM EVENTS E, SONGS S
	WHERE E.ARTIST_NAME = S.ARTIST_NAME
) SUB_Q2 
ORDER BY USERS_NUMBER DESC;
```

<img src="https://github.com/AnalyticsAndOm/digital-music-distributor/blob/main/QUERY%20OUTPUTS/Query%202.png">

**3. Which songs are being played frequently (songs that are appearing in multiple sessions)?**

-- **Purpose**: To create a daily mix/ playlist of songs appearing in multiple sessions in great numbers.

-- **Impact**: By providing details such as artist name of the song the customer might want to search for other songs of the artist thus improving customer engagement.

>Code Snippet:
```SQL
SELECT DISTINCT * FROM 
(
	SELECT 
		SONG_NAME, ARTIST_NAME, SONG_LEVEL, 
        		COUNT(SESSION_ID) OVER (PARTITION BY SONG_NAME) AS NO_OF_SESSIONS
	FROM EVENTS
    	WHERE SONG_NAME IS NOT NULL
    	ORDER BY NO_OF_SESSIONS DESC
) AS SUB_Q3;
```

<img src="https://github.com/AnalyticsAndOm/digital-music-distributor/blob/main/QUERY%20OUTPUTS/Query%203.png">

**4. Which songs are being played in loop or on repeat?**

-- **Purpose**: To identify songs being played multiple times by same user to activate ads on such songs.

-- **Impact**: Increase revenue through ads impression through these songs.

>Code Snippet:
```SQL
SELECT DISTINCT * FROM
(
	SELECT DISTINCT 
		SONG_NAME, ARTIST_NAME, SONG_LEVEL, 
        		COUNT(SESSION_ID) OVER(PARTITION BY SONG_NAME, USER_ID) REPEAT_COUNT
	FROM EVENTS
) SUB_Q4
WHERE SONG_NAME IS NOT NULL 
ORDER BY REPEAT_COUNT DESC;
```

<img src="https://github.com/AnalyticsAndOm/digital-music-distributor/blob/main/QUERY%20OUTPUTS/Query%204.png">

**5. Which artists’ songs are appearing in multiple sessions?**

-- **Purpose**: To identify popular artists and add more songs of such artists.

-- **Impact**: Better  customer experience, better customer retention and improve revenue.

>Code Snippet:
```SQL
SELECT DISTINCT * FROM
(
	SELECT DISTINCT 
		ARTIST_NAME, 
        		COUNT(SESSION_ID) OVER(PARTITION BY ARTIST_NAME) AS ARTIST_SESSIONS
	FROM EVENTS
) SUB_Q5
WHERE ARTIST_NAME IS NOT NULL 
ORDER BY ARTIST_SESSIONS DESC;
```

<img src="https://github.com/AnalyticsAndOm/digital-music-distributor/blob/main/QUERY%20OUTPUTS/Query%205.png">

**6. Who are the most active customers on the website?**
      
-- **Purpose**: To improve customer experience of such customers and make custom offers for such customers.

-- **Impact**: Reduction in customer churn rate and improved customer retention, repeat rate of membership purchase and attract new customer.

>Code Snippet:
```SQL
SELECT *, DENSE_RANK() OVER(ORDER BY SONGS_LISTENED DESC) USER_RANK
FROM 
(
	SELECT DISTINCT 
		USER_ID, USER_FIRST_NAME, USER_LAST_NAME, USER_GENDER, 
        COUNT(SONG_NAME) SONGS_LISTENED
	FROM EVENTS
	WHERE 
	SONG_NAME IS NOT NULL
	GROUP BY USER_ID, USER_FIRST_NAME, USER_LAST_NAME, USER_GENDER
) SUB_Q6;
```

<img src="https://github.com/AnalyticsAndOm/digital-music-distributor/blob/main/QUERY%20OUTPUTS/Query%206.png">

**7. What is the percentage of paid and free songs for each customer?**

-- **Purpose**: To provide customized offers to users with lower paid songs percentage.

-- **Impact**: Increased revenue and improved customer retention through customized offers for paid songs.

>Code Snippet:
```SQL
SELECT 
	USER_ID, CONCAT(USER_FIRST_NAME," ", USER_LAST_NAME) AS USERNAME, 
	ROUND(PAID_SONGS_NUMBER*100 / (PAID_SONGS_NUMBER + FREE_SONGS_NUMBER),2) PAID_SONGS_PERCENTAGE,
	ROUND(FREE_SONGS_NUMBER*100 / (PAID_SONGS_NUMBER + FREE_SONGS_NUMBER),2) FREE_SONGS_PERCENTAGE
FROM 
(
SELECT DISTINCT 
	USER_ID, USER_FIRST_NAME, USER_LAST_NAME,
	SUM(CASE SONG_LEVEL WHEN 'paid' THEN NO_ITEMS_IN_SESSION else 0 END) OVER(PARTITION BY USER_ID) AS PAID_SONGS_NUMBER,
	SUM(CASE SONG_LEVEL WHEN 'free' THEN NO_ITEMS_IN_SESSION else 0 END) OVER(PARTITION BY USER_ID) AS FREE_SONGS_NUMBER
FROM EVENTS
WHERE SONG_NAME IS NOT NULL 
) AS SUB_Q7 
ORDER BY PAID_SONGS_PERCENTAGE DESC;
```

<img src="https://github.com/AnalyticsAndOm/digital-music-distributor/blob/main/QUERY%20OUTPUTS/Query%207.png">

**8. What is the time spent by each customer on the website ?**

-- **Purpose**: To identify customers who spend less time on the website.

-- **Impact**: Customer targeting such customers to improve website usage.

>Code Snippet:
```SQL
SELECT 
	USER_ID, USER_NAME, TOTAL_PLAYTIME_IN_SECONDS,
    DENSE_RANK() OVER(ORDER BY TOTAL_PLAYTIME_IN_SECONDS DESC) AS RANK_BY_DURATION
FROM 
(
	SELECT DISTINCT 
		USER_ID, CONCAT(USER_FIRST_NAME," ",USER_LAST_NAME) AS USER_NAME, 
		ROUND(SUM(SONG_LENGTH_IN_SECONDS) OVER(PARTITION BY USER_ID),2) AS TOTAL_PLAYTIME_IN_SECONDS
	FROM EVENTS
	WHERE SONG_NAME IS NOT NULL
) AS SUB_Q8;
```

<img src="https://github.com/AnalyticsAndOm/digital-music-distributor/blob/main/QUERY%20OUTPUTS/Query%208.png">

## 4. Key Suggestions <a name="key_suggestions"></a>

Based on the insights from the analysis, below are the key suggestions:
  1. Adding more songs of top artists on the platform can help us increase traffic and increase revenue.
  2. Promoting and suggesting popular(most listened to) songs on the website to the customer can improve customer experience on the platform.
  3. Creating a daily mix/ playlist of songs appearing in multiple sessions in great numbers and providing details such as artist name of such songs to the customers might improve customer engagement as customers may want to search for other songs of these artists.
  4. Activating advertisements on songs being played multiple times by same user can increase revenue through ads impression through these songs.
  5. Website usage by customer can be improved by creating playlists/ curation of popular songs by most popular artist.
  6. Custum/ exclusive offers can be created for customers who are most active on the website to ensure high customer retention and reduce customer churn and improve subscription/purchase retention.
  7. Customers with lower paid songs ratio can be offered customised offers to increase platform adoption and improve revenue.
  8. Notifications, emails and push messages can be sent to customers who have lower login duration on the platform. This can help improve the customer engagement on the website.

