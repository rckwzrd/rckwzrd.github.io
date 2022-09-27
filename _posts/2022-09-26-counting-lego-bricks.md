---

layout: post
sets: 62
rb-db: https://rebrickable.com/downloads/
rb-api: https://rebrickable.com/api/v3/docs/?key=

---

Lego is a big deal around the house and we maintain a running list of all sets acquired over the years. It is pretty easy to track the total number of sets in the collection (currently {{page.sets}}) by just counting up set numbers. From this knowledge a deeper a question was asked:

>We know how many Lego sets we have, but how many bricks are in the collection?

With sets on display and large boxes of bricks counting by hand was not an option. Enter the [Rebrickable Lego Database]({{page.rb-db}}). This database contains data on every set, brick, and minifig released by Lego ever. The best part is that the database is accessible via an [API]({{page.rb-api}}). So with a bit of Python we can responsibly interact with the API and count the number of bricks in the collection.

Below is a simple workflow for requesting set data with `requests` and counting bricks with `pandas`:

1. Acquire a Rebrickable API token
2. Load a list of set numbers from `csv` 
3. Set up and run a Rebrickable `GET` request
3. Convert request responses into a `dataframe`
5. Count bricks and save results



