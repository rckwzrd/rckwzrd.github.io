---
layout: post
lego-post: https://rckwzrd.github.io/2022/10/26/creating-a-lego-database.html
api-post: https://rckwzrd.github.io/2022/09/26/counting-lego-bricks.html
cm-doc: https://docs.python.org/3/library/sqlite3.html#sqlite3-connection-context-manager
fmt-dc: https://docs.python.org/3/library/sqlite3.html#sqlite3-placeholders

---

In this post we will bulk load 498 unique lego themes from the Rebrickable API into a local lego database. This task leverages the API and database operations established in the [Counting Lego Bricks]({{page.api-post}}) and [Creating a Lego Database]({{page.lego-post}}) posts. Because we already wrote functions for requesting data and interacting with the database hydrating the themes table is actually a straightforward operation.

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

The procedure below only requires a minimal amount of new code to execute and verify the bulk load:

1. Define a bulk load python function and SQL statement
2. Request theme data and format for insertion
3. Wrap the procedure in a python function and execute
4. Verify the themes table was successfully hydrated

## Function and Statement

```python
def load_table(conn, load_sql, data):
    try:
        #NOTE: context manager
        with conn:
            conn.executemany(load_sql, data)
            print("Table Hydrated")
    except sqlite3.Error as e:
        raise e
```

```python
load_sql = """
    INSERT INTO themes VALUES(?, ?)
"""
```

## Request and Format

```python
def get_themes():
    url = "https://rebrickable.com/api/v3/lego/themes/?page=1&page_size=1000"
    req = requests.get(url=url, headers=headers)
    time.sleep(1.1)
    return req.json()["results"]
```

```python
themes = [(i["id"], i["name"]) for i in get_themes()]
```

## Wrap and Execute

```python
```

## Verify Data

```bash
```
