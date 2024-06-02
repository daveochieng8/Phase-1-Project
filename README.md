## Exploratory Data Analysis for Microsoft's New Movie Studio
## Overview

In this project, we will perform exploratory data analysis (EDA) to generate insights for Microsoft, 
helping them decide what type of films to create for their new movie studio. We will use various datasets 
related to movies, analyze trends, and provide actionable recommendations based on our findings.

## Business Problem

Microsoft sees all the big companies creating original video content and they want to get in on the fun. 
They have decided to create a new movie studio but lack knowledge about creating movies. Our task is to explore 
what types of films are currently doing the best at the box office and translate those findings into actionable insights 
for Microsoft's new movie studio.

## Objectives

The objectives of this project are to:
1. Compare budget to profitability.
2. Identify the top-performing genres.
3. Assess profitability by genre.

## Data 

The available datasets are:
1. Box Office Mojo (bom_movie_gross.csv)
2. Rotten Tomatoes Movie Info (rt.movie_info.tsv)
3. Rotten Tomatoes Reviews (rt.reviews.tsv)
4. TMDB Movies (tmdb.movies.csv)
5. The Numbers Movie Budgets (tn.movie_budgets.csv)
6. IMDB Movie Basics (imdb.movie_basics)
7. IMDB Movie Ratings (imdb.movie_ratings)

### Datasets Chosen for Analysis
After inspecting the datasets, the following were selected for analysis as they contain the necessary information to meet the project objectives:

1. The Numbers Movie Budgets (tn.movie_budgets.csv)
   Contains comprehensive budget details and earnings, which are crucial for analyzing profitability.

3. IMDB Movie Basics (imdb.movie_basics)
   Provides detailed information on movie titles, genres, and runtime.
   
3. IMDB Movie Ratings (imdb.movie_ratings)
   Offers insights into movie popularity through ratings and votes.

## Exploratory Data Analysis (EDA)

After cleaning and merging the datasets, we performed exploratory data analysis (EDA) to understand the data better and extract actionable insights. Below is a summary of the EDA process:

#### Distribution of Production Budgets
![image](https://github.com/daveochieng8/Phase-1-Project/assets/58653689/951adef8-9f36-4117-b254-cd8b64fbe6d7)

The histogram shows the distribution of production budgets for movies in the dataset. This helps identify the most common budget ranges and outliers.

#### Distribution of Domestic Gross
![image](https://github.com/daveochieng8/Phase-1-Project/assets/58653689/c27e4b0b-0ce4-46fe-98c3-691fda3096b6)

The histogram shows the distribution of domestic gross earnings. This helps understand how much revenue movies typically make domestically.

#### Distribution of Worldwide Gross
![image](https://github.com/daveochieng8/Phase-1-Project/assets/58653689/6d121884-00c1-4abd-a8bd-47f39f5e0fc1)

The histogram shows the distribution of worldwide gross earnings. This helps understand global revenue patterns for movies.

#### Distribution of IMDB Ratings
![image](https://github.com/daveochieng8/Phase-1-Project/assets/58653689/2ef4b5a3-8979-4ccf-babf-352757b945bc)

The histogram shows the distribution of average IMDB ratings. This helps identify the typical rating range for movies and any outliers.

#### Production Budget vs. Domestic Gross
![image](https://github.com/daveochieng8/Phase-1-Project/assets/58653689/a9e641c4-fedb-4aa5-bbe4-a5918d4ea659)

The scatter plot shows that as budgets increase, domestic revenues also tend to go up.

#### Production Budget vs. Worldwide Gross
![image](https://github.com/daveochieng8/Phase-1-Project/assets/58653689/f0930e5e-227c-4919-879a-d11f620ff856)

The scatter plot suggests that as budgets increase, worldwide revenues also tend to go up.

### Multivariate Analysis: Heatmap of Correlations in tn_movie_budgets
![image](https://github.com/daveochieng8/Phase-1-Project/assets/58653689/c7687b92-1dbb-403f-9892-5b80582ba39c)

There is a strong positive correlation between production budget and gross revenues for both domestic and worldwide, and between gross revenues and profits.

### Identifying the most profitable genres
![image](https://github.com/daveochieng8/Phase-1-Project/assets/58653689/9a8bad5a-386b-4153-b4ed-49a26bfcaa71)

The bar chart shows the average domestic profit for each genre. This helps identify the most profitable genres domestically.

### Average Worldwide Profit by Genre
![image](https://github.com/daveochieng8/Phase-1-Project/assets/58653689/6eece0b7-2b45-4a6e-85d9-9f2d287c8380)

The genres with the tallest bars are the most profitable on a global scale. Animation, adventure and Sci-Fi have the tallest bars.
These genres generate the highest average worldwide profits.
### Conclusion and Recommendations
### Conclusion:
The analysis reveals that certain genres consistently perform better both domestically and worldwide. Action, Science Fiction, and Adventure films generally generate the highest average revenues and profits. Furthermore, the analysis of different budget ranges reveals that mid-range to high-range budgets often result in the most profitable movies.

### Recommendations:

**1. Prioritize Profitable Genres:** Concentrate your efforts and resources on developing films within the Action, Science Fiction, and Adventure genres, as the analysis indicates these genres consistently generate the highest profits.

**2. Strategic Budget Management:** When allocating budgets for film productions, aim for the mid range. This budget range has been shown to yield the highest profits. Steer clear of excessively high budgets unless the potential returns are projected to be remarkably high and well-justified.




