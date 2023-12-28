# Week-3-Capstone-Statistics
# Below is the code:
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DA0321EN-SkillsNetwork/LargeData/m2_survey_data.csv")

# Assuming df is your DataFrame
sns.set(style="whitegrid")  # Set the style of the plot

# Distribution curve (kernel density estimate plot)
plt.figure(figsize=(10, 6))
sns.kdeplot(df['ConvertedComp'], fill=True, color='blue')
plt.title('Distribution Curve for Annual Salaries')
plt.xlabel('Annual Compensation $USD')
plt.ylabel('Density')
plt.show()

# HISTOGRAM for Annual Compensation
plt.figure(figsize=(10, 6))  # Set the size of the plot
sns.histplot(df['ConvertedComp'], color='blue', bins=30)  # Salary converted to annual USD salaries using the exchange rate on 2019-02-01.
plt.title('Annual Salaries')  # Set the title of the plot
plt.xlabel('Annual Compensation $USD')  # Set the x-axis label
plt.ylabel('Frequency')  # Set the y-axis label
plt.show()


# Descriptive Statistics
print(df['Gender'].value_counts().get("Man", 0)) # How many people identified themselves as Man
woman_data = df[df['Gender'] == 'Woman'] # Filter the DataFrame for individuals who identified as "Woman"
print(woman_data['ConvertedComp'].median()) # Calculate the median for those who identified as "Woman"
print(df['Age'].median()) # Median age
print("Median ConvertedComp before outliers: " + str(df['ConvertedComp'].median()))
print(df['Age'].describe(percentiles=[.25, .5, .75])) # Give the five number summary for the column Age

# HISTOGRAM for Age
plt.figure(figsize=(10, 6))  # Set the size of the plot
sns.histplot(df['Age'], color='blue', bins=30)  # Salary converted to annual USD salaries using the exchange rate on 2019-02-01.
plt.title('Age Distribution')  # Set the title of the plot
plt.xlabel('Age (years)')  # Set the x-axis label
plt.ylabel('Frequency')  # Set the y-axis label
#plt.show()

# Find out if outliers exist in the column ConvertedComp using a box plot
plt.figure(figsize=(10, 6))
sns.boxplot(x=df['ConvertedComp'])
plt.title('Box Plot of Annual Salary')
plt.xlabel('Annual Compensation')
plt.show()

#Find out the Inter Quartile Range for the column ConvertedComp
non_empty_salary = df['ConvertedComp'].dropna()  # Remove missing values if any
import numpy as np
# Calculate the interquartile range (IQR)
q3 = np.percentile(non_empty_salary, 75)
q1 = np.percentile(non_empty_salary, 25)
iqr = q3 - q1
print("Interquartile Range (IQR) for ConvertedComp = " + str(iqr)) # Print the result

# find the lower and upper bounds
lower_bound = q1 - 1.5 * iqr
upper_bound = q3 + 1.5 * iqr
print("The lower bound is " + str(lower_bound) + " and the upper bound is " + str(upper_bound))

# count outliers
outliers = (non_empty_salary < lower_bound) | (non_empty_salary > upper_bound)
num_outliers = sum(outliers) # Count the number of outliers
print("There are " + str(num_outliers) + " outliers in this data")

# Create a new DataFrame without outliers for the salaries only
df_no_outliers_salary = non_empty_salary[~outliers]
print(df_no_outliers_salary)

# Create a new data frame without outliers for the entire data frame
# Here we redefine outliers so that there are no indexing issues
outliers = (df['ConvertedComp'] < lower_bound) | (df['ConvertedComp'] > upper_bound)
df_no_outliers = df.loc[~outliers].reset_index(drop=True)
print(df_no_outliers)
print("Median ConvertedComp after outliers are removed : " + str(df_no_outliers['ConvertedComp'].median()))
print("Mean ConvertedComp after outliers are removed : " + str(df_no_outliers['ConvertedComp'].mean()))

# finding correlation between age and all other numerical columns
numerical_columns = df_no_outliers.select_dtypes(include='number') # find numerical columns
correlation_matrix = numerical_columns.corr() # Find correlations among all numerical columns
age_correlation = correlation_matrix['Age'] # Extract the correlation of 'Age' with other columns
print(age_correlation) # Display the correlation of 'Age' with other numerical columns
