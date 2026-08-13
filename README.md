Markdown

<img width="512" height="512" alt="Movie Database logo" src="https://github.com/user-attachments/assets/d1ae6632-d69a-4760-ba44-608cf4a4830c" />


# TMDB Movie Data Analysis

An easy-to-read analysis of 10,000+ movies (1960–2015) using **Python**. This project looks at what makes movies make money, which genres perform best, and how movie trends changed over time.

---

## 1. What is This Project About?
This project uses data from **The Movie Database (TMDb)** to answer basic business questions about the movie industry. The main goal is to find out what drives box office success and profit.

---

## 2. Key Questions Asked
* **Money:** Does spending more money on a movie mean making more money back?
* **Genres:** Which movie types (like Action or Animation) make the most money?
* **Ratings:** Do highly-rated movies always become popular and make the most money?
* **Time:** How have movie lengths and the number of movies made changed over the years?

---

## 3. About the Data
* **File:** `tmdb-movies.csv`
* **Total Movies:** 10,866 movies
* **Main Information:** Title, Budget, Revenue, Ratings, Popularity, Genres, Release Date, and Runtime.

---

## 4. How the Data Was Cleaned
Before analyzing the data, it was cleaned using **Python**:
* Removed repeated (duplicate) movies.
* Filled in missing information like missing director or actor names with `'Unknown'`.
* Filtered out movies with `$0` recorded budget or revenue so calculations stayed accurate.
* Created new columns for **Profit** (`Revenue - Budget`) and **ROI** (Return on Investment).
* Split lists of genres so each genre could be counted individually.

---

## 5. Python Code Used

### Step A: Load and Clean Data
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Load dataset
df = pd.read_csv('tmdb-movies.csv')

# 1. Remove duplicate movies
df.drop_duplicates(subset=['imdb_id'], inplace=True)

# 2. Fill missing text columns
df.fillna({'cast': 'Unknown', 'director': 'Unknown', 'genres': 'Unknown'}, inplace=True)

# 3. Fix dates
df['release_date'] = pd.to_datetime(df['release_date'])
df['release_month'] = df['release_date'].dt.month_name()

# 4. Remove zero values and calculate Profit & ROI
df_financial = df[(df['budget_adj'] > 0) & (df['revenue_adj'] > 0)].copy()
df_financial['profit_adj'] = df_financial['revenue_adj'] - df_financial['budget_adj']
df_financial['roi'] = df_financial['profit_adj'] / df_financial['budget_adj']
Step B: Budget vs. Revenue Code
Python
# Check relationship between budget and revenue
plt.figure(figsize=(8, 5))
sns.regplot(data=df_financial, x='budget_adj', y='revenue_adj', scatter_kws={'alpha': 0.3}, line_kws={'color': 'red'})
plt.title('Budget vs. Revenue')
plt.xlabel('Budget ($)')
plt.ylabel('Revenue ($)')
plt.savefig('visuals/budget_vs_revenue.png', bbox_inches='tight')
plt.show()

# Print correlation score
print("Correlation:", df_financial['budget_adj'].corr(df_financial['revenue_adj']))
Step C: Genre Analysis Code
Python
# Separate movies with multiple genres
df_genres = df_financial.assign(genres=df_financial['genres'].str.split('|')).explode('genres')

# Group by genre and find average revenue
top_genres = df_genres.groupby('genres')['revenue_adj'].mean().sort_values(ascending=False)

# Plot top genres
plt.figure(figsize=(10, 5))
sns.barplot(x=top_genres.head(10).values, y=top_genres.head(10).index, palette='Blues_r')
plt.title('Top 10 Genres by Average Revenue')
plt.xlabel('Average Revenue ($)')
plt.ylabel('Genre')
plt.savefig('visuals/top_genres_revenue.png', bbox_inches='tight')
plt.show()
6. What We Found Out (Results)
Big Budgets = Big Revenue: Movies with higher budgets usually bring in higher overall sales at the box office.

Top Money-Making Genres: Animation, Adventure, and Fantasy make the highest average revenue per movie.

Best Value for Money: Horror and Mystery movies cost less to make but bring back the highest percentage of profit (ROI).

Ratings vs. Popularity: Good reviews do not automatically mean high sales—some popular blockbusters have average user scores.

Movie Length: Most movies made today stay between 95 and 110 minutes.

7. Recommendations
For Low-Risk Investors: Focus on Horror and Mystery films. They require smaller budgets and offer the highest return on investment (ROI).

For Major Studios: Invest high budgets in Animation and Adventure projects, as these genres consistently generate the highest total box office revenue.

For Runtime Planning: Keep runtime around 90 to 110 minutes to balance viewer engagement with maximum theater screening times.

For Marketing Strategy: Do not rely solely on critical ratings. Focus early marketing efforts on building audience popularity and awareness before release.
