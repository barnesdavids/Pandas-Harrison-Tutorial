---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.14.0
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

## Pandas Mega Tutorial - World Happiness Data (archive set)

### original tutorial - https://www.youtube.com/watch?v=-Db9ecTQXUE

```python
import pandas as pd
import numpy as np
%matplotlib inline
### trying again
```

```python
ver = pd.__version__
ver
```

### Step 2 - Load Data

```python
## this is to test git settings
#%ls -al
# lets try again
```

```python
ls archive_world_happiness_report
```

```python
# read in a single file - 2019 only
# df = dataframe

df = pd.read_csv("archive_world_happiness_report/2019.csv")
```

### Step 3 - Inspect Data

```python
# returns all the data contained in dataframe read in object = df
# df contained 156 rows and 9 columns

df
```

#### Step 3.1 - use head() to inspect first 5 rows

```python
df.head()
```

#### Step 3.2 - use tail() to inspect last 5 rows

```python
df.loc[df['Country or region'] == 'Finland',['Country or region','Social support','Generosity']]
```

```python
df.tail()
```

#### Step 3.3 - use df.shape - for number of rows and columns

```python
df.shape
```

#### Step 3.4 - using python function to get column and row count

```python
row_count = len(df.index)
col_count = len(df.columns)
print(f" number of rows {row_count}")
print(f" number of columns {col_count}")
```

#### Step 3.5 using python function continued

```python
num_rows, num_cols = df.shape
print(f"total number of rows are {num_rows}, and total columnss are {num_cols}")
```

# Step 4 - What are the data types being used - using dtypes

```python
# object - refers to string datatype
# int - normal number (interger)
# float64 - normal decimal point data
# of all object datatype - cast from string to relevant dataset type
df.dtypes
```

#### Step 5 - statistical analysis of data - using describe() method

```python
df.describe()
```

#### Step 6 - using info to summarise findings

```python
# non-null count indicates all columns have valid data - i.e. 156 rows without 'zeros'
df.info()
```

```python

```

### What is a dataframe

```python
## a pandas dataframe is a collection of rows and columns
## df is object type dataframe
type(df)
```

```python
## looking at specific column to find its type - type is "series"
type(df["Country or region"])
```

```python
df.columns
## each column is a series
```

```python
for col in df.columns:
    print(f"{col} is of datatype:- {type(df[col])}")
```

### Dataframe has different methods and atributes vs a series


#### Methods available in a dataframe - (df)

```python
#dir(df)
```

#### Methods available in a series (i.e. columns)

```python
#dir(df["Country or region"])
```

### Recap.....

**DataFrame** = collection of columns,
**Series** = single column,
**DataFrame** = is a collection of series i.e. columns.


## Section 1 - Working with a Single dataset


### Question 1 - What is the average min, max happiness score

```python
df["Score"]
```

```python
mean_var = df["Score"].mean()
min_var = df["Score"].min()
max_var = df["Score"].max()
answer = (f"the mean score is {mean_var}\n"
          f"the max score is {max_var}\n"
          f"and the min score is {min_var}\n")
print(answer)
        
```

```python
df.max()
```

### Question 2 - what is the happiness score for your country


#### Option 1 - use bracket notation filtering

```python
## this method creates a Boolean Mask
df["Country or region"] == "United States"
```

```python
# lets assign mask as variable

sa_boolean_mask = df["Country or region"] == "South Africa"
sa_boolean_mask
```

```python
# lets filter on boolean mask
df[sa_boolean_mask]
```

```python
# lets filter on boolean mask and score column
df[sa_boolean_mask]["Score"]
```

```python
# lets filter using subset
df[df["Country or region"] == "South Africa"]["Score"].iloc[0]
```

#### Option 2 - using indices with .iloc and .loc

```python
### Using .iloc
### iloc returns value based on its indexed-location(iloc)
df.head()
```

```python
## Finland is indexed at position 0 (zero)
df.iloc[0]
## above method returns the first row of data - in a series
```

```python
## however we need the score for the south africa only
## so we pass in the second argument as second indexed location

df.iloc[105, 2]
```

### iloc (INDEXED LOCATION) is a way to access data based on the numeric position of the data in its rows and columns


#### loc however returns the values based - name of index and name of column 

```python
## lets look at our dataset
df.head()
```

```python
## lets re-index df based on Country of Origin
## method to use - df.set_index("Country or region")

df_country_idx = df.set_index("Country or region")

```

```python
## now using loc

df_country_idx.loc["South Africa", "Score"]

```

```python
## return entire row for South Africa
df_country_idx.loc["South Africa"]
```

```python
#### df.loc[(df["Sex"] == "female") & (df["Pclass"] == 1)]

df.loc[(df["Country or region"] == "United States")]
```

### Question 3 - Which country has the highest Generosity score

```python
df.sort_values(by="Generosity", ascending=False).iloc[0]
```

```python
### Using ARGMAX
max_gen_idx = df["Generosity"].argmax()
```

```python
df.iloc[max_gen_idx]
```

```python
df.iloc[max_gen_idx]["Country or region"]
```

### Question 4 - Basic Data Viz- What does the distribution of happiness score look like?

```python
score_dist = df["Score"]
```

```python
score_dist.hist()
```

### Question 5 - What does the distribution of all columns look like?

```python
_ = df.hist(figsize=(10, 10))
```

### Question 6 - What is the relationship between social support and happiness score?

```python
df.plot(kind="scatter", title="Relationship between social support and happiness", x="Social support", y="Score")
```

### Question 7 - Basic Data Transformation - What if we wanted score to be ranked out of 0-100 instead of 0-10?

```python
# currently score of out of 10
df["Score"]
```

```python
# score out of 100
df["Score"]*10
```

#### Lets add new score back into dataframe

```python
## start by making copy of dataframe

df_copy = df.copy()
```

```python
df_copy
```

```python
df_copy["Score out of 100"] = df_copy["Score"]*10
```

```python
df_copy
```

## Section 2 - Data Transformation - concatenating multiple files

```python
ls archive_world_happiness_report/
```

### Question 8 - how can I combine multiple datasets?


#### Step 1 - Find the files to be used/imported

```python
# can use glob function
import glob
```

```python
## glob.glob("filepath/file_types")
glob.glob("archive_world_happiness_report/*.csv")
```

```python
file_names = glob.glob("archive_world_happiness_report/*.csv")
```

#### Step 1.1 - Load all the data from files into dataframes

```python
## initialize empty dictionary to store file_names 
dfs = {}
```

#### Step 1.2 - we want to extract only the [year] portion of filename

```python
## can use Regex maybe???
##file_names[0].split("/")[-1].split(".")[0]
```

```python
file_names[0][-8:-4]
```

```python
for f in file_names:
    data = pd.read_csv(f)
    year = f[-8:-4]
    dfs[year] = data
```

```python
dfs.keys()
```

```python
dfs['2019']
```

```python
dfs["2015"]
```

#### Step 2 - look at the shape of each years dataframe

```python
## iterate over key-value pair of dictionary using for loop
for year, data in dfs.items():
    print(f"{year}, {data.shape}")
```

```python
## lets sort by years

years = sorted(dfs.keys())
for year in years:
    data = dfs[year]
    print(f"{year}, {data.shape}")
```

##### based on above only 2018 & 2019 data seems identical - same shape

##### lets inspect data

```python
#dfs["2018"].head(10)
```

```python
#dfs["2019"].head(10)
```

##### do any of the datasets have a common column on which we can join?

```python
#for year in years:
#    data = dfs[year]
#    print(year)
#    print(data.head(1))
```

### Step 3 - lets insert a new column [year] into each dataframe

```python
for year in years:
    dfs[year]["Year"] = year
```

```python
dfs["2016"].head()
```

### Step 4 - lets combine data for 2019 and 2018


##### lets inspect if column headers are same - using python "sets"

```python
## returns true if all the same column headers
## returns false if not
set(dfs["2019"].columns) == set(dfs["2018"].columns)
```

```python
## another way
set(dfs["2019"].columns) ^ set(dfs["2018"].columns)
```

```python
## lets concatenate/combine the dataframes

df_concat = pd.concat([dfs["2019"], dfs["2018"]])
```

```python
### above concats dataframe - however rows not equal
df_concat.loc[0]
```

```python
df_concat = pd.concat([dfs["2019"], dfs["2018"]], ignore_index=True)
```

```python
df_concat
```

### Step 4 - lets combine data for 2019,2018 and 2015

```python
dfs["2019"].head(1)
```

```python
dfs["2015"].head(1)
```

```python
#len(dfs["2019"].columns)
```

```python
len(dfs["2015"].columns)
```

```python
#set(dfs["2019"].columns) ^ set(dfs["2015"].columns)
```

##### Step 4.1 - rename/change/map columns names for 2019 to 2015

```python
new_2015_column_names = [
    "Country or region","Region","Overall rank","Score",
    "GDP per capita","Social support",
    "Healthy life expectancy","Freedom to make life choices",
    "Perceptions of corruption","Generosity","Standard Error","Dystopia Residual",
    "Year"]
```

```python
len(new_2015_column_names)
```

```python
dfs["2015"].columns = new_2015_column_names
```

```python
dfs["2015"]
```

```python
df_concat = pd.concat([dfs["2019"],dfs["2018"], dfs["2015"]], ignore_index=True)
df_concat
```

### Question 9 - how can I remove NAN's from Region? i.e. align Country to Region

```python
### Method 1 - using of Map Function
```

```python
### Method 2 - using sql joins function
```

```python
### what do we wanna do

# map country or region --> region(2015 data)
```

```python
# lets look at 2015 data
# create a new dataframe that maps region to country

region_mapping_df = dfs["2015"][["Country or region", "Region"]]
region_mapping_df
```

```python
df_concat = df_concat.drop(columns=["Region"])
```

```python
df_concat
```

```python
df_concat_with_region = pd.merge(left=df_concat, right=region_mapping_df, how="left", on="Country or region")
```

```python
df_concat_with_region
```

### Question 9.b - are there any regions that did not match-up - are there still NaN values?

```python
no_region_mask = df_concat_with_region["Region"].isna()
```

```python
no_region_mask
```

```python
## get count of NaNs not matched
no_region_mask.sum()
```

```python
### which are the rows with NaNs still?
df_concat_with_region[no_region_mask]
```

### Question 10 - now that we have a combined dataset - how can we convert it to new file for later usage

```python
df_concat_with_region.to_csv("combined-happiness-score.csv", index=False)
```

```python
ls
```

## Section 3 - working with new combined dataset

```python
df_concat_with_region.head(5)
```

### Question 11 - how many countries do we have per region?

```python
### using value_counts() method

df_concat_with_region["Year"].value_counts()
```

```python
## filter down to one year - boolean mask
df_concat_with_region["Year"] == "2019"
```

```python
## number of countries per region
df_concat_with_region[df_concat_with_region["Year"] == "2019"]["Region"].value_counts()
```

```python
## how many regions do we have?
df_concat_with_region[df_concat_with_region["Year"] == "2019"]["Region"].nunique()
```

```python
## how many regions do we have - full data set?
df_concat_with_region["Region"].nunique()
```

### Question 12 - what is the average happiness score per region?

```python
avg_happiness_by_region = df_concat_with_region[["Region", "Score"]].groupby("Region").mean()
avg_happiness_by_region
```

```python
## lets sort above
avg_happiness_by_region.sort_values(by="Score", ascending=False)
```

```python
### lets plot above
```

```python
avg_happiness_by_region.sort_values(by="Score", ascending=False).plot(title="Average Happiness per Region", kind="bar")
```

### Question 13 - what is the average happiness score over time?

```python
avg_happiness_per_year = df_concat_with_region.groupby("Year").mean()["Score"]
```

```python
avg_happiness_per_year
```

```python
avg_happiness_per_year.plot(title="Avg Happiness by Year", kind="bar")
```

### Question 14 - In 2019 how many countries in Africa(sub-saharan and Northern/Middle East) had a happiness score higher then the lowest score in Western Europe?


##### Question 14a - what was the lowest happiness score in Western Europe in 2019?

```python
df_2019 = df_concat_with_region[df_concat_with_region["Year"] == "2019"]
```

```python
df_2019[df_2019["Region"] == "Western Europe"].min()["Score"]
```

```python
df_2019[df_2019["Region"] == "Western Europe"].tail(1)
```

```python
western_europe_lowest_score = 5.287
```

##### Question 14b -how many african countries had a happiness score higher than western europe lowest score?

```python
### create table for African Regions
african_regions = ['Middle East and Northern Africa','Sub-Saharan Africa']
```

```python
df_2019[df_2019["Region"].map(lambda region: region in african_regions)].head(2)
```

```python
df_african_countries = df_2019[df_2019["Region"].map(lambda region: region in african_regions)]
```

```python
## boolean mask
(df_african_countries["Score"] > western_europe_lowest_score).sum()
```

```python
df_african_countries[df_african_countries["Score"] > western_europe_lowest_score]
```

### Question 15 - can we look at the average change in happiness levels per region over time?

```python
## using groupby - multiple conditions - year and region
## returns a multi-indexed object
df_concat_with_region.groupby(["Year","Region"]).mean()["Score"]
```

```python
## use unstack method
df_concat_with_region.groupby(["Year","Region"]).mean()["Score"].unstack(level=0)
```

```python
## lets plot
df_concat_with_region.groupby(["Year","Region"]).mean()["Score"].unstack(level=0).plot(kind="bar", figsize=(12,5))

```

### Question 16 - which countries had the highest score increase over time?

```python
df_concat_with_region.head(3)
```

```python
### using Pivot table functionality

df_countries_over_time = df_concat_with_region.pivot_table(values="Score", index=["Region","Country or region"], columns="Year")
```

```python
### create a column to calculate difference y-o-y
df_countries_over_time["Difference"] = df_countries_over_time["2019"] - df_countries_over_time["2018"]
```

```python
df_countries_over_time.head()
```

```python
## sort values to see highest climbers
df_countries_over_time.sort_values(by="Difference", ascending=False)
```

#### TO-DO's still:
1. incorporate 2016/2017 datasets
2. using pd.merge - find external dataset to join (e.g. population data)
3. find own dataset

```python

```

```python

```

```python

```
