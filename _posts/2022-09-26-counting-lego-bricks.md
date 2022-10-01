---

layout: post
sets: 62
rb-db: https://rebrickable.com/downloads/
rb-api: https://rebrickable.com/api/v3/docs/?key=
rb-reg: https://rebrickable.com/register
dotenv: https://pypi.org/project/python-dotenv/
os: https://docs.python.org/3/library/os.html

---

Lego is a big deal around the house and we maintain a running list of all sets acquired over the years. It is pretty easy to track the total number of sets in the collection (currently {{page.sets}}) by just counting up set numbers. From this knowledge a deeper a question was asked:

>We know how many Lego sets we have, but how many bricks are in the collection?

With sets on display and large boxes of bricks counting by hand was not an option. Enter the [Rebrickable Lego Database]({{page.rb-db}}). This database contains data on every set, brick, and minifig released by Lego ever. The best part is that the database is accessible via an [API]({{page.rb-api}}). So with a bit of Python we can responsibly interact with the API and count the number of bricks in the collection.

Below is a simple workflow for requesting set data and counting bricks:

1. Acquire a Rebrickable API token
2. Set up a Rebrickable `GET` request
3. Request data for a list of sets
4. Convert response into a `dataframe`
5. Count bricks and report results

## Acquire API token

Rebrickable requires a unique token to access the Lego database API. A token can can be aquired by:

1. Make a Rebrickable account [here]({{page.rb-reg}}) and login
2. Navigate to your account settings and select API
3. Copy your 32 character key to an `.env` file
4. Place the `.env` file in the same directory as `lego_api.py`

The `.env` should have the following format:

```
KEY=<copy 32 character key here>
```

API keys are sensitive information and should be obscured. Using an `.env` file will keep the key out of source code and secure access to the Rebrickable API. 

Now the key can be accessed in a Python script as an environment variable using the `os` and `dotenv` modules:

```python
# lego_api.py
import os
from dotenv import load_dotenv

# load api key
load_dotenv()
KEY = os.getenv("KEY")

# set headers
headers = {
        "Accept": "application/json",
        "Authorization": "key " + KEY
}
```

Once the key is loaded and assigned to `KEY` it can be used to construct a request header required to to authenticate with the API. Documentation on `dotenv` can be found at the [package repository]({{page.dotenv}}) and `os` can be referenced in the [standard library]({{page.os}}).

## Set up GET request

Now that the API key is set we can construct a `GET` request with the `requests ` package to pull set data from Rebrickable. One of the quirks of the interface is that data can only be returned for one set at a time. So a unique `GET` request needs to be made for each set in the collection. 

A helper function is used to build the unique  request for a given set and return the data:

```python
# lego-api.py
import requests

# helper to run request
def get_sets(set_id):
    url = f"https://rebrickable.com/api/v3/lego/sets/{set_id}"
    req = requests.get(url=url, headers=headers)
    time.sleep(1.1)
    return req.json()
```

The set id is passed as an arguement and then formatted into url string representing the body of the request. Next the request is executed with the url string and authorization header. The response is saved to a variable. Now the function sleeps for 1.1 seconds to avoid abusing the API. The data is extracted from the response as json and returned.


In Python json is effectively treated as a dictionary. The returned set data has the following form:

```python
{
  "set_num": "31062-1",
  "name": "Robo Explorer",
  "year": 2017,
  "theme_id": 672,
  "num_parts": 205,
  "set_img_url": "https://cdn.rebrickable.com/media/sets/31062-1/9935.jpg",
  "set_url": "https://rebrickable.com/sets/31062-1/robo-explorer/",
  "last_modified_dt": "2018-11-29T23:47:51.919445Z"
}
```

There are several bits of interesting information returned in the set data blob. The `num_parts` key wil be directly used to answer our question.

## Request set data

Now that the `get_sets()` helper function is in place requests can be efficiently executed for every set number in the collection.

With the help of `pandas` and a list comprehension set ids are loaded into a list and `get_sets()` is executed for every set in the collection:

```python
# lego-api.py
import pandas

# load set ids
set_ids = pandas.read_csv("./input_data/sets.csv")["set_num"].tolist()

# request data for each set
set_data = [get_sets(set_id) for set_id in set_ids]
```

The output assigned to `set_data` is a list of dictionaries. Every dictionary has the same keys and values unique to the set. 

## Convert to dataframe and count bricks

Now the accumulated `set_data` can be converted to a dataframe and the `num_parts` column can be summed to obtain a total brick count: 

```python
# lego-api.py
# convert request data to df
set_df = pandas.DataFrame(set_data)

# count sets
sets = len(set_ids)

# count bricks
bricks = set_df["num_parts"].sum()

# report
print(f"Total bricks in {sets} set collection: {bricks}")
```

The result is:
```
>>> Total bricks in 62 set collection: 23653 
```
That is a lot of bricks.

The full script is given below. This is not the most rigourous approach, but it gets the job done. If this was production grade code the request response codes would be checked, errors would be handled, and there would be unit tests.

The next question is what to do with the blob of data acquired from the Rebrickbable API. One option is dropping set data for the collection into a `SQLite` database. From that point it is possible to set up a pipeline to request set data and insert into the database for each new set added to the collection.

That may be a post for another time.

## Full script

```python
# lego-api.py
import os
import requests
import pandas

from dotenv import load_dotenv

# load api key
load_dotenv()
KEY = os.getenv("KEY")

# set headers
headers = {
        "Accept": "application/json",
        "Authorization": "key " + KEY
}

# helper to run request
def get_sets(set_id):
    url = f"https://rebrickable.com/api/v3/lego/sets/{set_id}"
    req = requests.get(url=url, headers=headers)
    time.sleep(1.1)
    return req.json()

# load set ids
set_ids = pandas.read_csv("./input_data/sets.csv")["set_num"].tolist()

# request data for each set
set_data = [get_sets(set_id) for set_id in set_ids]

# convert request data to df
set_df = pandas.DataFrame(set_data)

# count sets
sets = len(set_ids)

# count bricks
bricks = set_df["num_parts"].sum()

# report
print(f"Total bricks in {sets} set collection: {bricks}")

```

