# RecipeAnalysis
UCSD DSC80 project 

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
# Data Cleaning <a name="datacleaning"></a>
Before we analyze the data we must first clean it and make it ready for analysis. The first step is to merge our two DataFrames into one. Both of them shared the column for the recipe ID, so we decided on that column, leaving us with a big data frame with both the recipe information and the reviews. 

The next step in our cleaning process is to fill all of the 0's in our rating section with NaN's, the reason being, that 0 stars do not exist on food.com, rather it gets filled as 0 if the user doesn't fill out that part of the form. This matters as many statistics can not be correctly calculated unless we exclude these 0's from our data and the best way to do that is to make them NaN. 

The last step is to add a column called ratings per recipe, this column is going to be assigned to every recipe and is the the overall mean of that particular recipe. 

Now that we have a cleaned data frame, we drop all of the columns that we do not need, leaving us with just the column's id, minutes, rating, and ratings per recipe. We need ID to differentiate every recipe, minutes and ratings are both of the variables that we will be examining, and we keep rating per recipe as a way to fill in missing values in our rating.
# Univariate Analysis <a name="univariateanalysis"></a>
<iframe src="assets/fig_rating.html" width=800 height=600 frameBorder=0></iframe>
<iframe src="assets/fig_minutes.html" width=800 height=600 frameBorder=0></iframe>
# Bivariate Analysis <a name="Bivariate Analysis"></a>
<iframe src="assets/box_plot.html" width=800 height=600 frameBorder=0></iframe>
<iframe src="assets/scatter_plot.html" width=800 height=600 frameBorder=0></iframe>
# Interesting Aggregates <a name="bivariateanalysis"></a>
# Hypothesis Testing <a name="hypothesistesting"></a>
# NMAR Analysis <a name="NMAR Analysis"></a>
# Missingness Dependency <a name="missingnessdependency"></a>
# Hypothesis Testing <a name="Hypothesis Testing"></a>
Our area of focus for this data set is to find the relationship between the cooking time and the rating that the recipe receives.

To answer this question, we compared the ratings of recipes that took longer compared to those that required a shorter amount of time. We found that the median time of recipes was 35 minutes and thus categorized recipes  as long if they took longer than 35 minutes (not inclusive), and short if they took less (inclusive). We will conduct a permutation test.

Null Hypothesis H0: Both long and short recipes receive the same scale of ratings.

Alternative Hypothesis H1: The time it takes to make a recipe changes the ratings that it receives. 

Test Statistic: We decided to use the Kolmogorov-Smirnov test, as it paints a better picture of what ratings a recipe receives. This is important because even if the means of two data sets are the same, does not mean that the ratings it receive are the same, the Kolmogorov-Smirnov test is designed to detect if the distribution of the sets is different.

Observed Kolmogorov-Smirnov test: 

HISTOGRAM GOES HERE

We ran a permutation 10,000 times, the red line signifies the Observed Kolmogorov-Smirnov test result. We also decided to have the significance level be 0.05

Hypothesis Test Conclusion
The p-value for the testing is 0.0, because that is lower than our significance threshold of 0.05, we can reject the null hypothesis. 

The reason this might be the case is that recipes take longer to make and require more investment, thus when a person is done, they are more likely to hold an opinion of the dish that is more extreme compared to a person who just got done making a shorter time required recipe. 
# Conclusion <a name="conclusion"></a>
