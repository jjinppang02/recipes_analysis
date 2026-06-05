# **Overview**
For the DSC 80 Spring Final Project, I chose to investigate the Recipes Data Set!

Author : Jacob Lee
<br>
<br>

# **Introduction and Question Identification**
When it comes to recipes, there is much that can be analyzed. One thing that I wanted to dive deeper into was the efficiency of recipes, measured in number of steps per minute. In order to do so, I was given two sets of data, `recipes` and `ratings`.  

<br>

`recipes` contained the following 12 columns with 83782 rows:
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


<br>

And `ratings` contained the following 5 columsn with 731927 rows:
| Column Name | Dtype | Description |
| --------- | -------- | -------- |
| `user_id` | float64 | User ID. |
| `recipe_id` | float64| Recipe ID. |
| `date` | object| Date of interaction. |
| `rating` | float64 | Rating given. |
| `review` | object | Review text. |

<br>

I later left-merged the two datasets together, left on `id` and right on `recipe_id` to get `df`, a dataframe containing 17 columns and 234429 rows.

A question that I wanted to specifically answer in this investigation was "Are higher ratings given to recipes with higher efficiency?" This was a question I wanted to examine because generally people believe ratings come solely from taste of the final product. However, I believe that the process of cooking the dish also plays a huge role in the rating process as a food reviewer/chef myself. Whether a recipe produced either really good or really poor quality food, one thing that stuck with me most was how efficient the recipes were. Some recipes I picked said only 60 minutes but half of that time would be spent peeling potatoes. Although it was fairly short, and the actual cooking process shorter, the amount of boredom and feeling of unproductiveness being stuck on one step was enough for me to bring down good dishes' ratings and ones that felt very smooth to breeze by I gave a higher rating than what I would have given based solely on taste. Due to this, I wanted to investigate if recipes' ratings were influenced.

So, for this, I picked `minutes`, `n_steps`, and `rating` for my investigation.

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


<iframe src="assets/distribution_plots.html" width="800" height="500" frameborder="0"></iframe>


