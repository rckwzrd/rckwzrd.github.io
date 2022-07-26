---
layout: post
excerpt_separator: <!--more-->
---

Apple provides easy access to personal workout data. The challenge is parsing the blob of data.

**Below is a protocol for extracting heart rate time series data.**

Tools:

- [original idea](https://gist.github.com/hoffa/936db2bb85e134709cd263dd358ca309)
- [python json module](https://docs.python.org/3/library/json.html?highlight=json#module-json)
- [python xml api](https://docs.python.org/3/library/xml.etree.elementtree.html)
- [jq json processor](https://stedolan.github.io/jq/)

## Data retrieval

1. Go to health app on iphone
2. Tap profile icon and scroll to bottom
3. Select "export all health data" option
4. Push `export.zip` to cloud storage
5. Retrieve and extract archive on local machine

Note: Export process may take several minutes. Archive could be 10s of MB.

```
unzip export.zip -d /path/to/dir
```

## Data format

```
# file structure
```

Explain contents of each file. Note which file has hear rate data.

## Convert xml to json with python

```
import json
import sys
from xml.etree.ElementTree import iterparse

for _, elem in iterparse(sys.argv[1]):
    if elem.tag == "Record":
        if elem.attrib["type"] == "HKQuantityTypeIdentifierHeartRate":
            print(json.dumps(elem.attrib))
```

Explain that data is in xml. Need to use `parse.py` to find records with heart rate data.

## Stream json to csv with jq

```
python3 parse.py data/export.xml | \
jq -r '[.endDate, .type, .unit, .value] | @csv' \
> data/heart_rate.csv
```

Explain that `parse.py` combined with `jq` will stream heart time series data into a csv file.
