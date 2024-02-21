# mtor
import pandas as pd
import matplotlib.pyplot as plt

# Import the data from Excel file
file_path = 'firedata.xlsx'

# Read the Excel file into a pandas DataFrame
data = pd.read_excel(file_path)
# Print the column names
print(data.columns)

# Get the number of occurrences for each unique name in the 'Neighborhoods - Analysis Boundaries A' column
neighborhood_counts = data['Neighborhooods - Analysis Boundaries A'].value_counts()
print(neighborhood_counts)

# Convert the 'Received DtTm' column to datetime
data['Received DtTm'] = pd.to_datetime(data['Received DtTm'])

# Rename the 'Received DtTm' column to 'created_date'
data.rename(columns={'Received DtTm': 'created_date'}, inplace=True)

# Group calls by location and count call types
calls_grouped_by_location = data.groupby('Location')['Call Type'].count()
print(calls_grouped_by_location.sort_values(ascending=False).head(100))
#data.Location.value_counts().plot(kind='bar')
#plt.show()
# Count call types
print(data['Call Type'].value_counts().head(30))

# Count priorities
print(data['Priority'].value_counts())

# Analyze the relation between 'Original Priority' and 'Call Type'
print(pd.crosstab(data['Call Type'], data['Original Priority']))

# Analyze the relation between 'Final Priority' and 'Call Type'
print(pd.crosstab(data['Call Type'], data['Final Priority']))

# Convert "Received DtTm" and "On Scene DtTm" columns to datetime
data['created_date'] = pd.to_datetime(data['created_date'])
data['On Scene DtTm'] = pd.to_datetime(data['On Scene DtTm'])

# Create a new column with the time difference in seconds
data['Time to On Scene (s)'] = (data['On Scene DtTm'] - data['created_date']).dt.total_seconds()

# Convert time difference to minutes
data['Time to On Scene (m)'] = data['Time to On Scene (s)'] / 60

# Group by "Call Type" and calculate the mean time difference in minutes
time_to_scene_by_type = data.groupby('Call Type')['Time to On Scene (m)'].mean()

# Print the result
print(time_to_scene_by_type)
# Group calls by month and count call types

time_to_scene_by_type = data.groupby('Call Type')['Time to On Scene (m)'].mean()

plt.figure(figsize=(10, 6))
time_to_scene_by_type.plot(kind='bar')
plt.xlabel('Call Type')
plt.ylabel('Mean Time to On Scene (minutes)')
plt.title('Mean Time to On Scene by Call Type')
plt.grid()
plt.tight_layout()
plt.show()

call_counts_by_month = data.groupby(data['created_date'].dt.month)['Call Type'].count()
print(call_counts_by_month)
call_counts_by_day = data.groupby(data['created_date'].dt.day)['Call Type'].count()
print(call_counts_by_day)

call_counts_by_year = data.groupby(data['created_date'].dt.year)['Call Type'].count()
print(call_counts_by_year)

call_counts_by_year = data.groupby(data['created_date'].dt.year)['Call Type'].count()

plt.figure(figsize=(10, 6))
call_counts_by_year.plot(kind='line')
plt.xlabel('Year')
plt.ylabel('Count of Call Types')
plt.title('Call Types Count by Year')
plt.grid()
plt.show()

# Create a new DataFrame to store the converging rate
converging_rate = data.groupby('Call Type').apply(lambda x: (x['Original Priority'] == x['Final Priority']).sum() / len(x))

# Print the result
print(converging_rate)

# Plot the converging rate
converging_rate.plot(kind='bar')
plt.xlabel('Call Type')
plt.ylabel('Converging Rate')
plt.title('Converging Rate between Original Priority and Final Priority')


# Rotate the x-axis labels

plt.tight_layout()

plt.show()
