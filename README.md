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
I decided to examine the distribution of `avg_rating` in the dataset just to get a feel of the general spread of the data

<iframe
  src="assets/rating_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From, this I can see that the average rating, we can see that the distribution of average ratings in our dataset is heavily left-skewed meaning that a large proportion of recipes in our dataset has ratings that are above the mean of 4.63.

I equally decided to examine the distribution of `protein_PDV` in  my dataset. Upon first examination of protein, I found that the distribution was extremely skewed to the left, so in order to better capture the real distribution of the `protein_PDV` in my dataset, I took out the outliers and produced the following histogram:

<iframe
  src="assets/univ_prot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From this histogram, we can see that the distribution of protein amongst recipes in our data is heavy at values > 50, this also indicates that the median is a better measure of the center of this data.

### Bivariate Analysis

For my bivariate analysis, I wanted to examine the relationship between protein and avg_rating because I believe recipes with more protein would be rated higher on average, judging from a nutritionist stance. The following scatter plot was produced.

<iframe
  src="assets/protXrating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Here we can see that there is a somewhat positive relationship between protein and avarage rating but I would like to cross examine this with the relationship between calories and recipe rating, simply to see which has a stronger correlation to `avg_rating`.

<iframe
  src="assets/calXrating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From this, we can see that both protein and calories have some kind of positive relationship between average ratings, however the relationships are not so strong , probably because of the lack of low-rated recipes in or dataset.
Both scatter plots show a cluster of datapoints at higher average rating levels




                                        
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
However, the ratings could also be dependent on the time the recipe was submitted as newer recipes may not have had the oppurtunity to be rated. I will also investigate the `minutes` column too see if the missingness of the `avg_rating` column is dependent on this.

#### Testing Missingness Dependency of avg_rating based on how old a recipe is
Based on assumption, I believe that new recipes are more likely to have missing ratings as not enough people have been to exposed to the content yet at time of collection.

To test this, I engineered a new columns `recipe_age_days` and  which subtracts the `submission` date of the recipe fron  the most recent date in the dataframe. Therefore, a higher value for `recipe_age_days` indicates an older recipe. 

First of all, I examined the distributions of missing avg_ratings over recipe age (days) distribution using a kde plot

<iframe
  src="assets/ageXmissing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The two distributions look rather different, so I moved on to a permutation test to check how significant the difference in the distribution is.
My hypothesis test took the following form:
>H<sub>0</sub>: mean recipe age<sub>missing</sub> = mean recipe age<sub>notMissing</sub> 
<br>
                    vs. 
 <br>
H<sub>1</sub>:the means are different.

with the test statistic, being the observed absolute difference in means of `recipe_age_days` for recipes that do not have any missing ratings and for those that do have missing ratings.

After running my permutation test, I obtained the following distribution of permutated mean differences

<iframe
  src="assets/age_mtest.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We can see that the avg_ratings column is MAR dependent on the 'submitted' column with a p-value of 0. Here we reject the null. The observed difference of 273.42 is way too extreme to be explained by chance alone

#### Testing Missingness Dependency of `avg_rating` based on `protein_PDV`

I equally decided to test the whether the missingness of `avg_rating` was dependent on the `protein_PDV` column.
To assess whether the missingness in the `avg_rating` column is completely at random (MCAR) i.e not dependent on specific column, I tested for an association between the missingness of the `avg_rating` column and the numeric variable `protein`. Specifically, I performed a permutation test on the difference in mean protein between recipes with missing and non-missing `avg_rating`.

> Null hypothesis (MCAR): mean protein<sub>missing</sub> = mean protein<sub>not missing</sub>
> Alternative Hypothesis (MAR): means are not equal
> Test statistic: absolute difference in means of the two distributions  

The observed difference in the dataset was approx 1.28, so I performed multiple permutations of the dataset with shuffled columns to see how possible this value is to obtain by chance alone.
Below is an embedded Plotly histogram showing the permutation distribution  and the observed Δ_obs (red dashed line).

<iframe
  src="assets/protein_mtest.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


Because p = 0.20 > 0.05, we fail to reject the MCAR assumption. There is no evidence that the probability of `avg_rating` being missing depends on the protein content of the recipe (consistent with MCAR). Thus missingness of `avg_rating` column is not MAR dependent on `protein_PDV`.

---

### Hypothesis Testing

From my bivariate analysis, I observed a positive relationship between calories and average ratings which was likely due to outliers.
Upon further investigation, I found that the observed average rating was higher on average for low-calorie recipes than high-calorie recipes. As I plan on using `calories` in my predictive model, it is important for me to see if there is statistical significance between the calories a recipe contains and its average rating. This led me to conduct the following hypothesis test:

> Null hypothesis (H<sub>0</sub>): There is no difference in average rating between recipes with higher calories and those with lower calories

> Alternative hypothesis (H<sub>1</sub>): There is a difference in average rating between recipes with higher calories and those with lower calories

The test statistic I used for my hypothesis test was the absolute difference in means between higher calorie recipes and lower calorie recipes. The test was a two sided (non-directional) test 

For the hypothesis test, I measured the observed difference between high calorie meals and low meal calorie meals, using the median calorie count as the threshold for classification due to the right skew of the distribution of the `calories` column.

I performed 10,000 random permutation of the `avg_rating`'s and the results were as follows:

I obtained a p_value of 0.0642 and the following distribution of permutated differences

<iframe
  src="assets/hypothesis_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The p_value of 0.0642, means that at the 5% significance level, I fail to reject the null hypothesis. Meaning that the differences arent statistically significant and the difference in average_ratings could be as a result of chance. However, this result is marginally conclusive due to its proximity to the 5% significance level and a more lenient significance level such as the 10% significance level would have posed the difference as statistically significant.


### Framing a Prediction Problem

For my prediction model, I want to use a binary class classifier to predict a given recipe's average rating category. My model would classify recipes into low-rated (avg_rating < 4.6) or high-rated (avg_rating >= 4.6) once the information about the recipe is posted to the webiste . 
To address my prediction problem I will be using different classifiers as I am trying to classify the data into two groups (low or high). 
I chose the cutoff point of 4.6 because I believe it accurately represents my data, with 4.6 being the center of my data ,a rating of below 4.5 would represent under-performance in the distribution. I chose this problem because I feel individuals would be more concerned with finding high rated objects than a precise rating.I plan on doing this with only data available to us at the time a recipe is posted, which is all the data in the recipe dataset excluding the `avg-rating` column ofcourse as this is the prediction column. I have also seen correlation (shown in my bivariate analysis) between average rating and nutritional columns in my dataset : specifically `calorie` and `protein_PDV`. 

I plan on improving my baseline model by using other columns in my dataset, mainly the other nutritional columns in my dataset. These columns would be as features for building, combined with using logistic regression as it is more effective for a simple binary classification. I will aslo perform hyperparameter tuning on the logistic regression hyperparameters using 5-fold cross-validation and examing a confusion matrix to ensure low number of false positives and false negatives.
The performance of my model would be measured on the F1-score rather than the accuracy, as the F1 because the data is heavily skewed/ imperfectly balanced. From our univariate analysis, we can see that the highly-rated bucket contains most of the data in our dataset, so the F1-score would better measure the model's performance. The F1 score is a better representation of classification into smaller groups than simple model accuracy.


### Baseline Model
For my baseline model:

I first created a Y column, classifying the `avg_rating` into 0, and 1,  with the following descriptions:

> Class 0 = low‐rated (avg_rating < 4.6)

> Class 1 = high‐rated (avg_rating ≥ 4.6)

Then, I trained a Decision Tree Classifier. The model uses two features — `calories` and `protein_PDV` —both of which are quantitative. There are no ordinal or nominal features, so no encoding was required.

The data were split 80/20 (train/test) with a fixed random seed. On the test set, the model achieved:
- **Overall f1 macro score :** 50%
- **Low-rating class (0):** precision 32%, recall 35%  
- **High-rating class (1):** precision 68%, recall 66%

and the following confusion matrix

<iframe
  src="assets/baseline_conf.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From this we see that The model correctly identifies 1812 low and 7223 medium ratings, but mislabels 3425 low as medium and 3775 medium as low. The classifier performs similarly to average guessing, its poor performance on the low-rating class and the overall 50% f1 macro indicates that this baseline is not yet “good” for deployment. To improve my model, I plan on incorporating additional predictive features, engineer interactions and experiment with logistic regression as it is much less prone to overfitting than a single decision tree, so it usually generalizes better to new data.

### Final Model

Building on the baseline, I engineered a new feature, balanced the classes with SMOTE, and fitted a regularized logistic regression tuned for F1 performance.

**Engineered feature**  
- **Protein-to-calorie ratio** (`protein_PDV` / `calories`): captures how protein-rich a recipe is relative to its caloric content, which I feel can influence user ratings.

I transformed all featured using `StandardScaler` so as to mitigate influence of large values. I then applied SMOTE to upsample the low-rating class and trained a logistic regression with balanced class weight. 

**Final test performance**  
- **Low-rating class (0) F1 score:** 0.39 (vs baseline: 0.33)  
- **High-rating class (1) F1 score:** 0.64 (vs baseline: 0.67)  
- **Macro F1 score:** 0.52 (vs baseline: 0.50)

By adding a nutrient-density feature, rebalancing with SMOTE, and using a tuned logistic regression, I improved macro-F1 but only a little,as I was more focused in increasing performance for the minority class.

I also obtained the following confusion matrix:

<iframe
  src="assets/final_conf.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Compared to the baseline matrix, the final model correctly classifies 2342 low ratings (up from 1812) and cuts low→high errors by 530, boosting recall for the low-rating class; although high→low errors rise slightly

### Fairness Analysis

## Fairness Analysis

For my fairness analysis, I evaluated whether my final model’s precision differs between recipes with low protein values and recipes with high values, using the median as a cutoff due to the skew of the protein distribution

I seperated the `protein_PDV` into two groups to test on the final model:
- **Group X (Low‐to‐Median protein):** `protein_PDV` ≤ 18  
- **Group Y (Above‐Median protein):** `protein_PDV` > 18  

My hypothesis test had the following format:
> **Null hypothesis (H₀):** Precision_X − Precision_Y = 0 (no performance difference)  
> **Alternative hypothesis (H₁):** Precision_X − Precision_Y ≠ 0  
> **Test statistic:**  = Precision_X − Precision_Y  

I observed a test statistic of −0.004 (Precision_X = 0.694, Precision_Y = 0.697) when tested on my original test set. Them I performed a permutation test using 5,000 label‐shuffles of protein group membership to build the null distribution of the test statistic and obtained the following empirical distribution:

<iframe
  src="assets/fair.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


From the permutution test we see that the test has a p-value of 0.3476.
At the 5% significance level, Because p = 0.3476 > 0.05, I fail to reject H₀ and conclude there is no evidence that precision differs between the two protein groups. Therefore, my model is fair














