---
layout: post
lego-post: https://rckwzrd.github.io/2022/10/26/creating-a-lego-database.html
api-post: https://rckwzrd.github.io/2022/09/26/counting-lego-bricks.html
cm-doc: https://docs.python.org/3/library/sqlite3.html#sqlite3-connection-context-manager
fmt-dc: https://docs.python.org/3/library/sqlite3.html#sqlite3-placeholders

---

Intro paragraph. Bulk load. Use functions defined in lego-api.py and lego-db.py as a starting point. Review themes table schema.

```bash
$ sqlite3 lego.db 
SQLite version 3.37.2 2022-01-06 13:25:41
sqlite>
```

```bash
sqlite> .schema
CREATE TABLE themes (
            theme_id INT PRIMARY KEY,
            theme_name TEXT NOT NULL
        );
```

List of steps:

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
