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


6. Consequently, I dropped the `ingredients`, `steps`,`description` and `tags` columns as these are very text-heavy columns and not relevant in my analysis, seeing as we already have the nutritional contents of the dish. Though the `tags` column provides some significant insight I removed it because they are not relevant in the sense of my specific question and some information from them can be gotten from other columns in the dataset.

7. Finally, I converted the `submitted` column to datetime obejct to enable efficient analysis

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

| name                                 |     id |   minutes |   contributor_id | submitted   |   n_steps |   n_ingredients |   avg_rating |   calories |   fat_PDV |   sugar_PDV |   sodium_PDV |   protein_PDV |   saturated_fat_PDV |   carbohydrates_PDV |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|----------:|----------------:|-------------:|-----------:|----------:|------------:|-------------:|--------------:|--------------------:|--------------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  |        10 |               9 |            4 |      138.4 |        10 |          50 |            3 |             3 |                  19 |                   6 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  |        12 |              11 |            5 |      595.1 |        46 |         211 |           22 |            13 |                  51 |                  26 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  |         6 |               9 |            5 |      194.8 |        20 |           6 |           32 |            22 |                  36 |                   3 |
| millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  |         7 |               7 |            5 |      878.3 |        63 |         326 |           13 |            20 |                 123 |                  39 |
| 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  |        17 |              13 |            5 |      267   |        30 |          12 |           12 |            29 |                  48 |                   2 |



### Univariate Analysis
I decided to examine the distribution of ratings in the dataset just to get a feel of the general spread of the data

<iframe
  src="assets/rating_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


                                        
### Interesting Aggregates

I decided to examine how ratings differ at different levels of protein( % DV) and calories. I prepared a pivot table containing `avg_rating` at different intersections of protein and calorie levels.

| calorie_bucket   |   protein Q1 (Low) |   protein Q2 |   protein Q3 |    protein Q4 (High) |
|:-----------------|-------------------:|-------------:|-------------:|---------------------:|
| LowCal           |            4.65127 |      4.61956 |      4.59528 |              4.61022 |
| MedCal           |            4.65329 |      4.63716 |      4.62176 |              4.60198 |
| HighCal          |            4.62656 |      4.62643 |      4.65185 |              4.61192 |

From this aggregation, we can see that there is not a significant difference in ratings at any extremes, but recipes with medium calorie level and low protein levels have the highest average ratings. On the other hand, recipes with low calorie levels and moderate protein levels have the lowest avergare ratings.

I also decided to examine how the ratings differ at different recipy complexities. In this case I will use the Number of steps (`n_steps`) and the number of ingredients (`n_ingredients`) the recipe requires to determine the complexity.

| steps_bin   |   Few Ingredients |   Medium Ingredients |   Many Ingredients |
|:------------|------------------:|---------------------:|-------------------:|
| Few Steps   |           4.64488 |              4.60263 |            4.63114 |
| Med Steps   |           4.62711 |              4.60662 |            4.62098 |
| Many Steps  |           4.62545 |              4.62804 |            4.63069 |

There is also no significance in average ratings at different complexities here, but we can see that less complex recipes (recipes with few ingredients and steps) have the highest average rating.



## Assesment of Missingness 

### NMAR Ananlysis
The only column with significant amount of missing values is the avg_rating column. This average rating column could be NMAR as a recipe could not have ratings because a recipe could be unrated because of the actual quality of the recipe (hence dependent on the rating itself) and may not be dependent on other factors from the dataset. The avg_rating column may also be missing as users may not feel the significance of leaving a rating on the recipe and may try it out without rating . 

### Missingness Dependency
However, the ratings could also be dependent on the time the recipe was submitted as newer recipes may not have had the oppurtunity to be rated. I will also investigate the `minutes` column too see if the missingness of the `avg_rating` column is dependent on this


### Hypothesis Testing

From bivariate analysis, I observed a positive relationship between calories and average ratings which was likely due to outliers.
Upon further investigation, I found that the observed average rating was higher on average for low calorie recipes than high_calorie recipes. As I plan on using `calories` in my predictive model, it is important for me to see if there is statistical significance between the calories a recipe contains and its average rating. This led me to conduct the following hypothesis test:

> Null hypothesis (H~0~): There is no difference in average rating between recipes with higher calories and those with lower calories
> Alternative hypothesis (H~1~): There is a difference in average rating between recipes with higher calories and those with lower calories

The test statistic I used for my hypothesis test was the absolute difference in meeans between higher calorie recipes and lower calorie recipes. The test was a two sided (non-directional) test 

For the hypothesis test, I measured the observed difference between high calorie meals and low meal calorie meals, using the median calorie count as the threshold for classification due to the right skew of the distribution of the `calories` column.

I performed 10,000 random permutation of the `avg_rating`'s and the results were as follows:

I obtained a p_value of 0.0642 and the following distribution of permutated differences

<iframe
  src="assets/hypothesis_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The p_value of 0.0642, means that at the 5% significance level, I fail to reject the null hypothesis. Meaning that the differences arent statistically significant and the difference in average_ratings could be as a result of chance. However, this result is marginally conclusive due to how close it is to the 5% significance level and a more lenient significance level such as the 10% significance level would have posed the difference as statistically significant.







