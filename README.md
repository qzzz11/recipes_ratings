# An analysis of the relationship between a recipe's cooking time and its ratings
## Introduction
The dataset used in this project contains recipes and ratings from food.com. The dataset is a subset of the raw data, containing only the recipes and reviews posted since 2008. The question that this project centered around is: What is the relationship between a recipe's cooking time and its ratings? As a foodie, I love to search recipes online and cook according to those recipes at home. Some of those recipes have really high ratings while others do not. I believe people who are passionate about cooking and food would be curious, just as me, about the factors that correlate with a recipe's cooking time. Thus, I decide to choose this topic and use the above dataset for my project. There're 83782 rows in the RAW_recipes dataset, 731927 rows in the RAW_interactions dataset, and 234429 rows in the merged dataset from the two raw dataset. Relevant columns include:
- `'minutes'`: Minutes to prepare recipe; numerical values
- `n_steps`: Number of steps in recipe; all integers
- `rating`: Rating given; contains values 1,2,3,4,5
## Data Cleaning and Exploratory Data Analysis
For data cleaning, I replaced the `0` values with `NA` for `rating` and `minutes` columns. This step makes sure missing values are actually missing in the dataset and will not influence later analysis. For each unique recipe, I added a `average_rating` column that contains the average rating of that recipe. I added another column `hour` that is transformed from the `minute` column so the scope of cooking time data can be larger. Upon exploration of the dataset, I discovered there's one row with extremely large cooking time and the row does not actually represent recipe data, so I dropped it.
<iframe
  src="assets/average_ratings_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Assessment of Missingness
## Hypothesis Testing
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis

