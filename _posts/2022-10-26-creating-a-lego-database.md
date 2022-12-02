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
2. define sets table
3. define themes table
4. create tables
5. close connection

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

Becuase we want to be able to initialize a database and connect on demand the operation is wrapped in the `connect_db()` function. This function takes a database string as an arguement, returns a database connection, and fails gracefully if something goes awry:

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

Now we need to define the structure of the lego themes table using the following SQL `CREATE TABLE` statement:

```python
create_themes_sql = """
    CREATE TABLE IF NOT EXISTS themes (
        theme_id TEXT PRIMARY KEY,
        theme_name TEXT NOT NULL
    );
    """
```

This statment checks first if the `themes` table exists, creates one if it doesn't, and then declares the constraints for two fields. The `theme_id` is an text field and primary key representing the relational link to other tables in the database. The `theme_name` is a text field that will only accept non null entries. In practical terms the `theme_id` identifies each unique lego theme and the `theme_name` gives the full descriptive name.

## Define Sets Table

Now we will use a slightly more complicated `CREATE TABLE` statement to define the structure of the lego sets table:

```python
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
```

Following the same pattern as above, this statement checks if the `sets` table exists, creates if required, and then declares the constraints for five fields. The `set_num` is a text field and primary key for the table. The `set_name` is a non null text field. The `set_year`, `theme_id`, and `num_parts` are all non null integer fields. The `FOREIGN KEY` statement links the `theme_id` in the `sets` table to primary `theme_id` key in the `themes` table. Again in practical terms the `set_num` identifies each lego set, the `set_name` gives the official set name, the `set_year` list the year the set was introduced, the `num_parts` gives the number of bricks in the set. The `theme_id` gives the identifier for the theme the set belongs to and can be used to reference theme data held in the `themes` table.


## Creating the Tables

Note that both of the above SQL statements are wrapped in triple quotes and assigned to the variables `create_theme_sql` and `create_sets_sql`. While they contain valid SQL statements, these variables are simply python strings. In order to create the tables the statements need to be passed to the database, evaluated by the SQLite3 engine, and executed. We will use the `create_table()` function to carry out this tranasction for both the themes and sets tables:

```python
def create_table(conn, table_sql):
    try:
        c = conn.cursor()
        c.execute(table_sql)
        print("Table Created")
    except sqlite3.Error as e:
        raise e
```

This function accepts a database connection and a `CREATE TABLE` string as arguements. The `cursor()` method is called on the connection to generate a cursor object. The cursor is the primary tool for executing SQL statements against a database and capturing the returned response if applicable. The `CREATE TABLE` statement is now passed to the cursor's `execute()` method to be evaluated and the table is initialzed. This operation is wrapped in a `try/except` block to responsibly handle errors.

## Closing the connection

After intitializing the database by opening a connection and then creating tables we need to close the connection. Closing the database connection will explicitly commit the table creation transactions and protect the integrity of the database. It is always a good practice to close a SQLite3 database connection with the `close()` method. In our case the connection closure is wrapped in the `close_db()` function and includes a bit of error handling:

```python
def close_db(conn):
    try:
        conn.close()
        print("Connection Closed")
    except sqlite3.Error as e:
        raise e
```

## Full Script

The database initialization and table creation should be performed in the following sequence:

1. create connection
2. define sets table
3. define themes table
4. create tables
5. close connection

Because we chose to wrap the operations in functions the provisioning sequence can be organized in a `main()` function at the end of the script. The `main()` function can in turn be fired by running `python -m lego-db.py` in a terminal. If all goes as planned print statements detailing each step will print in quick succession. The full script is included below, with the `main()` function and corresponding `__name__` check.

At this point we have provisioned the lego database and are ready to continue building the lego data pipeline.

```python
# lego-db.py

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
