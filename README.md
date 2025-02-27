# Extracting Raster Values at Point Locations & Exporting to Excel

## Overview

This script extracts raster values at specific point locations from a set of geospatial raster files. It reads a shapefile containing point coordinates, iterates through a directory of raster datasets, and samples the raster values at each point location.

---
## Code Explanation

### **Step 1: Import Required Libraries**
Load essential libraries for spatial data processing:
```python
import rasterio
import geopandas as gpd
import glob
```
### **Step 2: Read Point Locations from Shapefile**
Load the shapefile containing point locations and extract relevant columns:
```python
pts = gpd.read_file('FloodPoints.shp')
pts = pts[['geometry', 'Time', 'lat', 'lon']]
```
Convert point geometries into coordinate tuples for sampling raster data:
```python
coords = [(x, y) for x, y in zip(pts['geometry'].x, pts['geometry'].y)]
```
### **Step 3: Retrieve Raster Files from Directory**
Use glob to dynamically retrieve all raster files from a specified directory:
```python
dirs = glob.glob('./TOPSIS-Python-master/DynamicVul TOPSIS P/Scenario3/P1km/DynamicVulmaps/*')
```
### **Step 4: Extract Raster Values for Each Point**
Iterate through each raster file and extract values at specified point locations:
```python
for i in range(len(dirs)):
    # Open the raster file
    src = rasterio.open(dirs[i])
    
    # Extract a unique identifier from the filename for column naming
    raster_value = dirs[i][-18:-10]
    
    # Sample raster values at point locations and store them in the dataframe
    pts[raster_value] = [x[0] for x in src.sample(coords)]
```
### **Step 5: Export Extracted Data to Excel**
Save the processed data as an Excel file:
```python
pts.to_excel(f"./Scenario3_P1km.xlsx")
```
## Alternative Excel Export (Without Brackets)
For a more refined export format:
```python
import numpy as np
import pandas as pd
df = pd.DataFrame(pts)
for col in df:
    df[col] = np.squeeze(df[col].tolist())
df.to_excel(f"./Scenario3_P1km.xlsx")
```
This alternative method ensures that exported values are not wrapped in brackets when written to Excel.
