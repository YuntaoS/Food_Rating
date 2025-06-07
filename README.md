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

We began our analysis by visualizing how recipe preparation time and number of steps relate to user ratings.

### ⏱️ Preparation Time vs. Average Rating

This scatter plot explores the relationship between preparation time (`minutes`) and average rating (`avg_rating`). While there is no strong correlation, we observe that recipes with extremely short or long preparation times exhibit greater variability in user ratings.

### 🍳 Number of Steps vs. Average Rating

This scatter plot illustrates the number of preparation steps (`n_steps`) versus the average rating. There appears to be a slight upward trend—recipes with more steps tend to receive marginally higher ratings, though the variance is substantial across all step counts.

---

### 🧮 Categorizing Recipe Complexity

To support our hypothesis testing, we engineered a new column called `complexity_level`, which classifies each recipe based on its preparation time and number of steps.

Specifically:
- A recipe is labeled **high complexity** if both `minutes` and `n_steps` are **above** their respective medians.
- A recipe is labeled **low complexity** if both values are **below** the medians.
- Recipes that are mixed (e.g., high time but low steps) are labeled **medium** and **excluded** from the hypothesis test.

---

### 📦 Complexity vs. Rating (Boxplot)

We created a boxplot comparing average ratings across different complexity levels. The plot suggests that **high-complexity recipes** tend to receive **slightly higher ratings** on average than low-complexity recipes. However, the spread within each group is fairly large, indicating variability in user preferences.

This categorization allows for structured comparisons and hypothesis testing, helping us assess whether recipe complexity has a statistically significant relationship with user satisfaction.

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

