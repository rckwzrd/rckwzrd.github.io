---
layout: post
excerpt_separator: <!--more-->
---

Apple provides easy access to personal health data. The challenge is parsing the blob of data and extracting useful features.

Below is a protocol for streaming heart rate telemetry from a large `xml` document into a simple `csv` file on a personal linux laptop. 

Tools:

- [original idea](https://gist.github.com/hoffa/936db2bb85e134709cd263dd358ca309)
- [python json module](https://docs.python.org/3/library/json.html?highlight=json#module-json)
- [python xml api](https://docs.python.org/3/library/xml.etree.elementtree.html)
- [jq json processor](https://stedolan.github.io/jq/)

## Retrieve health data

1. Go to health app on iphone
2. Tap profile icon and scroll to bottom
3. Select "export all health data" option
4. Push `export.zip` to cloud storage
5. Retrieve and extract archive on local machine

The export process may take several minutes. Archive could be 10s of MB.

The extracted health data has the following structure:

```
export
└── apple_health_export
    ├── export_cda.xml
    ├── export.xml
    └── workout-routes
        ├── route_2019-03-11_1.19pm.gpx
        ├── route_2019-03-12_1.29pm.gpx
        ├── route_2019-03-13_6.58pm.gpx
        ├── ......
```

All health telemetry is rolled up in the `export.xml` file. This file can be 100s of MB. 

Within `export.xml` the `HKQuantityTypeIdentifierHeartRate` record type holds heart rate data:

```
<Record type="HKQuantityTypeIdentifierHeartRate" 
    ...
    unit="count/min" 
    creationDate="2019-03-02 20:42:24 -0500" 
    startDate="2019-03-02 19:47:18 -0500" 
    endDate="2019-03-02 19:47:18 -0500" 
    value="79">
    ...
</Record>
```


## Parse heart rate data with python

Each record in `export.xml` can be parsed with the python xml api `iterparse` function and filtered using the `HKQuantityTypeIdentifierHeartRate` type.

Filtered heart rate records are then converted from `xml` to `json` and printed to a terminal for the next processing step.

Depending on the size of the `export.xml` the python script may run for several minutes:

```python
# parse.py
import json
import sys
from xml.etree.ElementTree import iterparse

for _, elem in iterparse(sys.argv[1]):
    if elem.tag == "Record":
        if elem.attrib["type"] == "HKQuantityTypeIdentifierHeartRate":
            print(json.dumps(elem.attrib))
```


## Stream heart rate json into csv with jq

Now that `json` like heart rate records are being printed to the terminal they can be picked up in a unix style pipeline.

The pipeline will stream heart rate data into `jq` to extract telemetry and then pump formatted data into a clean `csv`.

The entire pipeline can be ran in a bash terminal:

```bash
python3 parse.py data/export.xml | \
jq -r '[.endDate, .type, .unit, .value] | @csv' \
> data/heart_rate.csv
```

Now the fully parsed and neatly formatted heart rate data can be picked up by `pandas` and other tools for deeper analysis. 

If other records need to be extracted:

1. Modify the `parse.py` script to filter on a given type
2. Adjust the arguments in the `jq` command
3. Define a different `csv` name

While not the most performant, this method can reliably parse a large Apple health `export.xml` on a personal linux laptop. 
