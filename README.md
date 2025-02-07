# ABB-project
%matplotlib inline
import pandas as pd
import matplotlib.pyplot as plt
from sqlalchemy import create_engine

# Define your connection string using SQLAlchemy's create_engine
engine = create_engine('oracle+oracledb://root:the_curiosity1212@localhost:1521/?service_name=FREEPDB1')

# SQL query to join the necessary tables and fetch data
query = """
SELECT
    sa.RU_VAL AS category_ru,
    sa.EN_VAL AS category_en,
    sa.AZ_VAL AS category_az,
    mr.rate AS rate
FROM
    MERCHANT_RATE mr
JOIN
    MERCHANT_RATES_FAST_ANSWERS mrf ON mr.ID = mrf.MERCHANT_RATE_FK
JOIN
    SURVEY_FAST_ANSWER sa ON mrf.FAST_ANSWER_FK = sa.ID
"""

# Fetch data into a pandas DataFrame
df = pd.read_sql(query, con=engine)

# Display the first few rows of the DataFrame
print("First few rows of the DataFrame:")
print(df.head())

# Aggregate data for the pie chart (most chosen categories)
category_counts = df['category_en'].value_counts()
print("Category counts:")
print(category_counts)

# Plot the pie chart for the most chosen categories
plt.figure(figsize=(10, 6))
category_counts.plot(kind='pie', autopct='%1.1f%%', startangle=140, colormap='viridis')
plt.title('Most Chosen Categories')
plt.ylabel('')  # Hide y-axis label
plt.show()

# Aggregate data for the bar graph (average rate for each category)
average_rates = df.groupby('category_en')['rate'].mean()
print("Average rates:")
print(average_rates)

# Plot the bar graph for the average rate for each category
plt.figure(figsize=(12, 8))
average_rates.plot(kind='bar', color='skyblue')
plt.xlabel('Category')
plt.ylabel('Average Rate')
plt.title('Average Rate for Each Category')
plt.xticks(rotation=45)
plt.show()
