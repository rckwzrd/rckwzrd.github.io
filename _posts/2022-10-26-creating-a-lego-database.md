---

layout: post
sqlite-python: https://docs.python.org/3/library/sqlite3.html
sqilte: https://www.sqlite.org/index.html
sql-install:
lego-post: https://rckwzrd.github.io/2022/09/26/counting-lego-bricks.html

---

Explain how python handles connections and transactions. Explain how SQL statements are passed as strings and exectuted. Explain why SQLite3 was chosen.

Use Python to programmaticlly handle database operations and start building a reusable module for managing database actions.

If you spend time around lego sets you will notice that there is a structure between sets, bricks, and themes that is an obvious fit for a relational database system. In this post we will provision a SQLite3 database with Python to hold set and theme information. This will expand on the [Counting Lego Bricks]({{lego-post}}) post with the goal of piping lego data from the Rebrickable API into our local database.

[SQLite3]({{sqlite}}) is a fast and lightweight SQL database engine that lives on a hard drive. It does not have the overhead of traditional client-server database engines making it easy to use for small projects. We will interact with SQLite3 using the [Python API]({{sqlite-python}}) included in the standard library. The efficiency of SQLite3 combined with the utility of Python provides a good solution for creating and maintaining a lego database.

Before we can start piping lego data into the database we need to initialize a SQLite3 data store, create methods to operate on the database, and define the structure of tables that hold data. These operations are effectively SQL statements held in strings that are passed to the SQLite3 engine via Python. To meet these requirements we need to do the following: 

1. installing sqlite3
2. create connection
3. create sets table
4. create themes table
5. close connection

## Installing SQLite3

## Creating a connection

## Creating Sets Table

## Creating Themes table

## Closing the connection
