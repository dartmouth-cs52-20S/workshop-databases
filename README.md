# CS52 Workshops: Databases

![](https://media.giphy.com/media/sRFEa8lbeC7zbcIZZR/giphy.gif)

## Overview

In this workshop, we'll be learning some SQL to work with a remote MySQL database that we've set up for you. Then, we'll use what we've learned to solve a riddle!

So far in CS52, we've focused on NoSQL databases because their SQL counterparts require people to learn SQL, and as we learned last Thursday, there are several potential issues that using SQL could cause. However, a lot of databases still rely on SQL.

https://dev.mysql.com/downloads/mysql/

## Setup
In theory, you can run this tutorial from anywhere, so there's no need to set up a folder! Let's install the necessary packages.

### For Mac
Enter the following two commands in terminal to install the MySQL client required to connect to our remote MySQL server (thank you, Google Cloud):
```bash
brew install mysql-client
echo 'export PATH="/usr/local/opt/mysql-client/bin:$PATH"' >> ~/.zshrc
```
If that doesn't work, try echoing this:
```
export PATH=${PATH}:/usr/local/mysql/bin
```
Restart your terminal, and then enter the following to connect to the database server:
```
mysql --host=34.86.114.15 --user=root --password
```
When prompted for a password, enter `TimmyTregubov`.
Congratulations! If you've reached screen like the one below, you're all set up! Let's move on to the tutorial.

### For Windows

After downloading the zip from the link above (you can download without signing in/making an account, get the one thats 187.5MB). Unzip it.

Now here you have two options:

#### Option 1: Temporary (during your current terminal session)

1. Go to the unzipped folder and find the bin folder. Copy the address, but modify it so it looks something like this:`/c/Users/[YOUR_USERNAME]/Downloads/mysql-8.0.20-winx64/mysql-8.0.20-winx64/bin` where [YOUR_USERNAME] is your user account name.
2. Now add the address to your path for the session: 
```
export PATH=$PATH:/c/Users/[YOUR_USERNAME]/Downloads/mysql-8.0.20-winx64/mysql-8.0.20-winx64/bin
```
3. ^ That's approximately what it'll be, unless you default to downloading it elsewhere.
4. You can `echo $PATH` to confirm its at the tail of your path.


#### Option 2: Adding it to the System/User Variables

1. Go to the unzipped folder, and keep on going until you're at at the bin folder `\mysql-8.0.20-winx64\bin`.
2. From therem you can copy the address as text.
3. In the search icon to the right of the windows icon (or windows key + X and then click Search), type environment variables and click the result.
4. You should be in a window that says System Properties, click Environment Variables, double click Path under the User Variables part, click new, and paste the address.
5. Now you just need to restart your terminal and it should be in your path.

Now you can finally get in by doing:
```
mysql --host=34.86.114.15 --user=root --password
```
When prompted for the password, it's `TimmyTregubov`.

## Step by Step

## Part 1: SQL Practice
Here's a taste of what SQL looks like, if you're curious before we jump in!
![](https://i.imgur.com/kOGWSEA.png)

Something important to remember: **All commands must end with a semicolon ``;``.**

### Navigate to the correct database
If you've followed all of the steps in the set up section, you're currently inside of a MySQL shell in a MySQL server that we're hosting on Google Cloud. Just like what we did with Mongo on the last short assignment and in the current lab, we can create different databases inside of this server.

Let's start by jumping into the database ``practice``, which we made for you:
```sql
USE practice;
```

### Create a table

Great! Now we're in the right place! Let's start by making a table. We'll do this by cloning the schema (or column set up) of one that's already set up for you!
```sql
CREATE TABLE your_name LIKE people; 
INSERT INTO your_name SELECT * FROM people;
```

Above, we created a table by cloning the schema (or column names) of one that already exists and then inserting everything that already exists in ``people`` into our new table. If you wanted to create the same schema from scratch, you'd create it like this:
```sql
CREATE TABLE your_name (
    name VARCHAR(50), 
    year INTEGER(10),
    city VARCHAR(50),
    state VARCHAR(50),
    country VARCHAR(50),
    PRIMARY KEY(name)
);
```
When you create a table in SQL, you have to immediately specify the schema. You usually can't change this after the fact. Here, ```name```, ``year``, ``city``, ``state``, and ``country`` are the columns. The value next to each column title on the same line defines the type of values that will populate that column. For ``year``, by using ``INTEGER(10)``, we're defining a column of integers of size 10. For the other fields, which have ``VARCHAR(50)``, we're defining a variable-length string with a maximum column width of 50 characters. In the last line of this schema, we're also setting the primary key of the data to the ``name`` field. The primary key is the column that SQL uses to identify each row, so by setting it to ``name``, we're using that.

### Table population

Now that we have our new table, let's populate it:
```sql
INSERT INTO your_table(name,year) VALUES("your_full_name", class_year);
```
What happened here? Weren't there more columns in the schema above? Let's take a look!
```sql
SELECT * FROM your_table;
```
SQL allows you to insert only a subset of values for a row, as opposed to requiring a value for every column. It will automatically populate the rest of the row to `NULL`. Let's update our entry!
```sql
UPDATE your_table
SET city="your_city", state="your_state", country="your_country"
WHERE name="your_full_name";
```
In an SQL statement, the part after ``WHERE`` provides a condition under which the previous part should execute. Here, we're `SET`ing the rows for which `name` is equal to `your_name`.

Now that we've filled out our table, let's see how it looks now!
```sql
SELECT * FROM your_table;
```

Try adding another row into your table with different information! **Make sure to use a different value for the ``name`` field so you don't overwrite your existing data!**

If you want to populate all fields at once, you would do something like this:

```sql
INSERT INTO your_table VALUES(
    "your_full_name",
    class_year,
    "city_where_you_are",
    "state_where_you_are",
    "country_where_you_are");
```

Now that we have multiple rows, let's try to take a look at only one value:
```sql
SELECT * FROM your_table WHERE name='your_name';
```


Let's clear out all the extraneous rows in your table. When deleting data in SQL it is good practice to `SELECT *` first and see what will be deleted.
```sql
SELECT * FROM your_table WHERE name!="your_name";
```
As we did above, we're running a command based on a condition. Ultimately we want to `DELETE` any row from the table where the name isn't your own. Try this on your own!

<details>
<summary>If you got stuck or it didn't work, you can take a look here!</summary>

```sql
DELETE FROM your_table WHERE name!="your_name";
```
</details>

### Pass Rows Between Tables
In addition to allowing you to create your own table, SQL lets users merge different tables, too. Let's merge ours into the master table ``people`` (which is also where we got our schema from):
```sql
INSERT IGNORE 
INTO people
SELECT * FROM your_table;
```
We want to ignore duplicate rows, so we added the `IGNORE` statement. This means that if `people` and `your_table` have rows with the same name, the merger will default to the values in `people`.

### Other Ways to Merge Table Rows
You can combine tables that share a key! For instance, you could combine a `Cars` table with an `OwnerID` column and a `People` table with a `PersonID` column to make one table with some columns from each. How would this work? To merge the two tables by ids and keep only the `Model` column from `Cars` and the `Age` column from `People`, you could run:
```sql
SELECT Cars.Model, People.Age
FROM Cars INNER JOIN People ON Cars.OwnerID = People.PersonID;
```

Now you've got an understanding of how to interact with a MySQL database! On to the fun part!


## Part 2: Secret Message

Now that you're a MySQL expert, let's put your skills to the test and solve a secret message. 

The table containing the secret message is called `secretmessage`, so make sure you direct your queries there.

Instructions: we'll give you a series of jokes (prompts), and you need to select the row of the table with that prompt. The columns in `secretmessage` are (`joke`, `punchline`, `word`, `nextjoke`). You're going to need to query `secretmessage` with our starting joke, and then take the `next joke` from each response for your next query.
Example:
```sql
SELECT * FROM secretmessage WHERE joke="given joke";
```
This query should just return one row of the table. Your next query should be the exact contents of the `nextjoke` column. Make note of the contents of the `word` column for each returned row ... if you remember them in order, they'll combine to form a special message when you're done!

### Here's the first joke to get you started: "what did the giraffe say when he walked into a bar?"
<details>
<summary>If you got stuck or it didn't work, you can take a look here!</summary>

```sql
SELECT * FROM secretmessage WHERE joke="what did the giraffe say when he walked into a bar?";
```
</details>

### Keep moving down the chain of queries till your "nextjoke" column reads "done!" (reminder: each word of the message was in the "word" column of each table row your queries returned)

<details>
<summary>If you get stuck on any step, here are all three queries!</summary>

```sql
SELECT * FROM secretmessage WHERE joke="what did the giraffe say when he walked into a bar?";
SELECT * FROM secretmessage WHERE joke="i was sitting in traffic the other day";
SELECT * FROM secretmessage WHERE joke="i find some 1970s arstists rather vain";
```
</details>

### What does the message say??

<details>
<summary>If your terminal is nice and wide and want to see somethin cool:</summary>
Check this out!

```sql
SELECT * FROM test4444;
```
</details>


## Summary
By now, you've learned:
* [ ] How to work with a remote database
* [ ] How to use some pretty handy SQL commands
* [ ] What joins are

## Reflection
* [ ] How do MySQL, Sequelize, PostgresSQL, and Knex fit together?
* [ ] What are the comparative benefits and drawbacks of SQL and NoSQL databases?


## Resources
* https://www.freecodecamp.org/news/basic-sql-commands/
* Our lad Timmy for the Google Cloud SQL recommendation :wink: 
