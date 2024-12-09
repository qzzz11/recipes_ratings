# An analysis of the relationship between a recipe's cooking time and its ratings
## Introduction
The dataset used in this project contains recipes and ratings from food.com. The dataset is a subset of the raw data, containing only the recipes and reviews posted since 2008. The question that this project centered around is: What is the relationship between a recipe's cooking time and its ratings? As a foodie, I love to search recipes online and cook according to those recipes at home. Some of those recipes have really high ratings while others do not. I believe people who are passionate about cooking and food would be curious, just as me, about the factors that correlate with a recipe's cooking time. Thus, I decide to choose this topic and use the above dataset for my project. There're 83782 rows in the RAW_recipes dataset, 731927 rows in the RAW_interactions dataset, and 234429 rows in the merged dataset from the two raw dataset. Relevant columns include:
- `'minutes'`: Minutes to prepare recipe; numerical values
- `n_steps`: Number of steps in recipe; all integers
- `rating`: Rating given; contains values 1,2,3,4,5
## Data Cleaning and Exploratory Data Analysis
For data cleaning, I replaced the `0` values with `NA` for `rating` and `minutes` columns. This step makes sure missing values are actually missing in the dataset and will not influence later analysis. For each unique recipe, I added a `average_rating` column that contains the average rating of that recipe. I added another column `hour` that is transformed from the `minute` column so the scope of cooking time data can be larger. Upon exploration of the dataset, I discovered there's one row with extremely large cooking time and the row does not actually represent recipe data, so I dropped it.
- The distribution of the `average_rating` column in the merged dataset is skewed to the left, which means most of the recipes were rated between 4 to 5 stars and only a small number of recipes were rated less than 4 stars.
<iframe
  src="assets/average_ratings_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
- The scatterplot shows that recipes with more stars tend to require less time of cooking. However, this trend is very weak. There're quite a lot of recipes with few stars that have little time of cooking as well as recipes with 4 to 5 stars with greater cooking time.
<iframe
  src="assets/cooking_time_vs_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
- The following grouped table shows the mean of the cooking time of two groups: recipes with less than 4 stars and those with 4 or 5 stars. This table indicates that recipes with less than 4 stars have slightly less cooking time, which means less cooking time may correlate with lower average rating. However, we can't say if this difference is statiscally important by only looking at the table.

| With less than 4 stars | Cooking time (minutes) |
|-------------------------|------------------------|
| False                  | 102.586851            |
| True                   | 96.884257             |
## Assessment of Missingness
I believe the `review` column is NMAR. The reason is that if the user does not like the recipe and they know they would not say good words about the recipe, they may just leave the review blank. I believe the `average_rating` can explain the missingness because low rating means the user does not like the recipe, thereby making the missingness MAR.
To examine the missingness of the `rating` column in the merged dataset, I ran permutation tests on the column to see if its missingness depends on column `minutes`, `n_ingredients`, and `n_steps`. The p-values are 0.062, 0.0, 0.0 respectively. This means the missingness of `rating` depends on `n_ingredients` and `n_steps`, but not `minutes`. Thus, `rating` is MAR depending on `n_ingredients` and `n_steps`, but not on `minutes`. This might indicate that cooking time does not correlate with `average_rating`, but `n_ingredients` and `n_steps` do.
We can see that the two distributions are quite similar. When rating is not missing, `n_steps` is centered around smaller steps while it's more to the right when rating is missing.
<iframe
  src="assets/distribution_missing_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<iframe
  src="assets/distribution_not_missing_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
## Hypothesis Testing
- Null: There is no significant positive relationship between cooking time and the average rating of recipes.
- Alternative: Higher cooking time corresponds to a higher average rating of recipes.
My choice of test statistic is the Pearson correlation. I believe this is a good choice becuase it measures the strength of the linear relationship between two variables. It has a value between -1 to 1, with a value of -1 meaning a total negative linear correlation, 0 being no correlation, and + 1 meaning a total positive correlation. I choose a significance level of 0.05 because it is the mostly commonly used one. The result p-value is 0.4919284282862534. Since it's greater than 0.05, we fail to reject that there is no significant positive relationship between cooking time and the average rating of recipes.
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis

