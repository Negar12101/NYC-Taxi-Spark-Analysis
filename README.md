# NYC Taxi Data Analysis


## Overview
This project analyzes the NYC Taxi dataset using distributed data processing and machine learning on SDSC Expanse. All preprocessing and modeling were completed with Spark DataFrames and Spark MLlib to satisfy DSC 232R requirements for distributed computing.

## Dataset
https://www.kaggle.com/datasets/verifiedbysuman/nyc-taxi & 
https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page

## Project Structure
- `NYC Taxi.ipynb` : main notebook containing data exploration, preprocessing, model training, evaluation, and conclusions
- `README.md` : project summary and milestone overview

## Milestone 2 Summary
In Milestone 2, Spark DataFrame operations were used for large-scale data exploration of the NYC Taxi dataset.

Completed work:
- Loaded the 2017 NYC Taxi FHV dataset with Spark
- Counted total observations
- Inspected schema and column types
- Analyzed categorical and numeric columns
- Checked missing values
- Investigated duplicate records using a Spark-safe strategy
- Identified a target variable based on trip duration
- Planned preprocessing steps for modeling

Main findings:
- The dataset contains `317,546,944` observations
- Key columns include dispatching base IDs, pickup/drop-off timestamps, and pickup/drop-off location IDs
- The target variable for modeling was derived as `trip_duration_minutes`
- Missing values were present in some location columns
- Full duplicate-row checking was too expensive at this scale, so a Spark-safe approximate/sample-based strategy was used

## Milestone 3 Summary
In Milestone 3, preprocessing was completed with Spark and a first distributed machine learning model was trained using Spark MLlib on SDSC Expanse.

### Preprocessing
The following preprocessing steps were completed using Spark:
- Removed rows with missing pickup or drop-off timestamps
- Created `trip_duration_minutes`
- Filtered invalid trip durations
- Created a binary target label for classification
- Extracted time-based features:
  - `pickup_hour`
  - `pickup_month`
  - `pickup_dayofweek`
  - `is_weekend`
- Filled missing values in location columns
- Assembled features using Spark MLlib transformers

### First Distributed Model
The first model was trained using:
- `pyspark.ml.classification.RandomForestClassifier`

This satisfies the distributed model requirement because:
- training was performed with Spark MLlib
- data remained in Spark DataFrames
- training ran on SDSC Expanse
- computation used multiple Spark executors rather than driver-only processing

### Model Comparison
Two Random Forest models with different hyperparameters were compared:
- Model 1: fewer trees and lower depth
- Model 2: more trees and greater depth

The models were evaluated on:
- training set
- validation set
- test set

Evaluation metrics included:
- Accuracy
- F1 Score
- AUC

### Fitting Analysis
The fitting behavior of the models was analyzed by comparing training and validation/test performance:
- If both training and validation scores are low, the model is underfitting
- If training performance is much higher than validation/test performance, the model is overfitting
- The best model is the one that gives strong validation/test performance with a smaller generalization gap

### Conclusion
The first distributed model provided a reasonable baseline for predicting trip duration class in the NYC Taxi dataset. Spark made it possible to preprocess and train on a very large dataset without collecting data to the driver. Future improvements may include stronger tree-based models such as:
- `GBTClassifier`
- Spark XGBoost
- additional feature engineering and hyperparameter tuning

## Distributed Computing Evidence
This project meets DSC 232R distributed computing requirements:
- Spark DataFrames were used for preprocessing
- Spark MLlib was used for model training
- The model was trained on SDSC Expanse
- Parallel training was verified through Spark configuration and Spark UI

Example Spark configuration used:
- multiple executor instances
- distributed shuffle partitions
- parallel task execution across executors

## How to Run
Run the notebook on SDSC Expanse with Spark configured. Update dataset paths as needed for your environment.

## Notebook
- Attached as 2 and 3

## Author
Negar Ashktorab
