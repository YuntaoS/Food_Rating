# Dish Complexity vs. Rating

This project explores the relationship between the complexity of a dish and its rating.

This work is part of DSC 80 at UC San Diego.

## Team Members
- Yuntao Shan
- Yidong Shi

---

## Introduction

Food is one of the most universally loved aspects of life. Across cultures and generations, it brings people together, evokes emotion, and sparks creativity. There are countless ways to prepare a single dish, and new recipes are created every day as people experiment with different ingredients and techniques to delight their families and friends. In American cuisine, regional styles reflect this diversity in cooking complexity. For example, **Texas-style barbecue** is known for its slow-smoked meats that take hours to prepare, reflecting a high-complexity, time-intensive tradition. In contrast, dishes like **California avocado toast** or **New York deli sandwiches** embrace speed and simplicity while still delivering bold flavors. As the culinary world continues to evolve, so do the ways people approach cooking at home. Some prefer quick and easy meals that save time, while others find joy in crafting complex, multi-step recipes. This variety leads us to an intriguing question: **Does the complexity of a recipe influence how much users enjoy it?**

### Dataset Overview

Our analysis uses two datasets derived from Food.com, covering user-submitted recipes and reviews from 2008 onward.

---


#### `RAW_recipes.csv` (**83,782 rows × 12 columns**)

This dataset includes information about each recipe submitted by users. Each row represents a single recipe and contains metadata including preparation time, nutritional content, and steps.

| Column           | Description                                                                 |
|------------------|-----------------------------------------------------------------------------|
| `'name'`           | Recipe name                                                                 |
| `'id'`             | Recipe ID                                                                   |
| `'minutes'`        | Minutes to prepare recipe                                                   |
| `'contributor_id'` | User ID who submitted this recipe                                           |
| `'submitted'`      | Date recipe was submitted                                                   |
| `'tags'`           | Food.com tags for recipe                                                    |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), <br>sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; <br>PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe                                                   |
| `'steps'`          | Text for recipe steps, in order                                             |
| `'description'`    | User-provided description                                                   |

---

#### `interactions.csv` (**731,927 rows × 5 columns**)

This dataset contains user interactions with recipes in the form of ratings and reviews. Each row corresponds to one user’s interaction with one recipe.

| Column     | Description             |
|------------|-------------------------|
| `'user_id'`  | User ID                 |
| `'recipe_id'`| Recipe ID               |
| `'date'`     | Date of interaction     |
| `'rating'`   | Rating given            |
| `'review'`   | Review text             |

---

In this section, we test whether users rate **high-complexity** and **low-complexity** recipes differently on Food.com. We define recipe complexity based on the number of steps (`n_steps`): recipes with more steps than the dataset median are labeled as **high complexity**, while those with fewer steps are labeled **low complexity**. (Recipes with step counts exactly equal to the median are excluded to maintain distinct groups.)

To ensure valid results, we only include recipes with a non-missing `average_rating` and use a permutation test to determine whether the observed difference in ratings between the two groups is statistically significant.

### Data Columns Information Cleaned (**83782 rows x 9 columns**)

| Column Name        | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| `name`             | The title of the recipe                                                     |
| `id`               | Unique identifier for the recipe                                            |
| `minutes`          | Total time (in minutes) required to prepare the recipe                      |
| `submitted`        | The date the recipe was submitted                                           |
| `n_steps`          | Number of individual preparation steps in the recipe                        |
| `average_rating`   | Mean rating given by users                                                  |
| `calories`         | Total calorie content per serving                                           |
| `protein`          | Protein content per serving (in grams)                                      |
| `steps_bin`        | Complexity label: `'high'` if `n_steps` > median, `'low'` if below median   |

This structured dataset allows us to isolate the effect of complexity (`steps_bin`) on average user ratings while keeping other variables available for future modeling or exploratory analysis.

---

## Step 2: Data Cleaning and Exploratory Data Analysis

We began our analysis by exploring how recipe complexity—measured by preparation time and number of steps—relates to user satisfaction, as captured by average recipe ratings.

---

### ⏱️ Preparation Time vs. Average Rating

This scatter plot explores the relationship between preparation time (`minutes`) and average rating (`average_rating`). While there is no strong correlation, we observe that recipes with extremely short or long preparation times exhibit greater variability in user ratings. This suggests that preparation time alone doesn't fully determine user satisfaction.

<iframe
  src="assets/preparation-time-vs-average-recipe-rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

---

### 🍳 Number of Steps vs. Average Rating

This scatter plot illustrates the number of preparation steps (`n_steps`) versus the average rating. There appears to be a slight upward trend—recipes with more steps tend to receive marginally higher ratings, though the variance is substantial across all step counts.

<iframe
  src="assets/number-of-steps-vs-average-recipe-rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This supports the intuition that more elaborate recipes may be perceived as more professional or rewarding, but again, simplicity remains valuable for many users.

---

### 🧮 Categorizing Recipe Complexity

To support hypothesis testing, we created a new column called `complexity_level`, which categorizes each recipe based on both preparation time and number of steps:
- A recipe is labeled **high complexity** if both `minutes` and `n_steps` are **above** their respective medians.
- A recipe is labeled **low complexity** if both are **below** the medians.
- Recipes that fall in between (e.g., high time but low steps) are labeled **medium** and excluded from hypothesis testing.

<iframe
  src="assets/proportion-of-recipes-by-step-complexity.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This donut chart shows the distribution of recipes by complexity level. About 55% are low-complexity and 45% high-complexity, allowing us to compare the two groups fairly without major class imbalance.

---

### 📈 Average Number of Steps by Year

We also analyzed how the complexity of user-submitted recipes has changed over time. The chart below shows a gradual upward trend in the average number of steps, indicating that users may be sharing more detailed or complex recipes over the years.

<iframe
  src="assets/average-number-of-steps-by-year.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This trend suggests that Food.com users may be increasingly interested in more intricate cooking processes, or that the platform’s user base has shifted toward more experienced cooks.

---

### 🧪 Interesting Aggregates: Do Simpler Recipes Get Better Ratings?

To dive deeper, we computed the average user rating for high- and low-complexity recipes using the `steps_bin` column (which labels recipes as "high" or "low" based on number of steps relative to the median).

| Complexity Level | Average Rating |
|------------------|----------------|
| High             | 4.62           |
| Low              | 4.63           |

We observe that **low-complexity recipes have a slightly higher average rating (4.63) than high-complexity ones (4.62)**. Although this 0.01 difference is small, it’s statistically measurable and offers insight into user behavior.

<iframe
  src="assets/average-rating-by-step-complexity.html"
  width="600"
  height="500"
  frameborder="0"
></iframe>

This may suggest that users **marginally prefer simpler recipes**, possibly because they are faster, easier to follow, and more accessible for casual cooking.

However, this finding appears to contradict what we saw in the **boxplot below**, where **high-complexity recipes seem to have slightly higher median ratings**.

---

### 📦 Complexity Level vs. Rating Distribution (Boxplot)

This boxplot visualizes the full distribution of average ratings across complexity levels. While high-complexity recipes show a slightly higher median rating, they also exhibit a wider spread, indicating more variance in how users rate complex recipes.

<iframe
  src="assets/complexity-level-vs-rating-boxplot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

---

### 🧠 Interpreting Conflicting Trends

This tension between mean and median highlights a key statistical insight:

> **Average (mean) and distribution (spread or shape) can tell different stories.**

- Low-complexity recipes may appeal to a broader audience, leading to consistently high ratings.
- High-complexity recipes may be polarizing—receiving both very high and lower ratings depending on the cook’s experience and expectations.

These exploratory insights set the stage for our **hypothesis testing** in Step 4, where we formally examine whether recipe complexity significantly affects user ratings.


---

## Step 3: Assessment of Missingness

*(To be completed in a future update.)*

---

## Step 4: Hypothesis Testing

We sought to determine whether **recipe complexity** has a statistically significant effect on **user ratings**.

To do so, we categorized recipes as follows:

- **High-complexity:** Both `minutes` and `n_steps` are above their respective medians.
- **Low-complexity:** Both `minutes` and `n_steps` are below their respective medians.

### 🧪 Hypotheses

- **Null Hypothesis (H₀):**  
  There is no difference in the average rating between high-complexity and low-complexity recipes.

- **Alternative Hypothesis (H₁):**  
  There is a significant difference in the average rating between high-complexity and low-complexity recipes.

We used the **difference in average rating** between the two groups as our test statistic and conducted a **permutation test** with 1000 iterations.

---

### 📊 Results

- **Observed difference in average rating:** `-0.0050`  
- **P-value:** `0.8710`

---

### 📌 Conclusion

Since the p-value is significantly greater than the 0.05 threshold, we **fail to reject the null hypothesis**.  
This suggests that there is **no statistically significant evidence** that high-complexity recipes receive higher ratings than low-complexity recipes.  
In fact, the observed difference was slightly negative, indicating that **users may not prefer complex recipes more**—or at least, not in a statistically meaningful way.

