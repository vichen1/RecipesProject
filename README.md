# Healthiness Between Recipes 

This is a project for DSC80 at UCSD.

Author: Victor Chen

## Introduction 

Food is a natural neccessity for humans and cooking can be both a hobby and a necessity for health, depending on the perspective and circumstances of the person involved. As a hobby, cooking is an enjoyable and creative activity where people can oftern experiment with different ingredients, creating new recipes. On the other hand, people cook to control their nutrition, making sure they get the right amount of protein, vitamins, and other essential nutrients. According to the Centers for Disease Control and Prevention (CDC), the prevalence of obesity among adults (aged 20 and older) in the United States was 41.9% in 2017-2020. With this information in mind, I wonder how **healthy in nutritional value are the recipes shown on food.com.** I want to use data analysis techniques to compare health scores etc. between all the recipes using the datasets from [food.com](https://www.food.com/).

There are 2 datasets given on food.com, the first, 'recipe', contains 83782 rows and 12 columns:

| Column             | Description | 
| ------------------ | ----------- |
| `'name'`           | Name of Recipe | 
| `'recipe_id'`      | ID of Recipe | 
| `'minutes'`        | Time taken to make | 
| `contributor_id`   | ID of the person who submitted the recipe | 
| `'submitted'`      | Date the recipe was submitted | 
| `tags`             | Food.com tags for the recipe | 
| `'nutrition'`      | Nutritional values in the format [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)] | 
| `'n_steps'`        | Number of steps to prepare the recipe| 
| `'steps'`          | Directions for steps in order in text | 
| `'description'`   | Description that the user provided | 
| `'ingredients'`  | List of ingredients needed for the recipe| 
| `'n_ingredients'`  | Number of ingredients | 