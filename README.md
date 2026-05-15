# NYC Taxi Data Analysis


## Overview
This project analyzes the NYC Taxi dataset using distributed data processing and machine learning on SDSC Expanse. All preprocessing and modeling were completed with Spark DataFrames and Spark MLlib to satisfy DSC 232R requirements for distributed computing.


# NYC Taxi Project - Milestone 3

## Overview
This project analyzes the NYC Taxi dataset using distributed data processing and machine learning on SDSC Expanse. All exploration, preprocessing, and model training were completed with Spark DataFrames and Spark MLlib to satisfy the DSC 232R requirement for distributed computing.

## Dataset
https://www.kaggle.com/datasets/verifiedbysuman/nyc-taxi & 
https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page

Project files:
- `NYC Taxi (3).ipynb` – main notebook for Milestone 3
- `3.pdf` – exported results/report
- `README.md` – project summary

## Dataset Summary
The analysis uses the 2017 NYC Taxi FHV dataset.

Main dataset facts:
- Total observations: `317,546,944`
- Columns:
  - `dispatching_base_num`
  - `pickup_datetime`
  - `dropOff_datetime`
  - `PUlocationID`
  - `DOlocationID`
  - `SR_Flag`
  - `Affiliated_base_number`

The dataset contains a mix of:
- categorical identifier features
- timestamp-based temporal features
- location-based variables

## Data Exploration Results
Spark DataFrames were used to explore the dataset at scale.

### Schema and Variables
- `dispatching_base_num` and `Affiliated_base_number` are string identifier columns
- `pickup_datetime` and `dropOff_datetime` are timestamp columns
- `PUlocationID` and `DOlocationID` are stored as doubles but represent categorical location IDs
- `SR_Flag` is an integer indicator column

### Cardinality
- `dispatching_base_num`: `867` unique values
- `Affiliated_base_number`: `6099` unique values

This shows that both identifier columns have high cardinality, especially `Affiliated_base_number`.

### Numeric Summary
- `PUlocationID` range: `0` to `265`
- `DOlocationID` range: `0` to `265`
- `PUlocationID` mean: `142.94`
- `DOlocationID` mean: `6.51`

Although these columns are numeric in storage type, they represent location codes and should be treated as categorical variables.

### Missing Values
Missing values were found in several columns:
- `PUlocationID`: `18,393,090`
- `DOlocationID`: `71,010,112`
- `SR_Flag`: `71,544,632`
- `Affiliated_base_number`: `3,760`

Columns with no missing values:
- `dispatching_base_num`
- `pickup_datetime`
- `dropOff_datetime`

### Duplicate Values
The notebook reported:
- Total rows: `317,546,944`
- Distinct rows: `67,597,205`
- Duplicate rows: `249,949,739`

This indicates that duplicate handling is an important preprocessing step for this dataset.

## Visual Exploration
The notebook includes Spark-based aggregations and visualizations such as:
- Top pickup locations
- Histogram of pickup location IDs
- Histogram of trip duration
- Scatter plot of pickup hour vs. trip duration

These plots show that:
- some pickup locations are much busier than others
- trip durations are concentrated in shorter ranges
- pickup activity and trip length vary by hour of day

## Milestone 3: Preprocessing
Milestone 3 focused on building a reproducible Spark preprocessing workflow and training the first distributed model.

### Preprocessing Steps
The following preprocessing steps were completed in Spark:
- removed rows with missing `pickup_datetime` or `dropOff_datetime`
- created `trip_duration_minutes`
- filtered invalid trip durations
- created a binary classification label:
  - `label = 1` if `trip_duration_minutes >= 30`
  - `label = 0` otherwise
- engineered time-based features:
  - `pickup_hour`
  - `pickup_month`
  - `pickup_dayofweek`
  - `is_weekend`
- filled missing values in location columns
- assembled model features using Spark MLlib

### Features Used for Modeling
The first distributed model used:
- `PUlocationID`
- `DOlocationID`
- `pickup_hour`
- `pickup_month`
- `pickup_dayofweek`
- `is_weekend`

### Class Distribution
The final classification label distribution was:
- `label = 0`: `95,729,974`
- `label = 1`: `23,453,547`

### Training Sample
To make distributed training more stable and efficient, a Spark sample of `1%` of the processed dataset was used:
- Sampled dataset size: `1,193,817`

Split sizes:
- Train: `836,353`
- Validation: `178,705`
- Test: `178,759`

## Distributed Model Training
The first models were trained using Spark MLlib on SDSC Expanse with:

- `pyspark.ml.classification.RandomForestClassifier`

Two distributed Random Forest models were compared:

### Model 1: RF1
- `numTrees = 20`
- `maxDepth = 5`
- `maxBins = 300`

### Model 2: RF2
- `numTrees = 50`
- `maxDepth = 10`
- `maxBins = 300`

## Evaluation Results
The models were evaluated using:
- Accuracy
- F1 Score
- AUC

### RF1 Results
- Train Accuracy: `0.8118`
- Validation Accuracy: `0.8109`
- Test Accuracy: `0.8114`
- Train F1: `0.7430`
- Validation F1: `0.7417`
- Test F1: `0.7426`
- Train AUC: `0.6760`
- Validation AUC: `0.6726`
- Test AUC: `0.6753`

### RF2 Results
- Train Accuracy: `0.8310`
- Validation Accuracy: `0.8291`
- Test Accuracy: `0.8293`
- Train F1: `0.7887`
- Validation F1: `0.7869`
- Test F1: `0.7863`
- Train AUC: `0.7118`
- Validation AUC: `0.7066`
- Test AUC: `0.7084`

## Best Model
`RF2` performed better than `RF1` across validation and test metrics and was selected as the best first distributed model.

Best model test performance:
- Accuracy: `0.8293`
- F1 Score: `0.7863`
- AUC: `0.7084`

Confusion matrix for the best model:
- True Negative: `141,358`
- False Positive: `2,234`
- False Negative: `28,273`
- True Positive: `6,894`

## Fitting Analysis
The two Random Forest models were compared to analyze underfitting and overfitting.

- `RF1` serves as a simpler baseline model
- `RF2` improves performance by using more trees and greater depth
- `RF2` achieved better validation and test results while staying reasonably close to training performance

This suggests:
- `RF1` is closer to underfitting
- `RF2` provides a better balance between model complexity and generalization

## Distributed Computing Evidence
This project satisfies the DSC 232R distributed computing requirement because:
- preprocessing was done with Spark DataFrames
- training was done with Spark MLlib
- no driver-only scikit-learn model was used
- training ran on SDSC Expanse
- multiple executors were used during training

Spark configuration used in the notebook:
- Executor instances: `7`
- Shuffle partitions: `200`

The Spark UI can be used to verify that jobs, stages, and tasks were distributed across multiple executors.

## Conclusion
In Milestone 3, Spark was used to preprocess the NYC Taxi dataset and train distributed Random Forest models on SDSC Expanse. The workflow included missing value handling, feature engineering from timestamp columns, label creation from trip duration, feature assembly, model training, and evaluation.

The second Random Forest model outperformed the first model and provided a stronger baseline for future work. Distributed computing was essential because the NYC Taxi dataset is extremely large, and Spark made it possible to process and model the data without collecting it to the driver.

## Future Work
Possible next steps for Milestone 4 include:
- `GBTClassifier`
- Spark XGBoost
- additional feature engineering
- more hyperparameter tuning
- deeper analysis of location patterns and class imbalance

## Author
Negar Ashktorab
