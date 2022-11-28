---

layout: post
sqlite-python: https://docs.python.org/3/library/sqlite3.html
sqlite: https://www.sqlite.org/index.html
lego-post: https://rckwzrd.github.io/2022/09/26/counting-lego-bricks.html

---

If you spend time around lego sets you will notice that there is a structure between sets, bricks, and themes that is an obvious fit for a relational database system. In this post we will provision a SQLite3 database with Python to hold set and theme information. This will expand on the [Counting Lego Bricks]({{page.lego-post}}) post with the end goal of pumping lego data from the Rebrickable API into our local database.

[SQLite3]({{page.sqlite}}) is a fast and lightweight SQL database engine that lives on a hard drive. It does not have the overhead of traditional client-server database engines making it easy to use for small projects. We will interact with SQLite3 using the [Python API]({{page.sqlite-python}}) included in the standard library. The efficiency of SQLite3 combined with the utility of Python provides a good solution for creating and maintaining a lego database.

Before we can start pumping lego data into the database we need to initialize a SQLite3 data store, create methods to operate on the database, and define the structure of tables that hold data. These operations are effectively SQL statements held in strings and passed to the SQLite3 engine via the Python API. The following actions for provisioning a database can be recorded in a Python module and reused as the pipeline grows:

1. create connection
2. create sets table
3. create themes table
4. close connection

## Installing SQLite3

Before we can provision the lego databse we need to install SQLite on a personal linux machine. Fortunately installing SQLite3 is a simple task. 

First check if SQLite3 is installed by running `which` in a terminal:

```bash
$ which sqlite3
/usr/bin/sqlite3
```

If the output returns a path to the SQLite3 binary proceed. If not run the following `apt install` command:

```bash
$ sudo apt install sqlite3
```

Confirm that SQLite3 is installed with by checking the `version`:

```bash
$ sqlite3 --version
3.37.2 2022-01-06 13:25:41 872ba256cbf61d9290b571c0e6d82a20c224ca3ad82971edc46b29818d5dalt1
```

Now we can begin building our SQLite3 Python module!

## Creating a connection

One of the greatest advantages of SQLite3 is that the entire database is contained in one file. To initialize a database pass a file name with the `.db` extension to `sqlite3.connect()`. If the database with the passed name is not is not found a new database will be implictly generated. Calling `sqlite3.connect()` returns a connection object that is used to run other actions against the database.

Becuase we want to be able to initialize a database and connect on demand the operation is wrapped in the `connect_db` function. This function takes a database string as an arguement, returns a database connection, and fails gracefully if something goes awry:

```python
import sqlite3

def connect_db(db_file):
    try:
        conn = sqlite3.connect(db_file)
        print("Connection Created")
    except sqlite3.Error as e:
        raise e
    return conn
```

## Define Themes Table

Now we need to define the structure of the lego themes table using the following SQL `CREATE TABLE` syntax:

```SQL
CREATE TABLE IF NOT EXISTS themes (
    theme_id INT PRIMARY KEY,
    theme_name TEXT NOT NULL
);
```

## Define Sets Table

Using `theme_id` as a foreign key to link `themes` and `sets` tables.

## Creating the Tables

Pass connection object and SQL statement to a function.

## Closing the connection

Close database connection to commit the transactions and protect integrity of the database.

## Full Script

Call each function in sequence inside of a main function. 

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
