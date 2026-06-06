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

<table style="width:100%; border-collapse: collapse;">
  <thead>
    <tr>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Column Name</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Dtype</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td><code>name</code></td>
      <td>object</td>
      <td>Recipe name.</td>
    </tr>

    <tr>
      <td><code>id</code></td>
      <td>int64</td>
      <td>Recipe ID.</td>
    </tr>

    <tr>
      <td><code>minutes</code></td>
      <td>int64</td>
      <td>Minutes to prepare recipe.</td>
    </tr>

    <tr>
      <td><code>contributor_id</code></td>
      <td>int64</td>
      <td>User ID who submitted this recipe.</td>
    </tr>

    <tr>
      <td><code>submitted</code></td>
      <td>object</td>
      <td>Date recipe was submitted.</td>
    </tr>

    <tr>
      <td><code>tags</code></td>
      <td>object</td>
      <td>Food.com tags for recipe.</td>
    </tr>

    <tr>
      <td><code>nutrition</code></td>
      <td>object</td>
      <td>
        Nutrition information in the form 
        <code>[calories, total_fat%, sugar%, sodium%, protein%, sat_fat%, carbs%]</code>.
        PDV = percentage of daily value.
      </td>
    </tr>

    <tr>
      <td><code>n_steps</code></td>
      <td>int64</td>
      <td>Number of steps in recipe.</td>
    </tr>

    <tr>
      <td><code>steps</code></td>
      <td>object</td>
      <td>Text for recipe steps, in order.</td>
    </tr>

    <tr>
      <td><code>description</code></td>
      <td>object</td>
      <td>User‑provided description.</td>
    </tr>

    <tr>
      <td><code>ingredients</code></td>
      <td>object</td>
      <td>List of ingredients.</td>
    </tr>

    <tr>
      <td><code>n_ingredients</code></td>
      <td>int64</td>
      <td>Number of ingredients.</td>
    </tr>
  </tbody>
</table>

<br>

And `ratings` contained the following 5 columsn with 731927 rows:

<table style="width:100%; border-collapse: collapse;">
  <thead>
    <tr>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Column Name</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Dtype</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Description</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td><code>user_id</code></td>
      <td>float64</td>
      <td>User ID.</td>
    </tr>

    <tr>
      <td><code>recipe_id</code></td>
      <td>float64</td>
      <td>Recipe ID.</td>
    </tr>

    <tr>
      <td><code>date</code></td>
      <td>object</td>
      <td>Date of interaction.</td>
    </tr>

    <tr>
      <td><code>rating</code></td>
      <td>float64</td>
      <td>Rating given.</td>
    </tr>

    <tr>
      <td><code>review</code></td>
      <td>object</td>
      <td>Review text.</td>
    </tr>
  </tbody>
</table>


<br>

I later left-merged the two datasets together, left on `id` and right on `recipe_id` to get `df`, a dataframe containing 17 columns and 234429 rows.

A question that I wanted to specifically answer in this investigation was "Are higher ratings given to recipes with higher efficiency?" This was a question I wanted to examine because generally people believe ratings come solely from taste of the final product. However, I believe that the process of cooking the dish also plays a huge role in the rating process as a food reviewer/chef myself. Whether a recipe produced either really good or really poor quality food, one thing that stuck with me most was how efficient the recipes were. Some recipes I picked said only 60 minutes but half of that time would be spent peeling potatoes. Although it was fairly short, and the actual cooking process shorter, the amount of boredom and feeling of unproductiveness being stuck on one step was enough for me to bring down good dishes' ratings and ones that felt very smooth to breeze by I gave a higher rating than what I would have given based solely on taste. Due to this, I wanted to investigate if recipes' ratings were influenced.

So, for this, I picked `minutes`, `n_steps`, and `rating` for my investigation.

<br>

# **Data Cleaning**
Before I could fully dive into the analysis, I had to clean up the data to make it usable. First, I replaced all empty values with `NaN` values and then dropped all rows with NaN values. I also dropped rows with 0 as their rating as all ratings are in between 1-5, 0 for their number of ingredients, 0 for their number of steps, and 0 for their total minutes as they all indicate that there is an error with the recipe. After cleaning up the data, I also included two more columns, `avg_rating` and `efficiency`, where `avg_rating` represents that average rating the recipe received and `efficiency` the measurement of how efficient a recipe was represented by number of steps per minute according to the recipe. After dropping any possible further `NaN` values, I was left with a dataframe called `df_clean`. Although I included `avg_rating` to the dataframe, I will be using `rating` for the investigation.

Here is the `head` of `df_clean`:
  
<table style="width:100%; border-collapse: collapse;">
  <thead>
    <tr>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">name</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">id</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">minutes</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">contributor_id</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">submitted</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">tags</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">nutrition</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">n_steps</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">steps</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">description</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">ingredients</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">n_ingredients</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">user_id</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">recipe_id</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">date</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">rating</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">review</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">avg_rating</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">efficiency</th>
    </tr>
  </thead>

  <tbody>

    <!-- Row 1 -->
    <tr>
      <td><strong><a href="ca://s?q=show_brownies_row">1 brownies in the world best ever</a></strong></td>
      <td>333281</td>
      <td>40</td>
      <td>985201</td>
      <td>2008‑10‑27</td>
      <td>['60-minutes-or-less','time-to-make','course…']</td>
      <td>[138.4,10.0,50.0,3.0,3.0,19.0,6.0]</td>
      <td>10</td>
      <td>['heat the oven to 350f…']</td>
      <td>these are the most; chocolatey…</td>
      <td>['bittersweet chocolate','unsalted butter',…]</td>
      <td>9</td>
      <td>386585.0</td>
      <td>333281.0</td>
      <td>2008‑11‑19</td>
      <td>4.0</td>
      <td>These were pretty good…</td>
      <td>4.0</td>
      <td>0.250000</td>
    </tr>

    <!-- Row 2 -->
    <tr>
      <td><strong><a href="ca://s?q=show_cookie_row">1 in canada chocolate chip cookies</a></strong></td>
      <td>453467</td>
      <td>45</td>
      <td>1848091</td>
      <td>2011‑04‑11</td>
      <td>['60-minutes-or-less','time-to-make','cuisin…']</td>
      <td>[595.1,46.0,211.0,22.0,13.0,51.0,26.0]</td>
      <td>12</td>
      <td>['pre‑heat oven to 350…']</td>
      <td>this is the recipe we use at my school…</td>
      <td>['white sugar','brown sugar','salt','margar…']</td>
      <td>11</td>
      <td>424680.0</td>
      <td>453467.0</td>
      <td>2012‑01‑26</td>
      <td>5.0</td>
      <td>Originally I was gonna cut…</td>
      <td>5.0</td>
      <td>0.266667</td>
    </tr>

    <!-- Row 3 -->
    <tr>
      <td><strong><a href="ca://s?q=show_broccoli_row">412 broccoli casserole</a></strong></td>
      <td>306168</td>
      <td>40</td>
      <td>50969</td>
      <td>2008‑05‑30</td>
      <td>['60-minutes-or-less','time-to-make','course…']</td>
      <td>[194.8,20.0,6.0,32.0,22.0,36.0,3.0]</td>
      <td>6</td>
      <td>['preheat oven to 350…']</td>
      <td>since there are already 411 recipes…</td>
      <td>['frozen broccoli cuts','cream of chicken…']</td>
      <td>9</td>
      <td>29782.0</td>
      <td>306168.0</td>
      <td>2008‑12‑31</td>
      <td>5.0</td>
      <td>This was one of the best…</td>
      <td>5.0</td>
      <td>0.150000</td>
    </tr>

    <!-- Row 4 -->
    <tr>
      <td><strong><a href="ca://s?q=show_broccoli_row_2">412 broccoli casserole</a></strong></td>
      <td>306168</td>
      <td>40</td>
      <td>50969</td>
      <td>2008‑05‑30</td>
      <td>['60-minutes-or-less','time-to-make','course…']</td>
      <td>[194.8,20.0,6.0,32.0,22.0,36.0,3.0]</td>
      <td>6</td>
      <td>['preheat oven to 350…']</td>
      <td>since there are already 411 recipes…</td>
      <td>['frozen broccoli cuts','cream of chicken…']</td>
      <td>9</td>
      <td>1196280.0</td>
      <td>306168.0</td>
      <td>2009‑04‑13</td>
      <td>5.0</td>
      <td>I made this for my son's…</td>
      <td>5.0</td>
      <td>0.150000</td>
    </tr>

    <!-- Row 5 -->
    <tr>
      <td><strong><a href="ca://s?q=show_broccoli_row_3">412 broccoli casserole</a></strong></td>
      <td>306168</td>
      <td>40</td>
      <td>50969</td>
      <td>2008‑05‑30</td>
      <td>['60-minutes-or-less','time-to-make','course…']</td>
      <td>[194.8,20.0,6.0,32.0,22.0,36.0,3.0]</td>
      <td>6</td>
      <td>['preheat oven to 350…']</td>
      <td>since there are already 411 recipes…</td>
      <td>['frozen broccoli cuts','cream of chicken…']</td>
      <td>9</td>
      <td>768828.0</td>
      <td>306168.0</td>
      <td>2013‑08‑02</td>
      <td>5.0</td>
      <td>Loved this. Be sure to completely thaw…</td>
      <td>5.0</td>
      <td>0.150000</td>
    </tr>

  </tbody>
</table>

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

<table style="width:100%; border-collapse: collapse;">
  <thead>
    <tr>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Rating Group / Efficiency Quartiles</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Q1 (Not Efficient)</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Q2</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Q3</th>
      <th style="border-bottom: 2px solid #ccc; text-align: left;">Q4 (Very Efficient)</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>Low (&lt;3)</td>
      <td>3580</td>
      <td>3246</td>
      <td>2790</td>
      <td>2782</td>
    </tr>

    <tr>
      <td>Mid (3–4)</td>
      <td>9681</td>
      <td>10078</td>
      <td>8649</td>
      <td>8867</td>
    </tr>

    <tr>
      <td>Very High (&gt;4.5)</td>
      <td>41657</td>
      <td>44005</td>
      <td>41290</td>
      <td>42603</td>
    </tr>
  </tbody>
</table>

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

<iframe 
  src="assets/fairness_permutation_test.html" 
  width="800" 
  height="500" 
  frameborder="0"
></iframe>
