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

Here is a bar chart that shows the distribution of the count of each rating.

<iframe src="assets/rating_univariate.html" width=800 height=600 frameborder=0></iframe>

I noticed that it has a very imbalanced situation, where most of data are of rating 5. This observation suggests that most users tend to give a higher ratings to recipes, and that if I am performing any analysis or prediction on ratings, I should keep in mind this imbalanced situation and make adjustment accordingly.

### Bivariate Analysis

> Protein (PDV) vs. Number of Steps in Recipe

Here is a scatter plot that shows the relationship between `protein (PDV)` and `n_steps`.

<iframe src="assets/protein_vs_nstep.html" width=800 height=600 frameborder=0></iframe>

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

<iframe src="assets/mean_protein_n_ingred.html" width=800 height=600 frameborder=0></iframe>

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

<iframe src="assets/missing_one.html" width=800 height=600 frameborder=0></iframe>

The p-value we calculated from this permutation test is 0.109. If we use a significance level of 5%, p-value of 0.109 is greater than 0.05. We fail to reject the null hypothesis that the distribution of the 'minutes' when 'rating' is missing is the same as the distribution of the 'minutes' when 'rating' is not missing. Based on this result, we can conclude that the missingness of rating is MCAR.

2. Calories vs. Rating (MAR)
   Null Hypothesis: The distribution of the `calories` when `rating` is missing is the same as the distribution of the `calories` when `rating` is not missing.
   Alternative Hypothesis: The distribution of the `calories` when `rating` is missing is **not** the same as the distribution of the `calories` when `rating` is not missing.

I created a new column indicating the missingness status of `rating` and shuffled this column 1000 times for the permutation test. The test statistics I used is absolute difference in mean of minutes when rating is missing vs. not missing.

Here is the empirical distribution of the test statistic used in one of your permutation tests, along with the observed statistic:

<iframe src="assets/missingtwo.html" width=800 height=600 frameborder=0></iframe>

The p-value we calculated from this permutation test is 0.0. If we use a significance level of 5%, p-value of 0.0 is less than 0.05. We reject the null hypothesis that the distribution of the 'calories' when 'rating' is missing is the same as the distribution of the 'minutes' when 'rating' is not missing. Based on this result, we can conclude that the missingness of rating is MAR because the rating is dependent on the calories.

---

## Hypothesis Testing

As seen in exploratory data analysis section, there is an interesting increasing trend between number of ingredients and protein (PDV) value. To understand characteristics of recipes with high protein and low protein better, we will conduct a permutation test to investgate whether there is a relationship between number of ingredients and protein (PDV) value.

Null Hypothesis: The number of ingredients has no effect on the amount of protein in a recipe.
Alternative Hypothesis: Recipes with higher number of ingredients have higher level of protein.
Test statistics: Since number of ingredients is numerical data, I will use difference in means as the test statistics (mean number of ingredients of low protein recipe minus mean number of ingredients of high protein recipe)
Significance level: 5%

I define recipes with high protein as recipes with 'protein (PDV)' greater than 33 (which is the mean of the protein (PDV) column).

Here is a plot of the empirical Distribution of the mean differences:

<iframe src="assets/hyp_test.html" width=800 height=600 frameborder=0></iframe>

The p-value of this permutation test is 0.0. Since p-value 0.0 is less than the significance level 5%, we reject the null hypothesis. This result could be reasonable because the more ingredients there are in a recipe, there might be more ingredients containing protein, thus increasing the protein PDV value.

---

## Framing a Prediction Problem

After gaining some understanding on protein in recipe, I will move on to learning about ratings in recipe. Specifically, I will be performing a prediction task on `rating`.

My prediction problem is predicting ratings of recipe, and it will be a multiclass classification (since there are five ratings 1, 2, 3, 4, 5) using the random forest classifier.

The response variable would be `rating`.he reason why I am curious in predicting rating is because I want to see if I can use some of the information I learned from previous parts to predict the popularity of recipe.

The evaluation metrics I am using if F1-score (class='weighted') because as previously mentioned, `rating` has imbalanced class distribution, so I want to account for potential label imbalanced in predicted outputs.

Most features in the dataset are features that we would know at the "time of prediction". Some important features that we will pay attention to include: `n_steps`, `protein (PDV)`, `tags`, `name`, `submitted`.

---

## Baseline Model

> Modeling Algorithm and Features

To build my model, I am using the Random Forest Classifier from the sklearn library. I first performed train_test_split on my dataset to split it into X_train, X_test, y_train, and y_test.

The two features I am using in my pipeline are `n_steps` and `protein (PDV)`. The description and the encodings I performed on the two features are detailed as follows:

1. `n_steps`: It is a quantitative feature, and I did not perform any additional encodings.
2. `protein (PDV)`: This is a quantitative feature. However, I used the `Binarizer` transformer with a threshold of 33 on this feature to map each protein (PDV) to 1 or 0, depending on whether the value is greater than 33, or less than or equal to 33. As previously mentioned, I define recipes with high protein as recipes with 'protein (PDV)' greater than 33. This encoding is to categorize `protein (PDV)` into high protein and low protein.

> Model's Performance

The test F1-score of this baseline model is around 0.416. There is definitely room for improvement for this model. This is not only because the F1-score on the unseen test set is only around 0.416 right now, but also because it looks like I don't have enough features that make good predictions of ratings yet. To improve, I want to perform GridSearch to find the optimal hyperparameters and to include more features to see if I can increase the prediciton performance.

---

## Final Model

> Modeling Algorithm and Features

I am also using Random Forest Classifier as my final model. In my final model, the new features I added are detailed as follows:

1. `tags`: I extracted information whether `tags` includes whether 'vegetarian' or not, then encoded the resulted boolean column using OneHotEncoder(). I believe this could be good for prediction because I assume many vegetarians will look for recipes with a 'vegetarian' tag specifically. Since 'vegetarian' tag has its unique target audience, I believe it will be a good feature to add to predict rating.
2. `name`: I extracted information whether `name` has some common types of meat such as 'chicken', 'pork', and 'beef', then encoded the resulted boolean column using OneHotEncoder(). I believe this column will be a good feature to add because these are recipes with high protein keywords in their name could have effect on rating since 'name' is usually what users first notice.
3. `n_steps`: `n_steps` was also used in baseline model. In the final model, I transformed it with the StandardScaler() transformer. I think it could be a good idea to standardize the column to narrow down the range of the values, since the original data of `n_steps` cover a wide range of values.
4. `submitted`: I one hot encoded `submitted`. I believe this can be a good feature because I think recipe submission date can potentially group together recipes with certain characteristics, which could help predicting ratings. Moreover, it is possible that recipes with closer dates are discovered by more people.

> Grid Search

To find the optimal hyperparameters for the final model, I also performed GridSearchCV. I decided to use GridSearchCV to tune the hyperparameters, as it is most straightforward and performs cross validation during the searching process. The hyperparameters I plan to tune are max_depth, min_samples_split, and n_estimators.

1. **max_depth**: I chose max_depth because I believe controlling the depth of trees could prevent the model from being too complex and overfitting to the training data.
2. **min_samples_split**: I chose min_samples_split because increasing its value can also prevent overfitting and capturing too many distinctions in the training data.
3. **n_estimators**: I chose n_estimators, which represents the number of trees in the forest, because I want to know the optimal number of trees that contribute to a more robust model.

The hyperparameters that ended up working the best are the combination of max_depth=10, min_samples_split=5, and n_estimators=15.

> Model's Performance

The test F1-score of this baseline model is around 0.603. The improvement of this final model over the baseline model's performance can be seen from the F1-score on unseen test set, which improves from around 41.6% with baseline model to 60.3%, by 18.7%%. The difference between the F1-score on training set (0.610) and test set is also not big, which suggests that the final model is not overfitting the train set and predicting poorly on the test set.

---

## Fairness Analysis

To understand whether the model is fair or not, we want to conduct a fairness analysis of the final model to answer the question: "Does my model perform worse for high protein recipes than it does for low protein recipes?"

I will use a permutation test to compare our evaluation metrics F1-score (weighted) across the two groups, high protein recipes (with protein PDV greater than 33) and low protein recipes (with protein PDV less than or equal to 33). The null and alternative hypotheses of the test are as follows:
Null Hypothesis: Our model is fair. Its weighted f1 score for high protein and low protein are roughly the same, and any differences are due to random chance.
Alternative Hypothesis: Our model is unfair. Its weighted f1 score for high protein is lower than its weighted f1 score for low protein.

For test statsitics, I am using difference in F1-score (low protein minus high protein). Our signficance level is 5%.

<iframe src="assets/fairness.html" width=800 height=600 frameborder=0></iframe>

The p-value of this permutation test is 0.0. Since the p-value is 0.0, less than our significance level 5%, we reject the null hypothesis. It seems like the difference in weighted f1 score across the two groups is significant.
