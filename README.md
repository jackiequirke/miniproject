import matplotlib.pyplot as plt
import shapely as shp
import geopandas as gpd
import numpy as np
import datetime as dt
# import folium
import zoneinfo
import json
import random
import warnings
import pandas as pd


# imports sample data from Clarksville Pike and Buchanun St.
with open('./sample_data.json', 'r') as f:
    trajectories = json.load(f)
    print(f"Loaded {len(trajectories)} object trajectories.")


    for key, value in trajectories[0].items():
    if not isinstance(value, list):
        print(key, ": ", value)
    else:
        print(key, ": ", value[:6], "...")


        # print classes and locatoins in datatset
classes = set([traj['classification'] for traj in trajectories])
print("Available classes:", classes)
locations = set([traj['location_id'] for traj in trajectories])
print("Available locations:", locations)


# Create histogram of locations with speeds and frequency
loc_names = ['Inter: 26th & Clarksville', 'Inter: 25th & Clarksville', 'Mid-block: 24th/Clarksville',
             'Mid-block: 23rd/Clarksville', 'Inter: DB Todd & Clarksville', 'Inter: DB Todd & Buchanan',
             'Mid-block: 14th/Buchanan', 'Inter: 9th & Buchanan']
fig, axs = plt.subplots(2, 4, figsize=(18, 8))

for loc, loc_name, ax in zip(range(1, len(loc_names)+1), loc_names, axs.flatten()):
    median_speeds = []
    pct85_speeds = []
    # median and 85th percentile speed.
    for traj in trajectories:
        if traj['location_id'] == loc:
            # vehicle and large vehicle
            if traj['classification'] in ('VEHICLE', 'LARGE_VEHICLE'):
                # velocity in mph
                traj_speed_mph = np.sqrt(np.array(traj['vel_x'])**2 + np.array(traj['vel_y'])**2) * 2.23694
                # only take trajectories with 99th percentile above .5 mph
                if np.quantile(traj_speed_mph, 0.99) < 0.5:
                    continue
                median_speeds.append(np.quantile(traj_speed_mph, 0.5))
                pct85_speeds.append(np.quantile(traj_speed_mph, 0.85))
    # Plot histograms
    ax.hist(median_speeds, bins=25, histtype='step', fill=False, label="Median", color='green')
    ax.hist(pct85_speeds, bins=25, histtype='step', fill=False, label="85th pct.", color ='purple')
    ax.set_xlabel("Vehicle speed (mph)")
    ax.set_ylabel("Frequency (num. traj.)")
    ax.set_title(loc_name)
    ax.legend()
plt.tight_layout()
plt.show()


# Create histogram of locations with speeds and frequency
loc_names = ['Inter: 26th & Clarksville', 'Inter: 25th & Clarksville', 'Mid-block: 24th/Clarksville',
             'Mid-block: 23rd/Clarksville', 'Inter: DB Todd & Clarksville', 'Inter: DB Todd & Buchanan',
             'Mid-block: 14th/Buchanan', 'Inter: 9th & Buchanan']
fig, axs = plt.subplots(2, 4, figsize=(18, 8))

for loc, loc_name, ax in zip(range(1, len(loc_names)+1), loc_names, axs.flatten()):
    median_speeds = []
    pct85_speeds = []
    # median and 85th percentile speed.
    for traj in trajectories:
        if traj['location_id'] == loc:
            # vehicle and large vehicle
            if traj['classification'] in ('VEHICLE', 'LARGE_VEHICLE'):
                # velocity in mph
                traj_speed_mph = np.sqrt(np.array(traj['vel_x'])**2 + np.array(traj['vel_y'])**2) * 2.23694
                # only take trajectories with 99th percentile above 7 mph
                if np.quantile(traj_speed_mph, 0.99) < 7:
                    continue
                median_speeds.append(np.quantile(traj_speed_mph, 0.5))
                pct85_speeds.append(np.quantile(traj_speed_mph, 0.85))
    # Plot histograms
    ax.hist(median_speeds, bins=25, histtype='step', fill=False, label="Median", color='green')
    ax.hist(pct85_speeds, bins=25, histtype='step', fill=False, label="85th pct.", color ='purple')
    ax.set_xlabel("Vehicle speed (mph)")
    ax.set_ylabel("Frequency (num. traj.)")
    ax.set_title(loc_name)
    ax.legend()
plt.tight_layout()
plt.show()





# find total median and total 85th percentile speeds for the entire corridor
all_median_speeds = []
all_pct85_speeds = []
# use all trajectories in the data set
for traj in trajectories:
    if traj['classification'] in ('VEHICLE', 'LARGE_VEHICLE'):
        traj_speed_mph = np.sqrt(np.array(traj['vel_x'])**2 + np.array(traj['vel_y'])**2) * 2.23694
        if np.quantile(traj_speed_mph, 0.95) < 5:
            continue
        all_median_speeds.append(np.quantile(traj_speed_mph, 0.5))
        all_pct85_speeds.append(np.quantile(traj_speed_mph, 0.85))

# print total median and total 85th percentile speeds
total_median_speed = np.median(all_median_speeds)
total_pct85_speed = np.quantile(all_pct85_speeds, 0.85)
print(f"Total median speed: {total_median_speed:.2f} mph")
print(f"Total 85th percentile speed: {total_pct85_speed:.2f} mph")

# plot histograms
fig, ax = plt.subplots(figsize=(8, 6))
ax.hist(all_median_speeds, bins=25, histtype='step', fill=False, label="Median")
ax.hist(all_pct85_speeds, bins=25, histtype='step', fill=False, label="85th pct.")
ax.axvline(x=total_median_speed, color='r', linestyle='--', label='Total median')
ax.axvline(x=total_pct84_speed, color='g', linestyle='--', label='Total 85th pct.')
ax.set_xlabel("Vehicle speed (mph)")
ax.set_ylabel("Frequency (num. traj.)")
ax.set_title("Speed Profiles along the Corridor")
ax.legend()
plt.tight_layout()
plt.show()



# import location 3 dataset
with open('./jackie_mp2_loc3_data.json', 'r') as f:
    trajectories2 = json.load(f)
    print(f"Loaded {len(trajectories2)} object trajectories.")

for key, value in trajectories2[0].items():
    if not isinstance(value, list):
        print(key, ": ", value)
    else:
        # If the value type is a list, just print the first few values.
        print(key, ": ", value[:6], "...")


np.random.seed(42)  # For reproducibility
times_of_day = pd.date_range(start="06:00", end="19:00", freq="T")  # Every minute from 6am to 7pm
num_samples = len(times_of_day)

# Generate synthetic speed data (0 to 65 mph)
vehicle_speeds = np.random.uniform(0, 65, num_samples)

# Convert times_of_day to hours for plotting purposes
times_in_hours = [time.hour + time.minute / 60.0 for time in times_of_day]

# Plotting
plt.figure(figsize=(14, 6))
plt.hist2d(times_in_hours, vehicle_speeds, bins=[50, 30], cmap='viridis')

# Add color bar to represent frequency
cbar = plt.colorbar()
cbar.set_label("Frequency of Speeds")

# Labels and titles
plt.xlabel("Time of Day (hours)")
plt.ylabel("Vehicle Speed (mph)")
plt.title("Vehicle Speed vs Time of Day")
plt.xlim(6, 19)
plt.ylim(0, 65)

# Customize x-axis to show times in HH:MM format for readability
plt.xticks(range(6, 20), [f"{hour}:00" for hour in range(6, 20)])
plt.show()


