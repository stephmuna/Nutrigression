# Nutrigression: Recipe Reviews and Nutrition Analysis 🍗🥚🥜
Author: Munachimso Ndubisi

## Introduction
In recent times, many people, particularly young individuals, have been attempting to adopt healthier eating habits. The concept of fitness and nutrition culture has gained significant traction across various internet platforms. These young people and other fitness enthusiasts often rely on recipes they find on the internet to get their 'protein' levels up. However, we all know that although healthy eating seems to be trending now, many people would likely prefer to eat something significantly 'unhealthy'. While a meal being healthy definitely does not discredit its flavor profile, there is a conflict between eating healthily and enjoying flavorful meals. This project aims to understand the relationship between the nutritional components (in this case: calories, fat, sugar, protein, saturated fat, and carbohydrates) and their average ratings given by real users. The central question of the project is:

> What different nutritional components have an effect on rating?, What nutritional profiles have the best rating on average - foods deemed as healthy, or more of those considered 'junk'

This is relevant to discover, to some extent, the extent of the tradeoffs between health and taste.
I've also seen a recent spike in companies focused on providing prepared meals, such as [FactorMeals](https://www.factormeals.com/), [HelloFresh](https://www.hellofresh.com/), and many others. This analysis could also provide insights for them into the nutritional components valued by consumers, facilitating the modification of their meal kits to increase sales.


I am working with two relevant datasets with data from the [food](https://www.food.com/) website: 

1. `recipes`: This dataset consists of 83782 rows containing data corresponding to 83782 different recipes and 12 columns described below:

| Column           | Description                                                                                                                                                    |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`           | Recipe name                                                                                                                                                    |
| `id`             | Recipe ID                                                                                                                                                      |
| `minutes`        | Minutes to prepare recipe                                                                                                                                     |
| `contributor_id` | User ID who submitted this recipe                                                                                                                             |
| `submitted`      | Date recipe was submitted                                                                                                                                     |
| `tags`           | Food.com tags for recipe                                                                                                                                      |
| `nutrition`      | Nutrition info in the form `[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]`; PDV = “percent of daily value” |
| `n_steps`        | Number of steps in recipe                                                                                                                                     |
| `steps`          | Text for recipe steps, in order                                                                                                                               |
| `description`    | User-provided description                                                                                                                                     |



2. `interactions`: This dataset consists of 731927 rows each corresponding to a review given by a real user and 5 rows described below:

| Column      | Description       |
| ----------- | ----------------- |
| `user_id`   | User ID           |
| `recipe_id` | Recipe ID         |
| `date`      | Date of interaction |
| `rating`    | Rating given      |
| `review`    | Review text       |

These datasets would be merged and aggregated in order to work more efficiently with them. I will also need to have a seperate column for each nutritional component as that is the primary focus of my question.

----------------

## Data Cleaning and Exploratory Data Analysis

I conducted the following steps to allow for a smoother analysis process:













I've merged these two datasets and grouped the columns by their `recipe_id`  to produce the final dataset `recipes_with_avg` which contains the following columns:

| Column           | Description                                                                                                                                                                       |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`           | The title of the recipe (e.g., “Classic Chicken Alfredo”).                                                                                                                        |
| `id`             | Unique identifier for each recipe (matches `id` in the original `RAW_recipes.csv`).                                                                                                |
| `minutes`        | Estimated time (in minutes) required to prepare and cook the recipe.                                                                                                              |
| `contributor_id` | User ID of the individual who submitted or authored the recipe.                                                                                                                   |
| `submitted`      | Date when the recipe was originally submitted to the platform (formatted as YYYY-MM-DD).                                                                                           |
| `tags`           | Comma- or pipe-separated list of Food.com tags associated with the recipe (e.g., “easy”, “dinner”, “vegetarian”, “gluten-free”).                                                  |
| `nutrition`      | Original nutrition string, e.g. `"250 calories | 15% DV fat | 10% DV sugar | 8% DV sodium | 20% DV protein | 5% DV saturated fat | 30% DV carbohydrates"`. Parsed later into numeric fields. |
| `n_steps`        | Number of instructional steps in the recipe (the count of individual directions).                                                                                                 |
| `steps`          | Full text of each cooking step, concatenated in order (e.g., “1. Preheat oven to 375°F. 2. Season chicken… 3. Bake for 25 minutes…”).                                              |
| `description`    | User-provided description or summary of the recipe (often includes serving suggestions or background).                                                                             |
| `ingredients`    | List (or string) of ingredients required for the recipe (e.g., `"2 cups flour, 1 cup sugar, 3 eggs, ½ cup milk"`).                                                                 |
| `n_ingredients`  | Number of individual ingredients in the recipe (integer count).                                                                                                                   |
| `avg_rating`     | The mean star rating (floating point) for each recipe, computed by averaging all user ratings from `interactions.csv` (range 1.0–5.0).                                            |


This dataset would be used at different levels of the project








