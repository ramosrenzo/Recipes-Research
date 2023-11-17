# Exploring the Nexus Between Cook Time and Ratings in Recipes

A Data Science project exploring the relationship between a recipe's cooking time and its ratings, for DSC80 at UCSD.

**Authors:** Catherine Back, Lorenzo Ramos 

---

## Introduction

In this project, we are exploring the Recipes and Ratings datasets which are both provided to us from food.com. By analyzing 
this dataset, we are hoping to find the answer to the question: Do recipes with higher preparation time have higher ratings? 
for recipes?  By finding the answer to this question, we may be able to draw conclusions about why a recipe received a 
low or high rating just based on the preparation time of the recipe. 

The Recipes dataframe contains 83782 rows, meaning 83782 unique recipes, each given their own unique recipe id. 
The dataframe for ratings and reviews contain 731927 rows, which represent reviews for each (recipe) id. 
We are mainly interested in utilizing the columns: 'minutes' and 'rating'.  The 'minutes' column is found 
on the Recipes dataset and it uses an integer value to display the prep time for a recipe (in minutes). 
The 'rating' column is found on the Ratings dataset, and also uses an integer value to display ratings that 
has been given to a unique 'recipe_id'

---

## Cleaning and EDA

Before we can start the analysis of the recipe and rating data, we must clean it to ensure that our analysis 
is as smooth and accurate as possible. 

> The Merge 

We merged the recipe and the interaction data into one data frame to make the process of analysis easier. We 
merged the data frames on the columns that they share in common which are “recipe id” and “id”. 

> Fill in Missing Data Appropriately 

After exploring the columns to look for missing data, we decided to leave missing strings as the default because 
replacing them with empty strings is not the best way to handle this type of missing data. We did however notice 
that there are missing values for ratings which is important in our analysis, so we decided to fill the missing 
values with zeros. 

> Finding the Average Rating 

After combining the data frames and filling in missing values, we calculate the average ratings for each recipe 
with its unique ID. We calculate this to have averages rather than multiple ratings for each recipe because the 
average rating is more informative. 

> The Second Merge 

We then conduct a second merge with the original recipe data and the average ratings we calculated in the step 
before. We only care about the ratings from the interactions data set so this is why it is now left out of the 
further analysis. 

> The Time Bins 

The last step in the process of cleaning and preparing our data for analysis is to create bin intervals of 30 
minutes. For each interval of 30 minutes, each unique recipe is assigned a number according to the minutes of the 
recipe. For example, a recipe between 0 and 30 minutes is assigned bin 1, a recipe taking 31 to 60 minutes is 
assigned bin 2, etc. Our last bin is from 721 minutes to infinity. We decided to have this as our last bin as 
720 minutes represents 12 hours. 

> Cleaning Result 

Below is the cleaned dataframe (showing only the columns we are interested in using for our research). We kept 
id, contributor_id and the submitted columns incase we wanted to explore those columns further.

|     id | name                                 |   contributor_id | submitted   |   minutes |   average rating |
|-------:|:-------------------------------------|-----------------:|:------------|----------:|-----------------:|
| 333281 | 1 brownies in the world    best ever |           985201 | 2008-10-27  |        40 |                4 |
| 453467 | 1 in canada chocolate chip cookies   |          1848091 | 2011-04-11  |        45 |                5 |
| 306168 | 412 broccoli casserole               |            50969 | 2008-05-30  |        40 |                5 |
| 286009 | millionaire pound cake               |           461724 | 2008-02-12  |       120 |                5 |
| 475785 | 2000 meatloaf                        |          2202916 | 2012-03-06  |        90 |                5 |

---

### Univariate Analysis

> Average Rating Distribution 

In the chart below, we see the distribution of the average ratings for each unique recipe. It is left-skewed 
showing that there are many more recipes with a 5-star rating.

<iframe src="assets/rating_hist.html" width=800 height=600 frameBorder=0></iframe>

> Recipe Time Distribution 

This distribution below shows the number of unique recipes and which bin they are assigned to according to the 
minutes. There are a lot more recipes within the 0 to 90 minute mark; bins 1, 2, and 3. This shows a left-skew 
in the data and a significant cut-off in recipes that go over the 90-minute mark. 

<iframe src="assets/time_hist.html" width=800 height=600 frameBorder=0></iframe>

### Bivariate Analysis

> Average Rating Versus Preparation Time 

In the visual below, we see boxplots for each time bin and the average ratings. We see that the bins with the 
highest average rating median are the first 8 bins. We also see that the bins with the lowest medians 
are bins 10 through 19. 

<iframe src="assets/rating_time_box.html" width=800 height=600 frameBorder=0></iframe>

### Interesting Aggregates 

Here we have a pivot table with the average ratings and the average minutes, number of steps, and number 
of ingredients per rating. To condense the information to a legible pivot table, we decided to round the 
ratings their nearest whole numbers. This chart clearly shows that the average rating of 0 stars has the 
highest average number of minutes, the highest number of steps, and the highest number of ingredients.

|               |       0.0 |       1.0 |       2.0 |       3.0 |       4.0 |       5.0 |
|:--------------|----------:|----------:|----------:|----------:|----------:|----------:|
| minutes       | 225.776   | 115.954   | 145.74    | 115.706   | 101.079   | 113.631   |
| n_steps       |  11.554   |  10.6265  |  10.9105  |  10.3363  |   9.95176 |  10.0263  |
| n_ingredients |   9.44899 |   9.09877 |   9.32674 |   9.17365 |   9.28596 |   9.16872 | 

---

## Assessment of Missingness

### NMAR Analysis

The 'Review' column in the merged dataframe is NMAR because the chance that the value is missing does 
not depend on other columns, only on the piece of data itself. For example, some of the data from this column 
could be missing because the user who rated the recipe might have left it out if they were too lazy to write 
a review, or they just didn't feel that the recipe deserved a review along with the rating.

### Missingness Dependency

We are now going to test the dependency of missingness based on the recipe description and comparing that between 
the recipe cook time in minutes, and the recipe's number of steps.

> Description and Cooking Time

Null Hypothesis: The missingness of description does not depend on minutes 
Alternative Hypothesis: The missingness of description depends on minutes 
Test Statistic: Absolute difference of mean minutes between the two distributions

We ran permutation tests to simulate shuffling the missingness of description 1000 times in order to get 1000 
results of the mean absolute difference (visualized below).

<iframe src="assets/mins_dist.html" width=800 height=600 frameBorder=0></iframe>

After running the permutation tests, we then calculate the p-value to be 0.25.  Using a significance level of 0.05, 
we can see that 0.25 > 0.05, which means that we fail to reject the null hypothesis that description does not depend 
on minutes.  Based on the results, the missingness of the description is MCAR, since description is not dependent on 
minutes.

> Description and Number of Steps

Null Hypothesis: The missingness of description does not depend on the number of steps
Alternative Hypothesis: The missingness of description depends on the number of steps
Test Statistic: Absolute difference of mean number of steps between the two distributions

Once again, running 1000 permutation tests to get 1000 results of the mean absolute difference (visualized below).

<iframe src="assets/n_steps_dist.html" width=800 height=600 frameBorder=0></iframe>

After these permutation tests, the calculated p-value is 0.031, while still using a significance value of 0.05. 
Since 0.031 < 0.05, this means that we reject our null hypothesis that the missingness of description is not 
dependent on the number of steps.  Based on these results, the missingness of the description is MAR, 
due to the description being dependent to the number of steps in each recipe.

---

## Hypothesis Testing

> Null Hypothesis: The preparation time does not have a significant effect on the average ratings for recipes.

> Alternative Hypothesis: The preparation time significantly affects the average ratings for recipes.

Our test of choice is an independent double-sided t-test because we want to look at both sides of high ratings 
and low ratings and the time each side takes. We chose our statistical significance to be 0.05 because that is 
standard when conducting these tests. We chose our threshold to be the time bin of 3 because when looking at 
the distribution of the ordinal time, we see a sharp drop off after 90 minutes. 

The resulting p-value is 6.340444945728686e-33 which is below statistical significance. Since the p-value is 
below the statistical significance, we reject the null hypothesis. 

This result is reasonable because when people rate recipes they might have higher expectations for recipes that 
take a longer amount of time. The opposite might apply as well where recipes with shorter preparation time are 
lightly rated since they did not take up as much time therefore creating lower expectations. These assumptions 
are based on what we have observed with this specific dataset and our test. 

