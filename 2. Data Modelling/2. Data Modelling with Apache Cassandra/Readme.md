# Project
## Introduction
A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analysis team is particularly interested in understanding what songs users are listening to. Currently, there is no easy way to query the data to generate the results, since the data reside in a directory of CSV files on user activity on the app.

As a data engineer, I need to create an Apache Cassandra database which can create queries on song play data to answer the questions.

## Dataset
For this project, we have one dataset: `event_data`. The directory of CSV files partitioned by date. Here are examples of filepaths to two files in the dataset:
> `event_data/2018-11-08-events.csv
event_data/2018-11-09-events.csv`

After processing all the csv files, I created `event_datafile_new.csv` which is the denormalized data to be used for inserting data into apache cassandra tables. Below is the sample of `event_datafile_new.csv`:

![Sample Data](images/image_event_datafile_new.jpg "Sample Data")

## Queries
For data modelling on cassandra, we need to know about the queries that we will be performing, as we might need separate table for each query.

Below are the queries to be performed on the data:
1. Give me the artist, song title and song's length in the music app history that was heard during sessionId = 338, and itemInSession = 4
2. Give me only the following: name of artist, song (sorted by itemInSession) and user (first and last name) for userid = 10, sessionid = 182
3. Give me every user name (first and last) in my music app history who listened to the song 'All Hands Against His Own'

## Schema
For above queries, we need 3 different tables songs, artists, and users table.
### Query 1: SELECT artist, song, length FROM songs WHERE sessionId=338 AND itemInSession=4
`songs table`:
- sessionId INT
- itemInSession INT 
- artist TEXT
- song TEXT
- length FLOAT
- **PRIMARY KEY (sessionId, itemInSession)**

Since we need to filter on sessionId and itemInSession we can set these two fields as Primary Key and Clustering Key

### Query 2: SELECT artist, song, firstName, lastName FROM artists WHERE userId=10 AND sessionId=182
`artists table`:
- userId INT
- sessionId INT
- itemInSession INT
- artist TEXT
- song TEXT
- firstName TEXT
- lastName TEXT
- PRIMARY KEY (userId, sessionId, itemInSession)

Since we need to filter on userId and sessionId we can set these two fields as Primary Key and Clustering Key. Also we need the output to be sorted by itemInSession, we have to add this field in primary key as well.

### Query 3: SELECT userId, firstName, lastName FROM users WHERE song='All Hands Against His Own'
- song TEXT
- userId INT
- firstName TEXT
- lastName TEXT
- PRIMARY KEY (song, userId)

Since we need to filter on song, song is added to Primary key. But song field not sufficient as it might have multiple rows with same song name. hance, I have added usedId as well to Primary Key to uniquely identify each row.


