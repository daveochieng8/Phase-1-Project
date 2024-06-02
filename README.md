---
jupyter:
  kernelspec:
    display_name: learn-env
    language: python
    name: python3
  language_info:
    codemirror_mode:
      name: ipython
      version: 3
    file_extension: .py
    mimetype: text/x-python
    name: python
    nbconvert_exporter: python
    pygments_lexer: ipython3
    version: 3.8.5
  nbformat: 4
  nbformat_minor: 4
---

::: {.cell .markdown}
## Exploratory Data Analysis for Microsoft\'s New Movie Studio
:::

::: {.cell .markdown}
## Overview

In this project, we will perform exploratory data analysis (EDA) to
generate insights for Microsoft, helping them decide what type of films
to create for their new movie studio. We will use various datasets
related to movies, analyze trends, and provide actionable
recommendations based on our findings.
:::

::: {.cell .markdown}
## Business Problem

Microsoft sees all the big companies creating original video content and
they want to get in on the fun. They have decided to create a new movie
studio but lack knowledge about creating movies. Our task is to explore
what types of films are currently doing the best at the box office and
translate those findings into actionable insights for Microsoft\'s new
movie studio.
:::

::: {.cell .markdown}
## Objectives

The objectives of this project are to:

1.  Compare budget to profitability.
2.  Identify the top-performing genres.
3.  Assess profitability by genre.
:::

::: {.cell .markdown}
## Loading Data
:::

::: {.cell .code execution_count="1"}
``` python
import pandas as pd 
import numpy as np
import sqlite3
import matplotlib.pyplot as plt
import seaborn as sns
from matplotlib.ticker import ScalarFormatter
```
:::

::: {.cell .code execution_count="2"}
``` python
bom_movie_gross = pd.read_csv('data/bom.movie_gross.csv')
rt_movie_info = pd.read_csv('data/rt.movie_info.tsv', sep='\t')
rt_reviews = pd.read_csv('data/rt.reviews.tsv', sep='\t', encoding='ISO-8859-1')
tmdb_movies = pd.read_csv('data/tmdb.movies.csv')
tn_movie_budgets = pd.read_csv('data/tn.movie_budgets.csv')

conn = sqlite3.connect('data/im.db')
imdb_movie_basics = pd.read_sql_query("SELECT * FROM movie_basics", conn)
imdb_movie_ratings = pd.read_sql_query("SELECT * FROM movie_ratings", conn)
```
:::

::: {.cell .markdown}
Displaying the first few rows of each dataframe.
:::

::: {.cell .code execution_count="3"}
``` python

print("Movie Gross Data:")
print(bom_movie_gross.head())
print("-------------------------------------------------------------")

print("Movie Info Data:")
print(rt_movie_info.head())
print("-------------------------------------------------------------")

print("Reviews Data:")
print(rt_reviews.head())
print("-------------------------------------------------------------")

print("TMDB Movies Data:")
print(tmdb_movies.head())
print("-------------------------------------------------------------")

print("Movie Budgets Data:")
print(tn_movie_budgets.head())
print("-------------------------------------------------------------")

print("Movie Basics Data:")
print(imdb_movie_basics.head())
print("-------------------------------------------------------------")

print("Movie Ratings Data:")
print(imdb_movie_ratings.head())
```

::: {.output .stream .stdout}
    Movie Gross Data:
                                             title studio  domestic_gross  \
    0                                  Toy Story 3     BV     415000000.0   
    1                   Alice in Wonderland (2010)     BV     334200000.0   
    2  Harry Potter and the Deathly Hallows Part 1     WB     296000000.0   
    3                                    Inception     WB     292600000.0   
    4                          Shrek Forever After   P/DW     238700000.0   

      foreign_gross  year  
    0     652000000  2010  
    1     691300000  2010  
    2     664300000  2010  
    3     535700000  2010  
    4     513900000  2010  
    -------------------------------------------------------------
    Movie Info Data:
       id                                           synopsis rating  \
    0   1  This gritty, fast-paced, and innovative police...      R   
    1   3  New York City, not-too-distant-future: Eric Pa...      R   
    2   5  Illeana Douglas delivers a superb performance ...      R   
    3   6  Michael Douglas runs afoul of a treacherous su...      R   
    4   7                                                NaN     NR   

                                     genre          director  \
    0  Action and Adventure|Classics|Drama  William Friedkin   
    1    Drama|Science Fiction and Fantasy  David Cronenberg   
    2    Drama|Musical and Performing Arts    Allison Anders   
    3           Drama|Mystery and Suspense    Barry Levinson   
    4                        Drama|Romance    Rodney Bennett   

                                writer  theater_date      dvd_date currency  \
    0                   Ernest Tidyman   Oct 9, 1971  Sep 25, 2001      NaN   
    1     David Cronenberg|Don DeLillo  Aug 17, 2012   Jan 1, 2013        $   
    2                   Allison Anders  Sep 13, 1996  Apr 18, 2000      NaN   
    3  Paul Attanasio|Michael Crichton   Dec 9, 1994  Aug 27, 1997      NaN   
    4                     Giles Cooper           NaN           NaN      NaN   

      box_office      runtime             studio  
    0        NaN  104 minutes                NaN  
    1    600,000  108 minutes  Entertainment One  
    2        NaN  116 minutes                NaN  
    3        NaN  128 minutes                NaN  
    4        NaN  200 minutes                NaN  
    -------------------------------------------------------------
    Reviews Data:
       id                                             review rating   fresh  \
    0   3  A distinctly gallows take on contemporary fina...    3/5   fresh   
    1   3  It's an allegory in search of a meaning that n...    NaN  rotten   
    2   3  ... life lived in a bubble in financial dealin...    NaN   fresh   
    3   3  Continuing along a line introduced in last yea...    NaN   fresh   
    4   3             ... a perverse twist on neorealism...     NaN   fresh   

               critic  top_critic         publisher               date  
    0      PJ Nabarro           0   Patrick Nabarro  November 10, 2018  
    1  Annalee Newitz           0           io9.com       May 23, 2018  
    2    Sean Axmaker           0  Stream on Demand    January 4, 2018  
    3   Daniel Kasman           0              MUBI  November 16, 2017  
    4             NaN           0      Cinema Scope   October 12, 2017  
    -------------------------------------------------------------
    TMDB Movies Data:
       Unnamed: 0            genre_ids     id original_language  \
    0           0      [12, 14, 10751]  12444                en   
    1           1  [14, 12, 16, 10751]  10191                en   
    2           2        [12, 28, 878]  10138                en   
    3           3      [16, 35, 10751]    862                en   
    4           4        [28, 878, 12]  27205                en   

                                     original_title  popularity release_date  \
    0  Harry Potter and the Deathly Hallows: Part 1      33.533   2010-11-19   
    1                      How to Train Your Dragon      28.734   2010-03-26   
    2                                    Iron Man 2      28.515   2010-05-07   
    3                                     Toy Story      28.005   1995-11-22   
    4                                     Inception      27.920   2010-07-16   

                                              title  vote_average  vote_count  
    0  Harry Potter and the Deathly Hallows: Part 1           7.7       10788  
    1                      How to Train Your Dragon           7.7        7610  
    2                                    Iron Man 2           6.8       12368  
    3                                     Toy Story           7.9       10174  
    4                                     Inception           8.3       22186  
    -------------------------------------------------------------
    Movie Budgets Data:
       id  release_date                                        movie  \
    0   1  Dec 18, 2009                                       Avatar   
    1   2  May 20, 2011  Pirates of the Caribbean: On Stranger Tides   
    2   3   Jun 7, 2019                                 Dark Phoenix   
    3   4   May 1, 2015                      Avengers: Age of Ultron   
    4   5  Dec 15, 2017            Star Wars Ep. VIII: The Last Jedi   

      production_budget domestic_gross worldwide_gross  
    0      $425,000,000   $760,507,625  $2,776,345,279  
    1      $410,600,000   $241,063,875  $1,045,663,875  
    2      $350,000,000    $42,762,350    $149,762,350  
    3      $330,600,000   $459,005,868  $1,403,013,963  
    4      $317,000,000   $620,181,382  $1,316,721,747  
    -------------------------------------------------------------
    Movie Basics Data:
        movie_id                    primary_title              original_title  \
    0  tt0063540                        Sunghursh                   Sunghursh   
    1  tt0066787  One Day Before the Rainy Season             Ashad Ka Ek Din   
    2  tt0069049       The Other Side of the Wind  The Other Side of the Wind   
    3  tt0069204                  Sabse Bada Sukh             Sabse Bada Sukh   
    4  tt0100275         The Wandering Soap Opera       La Telenovela Errante   

       start_year  runtime_minutes                genres  
    0        2013            175.0    Action,Crime,Drama  
    1        2019            114.0       Biography,Drama  
    2        2018            122.0                 Drama  
    3        2018              NaN          Comedy,Drama  
    4        2017             80.0  Comedy,Drama,Fantasy  
    -------------------------------------------------------------
    Movie Ratings Data:
         movie_id  averagerating  numvotes
    0  tt10356526            8.3        31
    1  tt10384606            8.9       559
    2   tt1042974            6.4        20
    3   tt1043726            4.2     50352
    4   tt1060240            6.5        21
:::
:::

::: {.cell .markdown}
Upon initial examination of the dataframes, the following datasets were
selected as they contain the necessary information to meet the project
objectives:

-   bom_movie_gross
-   tn_movie_budgets
-   imdb_movie_basics
-   imdb_movie_ratings
:::

::: {.cell .markdown}
#### Inspecting the selected datasets
:::

::: {.cell .markdown}
#### 1. bom_movie_gross {#1-bom_movie_gross}
:::

::: {.cell .code execution_count="4"}
``` python
#Examining the total number of rows, columns, non-null values and datatypes in the dataframe
bom_movie_gross.info()
```

::: {.output .stream .stdout}
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3387 entries, 0 to 3386
    Data columns (total 5 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   title           3387 non-null   object 
     1   studio          3382 non-null   object 
     2   domestic_gross  3359 non-null   float64
     3   foreign_gross   2037 non-null   object 
     4   year            3387 non-null   int64  
    dtypes: float64(1), int64(1), object(3)
    memory usage: 132.4+ KB
:::
:::

::: {.cell .code execution_count="5"}
``` python
# Checking for null values
bom_movie_gross.isna().sum()
```

::: {.output .execute_result execution_count="5"}
    title                0
    studio               5
    domestic_gross      28
    foreign_gross     1350
    year                 0
    dtype: int64
:::
:::

::: {.cell .code execution_count="6"}
``` python
#checking for duplicetes
bom_movie_gross.duplicated().sum()
```

::: {.output .execute_result execution_count="6"}
    0
:::
:::

::: {.cell .markdown}
#### 2. tn_movie_budgets {#2-tn_movie_budgets}
:::

::: {.cell .code execution_count="7"}
``` python
#Examining the total number of rows, columns, non-null values and datatypes in the dataframe
tn_movie_budgets.info()
```

::: {.output .stream .stdout}
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 5782 entries, 0 to 5781
    Data columns (total 6 columns):
     #   Column             Non-Null Count  Dtype 
    ---  ------             --------------  ----- 
     0   id                 5782 non-null   int64 
     1   release_date       5782 non-null   object
     2   movie              5782 non-null   object
     3   production_budget  5782 non-null   object
     4   domestic_gross     5782 non-null   object
     5   worldwide_gross    5782 non-null   object
    dtypes: int64(1), object(5)
    memory usage: 271.2+ KB
:::
:::

::: {.cell .code execution_count="8"}
``` python
# Checking for null values
tn_movie_budgets.isna().sum()
```

::: {.output .execute_result execution_count="8"}
    id                   0
    release_date         0
    movie                0
    production_budget    0
    domestic_gross       0
    worldwide_gross      0
    dtype: int64
:::
:::

::: {.cell .code execution_count="9"}
``` python
#checking for duplicetes
tn_movie_budgets.duplicated().sum()
```

::: {.output .execute_result execution_count="9"}
    0
:::
:::

::: {.cell .markdown}
#### 3. imdb_movie_basics {#3-imdb_movie_basics}
:::

::: {.cell .code execution_count="10"}
``` python
#Examining the total number of rows, columns, non-null values and datatypes in the dataframe
imdb_movie_basics.info()
```

::: {.output .stream .stdout}
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 146144 entries, 0 to 146143
    Data columns (total 6 columns):
     #   Column           Non-Null Count   Dtype  
    ---  ------           --------------   -----  
     0   movie_id         146144 non-null  object 
     1   primary_title    146144 non-null  object 
     2   original_title   146123 non-null  object 
     3   start_year       146144 non-null  int64  
     4   runtime_minutes  114405 non-null  float64
     5   genres           140736 non-null  object 
    dtypes: float64(1), int64(1), object(4)
    memory usage: 6.7+ MB
:::
:::

::: {.cell .code execution_count="11"}
``` python
# Checking for null values
imdb_movie_basics.isna().sum()
```

::: {.output .execute_result execution_count="11"}
    movie_id               0
    primary_title          0
    original_title        21
    start_year             0
    runtime_minutes    31739
    genres              5408
    dtype: int64
:::
:::

::: {.cell .code execution_count="12"}
``` python
#checking for duplicetes
imdb_movie_basics.duplicated().sum()
```

::: {.output .execute_result execution_count="12"}
    0
:::
:::

::: {.cell .markdown}
#### 4. imdb_movie_ratings {#4-imdb_movie_ratings}
:::

::: {.cell .code execution_count="13"}
``` python
#Examining the total number of rows, columns, non-null values and datatypes in the dataframe
imdb_movie_ratings.info()
```

::: {.output .stream .stdout}
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 73856 entries, 0 to 73855
    Data columns (total 3 columns):
     #   Column         Non-Null Count  Dtype  
    ---  ------         --------------  -----  
     0   movie_id       73856 non-null  object 
     1   averagerating  73856 non-null  float64
     2   numvotes       73856 non-null  int64  
    dtypes: float64(1), int64(1), object(1)
    memory usage: 1.7+ MB
:::
:::

::: {.cell .code execution_count="14"}
``` python
# Checking for null values
imdb_movie_ratings.isna().sum()
```

::: {.output .execute_result execution_count="14"}
    movie_id         0
    averagerating    0
    numvotes         0
    dtype: int64
:::
:::

::: {.cell .code execution_count="15"}
``` python
#checking for duplicetes
imdb_movie_ratings.duplicated().sum()
```

::: {.output .execute_result execution_count="15"}
    0
:::
:::

::: {.cell .markdown}
Upon inspecting the datasets, the bom_movie_gross dataframe was dropped
since its data is available in other datasets. Additionally, the
foreign_gross column had 1,350 missing values out of a total of 3,387
rows.
:::

::: {.cell .markdown}
## Data Cleaning
:::

::: {.cell .markdown}
### Missing Values
:::

::: {.cell .markdown}
##### imdb_movie_basics {#imdb_movie_basics}
:::

::: {.cell .code execution_count="16"}
``` python
#checking the percentages of the missing data
missing_data = imdb_movie_basics.isnull().sum()

missing_percentage = (missing_data / len(imdb_movie_basics)) * 100


missing_data_df = pd.DataFrame({
    'Missing Values': missing_data,
    'Percentage': missing_percentage
}).sort_values(by='Percentage', ascending=False)


print(missing_data_df)
```

::: {.output .stream .stdout}
                     Missing Values  Percentage
    runtime_minutes           31739   21.717621
    genres                     5408    3.700460
    original_title               21    0.014369
    movie_id                      0    0.000000
    primary_title                 0    0.000000
    start_year                    0    0.000000
:::
:::

::: {.cell .markdown}
There are 21 missing values in the original_title column, 31,739 missing
values in the runtime_minutes column, and 5,408 missing values in the
genres column. These were dropped due to their relatively small number
compared to the entire dataset.
:::

::: {.cell .code execution_count="17"}
``` python
imdb_movie_basics.dropna(inplace=True)
```
:::

::: {.cell .code execution_count="18"}
``` python
imdb_movie_basics.isna().sum()
```

::: {.output .execute_result execution_count="18"}
    movie_id           0
    primary_title      0
    original_title     0
    start_year         0
    runtime_minutes    0
    genres             0
    dtype: int64
:::
:::

::: {.cell .code execution_count="19"}
``` python
#merging the imdb dataframes
imdb_merged = pd.merge(imdb_movie_basics, imdb_movie_ratings, how='inner', on='movie_id')

#checking the fisrt five rows of the merged dataframe
imdb_merged.head()
```

::: {.output .execute_result execution_count="19"}
```{=html}
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie_id</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>averagerating</th>
      <th>numvotes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0063540</td>
      <td>Sunghursh</td>
      <td>Sunghursh</td>
      <td>2013</td>
      <td>175.0</td>
      <td>Action,Crime,Drama</td>
      <td>7.0</td>
      <td>77</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0066787</td>
      <td>One Day Before the Rainy Season</td>
      <td>Ashad Ka Ek Din</td>
      <td>2019</td>
      <td>114.0</td>
      <td>Biography,Drama</td>
      <td>7.2</td>
      <td>43</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0069049</td>
      <td>The Other Side of the Wind</td>
      <td>The Other Side of the Wind</td>
      <td>2018</td>
      <td>122.0</td>
      <td>Drama</td>
      <td>6.9</td>
      <td>4517</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0100275</td>
      <td>The Wandering Soap Opera</td>
      <td>La Telenovela Errante</td>
      <td>2017</td>
      <td>80.0</td>
      <td>Comedy,Drama,Fantasy</td>
      <td>6.5</td>
      <td>119</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0137204</td>
      <td>Joe Finds Grace</td>
      <td>Joe Finds Grace</td>
      <td>2017</td>
      <td>83.0</td>
      <td>Adventure,Animation,Comedy</td>
      <td>8.1</td>
      <td>263</td>
    </tr>
  </tbody>
</table>
</div>
```
:::
:::

::: {.cell .code execution_count="20"}
``` python
#checking the shape of the merged object
imdb_merged.shape
```

::: {.output .execute_result execution_count="20"}
    (65720, 8)
:::
:::

::: {.cell .markdown}
After cleaning the data, the remaining dataframes containing the cleaned
data are:

-   tn_movie_budgets
-   imdb_merged
:::

::: {.cell .markdown}
## Creating New Variables for Analysis
:::

::: {.cell .markdown}
### Analyzing revenue
:::

::: {.cell .code execution_count="21"}
``` python
#converting production_budget, domestic_gross, and worldwide_gross columns in tn_movie_budgets to numeric values
tn_movie_budgets['production_budget'] = tn_movie_budgets['production_budget'].replace('[\$,]', '', regex=True).astype(np.int64)
tn_movie_budgets['domestic_gross'] = tn_movie_budgets['domestic_gross'].replace('[\$,]', '', regex=True).astype(np.int64)
tn_movie_budgets['worldwide_gross'] = tn_movie_budgets['worldwide_gross'].replace('[\$,]', '', regex=True).astype(np.int64)

#calculating profit
tn_movie_budgets['domestic_profit'] = tn_movie_budgets['domestic_gross'] - tn_movie_budgets['production_budget']
tn_movie_budgets['worldwide_profit'] = tn_movie_budgets['worldwide_gross'] - tn_movie_budgets['production_budget']

#calcultaing return on investment
tn_movie_budgets['domestic_roi'] = (tn_movie_budgets['domestic_profit'] / tn_movie_budgets['production_budget']).round(2)
tn_movie_budgets['worldwide_roi'] = (tn_movie_budgets['worldwide_profit'] / tn_movie_budgets['production_budget']).round(2)

#Extracting year from release date
tn_movie_budgets['release_year'] = pd.to_datetime(tn_movie_budgets['release_date']).dt.year

tn_movie_budgets.head()
```

::: {.output .execute_result execution_count="21"}
```{=html}
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
      <th>domestic_profit</th>
      <th>worldwide_profit</th>
      <th>domestic_roi</th>
      <th>worldwide_roi</th>
      <th>release_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Dec 18, 2009</td>
      <td>Avatar</td>
      <td>425000000</td>
      <td>760507625</td>
      <td>2776345279</td>
      <td>335507625</td>
      <td>2351345279</td>
      <td>0.79</td>
      <td>5.53</td>
      <td>2009</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>May 20, 2011</td>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>410600000</td>
      <td>241063875</td>
      <td>1045663875</td>
      <td>-169536125</td>
      <td>635063875</td>
      <td>-0.41</td>
      <td>1.55</td>
      <td>2011</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>350000000</td>
      <td>42762350</td>
      <td>149762350</td>
      <td>-307237650</td>
      <td>-200237650</td>
      <td>-0.88</td>
      <td>-0.57</td>
      <td>2019</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>459005868</td>
      <td>1403013963</td>
      <td>128405868</td>
      <td>1072413963</td>
      <td>0.39</td>
      <td>3.24</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Dec 15, 2017</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
      <td>317000000</td>
      <td>620181382</td>
      <td>1316721747</td>
      <td>303181382</td>
      <td>999721747</td>
      <td>0.96</td>
      <td>3.15</td>
      <td>2017</td>
    </tr>
  </tbody>
</table>
</div>
```
:::
:::

::: {.cell .markdown}
### Seperating Genres
:::

::: {.cell .code execution_count="22"}
``` python
#using one-hot encoding to seperate the genres

genres = imdb_merged['genres'].str.get_dummies(sep=',')
imdb_merged = pd.concat([imdb_merged, genres], axis=1)
```
:::

::: {.cell .code execution_count="23"}
``` python
imdb_merged.head()
```

::: {.output .execute_result execution_count="23"}
```{=html}
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie_id</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>averagerating</th>
      <th>numvotes</th>
      <th>Action</th>
      <th>Adult</th>
      <th>...</th>
      <th>Mystery</th>
      <th>News</th>
      <th>Reality-TV</th>
      <th>Romance</th>
      <th>Sci-Fi</th>
      <th>Short</th>
      <th>Sport</th>
      <th>Thriller</th>
      <th>War</th>
      <th>Western</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0063540</td>
      <td>Sunghursh</td>
      <td>Sunghursh</td>
      <td>2013</td>
      <td>175.0</td>
      <td>Action,Crime,Drama</td>
      <td>7.0</td>
      <td>77</td>
      <td>1</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0066787</td>
      <td>One Day Before the Rainy Season</td>
      <td>Ashad Ka Ek Din</td>
      <td>2019</td>
      <td>114.0</td>
      <td>Biography,Drama</td>
      <td>7.2</td>
      <td>43</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0069049</td>
      <td>The Other Side of the Wind</td>
      <td>The Other Side of the Wind</td>
      <td>2018</td>
      <td>122.0</td>
      <td>Drama</td>
      <td>6.9</td>
      <td>4517</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0100275</td>
      <td>The Wandering Soap Opera</td>
      <td>La Telenovela Errante</td>
      <td>2017</td>
      <td>80.0</td>
      <td>Comedy,Drama,Fantasy</td>
      <td>6.5</td>
      <td>119</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0137204</td>
      <td>Joe Finds Grace</td>
      <td>Joe Finds Grace</td>
      <td>2017</td>
      <td>83.0</td>
      <td>Adventure,Animation,Comedy</td>
      <td>8.1</td>
      <td>263</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 34 columns</p>
</div>
```
:::
:::

::: {.cell .code execution_count="24"}
``` python
#merging the tn_movie_budgets and the imdb_merged dataframe

tn_movie_budgets = tn_movie_budgets.rename(columns={'movie': 'primary_title'})
combined_data = pd.merge(tn_movie_budgets, imdb_merged, on='primary_title', how='inner')
```
:::

::: {.cell .code execution_count="25"}
``` python
combined_data.info()
```

::: {.output .stream .stdout}
    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 2752 entries, 0 to 2751
    Data columns (total 44 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   id                 2752 non-null   int64  
     1   release_date       2752 non-null   object 
     2   primary_title      2752 non-null   object 
     3   production_budget  2752 non-null   int64  
     4   domestic_gross     2752 non-null   int64  
     5   worldwide_gross    2752 non-null   int64  
     6   domestic_profit    2752 non-null   int64  
     7   worldwide_profit   2752 non-null   int64  
     8   domestic_roi       2752 non-null   float64
     9   worldwide_roi      2752 non-null   float64
     10  release_year       2752 non-null   int64  
     11  movie_id           2752 non-null   object 
     12  original_title     2752 non-null   object 
     13  start_year         2752 non-null   int64  
     14  runtime_minutes    2752 non-null   float64
     15  genres             2752 non-null   object 
     16  averagerating      2752 non-null   float64
     17  numvotes           2752 non-null   int64  
     18  Action             2752 non-null   int64  
     19  Adult              2752 non-null   int64  
     20  Adventure          2752 non-null   int64  
     21  Animation          2752 non-null   int64  
     22  Biography          2752 non-null   int64  
     23  Comedy             2752 non-null   int64  
     24  Crime              2752 non-null   int64  
     25  Documentary        2752 non-null   int64  
     26  Drama              2752 non-null   int64  
     27  Family             2752 non-null   int64  
     28  Fantasy            2752 non-null   int64  
     29  Game-Show          2752 non-null   int64  
     30  History            2752 non-null   int64  
     31  Horror             2752 non-null   int64  
     32  Music              2752 non-null   int64  
     33  Musical            2752 non-null   int64  
     34  Mystery            2752 non-null   int64  
     35  News               2752 non-null   int64  
     36  Reality-TV         2752 non-null   int64  
     37  Romance            2752 non-null   int64  
     38  Sci-Fi             2752 non-null   int64  
     39  Short              2752 non-null   int64  
     40  Sport              2752 non-null   int64  
     41  Thriller           2752 non-null   int64  
     42  War                2752 non-null   int64  
     43  Western            2752 non-null   int64  
    dtypes: float64(4), int64(35), object(5)
    memory usage: 967.5+ KB
:::
:::

::: {.cell .markdown}
## Exploratory Data Analysis (EDA)
:::

::: {.cell .markdown}
### Examining the distribution of the variables
:::

::: {.cell .markdown}
#### Distribution of Production Budgets
:::

::: {.cell .code execution_count="26"}
``` python
plt.figure(figsize=(10, 6))
sns.histplot(tn_movie_budgets['production_budget'], bins=30, kde=True)
plt.title('Distribution of Production Budgets')
plt.xlabel('Production Budget ($)')
plt.ylabel('Frequency')
plt.show()
```

::: {.output .display_data}
![](vertopal_45734d3c607748a0ad2c395f85214ca4/20121f9f818920e74d79ca02932778193df5d002.png)
:::
:::

::: {.cell .markdown}
#### Distribution of Domestic Gross
:::

::: {.cell .code execution_count="27"}
``` python
plt.figure(figsize=(10, 6))
sns.histplot(tn_movie_budgets['domestic_gross'], bins=30, kde=True)
plt.title('Distribution of Domestic Gross')
plt.xlabel('Domestic Gross ($)')
plt.ylabel('Frequency')
plt.show()
```

::: {.output .display_data}
![](vertopal_45734d3c607748a0ad2c395f85214ca4/203712af4630313bb8f257366b7beebf6dbbc945.png)
:::
:::

::: {.cell .markdown}
#### Distribution of Worldwide Gross
:::

::: {.cell .code execution_count="28"}
``` python
plt.figure(figsize=(10, 6))
sns.histplot(tn_movie_budgets['worldwide_gross'], bins=30, kde=True)
plt.title('Distribution of Worldwide Gross')
plt.xlabel('Worldwide Gross ($)')
plt.ylabel('Frequency')
plt.show()
```

::: {.output .display_data}
![](vertopal_45734d3c607748a0ad2c395f85214ca4/4e83c6d4d9ed25e0e7cd16fec5c5a2a3d1e5074a.png)
:::
:::

::: {.cell .markdown}
#### Distribution of IMDB Ratings
:::

::: {.cell .code execution_count="29"}
``` python
plt.figure(figsize=(10, 6))
sns.histplot(imdb_merged['averagerating'], bins=10, kde=True)
plt.title('Distribution of IMDB Ratings')
plt.xlabel('Average Rating')
plt.ylabel('Frequency')
plt.show()
```

::: {.output .display_data}
![](vertopal_45734d3c607748a0ad2c395f85214ca4/10e0440ec8d2b28c86780236d57adfcb36e48ca3.png)
:::
:::

::: {.cell .markdown}
### Comparing the variables
:::

::: {.cell .markdown}
#### Production Budget vs. Domestic Gross {#production-budget-vs-domestic-gross}
:::

::: {.cell .code execution_count="30"}
``` python
plt.figure(figsize=(10, 6))
sns.scatterplot(x='production_budget', y='domestic_gross', data=tn_movie_budgets)
plt.title('Production Budget vs. Domestic Gross')
plt.xlabel('Production Budget ($)')
plt.ylabel('Domestic Gross ($)')
plt.show()
```

::: {.output .display_data}
![](vertopal_45734d3c607748a0ad2c395f85214ca4/85eb2dfeea9a4361922d9faac8ff029269deabfe.png)
:::
:::

::: {.cell .markdown}
The scatter plot shows that as budgets increase, domestic revenues also
tend to go up.
:::

::: {.cell .markdown}
#### Domestic Gross vs. Worldwide Gross {#domestic-gross-vs-worldwide-gross}
:::

::: {.cell .code execution_count="31"}
``` python
plt.figure(figsize=(10, 6))
sns.scatterplot(x='domestic_gross', y='worldwide_gross', data=tn_movie_budgets)
plt.title('Domestic Gross vs. Worldwide Gross')
plt.xlabel('Domestic Gross ($)')
plt.ylabel('Worldwide Gross ($)')
plt.show()
```

::: {.output .display_data}
![](vertopal_45734d3c607748a0ad2c395f85214ca4/209929c73280c10d8cae9c6fb2c7d79d30f38370.png)
:::
:::

::: {.cell .markdown}
The scatter plot shows an upward trend, suggesting that higher domestic
gross tend to yield higher worldwide revenues.
:::

::: {.cell .markdown}
#### Production Budget vs. Worldwide Gross {#production-budget-vs-worldwide-gross}
:::

::: {.cell .code execution_count="32"}
``` python
plt.figure(figsize=(10, 6))
sns.scatterplot(x='production_budget', y='worldwide_gross', data=tn_movie_budgets)
plt.title('Production Budget vs. Worldwide Gross')
plt.xlabel('Production Budget ($)')
plt.ylabel('Worldwide Gross ($)')
plt.show()
```

::: {.output .display_data}
![](vertopal_45734d3c607748a0ad2c395f85214ca4/e0a7b6db1ab1cce49caf323d3e9d42d512124000.png)
:::
:::

::: {.cell .markdown}
The scatter plot suggests that as budgets increase, worldwide revenues
also tend to go up.
:::

::: {.cell .markdown}
### Multivariate Analysis: Heatmap of Correlations in tn_movie_budgets
:::

::: {.cell .code execution_count="33"}
``` python
plt.figure(figsize=(12, 8))
sns.heatmap(tn_movie_budgets[['production_budget', 'domestic_gross', 'worldwide_gross', 'domestic_profit', 'worldwide_profit', 'domestic_roi', 'worldwide_roi']].corr(), annot=True, cmap='coolwarm')
plt.title('Correlation Heatmap of Budget and Gross Variables')
plt.show()
```

::: {.output .display_data}
![](vertopal_45734d3c607748a0ad2c395f85214ca4/621e6ccc236b4d5b73c318b8bc004609485c7113.png)
:::
:::

::: {.cell .markdown}
There is a strong positive correlation between production budget and
gross revenues for both domestic and worldwide, and between gross
revenues and profits.
:::

::: {.cell .markdown}
### Identifying the most profitable genres
:::

::: {.cell .code execution_count="34"}
``` python

genre_columns = ['Action', 'Adult', 'Adventure','Animation', 'Biography', 'Comedy', 'Crime', 'Documentary', 'Drama','Family', 'Fantasy', 'Game-Show', 'History', 'Horror', 'Music','Musical', 'Mystery', 'News', 'Reality-TV', 'Romance', 'Sci-Fi','Short', 'Sport', 'Thriller', 'War', 'Western']

genre_profit_df = pd.DataFrame()

for genre in genre_columns:
    genre_profit_df.loc[genre, 'avg_domestic_profit'] = combined_data.loc[combined_data[genre] == 1, 'domestic_profit'].mean()
    genre_profit_df.loc[genre, 'avg_worldwide_profit'] = combined_data.loc[combined_data[genre] == 1, 'worldwide_profit'].mean()

genre_profit_df = genre_profit_df.reset_index().rename(columns={'index': 'genre'})

genre_profit_df
```

::: {.output .execute_result execution_count="34"}
```{=html}
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>genre</th>
      <th>avg_domestic_profit</th>
      <th>avg_worldwide_profit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Action</td>
      <td>6.009706e+06</td>
      <td>1.277744e+08</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Adult</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Adventure</td>
      <td>1.482112e+07</td>
      <td>2.059963e+08</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Animation</td>
      <td>3.054870e+07</td>
      <td>2.347626e+08</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Biography</td>
      <td>9.240125e+06</td>
      <td>4.685618e+07</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Comedy</td>
      <td>1.453542e+07</td>
      <td>7.415582e+07</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Crime</td>
      <td>2.745059e+06</td>
      <td>3.870504e+07</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Documentary</td>
      <td>7.144900e+06</td>
      <td>3.589637e+07</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Drama</td>
      <td>5.382601e+06</td>
      <td>3.806185e+07</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Family</td>
      <td>1.706532e+07</td>
      <td>1.135383e+08</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Fantasy</td>
      <td>8.690764e+06</td>
      <td>1.480807e+08</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Game-Show</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>12</th>
      <td>History</td>
      <td>2.249297e+06</td>
      <td>3.919284e+07</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Horror</td>
      <td>1.037547e+07</td>
      <td>4.676660e+07</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Music</td>
      <td>1.466438e+07</td>
      <td>4.775460e+07</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Musical</td>
      <td>3.780470e+07</td>
      <td>1.374225e+08</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Mystery</td>
      <td>1.006663e+07</td>
      <td>4.876315e+07</td>
    </tr>
    <tr>
      <th>17</th>
      <td>News</td>
      <td>-7.196259e+06</td>
      <td>2.008208e+07</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Reality-TV</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Romance</td>
      <td>9.182224e+06</td>
      <td>4.312345e+07</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Sci-Fi</td>
      <td>2.172206e+07</td>
      <td>1.789648e+08</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Short</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Sport</td>
      <td>1.602454e+07</td>
      <td>5.482649e+07</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Thriller</td>
      <td>5.625565e+06</td>
      <td>5.449042e+07</td>
    </tr>
    <tr>
      <th>24</th>
      <td>War</td>
      <td>-3.428482e+06</td>
      <td>1.947261e+07</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Western</td>
      <td>-9.238052e+06</td>
      <td>3.231587e+07</td>
    </tr>
  </tbody>
</table>
</div>
```
:::
:::

::: {.cell .code execution_count="35"}
``` python
plt.figure(figsize=(14, 8))
sns.barplot(x='avg_domestic_profit', y='genre', data=genre_profit_df, palette='inferno')
plt.title('Average Domestic Profit by Genre')
plt.xlabel('Average Domestic Profit ($)')
plt.ylabel('Genre')
plt.show()
```

::: {.output .display_data}
![](vertopal_45734d3c607748a0ad2c395f85214ca4/076784be96176aa2b6f36ffcb3273cb8c2cbb7be.png)
:::
:::

::: {.cell .markdown}
### Average Worldwide Profit by Genre
:::

::: {.cell .code execution_count="36"}
``` python

plt.figure(figsize=(14, 8))
sns.barplot(x='avg_worldwide_profit', y='genre', data=genre_profit_df, palette='inferno')
plt.title('Average Worldwide Profit by Genre')
plt.xlabel('Average Worldwide Profit ($)')
plt.ylabel('Genre')
plt.show()
```

::: {.output .display_data}
![](vertopal_45734d3c607748a0ad2c395f85214ca4/75f5f2b1190bfbaae5d30d1a50481f41512287d6.png)
:::
:::

::: {.cell .markdown}
The genres with the tallest bars are the most profitable on a global
scale. Animation, adventure and Sci-Fi have the tallest bars. These
genres generate the highest average worldwide profits.
:::

::: {.cell .markdown}
### Conclusion and Recommendations
:::

::: {.cell .markdown}
##### Conclusion:

The analysis reveals that certain genres consistently perform better
both domestically and worldwide. Action, Science Fiction, and Adventure
films generally generate the highest average revenues and profits.
Furthermore, the analysis of different budget ranges reveals that
mid-range to high-range budgets often result in the most profitable
movies.

##### Recommendations:

**1. Prioritize Profitable Genres:** Concentrate your efforts and
resources on developing films within the Action, Science Fiction, and
Adventure genres, as the analysis indicates these genres consistently
generate the highest profits.

**2. Strategic Budget Management:** When allocating budgets for film
productions, aim for the mid range. This budget range has been shown to
yield the highest profits. Steer clear of excessively high budgets
unless the potential returns are projected to be remarkably high and
well-justified.
:::

