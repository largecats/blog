---
layout: post
title:  "Turning addresses into coordinates via Google Map API"
date:   2019-07-30
categories: fun
tags: python
---

* content
{:toc}

I wanted to turn a list of addresses into coordinates and display them on a map widget.



## Preparation

I read [Google Map's guide on API key](https://developers.google.com/maps/documentation/embed/get-api-key).

## Method
First, create a Google Map API key following the above guide.

Then, import the necessary modules.
```python
import re
import pandas as pd
import numpy as np
import csv
import time
from time import sleep
import random
import googlemaps
import os
```
Read in the list of addresses from a `.csv` file and set up the geolocator using the API key created beforehand.
```python
# set working directory
path = ""
os.chdir(path)
API_key = ""
inputFileName = "addresses.csv"
outputFileName = "coordinates.csv"
pauseTime = [1,3]

# read in addresses
def read_csv_into_list(fileName):
  data = []
  with open(fileName, 'r', encoding = 'UTF-8') as f:
    for row in f:
      row = row.strip()
      data.append(row)
      print(row)
  data.pop(0)
  return data

addressList = read_csv_into_list(inputFileName)

# set up locator
geolocator = googlemaps.Client(key = API_key)

# set up output file
with open(outputFileName, 'w', encoding = 'UTF-8', newline = "") as outputFile:
  colNames = ['addresses', 'coordinates']
  writer = csv.writer(outputFile)
  writer.writerow(colNames)
```
Finally, loop through the list of addresses and obtain the latitude and longtitude for each address.
```python
for address in addressList:
  # pause the loop for a random amount of time
  sleep(random.randint(pauseTime[0], pauseTime[1]))
  location = geolocator.geocode(address)
  try:
    # record latitude and longitude
    coordinates = (location[0]['geometry']['location']['lat'], location[0]['geometry']['location']['lng'])
  except:
    coordinates = "NA"
  print(coordinates)
  with open(outputFileName, 'w', encoding = 'UTF-8', newline = "") as outputFile:
    data = [address, coordinates]
    print(data)
    writer = csv.writer(outputFile)
    writer.writerow(data)
```

## Result

Consider the sample input file `address.csv` shown below, 

![](/images/addresses.png){:width="800px"}
<div align="center">
<sup>List of addresses.</sup>
</div>

we obtain the output file `coordinates.csv` shown below.

![](/images/coordinates.png){:width="800px"}
<div align="center">
<sup>List of addresses together with latitude and longitude obtained via the geolocator.</sup>
</div>