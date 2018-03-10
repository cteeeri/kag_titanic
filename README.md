# Practice Kaggle prediction copetition

## Overview

Kaggle has provided this practice challenge to analyse the publicly available dataset to predict the survival of RMS Titanic passengers in the unfortunate shipwreck during the first maiden voyage in 1912.

The prediction will be a binary classification and based on python.

The dataset has already been split into training and test sets. The test set will not be used in training and is purely used as test set and the resulting predictions are submitted to Kaggle for performance evaluation.

## Data features

+ Survival - possible values: Yes or No
+ pclass - ticket class. Possible values: 1, 2, 3
+ Sex - gender. Possible values: Male, Female
+ sibsp - number of sibling or spouse onboard
+ parch - number of parents or children onboard
+ ticket - ticket number, mostly similar when port of embarkation is similar
+ fare - passenger fare 
+ cabin - cabin number
+ embarked + port of embarkation. Possible values: C, Q, S
+ name
+ passengerId

## Data Exploration
There are some features that are decided to be not useful for prediction. The following are identified as such and dropped.
+ passengerId
+ cabin (there are close to 80% of training set that lacked this feature entry)

Below are some features that are missing for some passengers. They are filled in.
+ Embarked
  
  I found that passengers without this entry has ticket starting with '113'. The others having this ticket number pattern mostly embarked from port 'S'. They were filled with 'S'.
  
+ Fare

  There is 1 row missing this feature. I used a dataframe groupby function with (pclass, sibsp and parch) (in this order) to calculate the mean, max, median and standard variance. The standard variance is also the same as the mean, which is not a great choice in this case. with this passenger of pclass=3, and 0 for sibsp and parch, I found that the median was quite consistent for the rest of passenger with similar attributes. The three most common fares for these passengers are compared and the range is from 8.05 to 7.75. $8.05 is used to fill in.
  
 + Age
 
   This is a very important feature, an almost deciding factor on whether a passenger survived or not. During a shipwreck, children and ladies were evacuated first before men in their peak. 
   A function fillBlankAge is coded to do this task. The function went through 3 steps to get the age. The dataset is grouped by (pclass, sibsp, parch, sex, age). Since the ages of many passengers were missing and there was a high possibility that certain attributes may not even have a value to use as reference to fill in for passengers missing age.
   First step is to get the most common age with the similar 4 attributes. Second step is to use the average of those ages. If both of the attempts failed then the overall mean age of all passenger is used to fill in.
   
   
   At first step, the most common age of the similar attributes is attempted.
   
+ Name

  Name may seems inappropriate for prediction. But a closer look revealed that they actually have a title to go with name, and the title make this name field potentially useful. After some exploration, I found that all names could be categorized into 12 groups of title. A funtion replace_title is coded to extract the title in the name and a new column 'Title' is created.
  
## Visual
Count of survivors by pclass
![Count of survivors by pclass](https://github.com/cteeeri/kag_titanic/blob/master/countbypclass.png)
![Count of survivors by pclass](https://github.com/cteeeri/kag_titanic/blob/master/countbygender.png)
![Count of survivors by pclass](https://github.com/cteeeri/kag_titanic/blob/master/countbyage.png)
  
## Model 
Features are preprocessed with one hot encoder or sklearn StandardScaler to make them suitable for modeling. 
A random prediction is used as a benchmark model. The accuracy is logged at 0.6.
I then proceed with decision tree, random forest and gradient boosting with sklearn provided ShuffleSplit cross validation. GridSeachCV is also used to fine tune hyperparameters.

Prediction result were submitted to Kaggle for performance evaluation. The score is 0.78947 which is at top 23%.
