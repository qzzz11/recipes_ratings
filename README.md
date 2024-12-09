# An analysis of the relationship between a recipe's cooking time and its ratings
### Author: Qingtong Zou
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
- My choice of test statistic is the Pearson correlation. I believe this is a good choice becuase it measures the strength of the linear relationship between two variables. It has a value between -1 to 1, with a value of -1 meaning a total negative linear correlation, 0 being no correlation, and + 1 meaning a total positive correlation. I choose a significance level of 0.05 because it is the mostly commonly used one. The result p-value is 0.4919284282862534. Since it's greater than 0.05, we fail to reject that there is no significant positive relationship between cooking time and the average rating of recipes.
## Framing a Prediction Problem
My prediction problem is that I want to predict the average rating of a recipe. This is a regression problem. The response variable is the `average_rating`. I chose it because as I stated in the introduction, I believe people who are interested in cooking would be curious about the factors that will impact the ratings of a recipe. This value reflects the features of a recipe that people care about and how those features influence people's opinions on the recipe. Thus, I believe this is an important prediction problem. I chose mse as one of the metrics because it quantifies the average squared difference between the predicted and actual values. MSE directly captures the magnitude of prediction errors, with larger errors contributing more due to squaring. The squaring of errors also makes MSE sensitive to outliers, which can be critical when detecting where the model struggles. I chose R2 because it represents the proportion of variance in the target variable that the model explains. It provides a standardized measure (from 0 to 1) of how well the model explains the variability in the data. R2 also can show if the model performs better than a naive baseline that predicts the mean of the target variable.
## Baseline Model
For the baseline model, I used two features in the dataset: `n_ingredients` and `n_steps`. Both two are quantitative features. `n_ingredients` represents the number of ingredients in a recipe and `n_steps` is the number of steps required in a recipe. The model is a Linear Regression model built using these two features. A pipeline is used to standardize the data with StandardScalar before fitting the model. This ensures that the features are scaled to have a mean of 0 and a standard deviation of 1, which is essential for linear regression to perform optimally. The MSE is 0.23937523853919804, which suggests the typical squared error magnitude but doesn’t give direct interpretability in terms of the original scale. The R2 score is -4.0397468397124214e-06, which implies that the model performs worse than a baseline that predicts the mean of the target. The model is not good because the model does not explain any variance in `average_rating`. It also fails to capture meaningful relationships between the input features and the target.
## Final Model
For the final model, I added two features: `log_minutes` and `interaction_feature`.
- log_minutes: Cooking time (minutes) likely has a diminishing effect on recipe ratings. For example, very short cooking times may limit recipe quality, while very long times might not always lead to higher satisfaction. A log transformation captures these diminishing returns by emphasizing small differences at lower values and compressing the impact of large values.
- interaction_feature: Interaction between `n_ingredients` and `n_steps`. Recipes with more steps might be more complex, while those with more ingredients might be more diverse or flavorful. Their interaction could capture the synergy between complexity and diversity, which might jointly influence ratings.
The chosen modeling algorithm for the final model is Random Forest Regressor. Hyperparameters tuned:
1. Number of Trees (`n_estimators`): Controls the number of decision trees in the Random Forest. Best value: 200
2. Maximum Depth (`max_depth`): Limits the depth of each tree to prevent overfitting. Best value: 20
3. Minimum Samples Split (`min_samples_split`): The minimum number of samples required to split a node. Best value: 2

GridSearchCV was used for hyperparameter tuning. It systematically tested all combinations of the specified parameter grid:
- n_estimators: [50, 100, 200]
- max_depth: [None, 10, 20, 30]
- min_samples_split: [2, 5, 10]
- Validation Method: 3-fold cross-validation
- Scoring Metric: Negative Mean Squared Error (minimizing the error in predictions).

The optimal hyperparameters were selected based on the configuration that minimized the validation error.
The new MSE is 0.21582940311488982, which is lower than the MSE of the baseline model, indicating better predictive accuracy. The R2 score also improved to 0.11173996250983265, meaning the model now explains approximately 11.2% of the variance in the target variable. While this is still modest, it is a meaningful improvement over the baseline, which explained none of the variance. Overall, the final model has better performance than the baseline model.
## Fairness Analysis
For the fairness analysis, I split the data into two groups: recipes with less than 5 ingredients and recipes with 5 or more ingredients. My evaluation is RMSE. My hypothesis are:
- Null Hypothesis: Our model is fair. Its rmse for recipes with less than 5 ingredients and those with equal to or more than 5 ingredients are roughly the same, and any differences are due to random chance.
- Alternative Hypothesis: Our model is unfair. Its rmse for recipes with less than 5 ingredients is lower than its rmse for those with 5 or more ingredients.

My choice of test statistic is the difference in RMSE and significance level is 0.05. The resulting p-value is 0.753, which is greater than 0.05, so we conclude that we fail to reject that our model is fair. Its rmse for recipes with less than 5 ingredients and those with equal to or more than 5 ingredients are roughly the same, and any differences are due to random chance.

