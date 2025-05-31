# Nutrigression : Recipe Reviews and Nutrition Analysis
Author: Munachimso Ndubisi

## Introduction
This project aims to understand the relationship between the nutritional components (in this case: calories, fat, sugar, protein, saturated fat and carbohydrates) and their average ratings given by real users. I am working with two relevant datasets with data from the [food](https://www.food.com/) website: 

1. `recipes`  Which has the following columns:
| Column            | Description                                                                                                                        |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `name`            | Recipe name                                                                                                                        |
| `id`              | Recipe ID                                                                                                                          |
| `minutes`         | Minutes to prepare recipe                                                                                                           |
| `contributor_id`  | User ID who submitted this recipe                                                                                                   |
| `submitted`       | Date recipe was submitted                                                                                                           |
| `tags`            | Food.com tags for recipe                                                                                                            |
| `nutrition`       | Nutrition information in the form `[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]`; PDV stands for “percentage of daily value” |
| `n_steps`         | Number of steps in recipe                                                                                                           |
| `steps`           | Text for recipe steps, in order                                                                                                     |
| `description`     | User-provided description   

2. `interactions` (contains one row per user‐recipe interaction, including a star rating between 1–5 and a `recipe_id` that links back to `RAW_recipes.csv`).

I've merged these 

This data







