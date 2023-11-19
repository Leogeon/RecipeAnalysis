# RecipeAnalysis
**Authors**: [Wenbin Jiang](https://github.com/Leogeon), [Jevan Chahal](https://github.com/JevanC)

# Table of Contents
- [Introduction](#introduction)
- [Data Cleaning](#datacleaning)
- [Univariate Analysis](#univariateanalysis)
- [Bivariate Analysis](#bivariateanalysis)
- [Interesting Aggregates](#interestingaggregates)
- [NMAR Analysis](#nmaranalysis)
- [Missingness Dependency](#missingnessdependency)
- [Hypothesis Testing](#hypothesistesting)
- [Conclusion](#conclusion)

# Introduction <a name="introduction"></a>
Exploring the world of recipes and ratings, we're curious about something simple: Does the time it takes to cook a dish affect how much people like it? In our project, we're diving into a big collection of recipes to figure out if spending more or less time in the kitchen relates to how well a recipe is rated. We all want tasty food, but does the effort put into cooking impact how much we enjoy it? This impacts everyone and can change the way we view cooking. 

The data we receive is split into two different DataFrames. Our first data frame is called recipes, it contains 83782 rows and 12 columns: name, id, minutes, contributor_id, submitted, tags, nutrition, n_steps, steps, and a description. Recipes contain the recipe for each recipe. For our research, the only important columns are id so we can see which recipe is which, and minutes because it is central to our research question.

Our second data frame is called interactions and it contains 731927 rows and 5 columns: user_id, recipe_id, date, rating, and review. Interactions contain each review left by users. Of note, the important columns are going to be recipe_id so we can merge it with our recipes DataFrame and rating as that column is also essential to answering our research question. 

Our research question that we will be answering is: how does the length of time required to make a recipe impact the distribution of rating it receives? 

### recipes data frame contains (83782 rows):

|    Column  |  Description |
|-----------:|------------:|
|    'name' |       Recipe name |
|     'id' |       Recipe ID |
|      'minutes' |       Minutes to prepare recipe |
|     'submitted' |      User ID who submitted this recipe |
| 'tags' |      Food.com tags for recipe |
|     'nutrition' |      Nutrition information in this form [calries (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
|     'n_steps' |      Number of steps in the recipe |
|    'steps' |      Step-by-step instructions to follow |
|     'description' |      A description of what the recipe makes |

### reviews data frame contains (731927 rows):

|    Column  |  Description |
|-----------:|------------:|
|    'user_id' |       User ID |
|     'recipe_id' |       Recipe ID |
|      'date' |       Date of interaction|
|     'rating' |      Rating (1-5) |
| 'review' |      Review text |


# Data Cleaning <a name="datacleaning"></a>

Before we analyze the data we must first clean it and make it ready for analysis. The first step is to merge our two DataFrames into one. Both of them shared the column for the recipe ID, so we decided on that column, leaving us with a big data frame with both the recipe information and the reviews. 

The next step in our cleaning process is to fill all of the 0's in our rating section with NaN's, the reason being, that 0 stars do not exist on food.com, rather it gets filled as 0 if the user doesn't fill out that part of the form. This matters as many statistics can not be correctly calculated unless we exclude these 0's from our data and the best way to do that is to make them NaN. 

The last step is to add a column called ratings per recipe, we do this by taking the average rating of a recipe and then adding that value to every instance that the recipe was reviewed.

Now that we have a cleaned data frame, we drop all of the columns that we do not need, leaving us with just the column's ID, minutes, rating, and ratings per recipe. We need ID to differentiate every recipe, minutes and ratings are both of the variables that we will be examining. We also remove an entry that takes longer than a million minutes, as it seems to be a joke recipe rather than an actual one.

### Data frame after cleaning: 

|    Column  |  Description |
|-----------:|------------:|
|     'id' |       Recipe ID |
|      'minutes' |       time required for recipe|
|     'rating' |      Rating (1-5) |
| 'review' |      Review text |

# Univariate Analysis <a name="univariateanalysis"></a>
<iframe src="assets/fig_rating.html" width=800 height=600 frameBorder=0></iframe>

Here we can see a bar graph of the ratings, what we notice is that very few people gave one, two, or three-star ratings, and of the remaining reviews, a little bit more than 80% of the reviews were five-star reviews. 

<iframe src="assets/fig_minutes.html" width=800 height=600 frameBorder=0></iframe>

The histogram that represents the distribution of cooking times, in minutes, for recipes on a logarithmic scale. Most of the data is concentrated in the lower end of the scale, indicating that the majority of recipes have shorter cooking times.


# Bivariate Analysis <a name="Bivariate Analysis"></a>

<iframe src="assets/box_plot.html" width=800 height=600 frameBorder=0></iframe>

From our BoxPlot we can see that most reviews, regardless of rating, have a very similar amount of minutes required for the recipe. However, we notice that there are more outliers with four and five-star rating reviews. 

<iframe src="assets/scatter_plot.html" width=800 height=600 frameBorder=0></iframe>

From our scatterplot, we see that as we increase the number of stars, the amount of minutes a recipe takes gets wider (except from one start to two). This is especially true for five-star reviews which seem to have the largest variety of how long it takes. 

# Interesting Aggregates <a name="bivariateanalysis"></a>

| rating   |    Mean Minutes |   Median Minutes |   Standard Deviation |    Recipe Count |
| 1            | 99.672474 | 40 |  454.664734 | 2870  |
| 2           | 98.021537 | 40 |  383.093263 | 2368 |
| 3           | 87.497630 | 40 |  579.985847 | 7172 |
| 4            | 91.585038 | 35 |  1688.647502 | 37307 |
| 5            | 94.534319 | 35 |  1000.087548 | 169674 |

From this pivot table, we are better able to understand the relationship that minutes and ratings share. We can see that on average amount of time it takes for a recipe is slightly higher in lower star ratings, however, what we also notice is that the standard deviation of minutes is significantly higher for four and five-star reviews.

# NMAR Analysis <a name="NMAR Analysis"></a>

The 'rating' column in the dataset likely exhibits NMAR (Not Missing At Random) characteristics, as the absence of a rating (originally recorded as zero and replaced with NaN) could be influenced by users' dissatisfaction with the recipe, implying that the missingness is related to the unobserved rating itself. To better understand this missingness and potentially shift it to MAR (Missing At Random), additional data such as user feedback on why they chose not to rate, or detailed interaction data like time spent on the recipe page, would be invaluable. This data could reveal whether the absence of a rating is due to factors observable in the data, like recipe complexity, rather than the unobserved quality of the recipe.

# Missingness Dependency <a name="missingnessdependency"></a>
```
new['rating_missing'] = new['rating'].isnull().astype(int)
def permutation_test(df, column_to_test, missingness_column, n_permutations=1000):
    # Calculate the initial difference in means (for numeric columns) or proportions (for categorical columns)
    if df[column_to_test].dtype in [np.int64, np.float64]:  # Numeric column
        original_diff = df.groupby(missingness_column)[column_to_test].mean().diff().iloc[-1]
    else:  # Categorical column
        original_diff = df.groupby([column_to_test, missingness_column]).size().unstack().fillna(0)
        original_diff = original_diff.div(original_diff.sum(axis=1), axis=0)
        original_diff = original_diff.diff(axis=1).iloc[:, -1].abs().sum()
    ###Permutation
    diffs = []
    for _ in range(n_permutations):
        # Shuffling the missingness indicator
        shuffled_missingness = df[missingness_column].sample(frac=1, replace=False).reset_index(drop=True)
        df['shuffled_missingness'] = shuffled_missingness

        # Recalculating the difference
        if df[column_to_test].dtype in [np.int64, np.float64]:  # Numeric column
            diff = df.groupby('shuffled_missingness')[column_to_test].mean().diff().iloc[-1]
        else:  # Categorical column
            diff = df.groupby([column_to_test, 'shuffled_missingness']).size().unstack().fillna(0)
            diff = diff.div(diff.sum(axis=1), axis=0)
            diff = diff.diff(axis=1).iloc[:, -1].abs().sum()

        diffs.append(diff)

    ###Calculating the p-value
    if df[column_to_test].dtype in [np.int64, np.float64]:  # Numeric column
        p_value = np.mean([abs(diff) >= abs(original_diff) for diff in diffs])
    else:  # Categorical column
        p_value = np.mean([diff >= original_diff for diff in diffs])

    return original_diff, np.mean(diffs), p_value

"Permutation test for 'minutes' (numeric column)"
original_diff_minutes, mean_diff_minutes, p_value_minutes = permutation_test(new, 'minutes', 'rating_missing')

"Permutation test for 'contributor_id' (categorical column)"
original_diff_contributor, mean_diff_contributor, p_value_contributor = permutation_test(new, 'contributor_id', 'rating_missing')

print('original_diff_minutes: ', original_diff_minutes, '\n'
      'mean_diff_minutes: ', mean_diff_minutes, '\n'
      'p_value_minutes: ', p_value_minutes, '\n'
      'original_diff_contributor: ', original_diff_contributor, '\n'
      'mean_diff_contributor: ', mean_diff_contributor, '\n'
      'p_value_contributor: ', p_value_contributor)
```
results:

| original_diff_minutes | 51.45237039852127 |
| mean_diff_minutes | -1.2053569146154905 |
| p_value_minutes | 0.108  |
| original_diff_contributor | 17338796.675696477 |
| mean_diff_contributor | -59804.915568635064  |
| p_value_contributor | 0.0 |

original_diff_minutes:  51.45237039852127 
mean_diff_minutes:  -1.2053569146154905 
p_value_minutes:  0.108 
original_diff_contributor:  17338796.675696477 
mean_diff_contributor:  -59804.915568635064 
p_value_contributor:  0.0

## Interpretation
### 'minutes' (Numeric Column):
Original Difference in Means: 51.45
Average Difference in Means from Permutations: -2.09
P-value: 0.101
Interpretation:

The original difference in the means of 'minutes' between the groups (where 'rating' is missing and where it's not) is 51.45.
However, the average difference obtained from the permutation test is -2.09, which indicates that such a difference could occur by chance.
The p-value of 0.101 suggests that the observed difference is not statistically significant at a typical alpha level of 0.05. This implies that there is not enough evidence to conclude that the missingness in the 'rating' column depends on the 'minutes' column.

### 'contributor_id' (Categorical Column):
Original Difference in Proportions: 17,338,796.68
Average Difference in Proportions from Permutations: -40,884.52
P-value: 0.0
Interpretation:

The original difference in proportions for the 'contributor_id' is extremely high compared to the average difference obtained from the permutation test.
The p-value of 0.0 is highly significant, indicating that the observed difference is extremely unlikely to have occurred by chance.
This result suggests a strong dependency of the missingness in the 'rating' column on the 'contributor_id' column. In other words, the likelihood of a rating being missing seems to be related to which contributor submitted the recipe.
In summary, while there appears to be no significant relationship between the missingness in 'rating' and the 'minutes' column, there is a significant relationship between the missingness in 'rating' and the 'contributor_id' column. This insight can be particularly useful for understanding the patterns and potential biases in your data collection or entry process.

# Hypothesis Testing <a name="hypothesistesting></a>
Our area of focus for this data set is to find the relationship between the cooking time and the rating that the recipe receives.

To answer this question, we compared the ratings of recipes that took longer compared to those that required a shorter amount of time. We found that the median time of recipes was 35 minutes and thus categorized recipes  as long if they took longer than 35 minutes (not inclusive), and short if they took less (inclusive). We will conduct a permutation test.

Null Hypothesis H0: Both long and short recipes receive the same scale of ratings.

Alternative Hypothesis H1: The time it takes to make a recipe changes the ratings that it receives. 

Test Statistic: We decided to use the Kolmogorov-Smirnov test, as it paints a better picture of what ratings a recipe receives. This is important because even if the means of two data sets are the same, does not mean that the ratings it receive are the same, the Kolmogorov-Smirnov test is designed to detect if the distribution of the sets is different.

Observed Kolmogorov-Smirnov test: 

<iframe src="assets/fig.html" width=800 height=600 frameBorder=0></iframe>

We ran a permutation 10,000 times, the red line signifies the Observed Kolmogorov-Smirnov test result. We also decided to have the significance level be 0.05

# Conclusion <a name="conclusion"></a>

The p-value for the testing is 0.0, because that is lower than our significance threshold of 0.05, we can reject the null hypothesis. 

The reason this might be the case is that recipes take longer to make and require more investment, thus when a person is done, they are more likely to hold an opinion of the dish that is more extreme compared to a person who just got done making a shorter time required recipe. 
