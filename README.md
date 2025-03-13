# Healthiness Between Recipes 

This is a project for DSC80 at UCSD.

Author: Victor Chen

## Introduction 

Food is a natural neccessity for humans and cooking can be both a hobby and a necessity for health, depending on the perspective and circumstances of the person involved. As a hobby, cooking is an enjoyable and creative activity where people can oftern experiment with different ingredients, creating new recipes. On the other hand, people cook to control their nutrition, making sure they get the right amount of protein, vitamins, and other essential nutrients. According to the Centers for Disease Control and Prevention (CDC), the prevalence of obesity among adults (aged 20 and older) in the United States was 41.9% in 2017-2020. With this information in mind, I wonder how **healthy shown from nutritional facts are the recipes shown on food.com.** I want to use data analysis techniques to compare health scores etc. between all the recipes using the datasets from [food.com](https://www.food.com/).

There are 2 datasets given on food.com, the first, `'recipe'`, contains 83782 rows and 12 columns:

| Column             | Description | 
| ------------------ | ----------- |
| `'name'`           | Name of Recipe | 
| `'id'`      | ID of Recipe | 
| `'minutes'`        | Time taken to make | 
| `'contributor_id'`   | ID of the person who submitted the recipe | 
| `'submitted'`      | Date the recipe was submitted | 
| `'tags'`             | Food.com tags for the recipe | 
| `'nutrition'`      | Nutritional values in the format [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)] | 
| `'n_steps'`        | Number of steps to prepare the recipe| 
| `'steps'`          | Directions for steps in order in text | 
| `'description'`   | Description that the user provided | 
| `'ingredients'`  | List of ingredients needed for the recipe| 
| `'n_ingredients'`  | Number of ingredients | 

The second dataset `'interations` contains 731927 rows and 5 columns. Each row in this dataset holds a review from users on a recipe:

| Column     | Description | 
| `'user_id'` | ID of the user submitting the review | 
| `'recipe_id'` | ID of a specific recipe | 
| `'date'` | Date of the review submitted | 
| `'rating'` | Rating from 1 - 5 given | 
| `'review'` | Review text | 

With these datasets, **I am analyzing the nutritional values of each recipe and how they impact the healthiness scale.** Since the original datasets do not contain columns for the nutritional facts for each recipe, I separated the values to create new columns in the corresponding dataset. The columns created were `'calories (#)'`, `'total fat (PDV)'`, `'sugar (PDV)'` etc. PDV, percent daily value which indicates how much intake of that nutrient is to your total daily diet. The columns I will be using more closely will be `'calories (#)'`, `'protein (PDV)'`, and `'carbohydrates (PDV)'` to calculate the health score and create insights.

## Data Cleaning and Exploratory Data Analysis

### Cleaning 

1. I start by renaming `'id` in the `'recipes'` dataframe to `'recipe_id'` to ensure a easier merge since `'id'` in the recipes dataframe is the same as `'recipe_id'` from the `'interactions'` dataframe. I will then perform a left merge from `'recipes'` to `'interactions'` on the `'recipe_id'` column. 

2. Zero would indicate that the recipe was not rated, therefore we would replace 0 with np.nan from the `'ratings'` column since ratings are usually on a scale of 1 to 5.

3. I am computing the `'average_rating'` and adding it as a new column in the dataset. Since there are a large number of individual recipe ratings, calculating the average provides a clearer picture of how each recipe is generally rated. This helps in understanding overall user preferences and the perceived quality of different recipes.

4. The `'nutrition'` column is stored as a string, containing nutritional information for each recipe. To extract meaningful values, I converted the string representation into a list format, allowing me to access specific nutritional metrics. These values—calories, total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)—were then separated into individual columns. These newly created columns will be used to analyze and compare the healthiness of different recipes.

5. Since I am investing the healthiness in the dataset, I created another column `'contains_vegetables'` that uses the `'tags'` column to check if the word "vegetable" is found. I also added the `'health_score'` column using 3 columns- `'protein (PDV)'`, `'calories'`, and `'carbohydrates (PDV)'`. Finally, to determine if the recipe is healthy in this dataset, I used the health score of the recipe compared to the median of the `'health_score'` column. 

6. Finally after obtaining new columns, I start dropping columns that are irrelevant to my research. This includes: `'contributor_id'`, `'submitted'`, `'steps'`, `'description'`,`'user_id'`, `'review'`, `'date'`, `'ingredients'`, `'tags'`, and `'nutrition'`.

The resulting dataframe datatypes are shown as:

| Column             | Description | 
| ------------------ | ----------- |
| `'name'`           | object | 
| `'recipe_id'` | int64 | 
| `'minutes'`        | int64 | 
| `'n_steps'`        | int64 | 
| `'n_ingredients'`  | int64 |
| `'rating'` | float64 | 
| `'average_rating'` | float64 |
| `'calories'` | float64 |
| `'total fat (PDV)'` | float64 |
| `'sugar (PDV)'` | float64 |
| `'sodium (PDV)'` | float64 |
| `'protein (PDV)'` | float64 |
| `'saturated fat (PDV)'` | float64 |
| `'carbohydrates (PDV)'` | float64 |
| `'contains_vegetables'` | bool |
| `'health_score'` | float64 |
| `'is_healthy'` | bool |

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

I have also plotted the distribution of protein content in recipes. 

<iframe
  src="assets/univariate2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I first had to filter out any outliers, so I used 100 grams of protein as a cutoff. As the plot shows, this distribution is skewed to the right, meaning that majority of recipes on food.com have a lower protein content. This suggestst that higher protein recipes are less frequent than recipes with lower protein content. 

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