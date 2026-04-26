# NYC Taxi Data Analysis (Milestone 2)

## Dataset
https://www.kaggle.com/datasets/verifiedbysuman/nyc-taxi
https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page

## Project Overview
This project analyzes NYC TLC taxi trip data using Apache Spark to explore mobility patterns and fare trends. The main goal of this milestone is to understand the structure and quality of the dataset before building machine learning models in later milestones. This includes examining the schema, identifying column types, checking for missing and duplicate values, describing the target column, and creating visualizations that help reveal travel patterns and potential preprocessing needs.

## SDSC Setup
(You will fill this later)

## Spark Configuration
(You will fill this later)

## Data Exploration

The dataset schema shows a mix of string, timestamp, double, and integer columns. The columns dispatching_base_num and Affiliated_base_number are string-based identifiers. The columns pickup_datetime and dropOff_datetime are timestamp values that can be used to engineer time-related features such as pickup hour or trip duration. The columns PUlocationID and DOlocationID are stored as doubles, but they represent location IDs and should therefore be treated as categorical variables rather than continuous numerical features. The SR_Flag column is stored as an integer and appears to act as an indicator-type variable.

The target column selected for this project is PUlocationID. This column represents the pickup location ID for each taxi trip. It contains 150,287,219 non-null rows and 265 unique target values, which means the prediction task is a multiclass classification problem with 265 classes. The repeated target values are expected, since many trips can originate from the same pickup location.

The dataset also contains missing values. The columns dispatching_base_num, pickup_datetime, and dropOff_datetime have no missing values, while PUlocationID, DOlocationID, SR_Flag, and Affiliated_base_number contain null values. Missingness is especially high in DOlocationID and SR_Flag, which suggests these columns will require special attention during preprocessing.

In addition, the duplicate-row check shows that the dataset contains a very large number of repeated rows. There are 317,546,944 total rows, but only 67,597,205 distinct rows, meaning the dataset includes 249,949,739 duplicate rows. These duplicates may affect analysis and model training, so they will need to be addressed later.

For categorical columns, dispatching_base_num has 867 unique values and Affiliated_base_number has 6099 unique values, showing that both features have high cardinality. For numerical columns, PUlocationID and DOlocationID range from 0 to 265, which is consistent with coded location identifiers. The SR_Flag column has no usable numeric summary because its values appear to be fully missing in the current output.

## Visualizations

Several visualizations were created to better understand the dataset. A bar chart of the top 10 pickup locations shows the most frequently occurring pickup location IDs in the dataset. This helps identify the busiest pickup areas and reveals that trip activity is concentrated in a small set of locations rather than being evenly distributed across all pickup zones.

A histogram of pickup location IDs was also used to examine the frequency distribution of PUlocationID values. The histogram shows that some pickup IDs occur much more often than others, again suggesting that taxi demand is not evenly distributed across all locations. Since PUlocationID is a coded identifier, the histogram should be interpreted as a distribution of category frequencies rather than a true continuous numeric distribution.

A scatter plot of pickup hour versus trip duration was used to examine whether trip duration varies by time of day. After filtering invalid and extreme values, this plot helps show whether trips taken during certain hours tend to last longer or shorter. This visualization is useful for identifying temporal travel patterns and possible rush-hour effects.

## Preprocessing Plan

The dataset will be preprocessed by first handling missing values in important columns such as PUlocationID, DOlocationID, pickup_datetime, and dropOff_datetime. If missing values occur in essential fields, the affected rows may be removed. For other columns, missing values may be filled depending on the feature type and the amount of missingness. For example, categorical columns may use a placeholder such as "Unknown".

If the target variable is imbalanced, the class distribution will be checked first. If some pickup location classes occur much more often than others, techniques such as undersampling, oversampling, or class weighting may be considered so that the model does not become biased toward majority classes.

Several transformations are planned before modeling. Categorical variables such as location IDs and base numbers may be encoded using indexing or one-hot encoding techniques. Time-based features such as pickup hour, dropoff hour, day of week, and trip duration may be engineered from the timestamp columns. If numerical variables are included in the final feature set, scaling may also be applied where appropriate.

Spark preprocessing operations will likely include dropna(), fillna(), filter(), and withColumn() for cleaning and feature engineering. For machine learning preparation, Spark ML tools such as StringIndexer, OneHotEncoder, VectorAssembler, and StandardScaler may be used.

## Notebook
https://sprite-pull-friend.expanse-user-content.sdsc.edu/lab/tree/nashktorab/NYC%20Taxi.ipynb

