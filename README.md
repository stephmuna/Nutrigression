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

1. First, I performed a left merge between recipes and interactions so that every recipe appears,
even if it has no corresponding user rating yet.


2. In the raw interactions file, a “rating” of 0 might signify “no rating given,” as it is not possible to give a rating of 0 on any platform. Due to this, I replaced all O's in the `rating` column with `np.nan`


3. I then grouped the resulting dataset by the recipe’s `id`, then compute the mean of the `rating` column. This produced a Series named “avg_rating” whose index is the recipe id.


4. Following this, I merged the average‐rating Series back into the original recipes DataFrame using a left merge so that recipes with no ratings receive a NaN for avg_rating.


5. After this, I converted all `nutrition` columns into six separate numeric columns: `calories`, `fat_PDV`, `sugar_PDV`, `sodium_PDV`,
       `protein_PDV`, `saturated_fat_PDV`, `carbohydrates_PDV`, each corresponding to an entry in the original `nutrition`, so that later on I could:  
   - Compute descriptive statistics easily.  
   - Efficiently run correlation tests.  
   - Fit regression models in scikit-learn.

6. I dropped any recipe with missing nutrient values, ensuring that no `NaN` values would break our t-tests or model-fitting routines. This was done to simplify our code and did not materially affect the results.

7. Left `avg_rating` and textual fields untouched**, so we still have full context for each recipe .

8. Lastly, I dropped the `ingredients`, `steps`,`description` and `tags` columns as these are very text-heavy columns and not relevant in my analysis, seeing as we already have the nutritional contents of the dish. Though the `tags` column provides some significant insight I removed it because they are not relevant in the sense of my specific question and some information from them can be gotten from other columns in the dataset.

By the end of these cleaning steps, we have a fully numeric, complete dataset that can be fed directly into visualizations, statistics, and predictive models—maximizing reproducibility and ensuring no hidden `NaN` values distort our findings. The final dataframe has the following columns:


| Column               | Description                                                                                                                       |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `name`               | The title of the recipe                                                                                                           |
| `id`                 | Unique identifier for each recipe                                                                                                  |
| `minutes`            | Estimated time (in minutes) required to prepare and cook the recipe                                                               |
| `contributor_id`     | User ID of the individual who submitted or authored the recipe                                                                     |
| `submitted`          | Date when the recipe was originally submitted to the platform (YYYY-MM-DD)                                                         |
| `n_steps`            | Number of instructional steps in the recipe                                                                                        |
| `n_ingredients`      | Number of individual ingredients in the recipe                                                                                      |
| `avg_rating`         | Mean star rating for each recipe, computed by averaging all user ratings; NaN if no ratings exist                                  |
| `calories`           | Number of calories per serving (parsed from the nutrition information)                                                              |
| `fat_PDV`            | Percent Daily Value of total fat (parsed from the nutrition information)                                                            |
| `sugar_PDV`          | Percent Daily Value of sugar (parsed from the nutrition information)                                                                |
| `sodium_PDV`         | Percent Daily Value of sodium (parsed from the nutrition information)                                                               |
| `protein_PDV`        | Percent Daily Value of protein (parsed from the nutrition information)                                                              |
| `saturated_fat_PDV`  | Percent Daily Value of saturated fat (parsed from the nutrition information)                                                        |
| `carbohydrates_PDV`  | Percent Daily Value of carbohydrates (parsed from the nutrition information)                                                        |



This dataset consists of 83781 and 15 columns, with the head of the dataset displayed below:

| name                              | id     | minutes | contributor_id | submitted   | n_steps | n_ingredients | avg_rating | calories | fat_PDV | sugar_PDV | sodium_PDV | protein_PDV | saturated_fat_PDV | carbohydrates_PDV |
| --------------------------------- | ------ | ------- | -------------- | ----------- | ------- | ------------- | ---------- | -------- | ------- | --------- | ---------- | ----------- | ----------------- | ----------------- |
| 1 brownies in the world best ever | 333281 | 40      | 985201         | 2008-10-27  | 10      | 9             | 4.0        | 138.4    | 10.0    | 50.0      | 3.0        | 3.0         | 19.0              | 6.0               |
| 1 in canada chocolate chip cookies| 453467 | 45      | 1848091        | 2011-04-11  | 12      | 11            | 5.0        | 595.1    | 46.0    | 211.0     | 22.0       | 13.0        | 51.0              | 26.0              |
| 412 broccoli casserole            | 306168 | 40      | 50969          | 2008-05-30  | 6       | 9             | 5.0        | 194.8    | 20.0    | 6.0       | 32.0       | 22.0        | 36.0              | 3.0               |
| millionaire pound cake            | 286009 | 120     | 461724         | 2008-02-12  | 7       | 7             | 5.0        | 878.3    | 63.0    | 326.0     | 13.0       | 20.0        | 123.0             | 39.0              |
| 2000 meatloaf                     | 475785 | 90      | 2202916        | 2012-03-06  | 17      | 13            | 5.0        | 267.0    | 30.0    | 12.0      | 12.0       | 29.0        | 48.0              | 2.0               |






                                        










