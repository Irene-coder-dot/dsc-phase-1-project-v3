# Project Overview 

For this project, you will use data cleaning, imputation, analysis, and visualization to generate insights for a business stakeholder.

## 1. Business Problem

This project is designed to support aviation entrepreneurs and startups by offering a data-driven analysis of historical aircraft accident data to identify aircraft types associated with lower safety risks. By uncovering patterns and trends in past incidents, the project provides valuable insights that can guide strategic fleet selection. These insights enable stakeholders to make informed decisions that prioritize operational safety, minimize potential liabilities, and support the successful launch and growth of aviation ventures.

### Stakeholders

The primary stakeholder for this project is the head of aviation division, who is responsible for overseeing fleet decisions, operational safety, and long-term strategic planning. This project provides data-driven insights that support informed decision-making around aircraft selection, helping minimize operational risk and ensure compliance with safety standards. By identifying aircraft models with lower historical accident rates, the project aims to enhance fleet safety while reducing the financial and reputational risks associated with high-risk aircraft.

### Conclusion: Implications
This project highlights how historical aviation accident data can be leveraged to generate actionable insights that inform aviation business strategy. By identifying aircraft models associated with lower incident rates, the analysis supports more informed and safety-conscious fleet selection. These insights not only enhance passenger safety but also promote long-term financial sustainability by helping aviation startups avoid high-risk investments.


## 2. Data Understanding

The dataset used in this project comes from the Aviation Accident Database (Synopses) on Kaggle, which is compiled from official U.S. National Transportation Safety Board (NTSB) accident reports. It contains essential information including the date and location of each incident, aircraft make and model, type of operation (e.g., commercial or private), number of fatalities and injuries, and a brief narrative summarizing the circumstances of the event.

This data is critical for uncovering safety-related patterns—such as which aircraft models are more commonly involved in accidents, which operational contexts carry higher risk, and the most frequent contributing factors. These insights directly support the project’s objective of assessing relative safety risks across various aircraft types.

An initial data exploration phase will focus on evaluating data quality, detecting missing or inconsistent entries, and analyzing the distribution of key variables. Establishing a strong understanding of the dataset at this stage is essential to ensure that the analysis remains accurate, meaningful, and aligned with the broader goal of recommending low-risk aircraft models for strategic fleet selection.

## Requirements

#### 1. Key Fields
* Aircraft make and model
* Date and location of incidents
* Number of fatalities and injuries
* Accident synopsis and summary

### 2. Exploratory Data Analysis(EDA)
* Identify patterns in accident frequency by aircraft model
* Determine which types of operations (commercial vs private) have higher risks
* Analyze accident severity by type

### 3. Data Cleaning 
* Handle missing and inconsistent values


## Load the Dataset

Before we load our dataset we import the below libraries. the Import warnings will tell python to supress all warnings messages.

# Importing necessary libraries 
import pandas as pd 
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings('ignore')

Next step is to load the dataset in a pandas dataframe using read_csv method and display the first few rows.

# Loading the dataset
df= pd.read_csv('data/Aviation_Data.csv')

# Shows the first few rows 
df.head()

The Aviation accident dataset contains historical records of aircraft incidents compiled from NTSB reports. This dataset will serve as the basis for identifying aircraft models associated with lower safety risk.

## Dataset Overview

# Dataset shape and summary
df.shape

The dataset has 90348 rows and 31 columns.

# Dataset summary 
df.info()

The .info() method provides a concise summary of the DataFrame. In this aviation dataset, there are 90,348 rows and 31 columns. Among these, 5 columns are of float data type, while the remaining 26 columns are of object data type, typically representing strings or mixed data.

# Summary statistics for numerical column

df.describe()

The summary statistics offer valuable insights into key variables such as the number of engines, fatal injuries, serious injuries, minor injuries, and the total number of uninjured individuals. On average, each incident involved approximately 0.65 fatal injuries, 0.36 minor injuries, and 0.28 serious injuries, while about 5.33 individuals remained uninjured per case. These averages indicate that most incidents did not result in any injuries, as the mean values for fatalities and injuries are all below one. This suggests that while some incidents were severe, the majority involved no injuries at all. However, the relatively high average of uninjured individuals points to a consistent presence of survivors or unaffected individuals in each incident.

# finding missing values 

df.isnull().sum()

The .isnull().sum() method helps detect missing data by returning the count of null (or missing) values in each column of the aviation dataset. Event Id,Event date and Accident number have no missing values. Location is missing 1511 entries ,Latitude and longitude have a high number of missing values. This indicates that geographical data is largely incomplete. Injury related fields like Total.Fatal.Injuries, Total.Serious.Injuries, and Total.Minor.Injuries have thousands of missing entries—this may reflect cases where injury details were not recorded or not applicable. Columns such as Schedule, Air.carrier, and FAR.Description are missing in over 70,000 records, which might significantly impact analysis involving those fields.

# Check for unique values in key categorical columns

df['Make'].value_counts().head(10)
df['Make']

# Check distribution of accident data

#Remove leading/trailing spaces from column names
df.columns = df.columns.str.strip()
#Convert the 'Event.Date' column to datetime format
df['Event.Date'] = pd.to_datetime(df['Event.Date'], errors='coerce')

# Check the date range

print("Date range of accident data:")
print(f"From {df['Event.Date'].min().date()} to {df['Event.Date'].max().date()}")

str.strip() removes any hidden spaces in column names (common issue). pd.to_datetime() converts date strings into actual datetime objects.
.min() and .max() find the earliest and latest dates.
.dt.year.value_counts() helps you see how many accidents occurred each year.

 # Data Cleaning
 Dropping columns with missing values like location which has 1511 missing values . The location column will not be used in this analysis.

 df.drop(columns=['Location'], inplace=True)

  Next we will impute the injury related fields i.e the Total.Fatal.Injuries, Total.Serious.Injuries, and Total.Minor.Injuries.
  # List of injury-related fields
injury_cols = ['Total.Fatal.Injuries', 'Total.Serious.Injuries', 'Total.Minor.Injuries']

# Fill missing injury data with 0 (indicating no injuries or not applicable)
df[injury_cols] = df[injury_cols].fillna(0)

# Optional: Convert injury columns to integers (if they were float initially)
df[injury_cols] = df[injury_cols].astype(int)

Top 10 Aircrafts Makes by Accident Frequency
# Clean and standardize the Make column to remove extra white spaces or different capitalization
df['Make']= df['Make'].str.upper().str.strip()

# Top 10 Aircraft makes by frequency

top_makes = df['Make'].value_counts().head(10)

#Plotting
plt.figure(figsize= (10,8))
sns.barplot(x= top_makes.values, y= top_makes.index, color="blue")
plt.title("Top 10 Aircraft Makes involved in accidents")
plt.xlabel("Number of Incidences")
plt.ylabel("Aircraft Make")
plt.tight_layout()
plt.show();

This chart displays the top 10 Aircraft manufactures most frequently involved in reported incidents. Cessna has the most leading incidences of over 20000 incidences while Piper, Bell, Boeing, Grumman and  Mooney manufactures have less than 5000 reported incidences. The Make column was standardized by converting all values to uppercase and removing leading/trailing whitespace. This ensures that different variations of the same manufacturer (like "Cessna", "CESSNA", or "CESSNA ") are treated consistently, avoiding duplicate entries in the analysis. 

Cessna consistently emerges as the aircraft make with the highest reported incidents.

## Most Frequently Involved Aircraft Models

#Top 10 Models involved in Accidents 

top_models = df['Model'].value_counts().head(10)

#Plot graph for the top_models
plt.figure(figsize=(10,8))
sns.barplot(x= top_models.values, y=top_models.index, color="green")
plt.title("Top 10 Aircraft Models Involved in Accidents")
plt.xlabel("Number of incidents")
plt.ylabel("Aircraft Models")
plt.tight_layout()
plt.show();

This chart highlights the aircraft models with the highest number of recorded incidents. The Aircraft Model 152 leads with over 2,000 incidents, while the Model 150M has a significantly lower incident count of just over 500.

This analysis gives us a clear picture of which specific aircraft models are most frequently involved in accidents. By identifying these models, we can focus on understanding the factors contributing to their high incident rates. For example, the higher number of incidents for Model 152 might be attributed to its widespread use or specific operational contexts, whereas Model 150M may require further analysis to determine why it has relatively fewer incidents.

### Analysis of Accident Severity 

With the most frequent aircraft makes and the top aircraft models involved in accidents identified, the next step is to analyze accident severity and correlate it with specific makes and models.

### 1. Assess Accident Severity:
You can calculate severity metrics like the number of fatalities, serious injuries, and minor injuries.

This will allow you to evaluate whether more frequent accidents correlate with more severe outcomes.

Severity metrics could include:

* Fatality Rate: Number of fatalities / Total number of accidents.

* Injury Rate: Total injuries (fatal + serious + minor) / Total number of accidents.

### 2. Group by Aircraft Make and Model:

You can group by both aircraft make and model to calculate these severity metrics.
#Severity metrics by Aircraft Make
# Normalize the 'Make' column to ensure consistency (e.g., convert all names to uppercase)
df['Make'] = df['Make'].str.upper()

# Group by 'make' and sum up the injury-related fields
severity_by_make = df.groupby('Make')[['Total.Fatal.Injuries', 'Total.Serious.Injuries', 'Total.Minor.Injuries']].sum()

# Calculate fatality rate and injury rate
accidents_by_make = df['Make'].value_counts()
severity_by_make['fatality_rate'] = severity_by_make['Total.Fatal.Injuries'] / accidents_by_make
severity_by_make['injury_rate'] = (severity_by_make['Total.Fatal.Injuries'] + severity_by_make['Total.Serious.Injuries'] + severity_by_make['Total.Minor.Injuries']) / accidents_by_make

# Sort by fatality rate or injury rate
severity_by_make_sorted = severity_by_make.sort_values(by='fatality_rate', ascending=False)

# Display the top 10 aircraft makes by fatality rate
print(severity_by_make_sorted.head(10))

### Key insights and Observations

**Aircraft Makes with the Highest Fatality Rate:**

* TUPOLEV has the highest fatality rate of 127.25 with 509 fatal injuries and no reported minor injuries. This suggests that accidents involving Tupolev aircraft are often fatal and do not typically involve non-fatal injuries. 

**Other Makes with Relatively High Fatality Rates are as follows:**

* VIKING AIR LIMITED has a fatality rate of 23 with 23 fatal injuries. Despite this relatively high rate, it reports no serious or minor injuries, indicating that accidents involving this make tend to result in fatalities but fewer other types of injuries.

* AVIOCAR CASA and MIL, each with 18 and 13 fatal injuries respectively, have fatality rates of 18 and 13, showing that accidents involving these aircraft tend to be fatal.

Makes like EMBRAER AIRCRAFT, AIRVAN, M7AERO, and JETSTREAM have lower fatality rates, suggesting that accidents with these aircraft are less likely to result in fatalities and may involve fewer injuries overall. ANTONOV has a small number of minor injuries, indicating that its accidents might have a more varied severity, though fatalities remain high.

**Conclusion:**

This dataset indicates that some aircraft makes, especially TUPOLEV, tend to be involved in accidents with very high fatality rates, while others have comparatively lower fatality rates but still report fatalities. The presence of minor injuries in a few cases (like ANTONOV and M7AERO) shows that not all accidents are as severe as those involving TUPOLEV.

# Severity Metrics by Aircraft Model 

# Group by 'model' and sum up the injury-related fields
severity_by_model = df.groupby('Model')[['Total.Fatal.Injuries', 'Total.Serious.Injuries', 'Total.Minor.Injuries']].sum()

# Calculate fatality rate and injury rate by model
accidents_by_model = df['Model'].value_counts()
severity_by_model['fatality_rate'] = severity_by_model['Total.Fatal.Injuries'] / accidents_by_model
severity_by_model['injury_rate'] = (severity_by_model['Total.Fatal.Injuries'] + severity_by_model['Total.Serious.Injuries'] + severity_by_model['Total.Minor.Injuries']) / accidents_by_model

# Sort by fatality rate or injury rate
severity_by_model_sorted = severity_by_model.sort_values(by='fatality_rate', ascending=False)

# Display the top 10 aircraft models by fatality rate
print(severity_by_model_sorted.head(10))

### Key Insights and Observations 

**Aircraft Models with the Highest Fatality Rates:**

* 747-168 and TU-154 both have the highest fatality rates of 349.0, with 349 fatal injuries. These models have no reported minor injuries, indicating that accidents involving these models tend to result in fatalities without lesser injuries.

* 777 - 206 also has a high fatality rate of 178.0, with 534 fatal injuries, but fewer fatalities compared to the 747-168 and TU-154 (despite having the highest number of fatal injuries). This suggests that accidents with the 777 - 206 are still highly fatal but might involve more cases where the fatalities are more frequent but not as severe in the average incident.

**Other Models with High Fatality Rates:**

* 767-366-ER has a fatality rate of 217.0, with 217 fatal injuries. It is also a model associated with a relatively high number of fatal accidents.

* A320 - 216 has 162 fatal injuries and a fatality rate of 162.0, showing that accidents involving this model tend to result in fatalities, but the fatality rate is not as high as some other models like the 747-168 or TU-154.

* A310 has 169 fatal injuries and a fatality rate of 169.0, indicating a relatively high number of fatalities compared to other aircraft.

**Aircraft Models with Lower Fatality Rates:**

* CitationJet 2 has 118 fatal injuries and a fatality rate of 118.0, which is still significant, but lower compared to other aircraft in the dataset.

* A310-300 has 124 fatal injuries, but it shows a relatively higher injury rate (183.0) due to the 59 serious injuries reported, making this model unique in having both a higher number of serious injuries and fatalities compared to other models. This suggests that accidents with A310-300 tend to result in more severe injuries, in addition to fatalities.

**Minor Injuries:**

A310 is the only model with 10 minor injuries reported, while the rest of the models have no reported minor injuries. This indicates that accidents involving A310 may involve a mix of serious or fatal outcomes as well as minor injuries.

**Conclusion:**

This dataset shows that many of the aircraft models, particularly 747-168, TU-154, 767-366-ER, and 777 - 206, are involved in accidents with high fatality rates, and they often result in fatalities with very few minor injuries. However, A310-300 stands out for having a higher injury rate due to the presence of serious injuries, indicating that its accidents may be more severe on average. CitationJet 2 and A320 - 216 have relatively lower fatality rates, suggesting fewer fatalities in accidents involving these models.

# Visualizing the severity

Visualize the severity of accidents for the top 10 aircraft makes and models, ranked by fatality and injury rates.
top_10_makes = severity_by_make_sorted.head(10)
top_10_models = severity_by_model_sorted.head(10)

# Creating visualizations 
# Plotting Top 10 Aircraft Makes
plt.figure(figsize=(14, 6))
sns.barplot(x=top_10_makes.index, y=top_10_makes['fatality_rate'], color='red', label='Fatality Rate')
sns.barplot(x=top_10_makes.index, y=top_10_makes['injury_rate'], color='blue',  label='Injury Rate')

plt.title("Severity of Accidents by Aircraft Make (Top 10)")
plt.ylabel("Rate per Accident")
plt.xlabel("Aircraft Make")
plt.xticks(rotation=45)
plt.legend()
plt.tight_layout()
plt.show();

## **Interpretation**

The bar plot reveals that the TUPOLEV aircraft make has experienced the highest number of accidents among the top 10, with a notably high injury rate but no recorded fatalities, indicating severe but non-fatal outcomes. In contrast, makes such as AIRVAN, M7AERO, and JETSTREAM show both lower accident counts and minimal injury rates, suggesting these aircraft types are involved in fewer and less severe incidents overall.

# Plotting Top 10 Aircraft Models
plt.figure(figsize=(14, 6))
sns.barplot(x=top_10_models.index, y=top_10_models['fatality_rate'], color='red', label='Fatality Rate')
sns.barplot(x=top_10_models.index, y=top_10_models['injury_rate'], color='blue',  label='Injury Rate')

plt.title("Severity of Accidents by Aircraft Model (Top 10)")
plt.ylabel("Rate per Accident")
plt.xlabel("Aircraft Model")
plt.xticks(rotation=45)
plt.legend()
plt.tight_layout()
plt.show();

## **Interpretation**

The bar plot indicates that aircraft models 747-168 and TU-154 have the highest number of accidents, each associated with high injury rates. In contrast, models like the 737-222 and CitationJet 2 show lower accident frequencies with relatively modest injury rates. Notably, the plot displays no recorded fatality rates across all models shown, suggesting that these incidents involved injuries but no reported fatalities.

# Compare Accident Frequency with Severity

### Step 1: Get Accident Frequency of Aircraft Make
accident_counts = df['Make'].value_counts() 

### Step 2 : Calculate Total Severity
severity_data = df.groupby('Make')[['Total.Fatal.Injuries', 'Total.Serious.Injuries', 'Total.Minor.Injuries']].sum()

### Step 3: Combine Frequency and Severity
# Convert series to DataFrame
accident_counts = accident_counts.rename('accident_count')

# Join with severity data
combined = severity_data.join(accident_counts)

# Optional: Add severity rate columns
combined['total_injuries'] = combined['Total.Fatal.Injuries'] + combined['Total.Serious.Injuries'] + combined['Total.Minor.Injuries']
combined['fatality_rate'] = combined['Total.Fatal.Injuries'] / combined['accident_count']
combined['injury_rate'] = combined['total_injuries'] / combined['accident_count']

### Step 4: Visualize the Comparison
plt.figure(figsize=(12, 8))
sns.scatterplot(data=combined, x='accident_count', y='fatality_rate', hue='total_injuries', size='accident_count', sizes=(50, 500))

plt.title("Accident Frequency vs. Fatality Rate by Aircraft Make")
plt.xlabel("Number of Accidents")
plt.ylabel("Fatality Rate")
plt.legend()
plt.tight_layout()
plt.show();


### *Interpretation*

The scatter plot illustrates the relationship between the number of accidents and the fatality rate per accident across different aircraft makes.

* Aircraft makes with a high accident count but low fatality rate likely represent models that are frequently used and may have better safety performance overall, despite being involved in more incidents.

* Aircraft makes with low accident counts but high fatality rates are less commonly involved in accidents, but when accidents do occur, they tend to be more severe, suggesting a potentially higher safety risk per incident.

* Aircraft makes with both high accident counts and high fatality rates may represent higher-risk types and could warrant closer evaluation or additional safety measures.


# *Recommendations for Low-Risk Aircraft Models Suitable for Fleet Selection*

**1.Prioritize Aircraft with Low Accident Counts and Low Injury Rates (e.g., AIRVAN, M7AERO, JETSTREAM, 737-222, CitationJet 2)**

* **Findings:** Aircraft such as AIRVAN, M7AERO, JETSTREAM, 737-222, and CitationJet 2 are associated with low accident counts and minimal injury rates, suggesting these models are safer and more reliable overall.

* **Recommendation:** Prioritize these aircraft models for regular operations, especially on high-traffic routes or missions where safety is a top priority. These models are proven to have a strong safety profile with fewer incidents and minimal injury outcomes.

* **Action:** Consider expanding the use of these aircraft types in your fleet or use them for high-demand operations. These aircraft are likely to maintain consistent and safe operational performance.

**2. Consider Aircraft with Strong Safety Performance Despite High Accident Frequency (e.g., 737-222)**

* **Findings:** The 737-222 model, although involved in a relatively high number of accidents, exhibits low injury rates, suggesting that it is frequently used, well-maintained, and likely equipped with robust safety systems that help minimize harm during incidents.

* **Recommendation:** While it's not entirely risk-free, aircraft with high utilization and low injury rates, such as the 737-222, can still be strong candidates for fleet inclusion. These models may have higher exposure due to frequent use, but their safety features effectively reduce the severity of accidents.

* **Action:** Continue monitoring these models closely, with a focus on regular maintenance and strict adherence to safety protocols. These aircraft could be particularly suitable for high-volume, cost-effective routes where frequent operation is necessary.

**3. Avoid Aircraft with High Injury Rates and Low Accident Counts (e.g., TUPOLEV, 747-168, TU-154)**

* **Findings:** Aircraft such as TUPOLEV, 747-168, and TU-154 exhibit high injury rates despite having relatively low accident counts, indicating that while accidents are infrequent, the severity of injuries in these events is significantly higher.

* **Recommendation:** Refrain from acquiring or using these aircraft types, unless substantial improvements are made to enhance their safety. These models pose a higher risk of severe injuries per incident, which could negatively affect both fleet safety and the company’s reputation.

**4. Emphasize Safety in Fleet Selection by Analyzing Accident and Fatality Data**

* **Findings:** The analysis of the bar and scatter plots reveals that while accidents occur across all models, there have been no recorded fatalities, indicating that fatalities are not a common result. However, injury severity remains an important consideration in assessing safety.

* **Recommendation:** Incorporate a thorough analysis of accident severity (including injury and fatality rates) into the fleet selection process. Prioritize models with lower injury severity and stronger overall safety records to enhance fleet safety.

* **Action:** Continuously review and update safety assessments as new data becomes available. Base fleet decisions on both accident frequency and the severity of injuries, as these factors provide a more accurate measure of safety performance.

**5. Integrate a Data-Driven Approach for Fleet Optimization**

* **Findings:** The scatter plot suggests that aircraft with high accident counts but low fatality rates tend to perform well overall, while aircraft with low accident counts but high fatality rates could pose a higher safety risk per incident.

* **Recommendation:** Leverage data-driven decision-making to continuously monitor the safety performance of aircraft in the fleet. Invest in predictive analytics to anticipate potential safety risks and proactively address them, selecting aircraft that demonstrate both low accident and injury rates.

* **Action:** Develop a safety monitoring system that tracks accident severity and makes data-driven recommendations on fleet composition. This system should continuously evaluate trends and suggest aircraft models with better safety performance for selection.



# **Summary**

By prioritizing low-risk aircraft models like AIRVAN, M7AERO, JETSTREAM, and 737-222, and exercising caution with aircraft that have higher injury rates, such as TUPOLEV and 747-168, aviation stakeholders can optimize fleet safety and mitigate operational risks. Employing data-driven safety assessments will be essential for continuously enhancing fleet safety performance and ensuring long-term operational success.

