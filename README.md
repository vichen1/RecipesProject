# Healthiness Between Recipes 

This is a project for DSC80 at UCSD.

Author: Victor Chen

## Introduction 

Food is a natural neccessity for humans and cooking can be both a hobby and a necessity for health, depending on the perspective and circumstances of the person involved. As a hobby, cooking is an enjoyable and creative activity where people can oftern experiment with different ingredients, creating new recipes. On the other hand, people cook to control their nutrition, making sure they get the right amount of protein, vitamins, and other essential nutrients. According to the Centers for Disease Control and Prevention (CDC), the prevalence of obesity among adults (aged 20 and older) in the United States was 41.9% in 2017-2020. With this information in mind, I wonder how **healthy in nutritional value are the recipes shown on food.com.** I want to use data analysis techniques to compare health scores etc. between all the recipes using the datasets from [food.com](https://www.food.com/).

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