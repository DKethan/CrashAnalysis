# Mid Term Project - Analyzing New York City Data with SQL, Python, and Version Control

### Project Report: Motor Vehicle Collision

---

### 1. Introduction

The objective of this project is to perform a comprehensive exploration, analysis, and insight generation from real-world data on motor vehicle collisions in New York City. Using open datasets from the NYC Open Data platform, this project applies data analysis techniques and collaborative development tools to explore factors affecting road safety, vehicle dynamics, and demographic attributes of urban traffic incidents. 

Key technologies:
- **SQL** for data merging and retrieval.
- **Python** for analysis, data processing, and visualization.
- **Version Control** to facilitate teamwork and track project versions.

By integrating data across `crashes_df`, `person_df`, and `vehicles_df` on the common identifier `COLLISION_ID`, this project involves data preprocessing, cleansing, and merging for effective exploratory data analysis (EDA). The aim is to uncover insights, detect patterns, and analyze factors associated with collisions, supporting initiatives like NYC's Vision Zero to improve traffic safety.

---

### 2. Project Data Flow

#### 2.1 Dataset Overview

- **Crashes Table**: Detailed records of NYC crash incidents, focusing on event circumstances.
- **Vehicles Table**: Data on each vehicle involved in crashes, including details on make, model, and contributing factors.
- **Person Table**: Records for each individual involved (drivers, pedestrians, etc.), including demographic and injury data.

#### 2.2 Dataset Structure

Each dataset contains multiple columns detailing crash information, vehicle specifics, and individual demographics. Key columns include `CRASH DATE`, `VEHICLE TYPE`, `PERSON TYPE`, `CONTRIBUTING FACTORS`, and more.

---

### 3. Data Preprocessing and Cleaning

#### 3.1 Loading the Dataset

Data from the Motor Vehicle Collision [Vehicles, Person, Crashes] datasets were imported into pandas DataFrames, converted to CSV for accessibility, and processed using functions like `.shape`, `.info()`, and `.describe()`.

#### 3.2 Handling Missing Values and Duplicates

- **Crashes Table**: NaN values in columns like `NUMBER OF PERSONS INJURED` were replaced with 0. Duplicates and irrelevant columns (e.g., `ZIP CODE`) were removed.
- **Vehicles Table**: Missing values in columns like `CONTRIBUTING_FACTOR` were filled with 'Unspecified', and duplicates were checked and handled.
- **Person Table**: Missing data in key columns was handled; columns with high NaN values were dropped, and duplicates were removed.

---

### 4. SQL Queries

#### 4.1 Collision Analysis by Vehicle Type
```sql
SELECT "VEHICLE TYPE CODE 1", COUNT(*) AS type_count
FROM crashes
GROUP BY "VEHICLE TYPE CODE 1"
ORDER BY type_count DESC;
```

#### 4.2 Location Analysis: Accident Frequency by Borough

This query analyzes the distribution of traffic accidents across different boroughs.

```sql
SELECT BOROUGH, COUNT(*) AS accident_count
FROM crashes
GROUP BY BOROUGH
ORDER BY accident_count DESC;
```


#### 4.3 Contributing Factors and Injury Severities

This query examines the relationship between the primary contributing factors of vehicle crashes and the resulting severity of injuries.

```sql
SELECT "contributing factor vehicle 1", Person_injury, COUNT(*) AS severity_count
FROM crashes 
JOIN person ON crashes.COLLISION_ID = person.COLLISION_ID
GROUP BY "contributing factor vehicle 1", Person_injury
ORDER BY severity_count DESC;
```


#### 4.4 Highest Contributing Factor of Crashes

This query identifies the contributing factors most frequently associated with crashes.

```sql
SELECT "contributing factor vehicle 1", COUNT(*) AS crash_count
FROM crashes
GROUP BY "contributing factor vehicle 1"
ORDER BY crash_count DESC;
```

#### 4.5 Total Injuries by Contributing Factor and Vehicle Type

This query finds the total number of injuries for each combination of contributing factor and vehicle type.

```sql
SELECT v."CONTRIBUTING_FACTOR_1", v."VEHICLE_TYPE", COUNT(p.PERSON_INJURY) AS total_injuries
FROM vehicles AS v 
JOIN person AS p ON v.COLLISION_ID = p.COLLISION_ID
WHERE p.PERSON_INJURY = 'Injured'
GROUP BY v."CONTRIBUTING_FACTOR_1", v."VEHICLE_TYPE"
ORDER BY total_injuries DESC
LIMIT 10;
```

#### 4.6 Top Crash Dates with the Most Injuries by Contributing Factor

This query identifies the dates with the highest number of injuries for each contributing factor.

```sql
SELECT c."CRASH DATE", v."CONTRIBUTING_FACTOR_1", COUNT(p."PERSON_INJURY") AS total_injuries
FROM crashes AS c 
JOIN person AS p ON c.COLLISION_ID = p.COLLISION_ID
JOIN vehicles AS v ON c.COLLISION_ID = v.COLLISION_ID
WHERE p."PERSON_INJURY" = 'Injured'
GROUP BY c."CRASH DATE", v."CONTRIBUTING_FACTOR_1"
ORDER BY total_injuries DESC
LIMIT 20;
```

#### 4.7 Number of Persons Injured Based on Their Position in the Vehicle

This query analyzes the relationship between the seating position of individuals in a vehicle and the number of injuries.

```sql
SELECT p.POSITION_IN_VEHICLE, SUM(c."NUMBER OF PERSONS INJURED") AS total_injuries
FROM person AS p 
JOIN crashes AS c ON p.COLLISION_ID = c.COLLISION_ID
WHERE c."NUMBER OF PERSONS INJURED" > 0
GROUP BY p.POSITION_IN_VEHICLE
ORDER BY total_injuries DESC;
```

#### 4.8 Collisions Involving Vehicles Registered in Different States

This query counts the number of unique collisions involving vehicles registered in various states.

```sql
SELECT STATE_REGISTRATION, COUNT(DISTINCT COLLISION_ID) AS collision_count
FROM vehicles
GROUP BY STATE_REGISTRATION
ORDER BY collision_count DESC;
```

#### 4.9 Number of Injuries Based on Safety Equipment Usage

This query examines the relationship between safety equipment usage and injury outcomes.

```sql
SELECT SAFETY_EQUIPMENT, PERSON_INJURY, COUNT(*) AS injury_count
FROM person
GROUP BY SAFETY_EQUIPMENT, PERSON_INJURY
ORDER BY SAFETY_EQUIPMENT, injury_count DESC;
```

#### 4.10 Pre-Crash Situation and Contributing Factor by Injury Count

This query analyzes the relationship between the pre-crash actions and contributing factors based on injury counts.

```sql
SELECT v.PRE_CRASH, v.CONTRIBUTING_FACTOR_1, SUM(c."NUMBER OF PERSONS INJURED") AS total_injuries
FROM vehicles AS v 
JOIN crashes AS c ON v.COLLISION_ID = c.COLLISION_ID
WHERE c."NUMBER OF PERSONS INJURED" > 0
GROUP BY v.PRE_CRASH, v.CONTRIBUTING_FACTOR_1
ORDER BY total_injuries DESC;
```


# Limitations of the Project

1. **Data Quality and Completeness**: The accuracy and completeness of the datasets, including crashes, person, and vehicles, are critical. Missing or inaccurately recorded data on factors such as injury severity, vehicle types, and contributing factors may affect the reliability of insights. Incomplete fields in `PRE_CRASH`, `SAFETY_EQUIPMENT`, and `CONTRIBUTING_FACTOR_1` could lead to skewed or incomplete conclusions.

2. **Scope of Contributing Factors**: The available data may not account for all potential contributing factors, such as road conditions, weather, or infrastructure quality, which are crucial for a comprehensive analysis of traffic accidents. Without this context, conclusions might overlook significant external factors.

3. **Contextual Factors in Behavior Analysis**: Behavioral factors such as `DISTRACTED DRIVING` or `AGGRESSIVE DRIVING` are often self-reported or observed after the incident, which can introduce reporting bias. This can affect the analysis of behavior-related factors contributing to crashes.

4. **Lack of Individualized Data**: Aggregating data at a group level, such as by borough or vehicle type, may overlook unique individual characteristics that could influence crash outcomes, such as driving experience, age, or personal driving habits.

5. **Memory Consumption**: Due to the size of the merged data, the memory consumption is increased, which affects the system's efficiency.
   

# Conclusions

1. **High-Risk Contributing Factors**: Certain behaviors, such as distracted driving and speeding, show a clear correlation with severe injuries. This reinforces the importance of targeted enforcement and awareness campaigns to mitigate these behaviors.

2. **Significance of Safety Equipment**: The analysis supports the role of safety equipment in reducing injury severity. Findings may encourage policymakers to strengthen safety equipment regulations and educate the public on proper equipment use, particularly for high-risk seating positions.

3. **Impact of Pre-Crash Actions**: Certain pre-crash actions, such as making a left turn or lane changes, emerge as particularly risky, especially when combined with high-risk behaviors. This suggests that infrastructure improvements, such as clearer lane markings and signage, could enhance safety in these scenarios.

4. **Identification of High-Injury Locations**: Geographic patterns in accident data, such as high injury counts in specific boroughs, can inform regional traffic safety initiatives, optimized patrolling, and infrastructure improvements.
