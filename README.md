# Nutrigression : Recipe Reviews and Nutrition Analysis
Author: Munachimso Ndubisi

## Introduction
In recent times, tons of people, specifically young people have been trying to switch to healthier eating habits. The idea of fitness and nutrition culture have expoloded all over the internet, across various platforms. These young people and other fitness enthusiasts often rely on recipes they find on the internet to get their 'protein' levels up. But we all know that although healthy eating seems to be trending now, a lot of people would most likely rather eat something majorly 'unhealthy'. While a meal being healthy definitely does not discredit its flavor profile, there is a fight between eating healthy and eating flavorful meals. This project aims to understand the relationship between the nutritional components (in this case: calories, fat, sugar, protein, saturated fat and carbohydrates) and their average ratings given by real users. The central question of the project being:

> What different nutritional components have an effect on rating?, What nutritional profiles have the best rating on average - foods deemed as healthy or more of those considered 'junk'

This is relevant to discover, to some extent, the extent of the tradeoffs between health and taste.
I've also seen a recent spike in companies that are focused on providing prepped meals such as [FactorMeals](https://www.factormeals.com/), [HelloFresh](https://www.hellofresh.com/) and many others. This analysis could also provide insights for them into what nutritional components are valued by consumers in order to facilitate modification of meal kits to increase sales


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





I've merged these 

This data







