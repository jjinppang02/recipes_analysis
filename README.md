---
title: A Dive Into the Efficiency of Recipes 
---

# **Overview**
For the DSC 80 Spring Final Project, I chose to investigate the Recipes Data Set!

Author : Jacob Lee
<br>

# **Introduction and Question Identification**
When it comes to recipes, there is much that can be analyzed. One thing that I wanted to dive deeper into was the efficiency of recipes, measured in number of steps per minute. In order to do so, I was given two sets of data, `recipes` and `ratings`.  

<br>

`recipes` contained the following 12 columns with 83782 rows:

<div style="overflow-x: auto;">

| Column Name | Dtype | Description |
| --------- | -------- | -------- |
| `name` | object | Recipe name. |
| `id` | int64| Recipe ID. |
| `minutes` | int64| Minutes to prepare recipe. |
| `contributor_id` | int64 | User ID who submitted this recipe. |
| `submitted` | object | Date recipe was submitted. |
| `tags` | obejct | Food.com tags for recipe. |
| `nutrition` | object | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”. |
| `n_steps` | int64 | Number of steps in recipe. |
| `steps` | object | Text for recipe steps, in order. |
| `description` | object | User-provided description. |
| `ingredients` | object | List of ingredients. |
| `n_ingredients` | int64 | Number of ingredients. |

</div>

<br>

And `ratings` contained the following 5 columsn with 731927 rows:

<div style="overflow-x: auto;">
  
| Column Name | Dtype | Description |
| --------- | -------- | -------- |
| `user_id` | float64 | User ID. |
| `recipe_id` | float64| Recipe ID. |
| `date` | object| Date of interaction. |
| `rating` | float64 | Rating given. |
| `review` | object | Review text. |
</div>

<br>

I later left-merged the two datasets together, left on `id` and right on `recipe_id` to get `df`, a dataframe containing 17 columns and 234429 rows.

A question that I wanted to specifically answer in this investigation was "Are higher ratings given to recipes with higher efficiency?" This was a question I wanted to examine because generally people believe ratings come solely from taste of the final product. However, I believe that the process of cooking the dish also plays a huge role in the rating process as a food reviewer/chef myself. Whether a recipe produced either really good or really poor quality food, one thing that stuck with me most was how efficient the recipes were. Some recipes I picked said only 60 minutes but half of that time would be spent peeling potatoes. Although it was fairly short, and the actual cooking process shorter, the amount of boredom and feeling of unproductiveness being stuck on one step was enough for me to bring down good dishes' ratings and ones that felt very smooth to breeze by I gave a higher rating than what I would have given based solely on taste. Due to this, I wanted to investigate if recipes' ratings were influenced.

So, for this, I picked `minutes`, `n_steps`, and `rating` for my investigation.

<br>

# **Data Cleaning**
Before I could fully dive into the analysis, I had to clean up the data to make it usable. First, I replaced all empty values with `NaN` values and then dropped all rows with NaN values. I also dropped rows with 0 as their rating as all ratings are in between 1-5, 0 for their number of ingredients, 0 for their number of steps, and 0 for their total minutes as they all indicate that there is an error with the recipe. After cleaning up the data, I also included two more columns, `avg_rating` and `efficiency`, where `avg_rating` represents that average rating the recipe received and `efficiency` the measurement of how efficient a recipe was represented by number of steps per minute according to the recipe. After dropping any possible further `NaN` values, I was left with a dataframe called `df_clean`. Although I included `avg_rating` to the dataframe, I will be using `rating` for the investigation.

Here is the `head` of `df_clean`:

<div style="overflow-x: auto; white-space: nowrap;">
  
| name | id | minutes | contributor_id | submitted | tags | nutrition | n_steps | steps | description | ingredients | n_ingredients | user_id | recipe_id | date | rating | review | avg_rating | efficiency |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **[1 brownies in the world best ever](ca://s?q=show_brownies_row)** | 333281 | 40 | 985201 | 2008‑10‑27 | ['60-minutes-or-less','time-to-make','course…'] | [138.4,10.0,50.0,3.0,3.0,19.0,6.0] | 10 | ['heat the oven to 350f…'] | these are the most; chocolatey… | ['bittersweet chocolate','unsalted butter',…] | 9 | 386585.0 | 333281.0 | 2008‑11‑19 | 4.0 | These were pretty good… | 4.0 | 0.250000 |
| **[1 in canada chocolate chip cookies](ca://s?q=show_cookie_row)** | 453467 | 45 | 1848091 | 2011‑04‑11 | ['60-minutes-or-less','time-to-make','cuisin…'] | [595.1,46.0,211.0,22.0,13.0,51.0,26.0] | 12 | ['pre‑heat oven to 350…'] | this is the recipe we use at my school… | ['white sugar','brown sugar','salt','margar…'] | 11 | 424680.0 | 453467.0 | 2012‑01‑26 | 5.0 | Originally I was gonna cut… | 5.0 | 0.266667 |
| **[412 broccoli casserole](ca://s?q=show_broccoli_row)** | 306168 | 40 | 50969 | 2008‑05‑30 | ['60-minutes-or-less','time-to-make','course…'] | [194.8,20.0,6.0,32.0,22.0,36.0,3.0] | 6 | ['preheat oven to 350…'] | since there are already 411 recipes… | ['frozen broccoli cuts','cream of chicken…'] | 9 | 29782.0 | 306168.0 | 2008‑12‑31 | 5.0 | This was one of the best… | 5.0 | 0.150000 |
| **[412 broccoli casserole](ca://s?q=show_broccoli_row_2)** | 306168 | 40 | 50969 | 2008‑05‑30 | ['60-minutes-or-less','time-to-make','course…'] | [194.8,20.0,6.0,32.0,22.0,36.0,3.0] | 6 | ['preheat oven to 350…'] | since there are already 411 recipes… | ['frozen broccoli cuts','cream of chicken…'] | 9 | 1196280.0 | 306168.0 | 2009‑04‑13 | 5.0 | I made this for my son's… | 5.0 | 0.150000 |
| **[412 broccoli casserole](ca://s?q=show_broccoli_row_3)** | 306168 | 40 | 50969 | 2008‑05‑30 | ['60-minutes-or-less','time-to-make','course…'] | [194.8,20.0,6.0,32.0,22.0,36.0,3.0] | 6 | ['preheat oven to 350…'] | since there are already 411 recipes… | ['frozen broccoli cuts','cream of chicken…'] | 9 | 768828.0 | 306168.0 | 2013‑08‑02 | 5.0 | Loved this. Be sure to completely thaw… | 5.0 | 0.150000 |
  
</div>

`df_clean` resulted in 19 columns and 219228 rows.

<br>

# **Univariate Analysis**
Two distributions that I analyzed was the distribution of ratings and the distributions of efficiency (steps/min).

<iframe 
  src="assets/distribution_plots.html" 
  width="800" 
  height="500" 
  frameborder="0"
></iframe>

From the graphs we see two things. First, in the distribution of ratings, we see that most recipes receive the highest possible rating of 5, indicating users tend to rate positively. Meanwhile, recipe efficiency (steps per minute) is right‑skewed — most recipes are relatively slow and detailed, with only a few achieving high efficiency.

<br>

# **Bivariate Analysis**
For the bivariate analysis, I delved into the conditional distribution of ratings by efficiency quartiles.

<iframe 
  src="assets/cond_distribution_plots.html" 
  width="800" 
  height="500" 
  frameborder="0"
></iframe>

This plot shows the conditional distribution of recipe ratings across efficiency quartiles. All quartile, ranging from slow (Q1) to fast (Q4, exhibit nearly identical patterns, with a dominant peak near rating 5, suggesting that recipe efficiency has little effect on users' ratings since most recipes, regardless of efficiency, receive high ratings, as we saw in the univariate analysis.

<br>

# **Interesting Aggregates**

Recipe Counts: Rating Group × Efficiency Quartile

| Rating Group / Efficiency Quartiles | Q1 (Not Efficient) | Q2 | Q3 | Q4 (Very Efficient) |
| --- | --- | --- | --- | --- |
| Low (<3) | 3580 | 3246 | 2790 | 2782 |
| Mid (3–4) | 9681 | 10078 | 8649 | 8867 |
| Very High (>4.5) | 41657 | 44005 | 41290 | 42603 |

This table gives us a little more insight into what we saw earlier. From this, it is hinted that efficiency does not have much influence over mid to high ratings, at least for this dataset. However, interestingly, we see in low reviews that efficiency does have some influence, where not efficient recipes made up most of the low-rated recipes. What we can infer from this data is that negative reviews tend to be more critical, where efficiency does show up as a factor in rating for low-rated recipes.

<br>

# **NMAR Analysis**
Before cleaning up the dataframe, I noticed that there many columns with `NaN` and missing values. Out of these, it is likely that `review` and `efficiency` is Not Missing At Random (NMAR). The reasoning behind this is that there may be some users that left a rating but did not write a review. Additionally, `efficiency` is NMAR as we calculated this from `n_steps` and `minutes`, depending on these values. In order for `efficiency` to have a `NaN` value would be if a value used to calculate it was `NaN` or if `minutes` was 0, creating a division error.

<br>

# **Missingness Dependency**
As most of our investigation is done on `rating`, `n_steps`, and `minutes`, I wanted to explore if the missingness of `rating` depended on either one of these columns.

First, we will look to see if there is a dependency between `rating` and `n_steps`. As such, these are the hypotheses used to test this:

<br>

**Null Hypothesis**: missingness of rating does not depend on n_steps

**Alternative Hypothesis**: missingness of rating does depend on n_steps

<br>

And below is the distrbution of `n_steps` by `rating_missingness`, which indicates with a `boolean` whether or not a row is missing `rating` data.

<iframe 
  src="assets/plot_nsteps_distribution.html" 
  width="800" 
  height="500" 
  frameborder="0"
></iframe>

<br>

I simulated 500 permutations of the `rating_missing` column. The resulting observed statistic was 1.3386 and the p-value was 0.000, meaning we reject the null hypothesis. The missingness of `rating` does depend on `n_steps`. Here are the plot of the results.

<iframe 
  src="assets/plot_nsteps_permutation.html" 
  width="800" 
  height="500" 
  frameborder="0"
></iframe>

<br>

Next, I tested if there is a dependency between `rating` and `minutes`. Again, here are the hypotheses used and the distribution of `minutes` by `rating_missingness`.

<br>

**Null Hypothesis**: missingness of rating does not depend on minutes

**Alternative Hypothesis**: missingness of rating does depend on minutes

<br>

<iframe 
  src="assets/plot_minutes_distribution.html" 
  width="800" 
  height="500" 
  frameborder="0"
></iframe>

<br>

The same process for `n_steps` was used for this permutation test. The resulting observed stat was 51.4524 and the p-value was 0.1220, meaning we fail to reject the null hypothesis; the missingness of `rating` does not depend on `minutes`. Here are the plot of the results.

<iframe 
  src="assets/plot_minutes_permutation.html" 
  width="800" 
  height="500" 
  frameborder="0"
></iframe>

<br>

It is also important to note that values were capped at 300 for this permutation test due to the very high outliers `minutes` had.

<br>

# **Hypothesis Testing**
With all this in mind, let us turn back to the question at hand, "Are recipes given higher ratings if they are more efficient?" I ran a two-sided permutation test in order to answer the question by exploring if the distributions are similar between efficiency quartiles (specifically between Q1 (Not Efficient) and Q4 (Very Efficient)) in terms of rating (specifically high-ratings (>= 4.5) since that is the nature of the question and majority of the ratings were high). To address this question, these were the designed hypotheses, test statistic, and significance level used:

<br>

**Null Hypothesis**: The proportion of recipes rated >= 4.5 is the same for the slowest (Q1) and fastest (Q4) efficiency groups. Any observed difference is due to random chance.

**Alternative Hypothesis**: The proportion of recipes rated >= 4.5 is higher for the fastest efficiency group (Q4) than the slowest (Q1).

**Test Stastic**: The difference in proportions of recipes rated >= 4.5 between Q4 and Q1

**Significance Level**: We used a significance level of 0.05

<br>

After running the permutation test, I got a p-value of 0.000, meaning we reject the null hypothesis. This means that the proportion of high-rated recipes is significantly different between Q1 and Q4, providing evidence that more efficient recipes are significantly more likely to receive a high rating than not efficient recipes.

Here was the resulting graph:

<iframe 
  src="assets/permutation_test_q1_q4.html" 
  width="800" 
  height="500" 
  frameborder="0"
></iframe>

<br>

# **Problem Identification**
On the area of efficiency, I wanted to also predict how long a recipe would take to finish in minutes. That way, people can see generally how long a recipe might take despite what is claimed by the recipe creator or if the creator did not provide a time estimate of the recipe. This is a regression problem, in which we will try to fit a model that predicts the minutes it takes to prepare a recipe.

The response variable will be `minutes`, since we are interested in predicting the length it takes to prepare a recipe. The metric I used to evalute my model was Mean Absolute Error (MAE). Although Root Mean Squared Error (RMSE) and R<sup>2</sup> are viable metrics to use, I settled for MAE because I did not need to punish big misses heavily; being off by 60 minutes, for example, isn't sixteen times worse than being off by 15 minutes, it's just proprotionally worse in this case. MAE reflects the intuition directly, and R<sup>2</sup> shows variance but doesn't necessarily answer the question of "how wrong is the model" unlike MAE. Additionally, MAE stays grounded in raw prediction error and is harder to flatter.

At the time of prediction, I am assuming that we would know at least `tags`, `n_steps`, and `n_ingredients` as we are trying to predict `minutes` in scenarios that the recipe poster forgot to include the `minutes` information or to see how long a recipe would actually take despite the posted amount, in which we are also assuming that none of these columns are dependent on `minutes` (which should be the case).

<br>

# **Baseline Model**
The baseline model will use a train-test split to separate our DataFrame into training and test groups. A Linear Regression model will be fit using our training data. The features used for the baseline will be n_steps, n_ingredients, and four binary features extracted from the tags column: 15-minutes-or-less, 30-minutes-or-less, 60-minutes-or-less, and 4-hours-or-less. The numerical features n_steps and n_ingredients will be standardized using StandardScaler so that they are on a comparable scale. The binary tag features are already 0/1 encoded and are passed through as it is.

The MAE I got from this model was 15.138 minutes. Intuitively, this demonstrates that my model is "good." The strong performance is largely driven by the time-based tag features, which directly encode cook time buckets. The remaining error can be explained by the inherent variability in cook times even within those buckets; a 60-minutes-or-less recipe could take anywhere from 1 to 60 minutes, which a linear model cannot resolve without more granular features.

<br>

# **Final Model**
The final model uses the same train-test split as the baseline. A Random Forest Regressor is fit using training data, chosen over Linear Regression because cook time has a non-linear relationship with its predictors and tree-based models handle this naturally.

On top of the baseline features, two new features are engineered. n_steps_sq is the square of n_steps; the relationship between steps and cook time is non-linear, since adding a 15th step to a recipe, for example, adds far more time than adding a 2nd step. steps_per_ingredient is the ratio of steps to ingredients, capturing recipe complexity; a high-step, low-ingredient recipe implies time-intensive technique, while a high-ingredient, low-step recipe (e.g. a salad) is quick. Two additional numeric features, calories and total_fat parsed from the nutrition column, are included and transformed with a QuantileTransformer to handle their heavy right skew; a small number of extremely high-calorie recipes would otherwise dominate tree splits.

The hyperparameters tuned are max_depth (controls overfitting — deeper trees memorise noise) and min_samples_split (regularises leaf-node splits). GridSearchCV with 3-fold CV selects max_depth=10 and min_samples_split=10.

The final model achieves a MAE of 4.645 minutes, drastically improving on the baseline MAE using the same held-out test set.

<br>

#  **Fairness Analysis**
To assess whether the model treats all recipes fairly, recipes in the test set were split into two groups by binarizing n_steps at the median of 9: simple recipes (<= 9 steps) and complex recipes (> 9 steps). This is a meaningful fairness question because complex, multi-step recipes tend to have more variable cook times, making them harder to predict.

RMSE was chosen as the evaluation metric, and the test statistic was defined as the difference between RMSE(complex) and RMSE(simple) (one-sided test). These were the hypotheses and significance level used to test this: 

**Null Hypothesis**: The model is fair. Its RMSE for simple complex recipes are roughly the same, and any observed difference is due to random chance 

**Alternative Hypothesis**: The model is unfair. Its RMSE for complex recipes is higher than its RMSE for simple recipes 

**Significance Level**: 0.05

The results do not support the alternative hypothesis. Simple recipes had an RMSE of 27.59 minutes, while complex recipes had an RMSE of 28.63 minutes, an observed difference of 1.03 minutes. A permutation test produced a p‑value of 0.068, which is above the 0.05 significance threshold, and the observed gap falls well within the middle of the permutation distribution rather than in its tail. The null hypothesis is therefore not rejected.

This means the model does not perform significantly worse on complex recipes, and the small difference in RMSE is likely due to random variation rather than systematic bias. The most plausible interpretation is that while complex recipes are naturally harder to predict, the model’s feature set, including n_steps, n_ingredients, and the binary time tags, provides enough structure for the model to handle both simple and complex recipes with comparable accuracy.

Here is the plot of the results:

<iframe 
  src="assets/fairness_permutation_test.html" 
  width="800" 
  height="500" 
  frameborder="0"
></iframe>
