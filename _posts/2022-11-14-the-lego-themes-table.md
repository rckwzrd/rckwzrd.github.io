---
layout: post
lego-post: https://rckwzrd.github.io/2022/10/26/creating-a-lego-database.html
api-post: https://rckwzrd.github.io/2022/09/26/counting-lego-bricks.html
cm-doc: https://docs.python.org/3/library/sqlite3.html#sqlite3-connection-context-manager
fmt-doc: https://docs.python.org/3/library/sqlite3.html#sqlite3-placeholders

---

In this post we will bulk load 458 unique lego themes from the Rebrickable API into a local lego database. This task leverages the API and database operations established in the [Counting Lego Bricks]({{page.api-post}}) and [Creating a Lego Database]({{page.lego-post}}) posts. Because we already wrote functions for requesting data and interacting with the database hydrating the themes table is a straightforward operation.

For context lets quickly review the schema for themes table in the lego database:

```bash
$ sqlite3 lego.db 
SQLite version 3.37.2 2022-01-06 13:25:41
...
sqlite> .schema
CREATE TABLE themes (
            theme_id INT PRIMARY KEY,
            theme_name TEXT NOT NULL
        );
```

The themes table has been provisioned to accept a `theme_id` integer as a primary key and a `theme_name` as a non-null text object. These are the two pieces of data that will be collected from the Rebrickable API and dropped into this table for permanent retention.

The procedure below requires minimal new code to execute the bulk load:

1. Define a bulk load python function and SQL statement
2. Request theme data and format for insertion
3. Wrap the procedure in a python function and execute
4. Verify the themes table was successfully hydrated

## Function and Statement

The bulk load function follows patterns established for interacting with the lego database in `lego_db.py` from the [lego database post]({{page.lego-post}}). The only additional components are the `with conn` context manager and the `conn.executemany()` method: 

```python
# lego_db.py
def load_table(conn, load_sql, data):
    try:
        with conn:
            conn.executemany(load_sql, data)
            print("Table Hydrated")
    except sqlite3.Error as e:
        raise e
```

The `conn.executemany()` method accepts a SQL insert statement string and a blob of data as arguements. This method can be used to load blobs of data in one call making it a good fit for the bulk load use case. One caveat is that the data passed needs to be a list of tuples. This requirement will be handled after the themes data is requested. 

The `with conn` context manager wraps the `conn.executemany()` call and commits the insert transactions taking place. If any sort of exception is thrown the context manager will roll back the transactions and protect the database. More information on the context manager can be found in the [context manager documentation]({{page.cm-doc}})

The SQL statement used to execute the bulk load is deceptively simple:

```python
load_sql = """
    INSERT INTO themes VALUES(?, ?)
"""
```
Loading data into a the themes table is triggered by the `INSERT INTO themes` statement. The key piece is the `VALUES` statement. The `?, ?` expression is SQL placeholder syntax that will insert a tuple of `theme_id` and `theme_name` into the themes table. The placeholder syntax is an important tool for loading data and enforcing security. Additional details can be found in the [placeholder documentation]({{page.fmt-doc}}).

## Request and Format

Now that there is a method for loading the themes table we can switch gears and work on requesting the 458 unique lego theme records. Fortunately we dervied an approach for aqcuiring the data in the [counting lego bricks ]({{page.api-post}}) post. Assuming the directory structure is correct, we can simply import the `get_themes()` function from the `lego_api.py` module and call it in the pipeline:

```python
# lego_api.py
def get_themes():
    url = "https://rebrickable.com/api/v3/lego/themes/?page=1&page_size=1000"
    req = requests.get(url=url, headers=headers)
    time.sleep(1.1)
    return req.json()["results"]
```

The single caveat is that the `load_table()` function is expecting a list of tuples. The blob of theme records returned from `get_themes()` can be reshaped into the required data structure with the following list comprehension:

```python
themes = [(i["id"], i["name"]) for i in get_themes()]
```
It is critical to note how fast this section came together when pre-existing code was leveraged to request bulk theme data. Writing generalizable functions takes skill, but it pays dividends in situations like this.

## Wrap and Execute

At this point all of the machinery needed to bulk load the themes table is in place. We just need to orchestrate the operation. To do this we will wrap each step in a function called `load_themes_table()` and then call it directly:

```python
# lego_db.py
def load_theme_table():

    themes = [(i["id"], i["name"]) for i in get_themes()]

    load_sql = """
        INSERT INTO themes VALUES(?, ?)
    """

    db_file = "lego.db"
    conn = connect_db(db_file)
    load_table(conn, load_sql, themes)
    close_db(conn)
    print("Themes table hydrated")

if __name__ == "__main__":
    load_theme_table()
```

This wrapper function requests the full blob of theme records, converts the data, connects to the lego database, loads the records with a SQL statement, and closes the connection. Using the `if __name__` idiom the wrapper can be executed as a script from the command line:

```bash
$ python3 src/lego_db.py
```

## Verify Data

After executing the bulk load we can quickly verify that records filled up the themes table using the SQLite3 terminal interface: 

```bash
$ sqlite3 lego.db 
SQLite version 3.37.2 2022-01-06 13:25:41
...
sqlite> select count(*) from themes;
459
```

```bash
$ sqlite lego.db
SQLite version 3.37.2 2022-01-06 13:25:41
...
sqlite> select * from themes limit 5;
1|Technic
3|Competition
4|Expert Builder
16|RoboRiders
17|Speed Slammers
```

The data is place and the pipeline is succussful! Now that the themes table is hydrated we can start thinking about individual set records, but this a task for another time. Hopefully this demonstrates how code grows over time and hints at the ways that SQL, Python, and APIs can be used to build persistent datastores.

## Full Script

```python
import sqlite3
from lego_api import get_themes

### other functions from lego_db.py
### ... 
### ...

def load_table(conn, load_sql, data):
    try:
        #NOTE: context manager
        with conn:
            conn.executemany(load_sql, data)
            print("Table Hydrated")
    except sqlite3.Error as e:
        raise e

def load_theme_table():
    themes = [(i["id"], i["name"]) for i in get_themes()]

    load_sql = """
        INSERT INTO themes VALUES(?, ?)
    """

    db_file = "lego.db"
    conn = connect_db(db_file)
    load_table(conn, load_sql, themes)
    close_db(conn)

if __name__ == "__main__":
    load_theme_table()
```
