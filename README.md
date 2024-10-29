Project Overview
The purpose of this project is to create a data pipeline and database structure for analyzing bike rental data. This analysis includes identifying key metrics related to ride durations, bike usage patterns, and how external factors like weather conditions might influence rental behavior. This project demonstrates my skills in data engineering, SQL, and Python, while also illustrating my design choices to create analytics-ready datasets.

Data Preparation
Data Loading and Cleaning
Data Consolidation
The raw data consists of multiple CSV files, which I loaded into individual pandas DataFrames, then merged into a single DataFrame. This consolidation provides a unified view of all bike rentals, enabling analysis across different timeframes.

Missing Data
I analyzed missing data to understand its extent and impact. For User Type (less than 1% missing), I chose to drop rows with missing values. For missing values in Birth Year (~10%), I used multiple imputation to estimate values based on related features.

Data Type Consistency
Ensuring the correct data types for each column was critical to allow for smooth integration with PostgreSQL, especially when dealing with date, integer, and categorical data types.

Database Design and Table Creation
Database Structure
Station Table: The station_table includes station IDs, names, and geographical coordinates, allowing for spatial analysis of station-specific data.
Bike Table: The bike_table stores unique bike IDs, providing a reference for each bike's journey across rentals.
Rentals Table: This main table links all trip information, including duration, start/end times, and user demographics. It serves as the core of the analysis, enabling easy integration with external datasets, like weather data.
Data Ingestion into PostgreSQL
Using SQLAlchemy, I created the Stations, Bikes, and Rentals tables in PostgreSQL. The IF EXISTS condition in each table creation ensured data consistency during development, preventing duplication and versioning conflicts.

Creating Analytical Views
To support the analytics team, I created views to answer potential analyst questions:

User Behavior Patterns
Views that analyze popular stations, average ride durations by user type, and how age impacts ride duration. These views aggregate data at daily, weekly, and monthly levels to reveal patterns and seasonality.

Weather Impact
By joining with external weather data, I created views to analyze metrics like total rentals on rainy days versus sunny days. I used an hourly breakdown to account for weather variability, which directly influences ridership.

Data Quality Considerations
Data Gaps and Anomalies:
Each view includes calculations for null counts in essential fields and outlier detection in Duration. This allows analysts to quickly assess data integrity and identify any areas where caution is warranted.

Sorting and Filtering:
Each view is sorted based on relevance, e.g., descending by total rentals or average ride duration, enabling quick access to the most impactful data.

Reflections and Next Steps
This project illustrates my approach to creating a robust analytics environment, with attention to data integrity and relevance to end users. Next, I’d like to integrate more advanced data pipelines, possibly with Apache Spark, to improve efficiency for larger datasets and further automate data quality checks.
