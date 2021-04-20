# Predicting-Mortality
Predicting mortality of ICU patients using Machine Learning

## Background

Beth Israel Deaconess Medical Centre is a large hospital in Boston, MA.  It employs more than 4,000 doctors and provides a range of emergency services.  

Currently, doctors in the ICU oversee a lot of patients and in order to make a judgement on the severity of a patient’s diagnosis, the doctor has to review the output of many different machines that are monitoring the patient’s vital signs as well as lab results and other information entered into the hospital’s electronic medical record (EMR) system.  This is both inefficient and costly, and because this is a manual process, doctors may even inadvertently overlook certain patients that are actually at high risk.  

## Objectives

So the aim of our project is to use the routine data that is collected electronically on each patient admitted into the ICU to build an AI model that can accurately predict their risk of mortality.

## Methodology

### Data
The data in our project came from the MIMIC database.  This database provides de-identified data for over 50,000 ICU patients at the Beth Israel Deaconess Medical Center.     

MIMIC is structured as a relational database consisting of many related tables.  Each table was provided to us as a separate CSV file that are linked together through unique identifiers. 

Our target variable for this supervised classification problem is the “hospital_expire_flag”, which is a binary variable indicating if the patient survived until hospital discharge (0) or died during their stay (1).  

### Feature Selection

To select the features for our model, we first looked at the ADMISSIONS and ICUSTAYS tables which contain information such as the patient’s age and demographics, and what type of hospital unit they stayed in. We also extracted our target variable ‘hospital_expire_flag’ from the ADMISSIONS table and renamed it to “mortality”.
 
However, since we’re trying to predict the outcome of a patient’s health condition, the most important features would actually be the physiological measurements and vital signs that are collected from each patient during their stay.  In order to select which of these physiological measurements are most relevant for our predictive model, we looked at several of the most commonly used manual scoring systems that predict mortality in ICUs and also talked to a retired ICU nurse to get her perspective on what are some of the most important things she would look for in a patient to determine their severity.  From these sources of expert opinion, we came up with a list of features to be extracted from the database and engineered two variables for each feature using its mean and standard deviation from the first 24 hours of a patient’s stay in the ICU.  The mean value provides a consistent measurement of each patient’s vital signs over a period of time, and the standard deviation shows how much these vital signs are fluctuating, both of which are important indicators as to the overall condition of the patient’s health.

Putting it all together, we merged the features that we’ve extracted from the database into one CSV file where each row represents a unique stay for a patient in the ICU and each column represents a predictor for the target variable of mortality.

### Exploratory Data Analysis

Looking at the dataset that we had put together, we first did some exploratory data analysis and data cleaning to take care of missing data, outliers and collinearity between variables.
 
The cleaned dataset was then split into train and test sets and fed through a transformation pipeline where numerical variables were scaled, categorical variables were one-hot encoded, and a custom feature selector method was used to select the top 50% of predictors that ranked the highest in terms of feature importance. 
 
This resulted in 41 features, with about 38,000 records in the training set and 9600 in the test set.

### ML Models used

Using the training set, we trained 8 different classification models and performed hyperparameter tuning on each one to improve on its base performance.  We chose a variety of different algorithms in order to compare the performance of simpler models such as logistic regression and decision tree with more complex models like random forest, XGBoost and neural network.

Looking at the resulting plot of the ROC curves for each of the models, we see that several models have similarly good performance, including random forest, xgboost, and the two neural networks -- one which was created through Keras and Tensorflow, and the other using MLP classifier, which is the sci-kit learn implementation of a neural network.

To try and further improve the performance of our model, we used the stacking ensemble technique to test out different combinations of base models and meta-classifiers.  In the end, the combination of the random forest, xgboost and MLP classifier models with another MLP classifier as the final estimator resulted in the best AUC score of .885, which is overall greater than any of our individual models. 
 
Therefore, we decided to choose the stacked ensemble as our final model.

## Results

We then tested the model’s predictions against the test set and found that the AUC score of the test set is .884, a different of only 0.1% compared to the results of the training set, showing that the performance of our model is indeed generalizable to unseen data.

You can refer to the power point presentation to get a detailed insight into how we think this would be useful in a real ICU setting.(please read speaker notes in the power point)
