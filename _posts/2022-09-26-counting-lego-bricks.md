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
4. Convert responses into a `dataframe`
5. Count bricks and save results

## Acquire API token

Rebrickable requires a unique token to access the Lego database API. 

A token can can be aquired by:

1. Make a Rebrickable account [here]({{page.rb-reg}}) and login
2. Navigate to your account settings and select API
3. Copy your 32 character key to an `.env` file
4. Place the `.env` file in the same location the request will be ran

The `.env` should have the following format:

```
KEY=<copy 32 character key here>
```

API keys are sensitive information and should be obscured. Using an `.env` file will keep the key out of source code and secure access to the Rebrickable API. 

Now the key can be accessed in a Python script as an environment variable using the `os` and `dotenv` modules:

```python
# lego_api.py

# bring in modules
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

## Request set data

## Convert response to dataframe

## Count and save





