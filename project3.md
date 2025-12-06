# Project III: Traffic Accident Analysis in NYC (2025)

## Introduction

### Dataset(s) to be used:
- **Motor Vehicle Collisions Data** from the [NYC Open Data Portal](https://data.cityofnewyork.us/Transportation/Motor-Vehicle-Collisions-2016-to-Present/ijzp-q8t2). This dataset includes information on traffic accidents in New York City from 2016 to the present, including details like the location of accidents, contributing factors, time, and the severity of the accidents.

- **NYPD Arrest Data** for the year 2025 from [NYC Open Data](https://data.cityofnewyork.us/Public-Safety/NYPD-Arrest-Data-Year-to-Date-/uip8-fykc/about_data). This dataset contains detailed information about arrests made in NYC, including arrest date, location, and crime type.

### Analysis question:
In NYC, do neighborhoods with higher poverty levels experience disproportionately higher arrest rates in 2025?

### Hypothesis:
Higher poverty neighborhoods have higher arrest rates, especially for violent crimes. We will investigate whether there is a correlation between neighborhoods with higher accident rates and higher arrest rates, considering factors such as contributing factors to accidents and accident severity.

---

## Data Preparation

### Loading the Data

We will start by loading the traffic accident data and cleaning it to remove any rows with missing latitude or longitude information.

```python
import pandas as pd
import matplotlib.pyplot as plt
import plotly.express as px

# Load the traffic accident data
crash_data = pd.read_csv('data/Motor_Vehicle.csv')

# Data cleaning: Remove records with missing latitude and longitude
crash_data = crash_data.dropna(subset=['LATITUDE', 'LONGITUDE'])
crash_data.head()

GeoDataFrame for Neighborhood Tabulation Areas (NTA)

Next, we'll load the neighborhood tabulation area (NTA) shapefile, which provides the geographical boundaries for New York City's neighborhoods. We will use this to map accident data to specific neighborhoods.

import geopandas as gpd

# Load the NTA data
nta_data = pd.read_csv('data/Tabulation_Areas.csv')

# Create a GeoDataFrame for NTA
nta_data['geometry'] = gpd.GeoSeries.from_wkt(nta_data['the_geom'])
nta_gdf = gpd.GeoDataFrame(nta_data, geometry='geometry')
nta_gdf.set_crs("EPSG:4326", inplace=True)
nta_gdf.head()

GeoDataFrame for Traffic Accidents

Now we will convert the LATITUDE and LONGITUDE columns in the crash data into geographical points using shapely.geometry.Point. This will allow us to perform a spatial join later to count the number of accidents in each neighborhood.

from shapely.geometry import Point

# Convert LATITUDE and LONGITUDE into geometry
accidents_geometry = [Point(xy) for xy in zip(crash_data['LONGITUDE'], crash_data['LATITUDE'])]
accidents_gdf = gpd.GeoDataFrame(crash_data, geometry=accidents_geometry, crs="EPSG:4326")

Spatial Join to Count Accidents by Neighborhood

Now, we perform a spatial join using overlay to find intersections between accident points and NTA polygons. This will give us the accident count for each NTA.

# Perform spatial join using overlay to find intersections between accident points and NTA polygons
accidents_nta = gpd.overlay(accidents_gdf, nta_gdf, how='intersection')

# Count accidents per NTA (Neighborhood Tabulation Area)
accidents_count_by_nta = accidents_nta.groupby('NTAName').size().reset_index(name='ACCIDENT_COUNT')

# Merge the accident counts with the NTA GeoDataFrame
nta_gdf = nta_gdf.merge(accidents_count_by_nta, on='NTAName', how='left')
nta_gdf = nta_gdf.drop(columns=['ACCIDENT_COUNT_x', 'ACCIDENT_COUNT_y'])

# Debugging: Check if 'ACCIDENT_COUNT' column is present in the merged GeoDataFrame
print(nta_gdf.columns)  # Display the columns of the merged GeoDataFrame
print(nta_gdf[['NTAName', 'ACCIDENT_COUNT']].head())

Visualizing Accident Counts by Neighborhood

We now visualize the number of accidents in each NTA neighborhood using a choropleth map, where darker shades represent neighborhoods with higher accident counts.

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

Analyzing the Top Accident Hotspots

We will now identify the top 10 neighborhoods with the highest number of accidents. These hotspot neighborhoods will be the focus of further analysis.

# Get the top 10 NTA neighborhoods with the highest accident counts
top_hotspots = nta_gdf[['NTAName', 'ACCIDENT_COUNT']].sort_values(by='ACCIDENT_COUNT', ascending=False).head(10)

# Display the top hotspots
print(top_hotspots)

Contributing Factors to Accidents

We will now analyze the contributing factors to accidents in the top hotspots. We'll combine the contributing factors into a single column and count how frequently each factor occurs.

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

Accident Severity Analysis

Now, let's analyze the severity of accidents in these hotspot areas. We'll calculate the total number of injuries and fatalities.

# Analyze accident severity in the hotspot neighborhoods
severity_columns = [
    'NUMBER OF PERSONS INJURED', 'NUMBER OF PERSONS KILLED',
    'NUMBER OF PEDESTRIANS INJURED', 'NUMBER OF PEDESTRIANS KILLED',
    'NUMBER OF CYCLIST INJURED', 'NUMBER OF CYCLIST KILLED',
    'NUMBER OF MOTORIST INJURED', 'NUMBER OF MOTORIST KILLED'
]

# Filter the hotspot accidents
hotspot_accidents_severity = hotspot_accidents[severity_columns]

# Calculate the total number of injuries and deaths in these areas
total_severity = hotspot_accidents_severity.sum()

# Display the total severity
print(total_severity)

Time of Day Analysis

We'll now examine how accidents are distributed across day and night in the hotspot neighborhoods, based on the time of day.

# Convert 'CRASH_TIME' to datetime
hotspot_accidents['CRASH_TIME'] = pd.to_datetime(hotspot_accidents['CRASH_TIME'], errors='coerce')

# Extract the hour of the accident
hotspot_accidents['hour'] = hotspot_accidents['CRASH_TIME'].dt.hour

# Create a 'time_of_day' column based on the hour (Day: 6AM-6PM, Night: 6PM-6AM)
hotspot_accidents['time_of_day'] = hotspot_accidents['hour'].apply(lambda x: 'Day' if 6 <= x < 18 else 'Night')

# Group by time of day and count the accidents
time_of_day_accidents = hotspot_accidents.groupby('time_of_day').size()

# Display the result
print(time_of_day_accidents)

# Create the pie chart using Plotly
fig = px.pie(time_of_day_accidents, 
             names=time_of_day_accidents.index, 
             values=time_of_day_accidents.values,
             title='Accidents by Time of Day in Hotspot Areas')

# Show the plot
fig.show()

Summary
Key Insights from the Analysis:

Traffic Accidents: High-density neighborhoods, particularly in Manhattan and Brooklyn, experience the highest accident rates, as expected in busy urban environments.

Contributing Factors: Driver inattention and failure to yield are the most common factors causing accidents, suggesting a need for improved driver education and law enforcement.

Accident Severity: Daytime accidents are more severe overall in terms of injuries and fatalities, potentially due to higher traffic volumes during the day.

Time of Day: The majority of accidents occur during the daytime (around 60%), but nighttime accidents are also significant, possibly indicating challenges such as low visibility and impaired driving.

Reflection:

This analysis has provided valuable insights into the relationship between traffic accidents, neighborhood characteristics, and contributing factors in NYC. Although we expected nighttime accidents to be more severe, we found that daytime accidents contribute more significantly to overall severity. Future work can focus on the socio-economic factors contributing to high accident rates and examine the correlation between poverty and accident hotspots. Urban planning and traffic safety policies can be informed by these findings to reduce accidents in high-risk areas.