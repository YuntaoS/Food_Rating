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
  src="assets/preptime-vs-rating.html"
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


We grouped the cleaned dataset by the `steps_bin` column, which classifies recipes as either "high" or "low" complexity based on their number of preparation steps. Then we calculated the average user rating for each group.

From the table above, we see that **low-complexity recipes have a slightly higher average rating (4.63) than high-complexity ones (4.62)**. Although the difference is minimal, it suggests that users may marginally prefer simpler recipes, possibly because they are quicker to prepare and easier to follow.



---

### 🧠 Interpreting Conflicting Trends

This tension between mean and median highlights a key statistical insight:

> **Average (mean) and distribution (spread or shape) can tell different stories.**

- Low-complexity recipes may appeal to a broader audience, leading to consistently high ratings.
- High-complexity recipes may be polarizing—receiving both very high and lower ratings depending on the cook’s experience and expectations.

These exploratory insights set the stage for our **hypothesis testing** in Step 4, where we formally examine whether recipe complexity significantly affects user ratings.


---

## Step 3: Assessment of Missingness

In this step, we explore where missing values occur in the dataset and whether their presence may be systematically related to observable variables—such as recipe complexity or nutrition. We focus particularly on the `review` and `average_rating` columns.

---

### 🧩 NMAR Analysis: Why Are Reviews Missing?

We believe that missing entries in the **`review`** column are **Not Missing At Random (NMAR)**.

Here's why: writing a review is a completely optional action, and whether a user chooses to write one likely depends on unobserved personal habits or preferences. For instance:

- A user may not write a review simply because they were in a hurry.
- Some users may never leave reviews regardless of recipe quality.
- Others may only review recipes that significantly impressed or disappointed them.

These behaviors are **not captured** by any of the existing features in our dataset (like `n_steps`, `minutes`, or `rating`). Therefore, we cannot model or predict the missingness based on observed data. The decision to write a review depends on **external**, user-level factors we don’t observe—such as login status, mood, or personality.

➡️ As a result, we categorize this missingness as **NMAR**.

---

### 📊 Missingness Dependency Tests

Next, we’ll check whether the empty spots in the **`rating`** column actually line up with any recipe traits.  We’ll compare two things: the recipe’s step count (`n_steps`) as a quick complexity gauge, and its protein content (`protein`) as a nutrition cue.  By running permutation tests on each feature, we can see if missing ratings pop up more (or less) often in certain groups or if they’re just random noise.


---

#### Dependency Test 1: `n_steps` and Rating

**Null Hypothesis (H₀)**: The missingness of `rating` does **not** depend on the number of steps in the recipe (`n_steps`).

**Alternative Hypothesis (H₁)**: The missingness of `rating` **does** depend on the number of steps.

**Test Statistic**: The absolute difference in the proportion of missing `rating` between the high-`n_steps` and low-`n_steps` groups, split by median.

**Significance Level**: 0.05

<iframe
  src="assets/kde-n_steps-by-rating_missing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

👀 **Observation**: Recipes missing a rating tend to have **fewer steps**, suggesting a potential relationship between complexity and user engagement.

---

### 🎲 Permutation Test: Step Count vs. Rating Missingness


<iframe
  src="assets/permutation-n_steps-vs-rating_missing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

- **Observed Δ**: −0.0093
- **p-value**: ~0.0000

The histogram above shows the null distribution of Δ missing-rate (high steps – low steps) produced by 1 000 random shuffles.  
Our observed difference (red dashed line) sits far in the left tail at **−0.0093**, and none of the shuffled differences reach that extreme (p ≈ 0.0000).

Because p < 0.05, we reject the null hypothesis: recipes with **more steps are noticeably less likely to be missing a rating**. In other words, rating missingness **does depend** on recipe complexity.

---

#### Dependency Test 2: `protein` and Rating

**Null Hypothesis (H₀)**: The missingness of `rating` does **not** depend on the protein content of the recipe.

**Alternative Hypothesis (H₁)**: The missingness of `rating` **does** depend on the protein content.

**Test Statistic**: The absolute difference in the mean protein content between recipes with missing vs. non-missing `rating`.

**Significance Level**: 0.05

<iframe
  src="assets/kde-protein-by-rating_missing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

⚠️ **Note**: To make this plot interpretable, we clipped the top 1% of outliers in protein values, which had extremely high grams but affected only a few data points.

<iframe
  src="assets/kde-protein-by-rating_missing_2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

---

### 🎲 Permutation Test: Protein vs. Rating Missingness

This permutation test compares the mean protein content between recipes with and without missing ratings.

<iframe
  src="assets/permutation-protein-vs-rating_missing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

- **Observed Δ**: +1.29g
- **p-value**: 0.20

The histogram shows the null distribution of the mean-protein difference generated by 1 000 random shuffles of the `rating_missing` labels.  
Our observed gap (red dashed line) is **+1.29 g**, with a p-value of **0.20**.  
Because p > 0.05, we **fail to reject the null hypothesis**—there’s no convincing evidence that a recipe’s protein content influences whether users leave a rating. In other words, rating missingness appears independent of protein.

---

### ✅ Final Summary

- **`review`** is **Not Missing At Random (NMAR)** due to personal user behaviors not reflected in the dataset.
- **`average_rating`** missingness is **related to recipe complexity** (`n_steps`) but **not** to nutritional value (`protein`).

These findings guide how we handle missing data in future modeling steps. Specifically, we may impute or model `rating` values with caution, but `review` missingness cannot be assumed to be predictable from existing data.

---

## Step 4: Hypothesis Testing

*We call a recipe **high-complexity** if its step count (`n_steps`) is **above** the median, and **low-complexity** if its step count is **below** the median.  (Ties at the median are dropped so the two groups don’t overlap.)*

---

### Hypotheses

| | Statement |
|---|---|
| **Null Hypothesis (H₀)** | People rate high-complexity and low-complexity recipes **the same on average**.  |
| **Alternative Hypothesis (H₁)** | The **mean rating differs** between the two complexity groups (direction agnostic). |

---

### Test setup  

* **Test statistic** 
  (difference in average `average_rating` between the two groups).  
* **Significance level** α = 0.05 (two-sided).  
* **Method** A **permutation test** with 1 000 label shuffles.  
  We randomly re-assign recipes to “high” / “low” while keeping group sizes fixed, recompute \(T\) each time, and compare the observed gap to this null distribution.

<iframe
  src="assets/permutation-complexity-vs-rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Result:**  
Observed Δ ( high-step – low-step recipes )  = –0.0050  
two-sided p-value                           = 0.2890

**Conclusion:**  
With a p-value of ≈ 0.29 (> 0.05), we fail to reject the null hypothesis. This means the small –0.005 difference in average rating between high-step and low-step recipes is well within what we’d expect from random variation—there’s no statistically significant evidence that recipes with more procedural steps are rated differently from simpler ones.

---

## Step 5: Framing a Prediction Problem

Our goal is to predict whether a newly published recipe will be well-received by users, based on only the information available at the time it’s posted. This helps authors and the platform understand early on whether a recipe is likely to perform well.

We frame this as a **binary classification** task by labeling recipes as **high-rated** (average rating ≥ 4.5) or **low-rated** (average rating ≤ 3.5), dropping recipes with average ratings in between to make the categories clearer. The target variable is `rating_bin`, which we derived in earlier steps.

As input features, we use information available before any user feedback: recipe complexity (`n_steps`, `minutes`), nutrition (`calories`, `protein`), and basic metadata such as the length of the title. We avoid using any post-rating data like review counts or tags.

This setup allows us to build a model that gives early feedback on a recipe’s potential popularity—before any reviews come in—helping surface quality recipes faster.

---

## Step 6: Baseline Model

To begin our modeling process, we built a baseline binary classifier that predicts whether a newly published recipe on Food.com will be **high-rated** (average rating ≥ 4.5) or **low-rated** (average rating ≤ 3.5), using only the information available at the time of publication. This helps establish a performance benchmark for more complex models later.

For our baseline model, we selected two quantitative features:

- **`n_steps`**: The number of procedural steps in the recipe. This acts as a proxy for recipe complexity, with the assumption that longer or more complicated recipes may influence how users feel about preparing them.
- **`protein`**: The protein content (in grams) per serving. This captures part of the nutritional profile, which might also shape user perception or satisfaction.

These features were chosen because they are available immediately upon recipe creation and do not rely on user interactions or reviews. Both are numerical, so we didn’t need to perform any categorical encoding.

We used **logistic regression** for our baseline classifier, implemented in a `sklearn` pipeline that included:

- **Standardization** of the numeric features using `StandardScaler()` to ensure they are on the same scale.
- **LogisticRegression()** as the modeling algorithm, since our goal is to perform binary classification.

To evaluate model performance, we used the **macro F1-score**. This metric is appropriate because the number of high-rated and low-rated recipes in our target label (`rating_bin`) is not perfectly balanced, and we want a metric that equally considers both precision and recall across classes.

After training and testing the model, our baseline classifier achieved a **macro F1-score of 0.4811** on the held-out test set. While this performance is modest, it provides a useful benchmark for future models. In the next steps, we will explore adding more features (e.g., `minutes`, `calories`, `name length`) and trying different algorithms to improve our predictive performance.

---

## Step 7: Final Model

To improve upon our baseline model, we engineered two additional features and performed hyperparameter tuning using `GridSearchCV`. The final model achieved an **F1 macro score of 0.4875**, a slight but meaningful improvement over the baseline score of **0.4811**.

---

### Features Added

We added four features to the original set (`n_steps`, `protein`, `minutes`, `submitted_year`) to help the model capture more meaningful patterns from the recipes:

- **`n_steps`**  
  This feature counts how many steps are required to complete a recipe. It serves as a proxy for **recipe complexity**—recipes with more steps may involve more elaborate techniques or ingredients, which could influence how users perceive and rate them.

- **`protein`**  
  This feature represents the protein content of a recipe (in grams). We included it because **nutritional value** might affect user ratings, especially for health-conscious users who tend to favor high-protein meals. It adds another dimension beyond taste or complexity.

- **`minutes`**  
  This feature represents the total time required to complete a recipe. We hypothesized that the amount of time needed may reflect a recipe’s complexity and effort, which could influence how people rate it.

- **`submitted_year`**  
  This is the year the recipe was submitted. It may help account for changes in user preferences or platform behavior over time. For example, older recipes might have accumulated more reviews and tend to be rated differently than newer ones.

---

### Modeling Pipeline

We created a `Pipeline` that:
- Scales all numeric features using `StandardScaler`
- Fits a `RandomForestClassifier`

To optimize model performance, we used `GridSearchCV` to search across the following hyperparameters:

```python
param_grid = {
    'clf__n_estimators': [100, 200],
    'clf__max_depth': [None, 10, 20],
    'clf__min_samples_split': [2, 5]
   }
```

Overall, these additions allowed the model to better account for recipe complexity, nutritional content, time effort, and temporal trends—all of which contribute to more accurate rating predictions.

---

## Step 8: Fairness Analysis

For our fairness analysis, we split the recipes into two groups based on step complexity. Recipes with steps above the median were labeled as high complexity, and those below were low complexity. We chose to evaluate precision as the metric because we want the model to be equally accurate in both groups — misclassifying a complex or simple recipe could mislead users about its quality.

Null Hypothesis: Our model is fair. Its precision for high-complexity and low-complexity recipes is roughly the same, and any differences are due to random chance.

Alternative Hypothesis: Our model is unfair. Its precision for one group (e.g. high-complexity) is significantly higher or lower than the other.

Test Statistic: Difference in precision (low complexity − high complexity)

Significance Level: 0.05

<iframe
  src="assets/fairness_precision_diff_by_step_complexity.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot above shows the result of our permutation test comparing the model’s precision between low- and high-complexity recipes. The red dashed line represents the observed precision difference (Δ = -0.0093), which lies far in the left tail of the null distribution generated by 1000 shuffles. This indicates that the model performs worse on low-complexity recipes. Since the p-value is approximately 0, we reject the null hypothesis and conclude that the model’s precision is not consistent across complexity levels — suggesting unfairness.
