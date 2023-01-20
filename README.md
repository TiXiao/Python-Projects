# Part 1

## CSV file link for "restaurants"
[file:///Users/tt/Desktop/sql-crud-TiXiao-1/rest.csv]
[file:///Users/tt/Desktop/sql-crud-TiXiao-1/users.csv]
[file:///Users/tt/Desktop/sql-crud-TiXiao-1/posts.csv]

## Create Restaurants Table and Check the Table
```
create table restaurants (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    category TEXT NOT NULL,
    price_tier TEXT NOT NULL,
    neighborhood TEXT NOT NULL,
    opening_hours TIME NOT NULL,
    ave_rating INTEGER NOT NULL,
    good_for_kids TEXT NOT NULL
    );
.tables
.schema restaurants
```

## Import the CSV into a temporary table that matches the CSV exactly
```
create table temp_table (
    name TEXT NOT NULL,
    category TEXT NOT NULL,
    price_tier TEXT NOT NULL,
    neighborhood TEXT NOT NULL,
    opening_hours TIME NOT NULL,
    ave_rating INTEGER NOT NULL,
    good_for_kids TEXT NOT NULL
    );
```

## Set up the environment:
 ```
 .mode csv
 .headers on
 ```

## Import the CSV into this temp_table, informing SQLite to skip the first row with headers in the file:
```
.import /Users/tt/Desktop/sql-crud-TiXiao-1/rest.csv temp_table
```
## Copy the data from temp_table into permanent restaurants table:
```
INSERT INTO restaurants
(id, name, category, price_tier, neighborhood, opening_hours, ave_rating, good_for_kids)
SELECT * FROM temp_table;
```

## Drop the temporary table
```
DROP TABLE temp_table;
```

## Create Reviews Table for Part1 
```
create table reviews (
    id INTEGER PRIMARY KEY,
    reviews INTEGER NOT NULL
    );
```

## Get average rating
```
select avg(reviews) from restaurants;
```

## 1. All cheap restaurants in a particular neighborhood
```
select id, name from restaurants where price_tier = "cheap" and neighborhood = "Manhattan";
```
 
## 2. All Restaurants In a Particular Genre with 3 stars or more order by desc star
```
select id, name from restaurants where category = "Chinese" and ave_rating >= "3" order by ave_rating desc;
```

## 3. All restaurants that are open now
```
select id, name from restaurants where strftime('%H:%M', 'now');
```

## 4, Leave a review for a restaurant
```
alter table restaurants add column reviews TEXT;
UPDATE restaurants
SET reviews = "Amazing place for dinner!"
where id = 2;
```

## 5. Delete all restaurants that are not good for kids
```
delete from restaurants where good_for_kids = "False";
```

## 6. Find the number of restaurants in each NYC neighborhood
```
select neighborhood, count(neighborhood) from restaurants group by neighborhood;
```


# Part 2

## Create Users Table
```
create table Users (
    id INTEGER PRIMARY KEY,
    email TEXT NOT NULL,
    password TEXT NOT NULL,
    handle TEXT NOT NULL
    );
```

## Import users.csv
```
.mode csv
.separator ","
.headers on
.import /Users/tt/Desktop/sql-crud-TiXiao-1/users.csv users
select * from users;
```


## Register a new user
```
insert into users (email, password, handle) values ("ameliattxx@gmail.com", "Td8824", "tt");
```

## Create Posts Table
```
DROP TABLE IF EXISTS posts;
create table posts (
    id INTEGER,
    message TEXT ,
    visible TEXT,
    mfrom INTEGER,
    mto INTEGER,
    stories INTEGER,
    time TIME
    );
```

## Import posts.csv
```
.mode csv
.separator ","
.headers on
.import /Users/tt/Desktop/sql-crud-TiXiao-1/posts.csv posts
select * from posts;
```

## Create a new massage sent from user1 to user2
```
insert into posts(id, message, visible, mfrom, mto, stories, time) values ("1001", "toyhgyugcutrdigufdotahh", "True", "34", "12", "1", CURRENT_TIMESTAMP);
```

## Show the 10 most recent visible Messages and Stories, in order of recency.
```
SELECT id, time, message from posts where visible = "true" order by ROUND((JULIANDAY('now') - JULIANDAY(time)) * 24) asc limit 10;
```

## Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.
```
SELECT id, message from posts where visible = "true" and mfrom="1" and mto = "696" order by ROUND((JULIANDAY('now') - JULIANDAY(time)) * 24) asc limit 10;
```

## Make all Stories that are more than 24 hours old invisible.
```
update posts set visible = "false" where ROUND((JULIANDAY('now') - JULIANDAY(time)) * 24) > 24;
select * from posts where id = 1;
```

## Show all invisible Messages and Stories, in order of recency.
```
select id, message from posts where visible = "false" order by ROUND((JULIANDAY('now') - JULIANDAY(time)) * 24) asc; 
```

## Show the number of posts by each User.
```
select users.id, posts.id, count(message)from users inner join posts on users.id = posts.mfrom group by users.id;
```

## Show the post text and email address of all posts and the User who made them within the last 24 hours.
```
select message, email from posts inner join users on users.id = mfrom order by ROUND((JULIANDAY('now') - JULIANDAY(time)) * 24) < 24 limit 10;
```

## Show the email addresses of all Users who have not posted anything yet.
```
select email from users left join posts on users.id = mfrom where mfrom is null;
```