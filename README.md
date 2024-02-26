numpy as np
import pandas as pd
from matplotlib import pyplot as plt

print(pd.__version__)
print(np.__version__)

# IMPORT DATA

df = pd.read_csv('/Users/christopherli/Desktop/datasets/vgsales.csv')
# 'df' is the variable name where the dataset is stored. Short for DataFrame.
# 'pd' is pandas and the period is the way we access the functions from pandas.
# 'read_csv()' is a pandas function that allows us to read in a commas seperated data file(CSV)


# SUMMARY

# Display the first few rows of the dataframe
print("First 5 rows\m", df.head())
# produces the first N rows of the dataframe, where default N is 5


# Consise DataFrame Summary
print("Consise Summary of DataFrame:\n", df.info())
# provides a summary of the dataframe, index dtype and column dtypes, non-null values, and memory usage


# Descriptive statistics for numeric columns
print("Descriptive Statistics:\n", df.describe())
# provides  count, mean, standard deviation, minimum and maximum values, and the quartiles of the data

# Check for missing values in each column
print("Missng values:\n", df.isnull().sum())
# 'insull()' is a function that creates a new boolean dataframe where an obs is true if there is data and false if no
# data 'sum()' returns the count of missing numbers

# Returns the last N rows (default N=5), useful for checking the dataset's end.
# df.tail()

# Returns a random sample of N items from the DataFrame, good for a data snapshot.
# df.sample(n=5)

# Attribute showing the DataFrame's dimensions as (rows, columns), for size overview.
# df.shape

# Attribute listing all column names, useful for referencing specific columns.
# df.columns

# Shows data types for each column, crucial for data handling (e.g., int, float, object).
# df.dtypes

# Counts unique values in each column, helpful for understanding data diversity.
# df.nunique()

# For a Series, counts occurrences of each unique value, useful for category distribution.
# df['column_name'].value_counts()

# Reports memory usage for each column in bytes, important for large data management.
# df.memory_usage()

# Calculates pairwise correlation between columns, aids in understanding variable relationships.
# df.corr()

# Identifies missing values in the DataFrame, .isna() is more readable than .isnull().
# df.isna()


# CLEANING

# column with mean of that column
# data['VARIABLE'] = data['VARIABLE'].fillna(data['VARIABLE'].mean())

# with median of that column
# data['VARIABLE'] = data['VARIABLE'].fillna(data['VARIABLE'].median())

# standard deviation of that column
# data['VARIABLE'] = data['VARIABLE'].fillna(data['VARIABLE'].std())

# minimum number of that column
# data['VARIABLE'] = data['VARIABLE'].fillna(data['VARIABLE'].min())

# maximum number of that column
# data['VARIABLE'] = data['VARIABLE'].fillna(data['VARIABLE'].max())

df['Year'] = df['Year'].fillna(0)
# Fill missing 'Year' values with 0
df['Publisher'] = df['Publisher'].fillna("Unkown")
# Fill missing 'Publisher' values with 'UNKOWN'

print("Missng values:\n", df.isnull().sum())
# Recheck if there are misisng values


# ANALYSIS


# Sales Trends Over Time
# Note: When cleaning the data I chose to replace missing values of year with 0
# -- this will change how the data is seen visually as it will create left skewness
# -- so I will filter out the 0's from the df and plot the filtered df
# Filtering out entries with 'Year' as 0
filtered_df = df[df['Year'] != 0]
# Re-calculating Sales Trends Over Time without Year 0
filtered_yearly_sales = filtered_df.groupby('Year')['Global_Sales'].sum()
# Creating a new line plot for yearly sales trends without Year 0
plt.figure(num=1, figsize=(10, 6))  # Sets the figure size
plt.plot(filtered_yearly_sales, marker='o', linestyle='-', color='b')  # Plot with markers, a solid line, and blue color
# Adding titles and labels
plt.title('Global Video Game Sales by Year (Excluding Year 0)')  # Chart title
plt.xlabel('Year')  # X-axis label
plt.ylabel('Global Sales (in millions)')  # Y-axis label
plt.grid(True)  # Adds a grid for easier readability
plt.xticks(rotation=45)  # Rotates x-axis labels to avoid overlap
plt.tight_layout()  # Adjusts subplot params for the plot to fit into the figure area
# Show the new plot, ensure the usage of block so that the plots pop up simultaneously
plt.show(block=False)

# Sales by Platform
platform_sales = df.groupby('Platform')['Global_Sales'].sum().sort_values(ascending=False)
# Creating a bar plot for sales by platform
plt.figure(num=2, figsize=(12, 8))
platform_sales.plot(kind='bar', color='skyblue')  # Creates a bar plot
# Adding titles and labels
plt.title('Total Global Video Game Sales by Platform')
plt.xlabel('Platform')
plt.ylabel('Global Sales (in millions)')
plt.xticks(rotation=45)  # Rotates x-axis labels to avoid overlap
plt.tight_layout()
# Show the plot
plt.show(block=False)

# Sales by Genre
genre_sales = df.groupby('Genre')['Global_Sales'].sum().sort_values(ascending=False)
# Creating a bar plot for sales by platform
plt.figure(num=3, figsize=(12, 8))
genre_sales.plot(kind='bar', color='skyblue')
# Adding titles and labels
plt.title('Total Global Video Game Sales by Genre')
plt.xlabel('Genre')
plt.ylabel('Global Sales (in millions)')
plt.xticks(rotation=45)
plt.tight_layout()
# Show the plot
plt.show(block=False)

# Top Publishers
publisher_sales = df.groupby('Publisher')['Global_Sales'].sum().sort_values(ascending=False).head(10)
# Creating a bar plot for sales by platform
plt.figure(num=4, figsize=(12, 8))
publisher_sales.plot(kind='bar', color='skyblue')
# Adding titles and labels
plt.title('Top 10 Publishers')
plt.xlabel('Publisher')
plt.ylabel('Global Sales (in millions)')
plt.xticks(rotation=45)
plt.tight_layout()
# Show the plot
plt.show(block=False)

# Regional Preferences
region_sales = df[['NA_Sales', 'EU_Sales', 'JP_Sales', 'Other_Sales']].sum()
# Creating a pie chart for regional sales
plt.figure(num=5, figsize=(8, 8))  # Sets the figure size
plt.pie(region_sales, labels=region_sales.index, autopct='%1.1f%%', startangle=140,
        colors=['gold', 'lightblue', 'lightgreen', 'lavender'])
# Adding a title
plt.title('Video Game Sales Distribution by Region')
# Display the pie chart
plt.show()


# Correlation Analysis
# Calculating the correlation matrix to understand the relationship between North American sales and other regions
# and between the year of release and global sales.
correlation_matrix = df[['Year', 'NA_Sales', 'EU_Sales', 'JP_Sales', 'Other_Sales', 'Global_Sales']].corr()
print(correlation_matrix)
