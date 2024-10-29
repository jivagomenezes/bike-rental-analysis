# Bike Rental Data Analytics Project

## Project Overview
This project creates a database structure and pipeline for analyzing bike rental data, focusing on ride durations, usage patterns, and the impact of factors like weather. The project demonstrates my skills in data processing with Python and pandas, SQL, and data engineering.

## Development Process

### Step 1: Data Loading and Consolidation

#### Directory and File Selection
I specified the directory containing CSV files and filtered for files ending with `tripdata.csv`, which contain relevant bike rental data.

```python
csv_dir = "/home/jivagomenezes/projects/code-academy/bike-rental/bike-rental-starter-kit/data"
csv_files = [f for f in os.listdir(csv_dir) if f.endswith('tripdata.csv')]
```

#### Loading and Concatenating Data
Each CSV file was loaded into a pandas DataFrame, and all DataFrames were concatenated into a single `final_dataframe` to provide a unified dataset. This approach allows for more efficient data processing and analytics across all data.

```python
dataframes = [pd.read_csv(os.path.join(csv_dir, file)) for file in csv_files]
final_dataframe = pd.concat(dataframes, ignore_index=True)
```

### Step 2: Data Exploration and Quality Check

#### Initial Inspection
Basic inspection of the data structure and summary statistics was done to understand the contents and distribution of data values.

```python
print(final_dataframe.head())
print(final_dataframe.describe())
print(final_dataframe.isna().sum())
```

#### Missing Data Analysis
Calculated missing data as a percentage of total records to understand the extent of missing values in each column.

```python
missing_data = final_dataframe.isna().sum() / len(final_dataframe) * 100
```

#### Handling Missing Data

- **User Type Column**: Less than 1% missing values. Dropped rows with missing User Type since it represents a small fraction.
- **Birth Year Column**: 10% missing values. Applied Multiple Imputation using `sklearn`’s `IterativeImputer` to estimate values based on related data.

```python
from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer

imputer = IterativeImputer(max_iter=10, random_state=0)
final_dataframe['Birth Year'] = imputer.fit_transform(final_dataframe[['Birth Year']])
```

### Step 3: Data Preparation for Database Ingestion

#### 1. Creating the Station Table
- **Column Selection**: Selected only columns related to station details (`Start Station ID`, `Start Station Name`, etc.) and removed duplicates to create a distinct table for stations.
- **Column Renaming and Type Conversion**: To match SQL table requirements and for consistency, column names were converted to lowercase and spaces replaced with underscores.

```python
station_table = final_dataframe[['Start Station ID', 'Start Station Name', 'Start Station Latitude', 'Start Station Longitude']].drop_duplicates()
station_table.columns = ['station_id', 'station_name', 'latitude', 'longitude']
```

#### 2. Creating the Bike Table
- **Column Selection**: Selected `Bike ID` as a unique identifier and removed duplicates to create a clean, distinct table for bike IDs.

```python
bike_table = final_dataframe[['Bike ID']].drop_duplicates()
bike_table.columns = ['bike_id']
```

#### 3. Creating the Rentals Table
- **Column Selection and Renaming**: The main table `rentals_table` includes all trip-related data. Columns were renamed for consistency and index was reset to create a `trip_id` column for unique identification.
- **Data Type Conversion**: Ensured all columns have the correct data types before ingestion into the PostgreSQL database.

```python
rentals_table['start_time'] = pd.to_datetime(rentals_table['start_time'])
rentals_table['duration'] = rentals_table['duration'].astype(int)
```

### Step 4: Database Ingestion with SQLAlchemy
Using `SQLAlchemy`, I established a connection to a PostgreSQL database and inserted each table using `to_sql`. The `if_exists='replace'` condition ensures that tables are updated with each run, preventing data duplication.

```python
from sqlalchemy import create_engine

engine = create_engine('postgresql://postgres:postgres@localhost:5432/postgres')
station_table.to_sql('Stations', engine, if_exists='replace', index=False)
bike_table.to_sql('Bikes', engine, if_exists='replace', index=False)
rentals_table.to_sql('Rentals', engine, if_exists='replace', index=False)
```

### Step 5: Considerations for Analytical Views

#### Analyst Questions
Common questions include identifying popular stations, rental patterns by time, and how factors like age and gender influence ride behavior.

#### Aggregations for Time-Based Analysis
Created views with daily, weekly, and monthly aggregates to support analyses on rental trends and seasonality.

#### Integrating Weather Data
Weather data was incorporated to assess the impact on ridership, making it possible to see differences in rentals on rainy versus sunny days.

#### Data Quality Checks
Included null count and outlier detection in each view to ensure data quality, allowing analysts to focus on accurate data insights.

This setup not only provides the analytics team with essential insights into bike rentals but also addresses data quality issues and prepares for further integrations, such as weather impacts on usage patterns.
```

To use this in your README on GitHub, simply copy and paste this entire section directly into your `README.md` file.
