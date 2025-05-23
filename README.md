# Analyzing the Healthiness Between Recipes 

Author: Victor Chen

## Introduction 

Food is a natural neccessity for humans and cooking can be both a hobby and a necessity for health, depending on the perspective and circumstances of the person involved. As a hobby, cooking is an enjoyable and creative activity where people can oftern experiment with different ingredients, creating new recipes. On the other hand, people cook to control their nutrition, making sure they get the right amount of protein, vitamins, and other essential nutrients. In 2024, the CDC reports that over 40% of U.S. adults are obese. With this information in mind, I wonder how **healthy are the recipes shown on food.com using the nutritioncal facts.** I want to use data analysis techniques to compare health scores etc. between all the recipes using the datasets from [food.com](https://www.food.com/).

There are 2 datasets given on food.com, the first, `'recipe'`, contains 83782 rows and 12 columns:

| Column             | Description | 
| ------------------ | ----------- |
| `'name'`           | Name of Recipe | 
| `'id'`             | ID of Recipe | 
| `'minutes'`        | Time taken to make | 
| `'contributor_id'` | ID of the person who submitted the recipe | 
| `'submitted'`      | Date the recipe was submitted | 
| `'tags'`           | Food.com tags for the recipe | 
| `'nutrition'`      | Nutritional values in the format [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)] | 
| `'n_steps'`        | Number of steps to prepare the recipe| 
| `'steps'`          | Directions for steps in order in text | 
| `'description'`    | Description that the user provided | 
| `'ingredients'`    | List of ingredients needed for the recipe| 
| `'n_ingredients'`  | Number of ingredients | 

The second dataset `'interations` contains 731927 rows and 5 columns. Each row in this dataset holds a review from users on a recipe:

| Column        | Description | 
| `'user_id'`   | ID of the user submitting the review | 
| `'recipe_id'` | ID of a specific recipe | 
| `'date'`      | Date of the review submitted | 
| `'rating'`    | Rating from 1 - 5 given | 
| `'review'`    | Review text | 

With these datasets, **I am analyzing the nutritional values of each recipe and how they impact the healthiness scale.** Since the original datasets do not contain columns for the nutritional facts for each recipe, I separated the values to create new columns in the corresponding dataset. The columns created were `'calories (#)'`, `'total fat (PDV)'`, `'sugar (PDV)'` etc. PDV, percent daily value which indicates how much intake of that nutrient is to your total daily diet. The columns I will be using more closely will be `'calories (#)'`, `'protein (PDV)'`, and `'carbohydrates (PDV)'` to calculate the health score and create insights.

## Data Cleaning and Exploratory Data Analysis

### Cleaning 

1. I start by renaming `'id'` in the `'recipes'` dataframe to `'recipe_id'` to ensure a easier merge since `'id'` in the recipes dataframe is the same as `'recipe_id'` from the `'interactions'` dataframe. I will then perform a left merge from `'recipes'` to `'interactions'` on the `'recipe_id'` column. 

2. Zero would indicate that the recipe was not rated, therefore we would replace 0 with np.nan from the `'ratings'` column since ratings are usually on a scale of 1 to 5.

3. I am computing the `'average_rating'` and adding it as a new column in the dataset. Since there are a large number of individual recipe ratings, calculating the average provides a clearer picture of how each recipe is generally rated. This helps in understanding overall user preferences and the perceived quality of different recipes.

4. The `'nutrition'` column is stored as a string, containing nutritional information for each recipe. To extract meaningful values, I converted the string representation into a list format, allowing me to access specific nutritional metrics. These values—calories, total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)—were then separated into individual columns. These newly created columns will be used to analyze and compare the healthiness of different recipes.

5. Since I am investing the healthiness in the dataset, I created another column `'contains_vegetables'` that uses the `'tags'` column to check if the word "vegetable" is found. I also added the `'health_score'` column using 3 columns- `'protein (PDV)'`, `'calories'`, and `'carbohydrates (PDV)'`. Finally, to determine if the recipe is healthy in this dataset, I used the health score of the recipe compared to the median of the `'health_score'` column. 

  - To calculate the `'health_score'`, (protein / calories) - (carbohydrates / calories)

6. Finally after obtaining new columns, I start dropping columns that are irrelevant to my research. This includes: `'contributor_id'`, `'steps'`, `'description'`,`'user_id'`, `'review'`, `'date'`, `'ingredients'`, `'tags'`, and `'nutrition'`.

The resulting dataframe datatypes are shown as:

| Column             | Description | 
| ------------------ | ----------- |
| `'name'`           | object | 
| `'recipe_id'`      | int64 | 
| `'minutes'`        | int64 | 
| `'submitted'`      | datetime64[ns] | 
| `'n_steps'`        | int64 | 
| `'n_ingredients'`  | int64 |
| `'rating'`         | float64 | 
| `'average_rating'` | float64 |
| `'calories'`       | float64 |
| `'total fat (PDV)'`| float64 |
| `'sugar (PDV)'`    | float64 |
| `'sodium (PDV)'`   | float64 |
| `'protein (PDV)'`  | float64 |
| `'saturated fat (PDV)'` | float64 |
| `'carbohydrates (PDV)'` | float64 |
| `'contains_vegetables'` | bool |
| `'health_score'`        | float64 |
| `'is_healthy'`          | bool |

The cleaned dataframe consists of 234429 rows and 17 columns. Since there are a good amount of columns, I chose columns that are relevant to my research. Here are the first 5 rows of the dataframe:

| name                             |  recipe_id |   minutes |  rating |   average rating |   calories |   protein (PDV) | carbohydrates (PDV) |contains_vegetables   |   health_score | is_healthy |
|:-------------------------------------|-------:|----------:|---------:|-----------------:|---------------:|--------------:|:-------------        | -------------:|
| 1 brownies in the world    best ever | 333281 |        40 |         4.0 |  4.0   |          138.4 |            3.0 | 6.0 | False               |    -0.02  | False |
| 1 in canada chocolate chip cookies   | 453467 |        45 |      5.0    |   5.0  |          595.1 |           13.0 | 26.0 |False               |    -0.02  | False |
| 412 broccoli casserole               | 306168 |        40 |       5.0  |    5.0   |          194.8 |           22.0 | 3.0 |True               |    0.1 | True |
| 412 broccoli casserole               | 306168 |       40  |        5.0 |    5.0   |          194.8 |           22.0 | 3.0 |True                |    0.1  | True |
| 412 broccoli casserole               | 306168 |        40 |         5.0 |   5.0   |         194.8  |           22.0 | 3.0 |True              |    0.1 |  True | 

### Univariate Analysis 

For this analysis, I examined the distribution of calories in a recipe. I first had to filter the dataframe to be less than 5000 calories to remove any outliers. As the plot shows, the distribution is skewed to the right, indicating that most recipes on food.com have a lower calorie content. This plot is also showing a decreasing trend, meaning that as the calorie content increases, there tend to be less recipes. 

<iframe
  src="assets/univariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I have also plotted the distribution of protein content in recipes. For this analysis, I first had to filter out any outliers, so I used 100 grams of protein as a cutoff. As the plot shows, this distribution is skewed to the right, meaning that majority of recipes on food.com have a lower protein content. This suggestst that higher protein recipes are less frequent than recipes with lower protein content. 

<iframe
  src="assets/univariate2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


### Bivariate Analysis 

For this analysis, the scatter plot shows that as protein content (PDV) increases, recipe ratings tend to decrease slightly, indicating a negative relationship. However, the slope appears relatively shallow, suggesting the effect might be small. Most recipes cluster around higher ratings, even when protein content is moderately high.

<iframe
  src="assets/bivariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregations

| contains_vegetable | is_healthy | Avg Calories | Avg Rating |
| False          | False       | 431.83     |      4.68 |
| False          | True        | 459.97     |      4.66 | 
| True           | False       | 298.87     |      4.72 | 
| True           | True        | 358.92     |      4.71 |

This grouped table shows that recipes with vegetables tend to have lower calories and slightly higher ratings compared to those without vegetables. Interestingly, "healthy" recipes without vegetables have higher average calories than unhealthy ones, suggesting that the "healthy" label may depend on factors beyond just calorie count. This analysis helps identify patterns in recipe nutrition and user preferences, offering insights for healthier meal choices.

## NMAR Analysis

I believe that the rating column in the dataset is NMAR (Not Missing At Random). The missingness in this column is likely due to the fact that some recipes were never rated by users, which is directly related to the missing values themselves. If a recipe is new, unpopular, or rarely viewed, it is more likely to have a missing rating, meaning the reason for missingness is not random but instead depends on user engagement. This makes it NMAR, as the missing data is influenced by an unobserved factor—whether or not users chose to rate the recipe.

To better understand this missingness and potentially classify it as MAR (Missing At Random) instead of NMAR, I would want to obtain additional data such as the number of views or interactions each recipe has received. If I find that recipes with fewer views tend to have missing ratings, then the missingness could be explained by another observed variable (recipe popularity), making it MAR rather than NMAR.

### Missing Dependency

For this part, I am going to test if the missingness of `'rating'` column depends on other columns. The two columns that I am using are `'submitted'` for the month and `'minutes'`. The significance level that I chose for both tests is **0.05**, and the test statistic is the absolute difference in means and the difference in means. 

First, I am performing a permutation test on `'rating'` and `'submitted'`.

**Null Hypothesis**: The missingness of `'rating'` is independent of the month the recipe was `'submitted'`. 

**Alternative Hypothesis**: The missingness of `'rating'` depends on the month the recipe was `'submitted'`.

**Test Statistic**: Absolute difference in mean submission month between recipes with missing ratings and those with non-missing ratings

<iframe
  src="assets/nmar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

After performing permutation tests, I found that the **observed statistic** (vertical line) for this test to be 0.0849 and a **p-value** of 0.024. Since the p-value is less than the 0.05 significance level, I reject the null hypothesis. Therefore, the missingness of `'rating'` depends on the `month` the recipe was submitted.

Now I will perform a premutation test on `'rating'` and `'minutes'`.

**Null Hypothesis**: The missingness of `'ratings'` is independent of cooking time (`'minutes'`). 

**Alternative Hypothesis**: The missingness of `'ratings'` depends on cooking time (`'minutes'`).

**Test Statistic**: Difference in mean cooking time (`'minutes'`) between recipes with missing ratings and those with non-missing ratings

<iframe
  src="assets/nmar2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The **observed statistic** is 51.4524 (vertical line) and the **p-value** is 0.202 which is more than the 0.05 significance level. Since the p-value is more than the significance level, I fail to reject the null hypothesis. Therefore, the missingness of `'rating'` does not depend on the `'minutes'` column.

## Hypothesis Testing

In this hypothesis test, I will be testing if there is a a difference in health scores over recipes that contain vegetebles and no vegetables. The columns needed for this investigation will be `'contains_vegetables'` and `'health_score'`.

**Null Hypothesis**: There is no difference in the average health score between recipes that contain vegetables and recipes that do not contain vegetables.

**Alternative Hypothesis**: There is a difference in the average health score between recipes that contain vegetables and recipes that do not contain vegetables.

**Test Statistic**: Difference in mean health scores between recipes with vegetables and non-vegetables.

Here is the histogram containing the distribution of health scores between vegetables and non-vegetables using a premutation test, shuffling the health scores from both groups.

<iframe
  src="assets/hypothesis_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From this distribution, we have an **observed statistic** of -0.00534 and a **p-value** of 0.0, this is lower than the significance level of 0.05, meaning that we **reject** the null hypothesis. This suggests that there is a statistically significant difference in the average health scores between recipes that contain vegetables and those that do not. Since the observed difference is negative (-0.00534), it indicates that recipes without vegetables tend to have a slightly higher health score compared to those with vegetables.

## Framing a Prediction Problem

From the last section, I found out that having vegetables in your recipe may have an impact on the health score. Since I only investigated the healthiness of vegetables, do other nutritional facts affect the health score of a recipe?

The prediction problem I am addressing is a binary classification task, where the goal is to predict whether a recipe is healthy or not based on its nutritional content. The response variable is `'is_healthy'`, which is a binary label indicating whether a recipe meets certain health criteria. I chose this variable because it provides valuable insights into how different nutritional facts contribute to a recipe's overall healthiness. To evaluate the model, I am using F1-score in addition to accuracy, as F1-score balances precision, making it more reliable in cases where the dataset may be imbalanced. 

Lastly, all features used in training, such as calories, protein, and carbohydrate content, would be available at the time of prediction, ensuring the model makes decisions based on realistic and accessible data.

## Baseline Model

In this model, I built a Decision Tree Classifier to predict whether a recipe is healthy based on the nutritional content. The features I selected-`'calories'` and `'carbohydrates (PDV)'`- I beleive these nutritional facts are very important to understand to operate throughout the day. To ensure that these features were comparable, I applied StandardScaler to normalize them. Then, I split the dataset into training and testing sets so the model could learn from a portion of the data and be evaluated on unseen data. I also limited the `max_depth` of the Decision Tree to 5 to prevent the model from overfitting and memorizing the training data rather than learning general patterns.

The model achieved an accuracy of **0.7525**, meaning that nearly 75% of recipes were correctly classified as either healthy or unhealthy. Additionally, the F1-score of **0.7591** indicates a okay balance between precision and recall, ensuring that the model is not biased toward either false positives (incorrectly classifying unhealthy recipes as healthy) or false negatives (missing actual healthy recipes). These high scores suggest that the Decision Tree Classifier is effectively identifying patterns in the nutritional features to determine healthiness. Even though, the scores for this model is mediocre, our model still has a good amount of space for improvement. I will be adding more features, and tuning hyperparameters for the Final Model.

## Final Model

For the final model, I added 5 more features: `'protein (PDV)'`, `'total fat (PDV)'`, `'sodium (PDV)'`, `'sugar (PDV)'`, and `'saturated fat (PDV)'` to the 2 we had-`'calories'` and `'carbohydrates (PDV)'`. I chose the other 5 because I believe there are negatives and positives that these nutrional facts output. For example, protein is a positive since this nutrient is needed for recovery and muscle growth. While a negative like `'saturated fat (PDV)'`, can lead to the increase in heart diseases. These features added will make my final model more accurate since I am calculating both the negatives and positives together. 

The model that I am using is a Random Forest Classifier which is trained to predict whether a recipe is healthy based on nutritional facts. It uses 7 features stated before which are scaled for consistency. A GridSearchCV to find-tune hyperparameters: The `n_estimators` parameter controls the number of trees in the forest, with values of 50 and 100 being tested to balance performance and efficiency. The `max_depth` parameter limits how deep each tree can grow, with values of 5 and 10 helping to prevent overfitting. The `min_samples_split` parameter determines the minimum number of samples needed to split a node, with values of 2, 5, and 10 being tested to ensure the model generalizes well. Lastly, the `criterion` parameter tests both "gini" and "entropy" to determine the best method for measuring the quality of splits in the decision trees.

The accuracy score is now **0.991788**, meaning 99.18% of the time this model was able to predict the recipe is healthy which is a huge improvement from our baseline model (0.7525). The F1-score has also improved being **0.991785** from our baseline model (0.7591), since our F1-score is super high, we can ensure that this final model balances precision and recall, ensuring high accuracy without bias towards false positives or false negatives. Since these are huge improvements, this suggests that our adjustment to the final model is effiency for our predictions.  

## Fairness Analysis

For this fairness analysis, I compared the model's performance between recipes that contain vegetables and recipes that do not contain vegetables. I will be using the F1-score to ensure a fair assessment of model performance across both groups.

The groups that I decided to evaluate are recipes that contain vegetables vs. no vegetables because vegetables are often associated with healthier food choices, and wanted to see if this model's performance is based on this key ingredient.

**Null Hypothesis**: The model is fair. The F1-score for recipes with vegetables and those without vegetables are roughly the same, and any difference is due to random chance.

**Alternative Hypothesis**: The model is unfair. The F1-score for recipes with vegetables is lower than for those without vegetables.

<iframe
  src="assets/fairness_analysis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

After performing a permutation test with 10000 trials, the p-value I got is **0.2403** and since this value is greater than the 0.05 significance level, we **fail to reject** the null hypothesis. This suggest there is no significant difference in F1-scores between the two groups, the model performs fairly across recipes with and without vegetables.

