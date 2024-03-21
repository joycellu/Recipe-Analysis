# An Investigation on Protein and Ratings in Recipes

This project is for DSC 80 at UCSD.

**Author**: Joyce Lu

---

## Introduction

In recent years, there has been a significant shift towards health-conscious eating, with a particular focus on protein. Exploring the correlation between protein content and recipe popularity could help enhance understanding on consumer health trends and dietary preferences. **To understand this, my research question is: What are the characteristics of recipes with high protein? Is there a relationship between the amount of protein in a recipe and the recipe rating?** (Recipes with 'High protein' in this investigation is defined as recipes with 'Protein (PDV)' > 33)

The first dataset I am using contains the information of 83782 recipes from 2008 to 2018 on food.com, which consists of 12 columns as follows:

| Column             | Description                                                                                                                                                                                       |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `'name'	`           | Recipe name                                                                                                                                                                                       |
| `'id'`             | Recipe ID                                                                                                                                                                                         |
| `'minutes'`        | Minutes to prepare recipe                                                                                                                                                                         |
| `'contributor_id'` | User ID who submitted this recipe                                                                                                                                                                 |
| `'submitted'`      | Date recipe was submitted                                                                                                                                                                         |
| `'tags'`           | Food.com tags for recipe                                                                                                                                                                          |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe                                                                                                                                                                         |
| `'steps'`          | Text for recipe steps, in order                                                                                                                                                                   |
| `'description'`    | User-provided description                                                                                                                                                                         |
| `'ingredients'`    | Ingredients in recipe                                                                                                                                                                             |
| `'n_ingredients'`  | Number of ingredients in recipe                                                                                                                                                                   |

The second dataset I am using contains people's reviews and ratings submitted for the recipes, which consists of 731927 rows with 5 columns as follows:

| Column        | Description         |
| ------------- | ------------------- |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

In this investigation, the relevant columns I am using are as follows:

From the first dataset:

1. `name`, which contains recipe name.
2. `submitted`, which contains information of the date recipe was submitted. I also extracted year from the `submitted` column and assigned it as a new column `year`, to invesitgate our topic in terms of years when doing exploratory data analysis.
3. `tags`, which contains food.com tags for recipe.
4. `nutrition`, which contains information of calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV), where PDV stands for “percentage of daily value”. I separated each nutrition and assign them to individual columns. I will specifically be using `protein (PDV)` in this investigation.
5. `n_steps`, which contains the number of steps of recipe.
6. `n_ingredients`, which contains the number of ingredients of recipe.

From the second dataset:

1. `rating`, which is the rating given for the recipe.

---

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

Before conducting analysis, I first conducted data cleaning to make data more readable for analysis. I performed the following steps to clean our dataframe:

1. **Convert `submitted` to datetime object**
   I noticed that `submitted` is a object type, so I converted it into datetime object so that we can perform analysis more smoothly later on.
2. **Convert objects to lists**
   When looking at column types, there are columns such as `nutrition` that contain values that look like lists, but are strings that look like lists. Therefore, I converted `nutrition` to list of floats and converted `tags`, `steps`, and `ingredients` to lists of strings.
3. **Assign `nutrition` into individual columns**
   Since the values in `nutrition` represent `calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)`, I separated the values and assigned each nutrition into individual column.
4. **Left merge the two datasets**
   I left merged the recipe dataset and the review dataset for the purpose of matching reviews with their corresponding recipes to make analysis easier later on. We will refer to this merged dataset as `recipes`.
5. **Fill all ratings of 0 with np.NaN**
   I performed this step because ratings of 0 indicates missing values in rating, since usually the lowest ratings on website is 1.
6. **Find the average rating per recipe, and assign it back to the recipes dataset**
   Since `rating` is one of the most important data in the dataset, and that it would be convenient if I could look at one average rating for every recipe, I found the average rating per recipe, and assign this series to the merged dataset `recipes` as column `average_rating`.
7. **Add `year` column, derived from `submitted`**
   Since it would be helpful to look at trends over years, I extracted year from `submitted` column and assign it as a new column `year`.

After cleaning the data, the result dataframe look likes the following (our result dataframe has 234429 rows and 21 columns, but we are only displaying the first 5 rows and the important columns):

| name                               | submitted           | n_steps | protein (PDV) | average_rating | year |
| :--------------------------------- | :------------------ | ------: | ------------: | -------------: | ---: |
| 1 brownies in the world best ever  | 2008-10-27 00:00:00 |      10 |             3 |              4 | 2008 |
| 1 in canada chocolate chip cookies | 2011-04-11 00:00:00 |      12 |            13 |              5 | 2011 |
| 412 broccoli casserole             | 2008-05-30 00:00:00 |       6 |            22 |              5 | 2008 |
| 412 broccoli casserole             | 2008-05-30 00:00:00 |       6 |            22 |              5 | 2008 |
| 412 broccoli casserole             | 2008-05-30 00:00:00 |       6 |            22 |              5 | 2008 |

### Univariate Analysis

> Distribution of Ratings
> Here is a bar chart that shows the distribution of the count of each rating.

<iframe
  src="assets/rating_univariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I noticed that it has a very imbalanced situation, where most of data are of rating 5. This observation suggests that most users tend to give a higher ratings to recipes, and that if I am performing any analysis or prediction on ratings, I should keep in mind this imbalanced situation and make adjustment accordingly.

### Bivariate Analysis

> Protein (PDV) vs. Number of Steps in Recipe
> Here is a scatter plot that shows the relationship between `protein (PDV)` and `n_steps`.

<iframe
  src="assets/protein_vs_nstep.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This graph shows that there are more data points clustered around lower n_steps, and that data points with higher protein (PDV) tend to have lower n_steps. his plot suggests that most recipes have lower number of steps, and protein (PDV) values don't seem to have an obvious correlation with the number of steps in recipe.

### Interesting Aggregates

In this section, we will look into protein (PDV) with number of ingredients.

Here is a pivot table of n_ingredients with max, mean, median, and min protein (PDV):

| ('n_ingredients', '') | ('protein (PDV)', 'max') | ('protein (PDV)', 'mean') | ('protein (PDV)', 'median') | ('protein (PDV)', 'min') |
| --------------------: | -----------------------: | ------------------------: | --------------------------: | -----------------------: |
|                     1 |                      157 |                   54.7529 |                          22 |                        0 |
|                     2 |                     1051 |                   27.6896 |                           7 |                        0 |
|                     3 |                     1043 |                   17.1535 |                           5 |                        0 |
|                     4 |                     4356 |                   17.8903 |                           7 |                        0 |
|                     5 |                     2030 |                   23.0825 |                          10 |                        0 |

<iframe
  src="assets/mean_protein_n_ingred.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

When I looked specifically at mean protein (PDV) and n_ingredients, I noticed that mean protein (PDV) has an positive increasing trend as n_ingredients value increases, reaches local maximum when n_ingredients is around 30, then starts decreasing. This can be interpreted as when number of ingredients is less than 30, higher number of ingredients tend to correspond to higher protein (PDV), but the number of ingredients doesn't seem to have as much positive correlation with protein (PDV) values after 30.

---

## Assessment of Missingness

### NMAR Analysis

One column in the dataset with missing values that is likely `review` column. The missingness of the review can possibly due to users thinking that the recipe does not have anything special to write about. People tend to have more opinions about a recipe when it's bad or good, but if the recipe happens to be an average recipe, then people usually have less to comment about. This reason can explain why the missingness of `review` column is NMAR. If we generate another column that contains information of people's evaluation of the recipe (such as from a scale 0 to 5, with 5 being satisfied and 0 being unsatisfied), then the missingness can become MAR.

### Missingness Dependency

in this part, we will focus on the missingness of `rating`. We will test the dependency of the missingness of `rating` on `minutes` and `calories`.

1. Minutes vs. Rating (MCAR)
   Null Hypothesis: The distribution of the `minutes` when `rating` is missing is the same as the distribution of the `minutes` when `rating` is not missing.
   Alternative Hypothesis: The distribution of the `minutes` when `rating` is missing is **not** the same as the distribution of the `minutes` when `rating` is not missing.

I created a new column indicating the missingness status of `rating` and shuffled this column 1000 times for the permutation test. The test statistics I used is absolute difference in mean of minutes when rating is missing vs. not missing.

Here is the empirical distribution of the test statistic used in one of your permutation tests, along with the observed statistic:

<iframe
  src="assets/missing_1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The p-value we calculated from this permutation test is 0.109. If we use a significance level of 5%, p-value of 0.109 is greater than 0.05. We fail to reject the null hypothesis that the distribution of the 'minutes' when 'rating' is missing is the same as the distribution of the 'minutes' when 'rating' is not missing. Based on this result, we can conclude that the missingness of rating is MCAR.

2. Calories vs. Rating (MAR)
   Null Hypothesis: The distribution of the `calories` when `rating` is missing is the same as the distribution of the `calories` when `rating` is not missing.
   Alternative Hypothesis: The distribution of the `calories` when `rating` is missing is **not** the same as the distribution of the `calories` when `rating` is not missing.

I created a new column indicating the missingness status of `rating` and shuffled this column 1000 times for the permutation test. The test statistics I used is absolute difference in mean of minutes when rating is missing vs. not missing.

Here is the empirical distribution of the test statistic used in one of your permutation tests, along with the observed statistic:

<iframe
  src="assets/missing_2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The p-value we calculated from this permutation test is 0.0. If we use a significance level of 5%, p-value of 0.0 is less than 0.05. We reject the null hypothesis that the distribution of the 'calories' when 'rating' is missing is the same as the distribution of the 'minutes' when 'rating' is not missing. Based on this result, we can conclude that the missingness of rating is MAR because the rating is dependent on the calories.

---

## Hypothesis Testing

As seen in exploratory data analysis section, there is an interesting increasing trend between number of ingredients and protein (PDV) value. To understand characteristics of recipes with high protein and low protein better, we will conduct a permutation test to investgate whether there is a relationship between number of ingredients and protein (PDV) value.

Null Hypothesis: The number of ingredients has no effect on the amount of protein in a recipe.
Alternative Hypothesis: Recipes with higher number of ingredients have higher level of protein.
Test statistics: Since number of ingredients is numerical data, I will use difference in means as the test statistics (mean number of ingredients of low protein recipe minus mean number of ingredients of high protein recipe)
Significance level: 5%

In this part, I define recipes with high protein as recipes with 'protein (PDV)' greater than 33 (which is the mean of the protein (PDV) column).

Here is a plot of the empirical Distribution of the mean differences:

<iframe
  src="assets/hyp_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The p-value of this permutation test is 0.0. Since p-value 0.0 is less than the significance level 5%, we reject the null hypothesis. This result could be reasonable because the more ingredients there are in a recipe, there might be more ingredients containing protein, thus increasing the protein PDV value.

---

## Framing a Prediction Problem

---

## Baseline Model

---

## Final Model

---

## Fairness Analysis
