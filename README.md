# Data Analytics and Visualization --- Practical 10

## Mini Project: Anatomy of Terrorism

### Power BI Dashboard: Global Terrorism Analysis (1970--2017)

**Enrollment No.:** 240840116123

------------------------------------------------------------------------

## 1. Practical 10 --- Mini Project

### Aim

Analyze a real-world dataset, build an interactive dashboard, and
present actionable insights with ethical considerations.

### Project Description

This mini project analyzes the Global Terrorism Dataset covering 1970 to
2017. The original dataset contained many columns, so the project uses a
reduced dataset containing 20 relevant dimensions for focused analysis
and dashboard reporting.

The analysis examines:

-   **When:** yearly pattern and frequency of incidents.
-   **Where:** countries, regions, provinces, cities, and geographic
    coordinates.
-   **How and whom:** attack types, target categories, target
    subcategories, groups, and weapon types.
-   **Impact:** fatalities, injuries, and total casualties.

The cleaned dataset is visualized in Python and then presented through
an interactive Power BI dashboard titled **"Anatomy of Terrorism"**. The
purpose is to understand historical patterns and communicate
evidence-based findings, not to predict or profile individuals or
groups.

------------------------------------------------------------------------

## 2. Phase 1: Load the Dataset

Load the prepared CSV file using Pandas. Keep `final_dataset.csv` in the
same folder as this Jupyter Notebook.

``` python
# Import the required libraries
import pandas as pd
import matplotlib.pyplot as plt
import plotly.express as px

# Load the prepared dataset
data = pd.read_csv("final_dataset.csv")

# Display the first five records
data.head()
```

The first five records show fields such as event ID, year, month, day,
country, region, province/state, city, coordinates, attack type, target
category, target subtype, group, weapon type, fatalities, injuries,
property indicator, and total casualties.

------------------------------------------------------------------------

## 3. Phase 2: Basic Dataset Inspection

Inspect the dataset before performing analysis. These commands help us
understand the number of records, column names, data types, missing
values, and statistical distribution.

### 3.1 Display the First Five Records

``` python
data.head()
```

### 3.2 Display the Last Five Records

``` python
data.tail()
```

The final records in the supplied dataset are from July 2017.

### 3.3 Check the Number of Rows and Columns

``` python
data.shape
```

**Output:**

``` text
(172599, 21)
```

The supplied CSV contains **172,599 rows and 21 columns**.

### 3.4 Display Column Names

``` python
data.columns
```

**Columns:**

``` text
[
    'Unnamed: 0', 'eventid', 'iyear', 'imonth', 'iday', 'extended',
    'country_txt', 'region_txt', 'provstate', 'city', 'latitude',
    'longitude', 'attacktype1_txt', 'targtype1_txt', 'targsubtype1_txt',
    'gname', 'weaptype1_txt', 'nkill', 'nwound', 'property',
    'total_casualties'
]
```

### 3.5 Display Data Types and Non-Null Counts

``` python
data.info()
```

**Dataset information:**

-   Total records: 172,599
-   Total columns: 21
-   Data types:
    -   `float64`: 5 columns
    -   `int64`: 7 columns
    -   `object`: 9 columns
-   Memory usage: approximately 27.7 MB

### 3.6 Generate Descriptive Statistics

``` python
data.describe()
```

`describe()` generates descriptive statistics for numerical columns,
including:

-   **count:** number of non-null observations
-   **mean:** average value
-   **std:** standard deviation
-   **min:** minimum value
-   **25%:** first quartile
-   **50%:** median
-   **75%:** third quartile
-   **max:** maximum value

Some values shown in the supplied output include:

  Measure                              Value
  -------------------------------- ---------
  Number of records                  172,599
  Minimum year                          1970
  Maximum year                          2017
  Mean year                          2002.54
  Mean fatalities (`nkill`)            2.227
  Mean injuries (`nwound`)             2.927
  Mean total casualties                5.154
  Maximum fatalities in a record       1,570
  Maximum injuries in a record         8,191

### 3.7 Check Missing Values

``` python
data.isnull().sum()
```

The supplied CSV reports zero null values in all 21 columns.

### 3.8 Initial Observation

The supplied CSV contains **172,599 records**. It has an additional
`Unnamed: 0` column from the saved CSV index; this is not one of the
selected analytical dimensions. The project analysis uses the 19
selected source columns and the derived `total_casualties` column,
giving 20 analytical columns.

------------------------------------------------------------------------

## 4. Phase 3: Data Cleaning and Feature Engineering

The following steps follow the cleaning notebook supplied for this
project:

1.  Select the 19 meaningful source columns.
2.  Replace missing fatality and injury values with `0`.
3.  Replace missing province, city, and target-subtype values with
    `Unknown`.
4.  Create `total_casualties` by adding fatalities and injuries.
5.  Remove records without latitude or longitude.

**Note:** The supplied `final_dataset.csv` is already the prepared
output of this cleaning workflow. The same steps are shown here to
document the project process and are applied again to keep the notebook
workflow consistent. The original 135-column raw dataset was not
supplied, so this notebook does not recreate the initial
column-reduction operation from the raw file.

### 4.1 Select the 19 Meaningful Source Columns

``` python
meaningful_cols = [
    # Time and logistics
    'eventid', 'iyear', 'imonth', 'iday', 'extended',

    # Location analysis
    'country_txt', 'region_txt', 'provstate', 'city',
    'latitude', 'longitude',

    # Attack types, targets, groups, and weapons
    'attacktype1_txt', 'targtype1_txt', 'targsubtype1_txt',
    'gname', 'weaptype1_txt',

    # Casualties and impact
    'nkill', 'nwound', 'property'
]

# Create a focused copy of the selected columns
df_clean = data[meaningful_cols].copy()

# Display the selected dataset shape
print("Shape after column selection:", df_clean.shape)
df_clean.head()
```

**Output:**

``` text
Shape after column selection: (172599, 19)
```

### 4.2 Handle Missing Numerical Values

Replace missing values in the fatalities and injuries columns with zero.

``` python
df_clean['nkill'] = df_clean['nkill'].fillna(0)
df_clean['nwound'] = df_clean['nwound'].fillna(0)
```

### 4.3 Handle Missing Text Values

Replace missing values in province/state, city, and target subtype with
`Unknown`.

``` python
string_cols_to_fill = [
    'provstate',
    'city',
    'targsubtype1_txt'
]

df_clean[string_cols_to_fill] = (
    df_clean[string_cols_to_fill].fillna('Unknown')
)
```

### 4.4 Create the Total Casualties Column

Total casualties are calculated by adding fatalities and injuries.

``` python
df_clean['total_casualties'] = (
    df_clean['nkill'] + df_clean['nwound']
)
```

### 4.5 Remove Rows with Missing Geographic Coordinates

``` python
df_clean = df_clean.dropna(
    subset=['latitude', 'longitude']
)
```

### 4.6 Display the Cleaned Dataset

``` python
df_clean.head()
```

### 4.7 Verify Missing Values After Cleaning

``` python
df_clean.isnull().sum()
```

The supplied output shows zero missing values in every final analytical
column.

### 4.8 Check the Final Dataset Structure

``` python
print("Final dataset shape:", df_clean.shape)
df_clean.info()
```

**Final dataset shape:**

``` text
(172599, 20)
```

The final dataset contains:

-   20 columns
-   172,599 records
-   5 `float64` columns
-   6 `int64` columns
-   9 `object` columns
-   Approximately 26.3 MB memory usage

### 4.9 Cleaning Summary

-   Missing values in `nkill` and `nwound` are filled with `0`.
-   Missing values in `provstate`, `city`, and `targsubtype1_txt` are
    filled with `Unknown`.
-   `total_casualties` is calculated as `nkill + nwound`.
-   Rows with missing latitude or longitude are removed.
-   The final analytical dataset contains 20 columns.

------------------------------------------------------------------------

## 5. Phase 4: Data Visualization

The following Python plots correspond to the visual types and measures
shown in the **Anatomy of Terrorism** Power BI dashboard:

1.  Treemap --- incident count by target category.
2.  Pie chart --- fatalities by country.
3.  Line chart --- incident count by year.
4.  Column chart --- total casualties by year.
5.  Bar chart --- incident count by country.

These plots use the cleaned dataset and the same measures represented in
the dashboard.

### 5.1 Incident Count by Target Category --- Treemap

A treemap displays incident counts for each target category. The size of
each rectangle represents the incident count for that category.

``` python
# Count incidents for each target category
target_count = (
    df_clean['targtype1_txt']
    .value_counts()
    .reset_index()
)

target_count.columns = [
    'Target Category',
    'Incident Count'
]

# Create the treemap
fig = px.treemap(
    target_count,
    path=['Target Category'],
    values='Incident Count',
    title='Count of Incidents by Target Category'
)

fig.show()
```

### 5.2 Fatalities by Country --- Pie Chart

This chart compares the countries with the highest total number of
fatalities. The chart displays the top 10 countries for readability.

``` python
# Calculate total fatalities by country and select the top 10
country_kills = (
    df_clean.groupby('country_txt')['nkill']
    .sum()
    .sort_values(ascending=False)
    .head(10)
    .reset_index()
)

# Create the pie chart
fig = px.pie(
    country_kills,
    names='country_txt',
    values='nkill',
    title='Top 10 Countries by Total Fatalities'
)

fig.show()
```

### 5.3 Incident Count by Year --- Line Chart

A line chart displays how the number of recorded incidents changes over
the years.

``` python
# Count incidents for each year
year_events = (
    df_clean.groupby('iyear')['eventid']
    .count()
    .reset_index(name='Count of Incidents')
)

# Create the line chart
fig = px.line(
    year_events,
    x='iyear',
    y='Count of Incidents',
    title='Count of Incidents by Year',
    labels={'iyear': 'Year'}
)

fig.show()
```

### 5.4 Total Casualties by Year --- Column Chart

This chart shows the total casualties for each year. Total casualties
combine fatalities and injuries.

``` python
# Calculate total casualties for each year
year_casualties = (
    df_clean.groupby('iyear')['total_casualties']
    .sum()
    .reset_index()
)

# Create the column chart
fig = px.bar(
    year_casualties,
    x='iyear',
    y='total_casualties',
    title='Total Casualties by Year',
    labels={
        'iyear': 'Year',
        'total_casualties': 'Total Casualties'
    }
)

fig.show()
```

### 5.5 Incident Count by Country --- Bar Chart

The chart displays the countries with the highest incident counts,
matching the country-wise incident visual in the Power BI dashboard. It
displays the top 20 countries.

``` python
# Count incidents for each country and select the top 20
country_events = (
    df_clean['country_txt']
    .value_counts()
    .head(20)
    .reset_index()
)

country_events.columns = [
    'Country',
    'Incident Count'
]

# Create the bar chart
fig = px.bar(
    country_events,
    x='Country',
    y='Incident Count',
    title='Top 20 Countries by Incident Count'
)

fig.update_layout(xaxis_tickangle=-45)
fig.show()
```

------------------------------------------------------------------------

## 6. Phase 5: Power BI Dashboard

The same cleaned dataset was used to create the interactive Power BI
dashboard.

**Dashboard title:** Anatomy of Terrorism

The dashboard presents:

-   Incident counts
-   Target categories
-   Fatalities by country
-   Yearly trends

The dashboard provides an interactive way to explore the historical
patterns represented in the cleaned dataset.

------------------------------------------------------------------------

## 7. Phase 6: Key Insights

The following insights are based on the supplied cleaned CSV and the
dashboard measures.

### 7.1 Private Citizens and Property Are the Largest Target Category

The dataset records **41,364 incidents** under "Private Citizens &
Property", making it the largest target category. This represents
approximately **24% of the 172,599 incidents**.

Military, Police, Government (General), and Business are also prominent
target categories.

### 7.2 Fatalities Are Concentrated in a Small Group of Countries

Iraq has the highest recorded fatalities with **76,617**, followed by:

  Country         Recorded fatalities
  ------------- ---------------------
  Iraq                         76,617
  Afghanistan                  36,190
  Pakistan                     23,302
  Nigeria                      21,675

Together, these four countries account for approximately **41% of all
fatalities** in this prepared dataset.

### 7.3 Incident Frequency Rises Sharply in the Later Years

The highest annual incident count occurs in **2014**, with **16,794
incidents**.

  Year     Incident count
  ------ ----------------
  2014             16,794
  2015             14,861
  2016             13,530
  2013             11,926

This shows a pronounced rise in recorded incident frequency during the
2013--2016 period.

### 7.4 The Highest Annual Casualty Total Is Recorded in 2014

The year **2014** has the highest total casualties, with **85,230**
(fatalities plus injuries).

  Year     Total casualties
  ------ ------------------
  2014               85,230
  2015               82,707
  2016               74,766

The annual casualty chart therefore shows a major concentration of
impact in these years.

### 7.5 Incident Frequency and Fatalities Measure Different Aspects

Iraq has both the highest incident count (**23,573**) and the highest
fatalities (**76,617**) in this dataset.

However, country rankings by incident count and by fatalities are not
identical. For example, India ranks fourth by incident count but fifth
by fatalities.

This shows why both frequency and human impact should be examined rather
than relying on one measure alone.

### 7.6 Injuries Make Up a Substantial Part of the Recorded Human Impact

Across the prepared dataset:

  Measure                   Total
  --------------------- ---------
  Recorded fatalities     384,395
  Recorded injuries       505,114
  Total casualties        889,509

The derived `total_casualties` measure combines fatalities and injuries
to provide a broader view of recorded human impact.

### 7.7 Ethical Considerations

-   The dataset describes historical incidents; it should not be used to
    stereotype people, communities, religions, or nationalities.
-   Country totals are descriptive counts and do not explain the causes
    of incidents or the circumstances behind each event.
-   Missing casualty values were filled with zero according to the
    supplied cleaning workflow. This is a processing choice and should
    not be interpreted as proof that no casualties occurred in every
    originally missing record.
-   The results depend on the coverage, definitions, and reporting
    quality of the source dataset.
-   Dashboard findings should support careful research and responsible
    public-safety discussion, not individual-level profiling or
    unsupported predictions.

------------------------------------------------------------------------

## 8. Conclusion

In this mini project, the Global Terrorism Dataset covering
**1970--2017** was inspected and prepared using the supplied cleaning
steps. The analysis focused on 20 relevant dimensions related to time,
location, attack and target categories, groups, weapons, and casualties.

Five Python visualizations were created to match the main analytical
views in the **Anatomy of Terrorism** Power BI dashboard. The dashboard
and the supporting analysis highlight historical patterns in incident
frequency, target categories, geographic distribution, and human impact.

The findings are descriptive and should be interpreted with the
dataset's limitations and ethical considerations in mind.

------------------------------------------------------------------------

## 9. Libraries Used

  -----------------------------------------------------------------------
  Library                             Purpose in this project
  ----------------------------------- -----------------------------------
  Pandas                              Loading, inspecting, cleaning,
                                      grouping, and analyzing tabular
                                      data

  Matplotlib                          Imported as a plotting library

  Plotly Express                      Creating interactive treemap, pie,
                                      line, and bar/column charts

  Power BI                            Building the interactive dashboard
  -----------------------------------------------------------------------

## 10. Project Files and Execution Notes

The notebook expects a prepared CSV file named `final_dataset.csv` in
the same folder.
