---

layout: post
sqlite-python: https://docs.python.org/3/library/sqlite3.html
sqilte: https://www.sqlite.org/index.html
sql-install:
lego-post: https://rckwzrd.github.io/2022/09/26/counting-lego-bricks.html

---

Explain how python handles connections and transactions. Explain how SQL statements are passed as strings and exectuted. Explain why SQLite3 was chosen.

Use Python to programmaticlly handle database operations and start building a reusable module for managing database actions.

If you spend time around lego sets you will notice that there is a structure between sets, bricks, and themes that is an obvious fit for a relational database system. In this post we will provision a SQLite3 database with Python to hold set and theme information. This will expand on the [Counting Lego Bricks]({{page.lego-post}}) post with the end goal of piping lego data from the Rebrickable API into our local database.

[SQLite3]({{page.sqlite}}) is a fast and lightweight SQL database engine that lives on a hard drive. It does not have the overhead of traditional client-server database engines making it easy to use for small projects. We will interact with SQLite3 using the [Python API]({{page.sqlite-python}}) included in the standard library. The efficiency of SQLite3 combined with the utility of Python provides a good solution for creating and maintaining a lego database.

Before we can start piping lego data into the database we need to initialize a SQLite3 data store, create methods to operate on the database, and define the structure of tables that hold data. These operations are effectively SQL statements held in strings and passed to the SQLite3 engine via the Python API. These actions can be codified in reusable module for managing database operations and expanded as the pipeline grows: 

1. create connection
2. create sets table
3. create themes table
4. close connection

## Installing SQLite3

Installing SQLite3 on a linux machine is fortunately a simple task. 

First check if SQLite3 is installed by running `which` in a terminal:

```bash
$ which sqlite3
/usr/bin/sqlite3
```

If the output returns a path to the SQLite3 executable proceed. If not run the following `apt install` command:

```bash
$ sudo apt install sqlite3
```

Now confirm that SQLite3 is installed with by checking the `version`:

```bash
$ sqlite3 --version
3.37.2 2022-01-06 13:25:41 872ba256cbf61d9290b571c0e6d82a20c224ca3ad82971edc46b29818d5dalt1
```

## Creating a connection

## Creating Sets Table

## Creating Themes table

## Closing the connection

## Full Script

```python
import sqlite3


def connect_db(db_file):
    try:
        conn = sqlite3.connect(db_file)
        print("Connection Created")
    except sqlite3.Error as e:
        raise e
    return conn


def close_db(conn):
    try:
        conn.close()
        print("Connection Closed")
    except sqlite3.Error as e:
        raise e


def create_table(conn, table_sql):
    try:
        c = conn.cursor()
        c.execute(table_sql)
        print("Table Created")
    except sqlite3.Error as e:
        raise e


def main():
    db_file = "lego.db"

    create_themes_sql = """
        CREATE TABLE IF NOT EXISTS themes (
            theme_id INT PRIMARY KEY,
            theme_name TEXT NOT NULL
        );
        """
    create_sets_sql = """
        CREATE TABLE IF NOT EXISTS sets (
            set_num TEXT PRIMARY KEY,
            set_name TEXT NOT NULL,
            set_year INT NOT NULL,
            theme_id INT NOT NULL,
            num_parts INT NOT NULL,
            FOREIGN KEY (theme_id) REFERENCES themes (theme_id)
        );
        """

    conn = connect_db(db_file)
    create_table(conn, create_themes_sql)
    create_table(conn, create_sets_sql)
    close_db(conn)


if __name__ == "__main__":
    main()
```
