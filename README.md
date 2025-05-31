<style>
  /* Darken the banner title and subtitle */
  .site-title, .site-description {
    color: #222222 !important;
  }
  /* Make all regular paragraph text darker */
  body, 
  .markdown-body p, 
  .markdown-body li {
    color: #333333 !important;
  }
  /* Ensure code “pills” (inline code) remain legible */
  .markdown-body code {
    background-color: #f4f4f4 !important;
    color: #d6336c !important; /* or any color you like */
  }
</style>

# Nutrigression : Recipe Reviews and Nutrition Analysis
Author: Munachimso Ndubisi

## Introduction
This project aims to understand the relationship between the nutritional components (in this case: calories, fat, sugar, protein, saturated fat and carbohydrates) and their average ratings given by real users. We have two relevant datasets from the [food](https://www.food.com/) website:

1. `RAW_recipes.csv`(contains one row per recipe, including a “nutrition” string that lists calories, percent daily‐value of fat, sugar, protein, etc.).

2. `interactions.csv` (contains one row per user‐recipe interaction, including a star rating between 1–5 and a recipe_id that links back to RAW_recipes.csv).







