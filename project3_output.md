
# Project III: Traffic Accident Analysis in NYC (2025)

## Introduction

Dataset(s) to be used: 
**Motor Vehicle Collisions Data** from the [NYC Open Data Portal](https://data.cityofnewyork.us/Transportation/Motor-Vehicle-Collisions-2016-to-Present/ijzp-q8t2). This dataset includes information on traffic accidents in New York City from 2016 to the present, including details like the location of accidents, contributing factors, time, and the severity of the accidents.

- **NYPD Arrest Data** for the year 2025 from [NYC Open Data](https://data.cityofnewyork.us/Public-Safety/NYPD-Arrest-Data-Year-to-Date-/uip8-fykc/about_data). This dataset contains detailed information about arrests made in NYC, including arrest date, location, and crime type.

Analysis question: In NYC, do neighborhoods with higher poverty levels experience disproportionately higher arrest rates in 2025?

Hypothesis: Higher poverty neighborhoods have higher arrest rates, especially for violent crimes. We will investigate whether there is a correlation between neighborhoods with higher accident rates and higher arrest rates, considering factors such as contributing factors to accidents and accident severity.

### Loading the Data

We will start by loading the traffic accident data and cleaning it to remove any rows with missing latitude or longitude information.

```python
import pandas as pd
import matplotlib.pyplot as plt
import plotly.express as px

# Load the traffic accident data
crash_data = pd.read_csv('data/Motor_Vehicle.csv')
crash_data.head()

# Data cleaning: Remove records with missing latitude and longitude
crash_data = crash_data.dropna(subset=['LATITUDE', 'LONGITUDE'])
crash_data.head()
```

### GeoDataFrame for Neighborhood Tabulation Areas (NTA)

Next, we load the Neighborhood Tabulation Area (NTA) shapefile, which provides the geographical boundaries for New York City's neighborhoods. We will use this to map the accident data to specific neighborhoods. The NTA dataset includes polygons representing the boundaries of neighborhoods, and we can perform a spatial join with the accident data to understand accident distributions across neighborhoods.

```python
import geopandas as gpd

nta_data = pd.read_csv('data/Tabulation_Areas.csv')

nta_data['geometry'] = gpd.GeoSeries.from_wkt(nta_data['the_geom'])

# Create a GeoDataFrame for NTA
nta_gdf = gpd.GeoDataFrame(nta_data, geometry='geometry')
nta_gdf.set_crs("EPSG:4326", inplace=True)
nta_gdf.head()
```

### GeoDataFrame for Traffic Accidents

Now, we will convert the LATITUDE and LONGITUDE columns in the crash data into geographical points using shapely.geometry.Point. This allows us to perform a spatial join later to count the number of accidents in each neighborhood.

```python
from shapely.geometry import Point
accidents_geometry = [Point(xy) for xy in zip(crash_data['LONGITUDE'], crash_data['LATITUDE'])]
accidents_gdf = gpd.GeoDataFrame(crash_data, geometry=accidents_geometry, crs="EPSG:4326")
```

### Spatial Join to Count Accidents by Neighborhood

Now, we perform a spatial join using overlay to find intersections between accident points and NTA polygons. This will give us the accident count for each NTA.

```python
# Use overlay to find intersections between accident points and NTA polygons
accidents_nta = gpd.overlay(accidents_gdf, nta_gdf, how='intersection')

# Step 8: Count accidents per NTA (Neighborhood Tabulation Area)
accidents_count_by_nta = accidents_nta.groupby('NTAName').size().reset_index(name='ACCIDENT_COUNT')

# Step 9: Merge the accident counts with the NTA GeoDataFrame
nta_gdf = nta_gdf.merge(accidents_count_by_nta, on='NTAName', how='left')
nta_gdf = nta_gdf.drop(columns=['ACCIDENT_COUNT_x', 'ACCIDENT_COUNT_y'])
```

### Visualizing Accident Counts by Neighborhood

We now visualize the number of accidents in each NTA neighborhood using a choropleth map, where darker shades represent neighborhoods with higher accident counts.

```python
fig, ax = plt.subplots(figsize=(10, 10))

# Check if 'ACCIDENT_COUNT' is available before plotting
if 'ACCIDENT_COUNT' in nta_gdf.columns:
    nta_gdf.plot(column='ACCIDENT_COUNT', ax=ax, legend=True,
                 legend_kwds={'label': "Number of Accidents by NTA",
                              'orientation': "horizontal"},
                 cmap='OrRd')

    ax.set_title('Traffic Accidents by Neighborhood in NYC (2025)', fontsize=16)
    plt.show()
else:
    print("The 'ACCIDENT_COUNT' column is not found in the merged GeoDataFrame.")
```

![accident_counts_by_nta](project3_files/accident_counts_by_nta.png)

### Analyzing the Top Accident Hotspots

We will now identify the top 10 neighborhoods with the highest number of accidents. These hotspot neighborhoods will be the focus of further analysis.

```python
# Get the top 10 NTA neighborhoods with the highest accident counts
top_hotspots = nta_gdf[['NTAName', 'ACCIDENT_COUNT']].sort_values(by='ACCIDENT_COUNT', ascending=False).head(10)

# Display the top hotspots
print(top_hotspots)
```

### Contributing Factors to Accidents

We will now analyze the contributing factors to accidents in the top hotspots. We'll combine the contributing factors into a single column and count how frequently each factor occurs.

```python
# Combine all contributing factors into a single column to count each factor
contributing_factors_columns = [
    'CONTRIBUTING FACTOR VEHICLE 1', 'CONTRIBUTING FACTOR VEHICLE 2',
    'CONTRIBUTING FACTOR VEHICLE 3', 'CONTRIBUTING FACTOR VEHICLE 4',
    'CONTRIBUTING FACTOR VEHICLE 5'
]

# Filter accidents that happened in the top NTA neighborhoods
hotspot_accidents = accidents_nta[accidents_nta['NTAName'].isin(top_hotspots['NTAName'])]

# Combine contributing factors into a single column
all_contributing_factors = hotspot_accidents[contributing_factors_columns].stack().value_counts()

# Display the most common contributing factors
print(all_contributing_factors)
```

![contributing_factors](project3_files/contributing_factors.png)

### Time of Day Analysis

We will now examine how accidents are distributed across day and night in the hotspot neighborhoods, based on the time of day.

```python
# Convert 'CRASH_TIME' to datetime
hotspot_accidents['CRASH TIME'] = pd.to_datetime(hotspot_accidents['CRASH TIME'], errors='coerce')

# Extract the hour of the accident
hotspot_accidents['hour'] = hotspot_accidents['CRASH TIME'].dt.hour

# Create a 'time_of_day' column based on the hour (Day: 6AM-6PM, Night: 6PM-6AM)
hotspot_accidents['time_of_day'] = hotspot_accidents['hour'].apply(lambda x: 'Day' if 6 <= x < 18 else 'Night')

# Group by time of day and count the accidents
time_of_day_accidents = hotspot_accidents.groupby('time_of_day').size()

# Create the pie chart using Plotly
fig = px.pie(time_of_day_accidents, 
             names=time_of_day_accidents.index, 
             values=time_of_day_accidents.values,
             title='Accidents by Time of Day in Hotspot Areas')

# Save the plot
fig.write_image("time_of_day_accidents.png")
```

![time_of_day_accidents](project3_files/time_of_day_accidents.png)

### Summary and Reflection

This project aims to explore the correlation between traffic accidents and neighborhood factors in New York City. The analysis reveals significant hotspots where accidents are most concentrated. By analyzing contributing factors, we found that "Driver Inattention" is the leading cause of accidents, which suggests potential areas for safety interventions.

In terms of the time of day, accidents are more likely to occur during the daytime, which could be attributed to higher traffic volume during the day. However, nighttime accidents, although fewer, still represent a significant portion and may be attributed to factors like impaired driving or fatigue.

In summary, while accident rates are concentrated in certain neighborhoods, the contributing factors provide valuable insights into potential interventions. Additionally, the time-of-day analysis could guide targeted efforts to improve safety during peak hours.

### Reflection

This project has provided an opportunity to better understand how traffic accidents are distributed across neighborhoods in New York City. By utilizing data from both traffic accident records and neighborhood tabulation areas, we were able to gain insights into accident severity, contributing factors, and the time of day. The next steps would involve analyzing further socioeconomic factors, such as poverty levels, to understand how these influence accident rates.

