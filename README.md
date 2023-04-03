# Netflix's Top 10 Analysis
<p align="center">
  <img src="https://user-images.githubusercontent.com/125743309/229554977-0806c267-acc8-494b-9263-5841d4e5e2a6.png" alt="Description of the image">
</p>

## 📖 Background
The Netflix Top 10 charts represent the most popular movies and TV series, with millions of viewers around the globe. Understanding what makes the biggest hits is crucial to making more hits.

## 💪 Challenge
Explore the dataset to understand the most common attributes of popular Netflix content. Your published notebook should contain a short report on the popular content, including summary statistics, visualizations, statistical models, and text describing any insights you found.

## 💾 The data
There are three datasets taken from [Netflix Top 10](https://top10.netflix.com).
- `all_weeks_global`: This contains the weekly top 10 list for movies (films) and TV series at a global level.
- `all_weeks_countries`: This contains the weekly top 10 list for movies (films) and TV series by country.
- `most_popular`: All-time most popular content by number of hours viewed in the first 28 days from launch.

# Top Weekly Global Movies on Netflix 


```python
# create a dataframe named named world from all_weeks_global postgresql table
import pandas as pd
df = pd.read_csv('all-weeks-global.csv')
world  = df
world.head(5)
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>week</th>
      <th>category</th>
      <th>weekly_rank</th>
      <th>show_title</th>
      <th>season_title</th>
      <th>weekly_hours_viewed</th>
      <th>cumulative_weeks_in_top_10</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2023-03-26</td>
      <td>Films (English)</td>
      <td>1</td>
      <td>Luther: The Fallen Sun</td>
      <td>NaN</td>
      <td>24700000</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2023-03-26</td>
      <td>Films (English)</td>
      <td>2</td>
      <td>The Magician's Elephant</td>
      <td>NaN</td>
      <td>16730000</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2023-03-26</td>
      <td>Films (English)</td>
      <td>3</td>
      <td>I See You</td>
      <td>NaN</td>
      <td>10830000</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2023-03-26</td>
      <td>Films (English)</td>
      <td>4</td>
      <td>Dragged Across Concrete</td>
      <td>NaN</td>
      <td>9310000</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2023-03-26</td>
      <td>Films (English)</td>
      <td>5</td>
      <td>Money Shot: The Pornhub Story</td>
      <td>NaN</td>
      <td>5000000</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>




```python
# find the total number of rows and columns

world.shape
```




    (3640, 7)




```python
# any missing values?

world.isna().sum()
```




    week                             0
    category                         0
    weekly_rank                      0
    show_title                       0
    season_title                  1879
    weekly_hours_viewed              0
    cumulative_weeks_in_top_10       0
    dtype: int64




```python
# dropping missing values

world.dropna(inplace = True)
```


```python
# display max number of rows
pd.set_option('display.max_rows', None)

world.dtypes
```




    week                          object
    category                      object
    weekly_rank                    int64
    show_title                    object
    season_title                  object
    weekly_hours_viewed            int64
    cumulative_weeks_in_top_10     int64
    dtype: object




```python
# changing datatype of week to datetime
world['week'] = world['week'].apply(pd.to_datetime)
```


```python
pd.set_option('display.max_rows', None)

world.dtypes
```




    week                          datetime64[ns]
    category                              object
    weekly_rank                            int64
    show_title                            object
    season_title                          object
    weekly_hours_viewed                    int64
    cumulative_weeks_in_top_10             int64
    dtype: object




```python
# Checking min and max date of data
print(world.week.min())
print(world.week.max())
```

    2021-07-04 00:00:00
    2023-03-26 00:00:00
    


```python
len(world.week.value_counts())
```




    91



## This data was derived for over a period of 91 weeks (637 days) from 2021-07-04 to 2023-03-26

## Most watched category  worldwide in descending order


```python
world.groupby(['category'])['weekly_hours_viewed'].mean().round().sort_values(ascending = False).reset_index()
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>weekly_hours_viewed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>TV (English)</td>
      <td>35681998.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>TV (Non-English)</td>
      <td>22284171.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Films (Non-English)</td>
      <td>3160000.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
import matplotlib.pyplot as plt
import seaborn as sns

plt.figure (figsize = (10,8))
sns.lineplot(x='week', y = 'weekly_hours_viewed', hue = 'category',  data = world)
plt.ylabel('Weekly hours viewed (per 100 million)')
plt.xlabel ('Week')
plt.xticks (rotation = 60)
sns.despine()
```


    
![output_17_0](https://user-images.githubusercontent.com/125743309/229553458-bae1ade8-d9bc-4443-8de3-2711b4fa34ff.png)
    


### From the table and graph depicted above, the top most viewed categories in descending order are 
1. TV (English) 
2. TV (Non-English)
3. Films (English) 
3. Films (Non-English) 

## Top 10 TV shows with the most weekly hours viewed worldwide


```python
top_TV_shows = world.groupby(['season_title', 'category'])['weekly_hours_viewed'].mean().round().sort_values(ascending = False)[:10]
top_TV_shows = top_TV_shows.reset_index()
top_TV_shows
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>season_title</th>
      <th>category</th>
      <th>weekly_hours_viewed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The Night Agent: Season 1</td>
      <td>TV (English)</td>
      <td>168710000.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>DAHMER: Monster: The Jeffrey Dahmer Story</td>
      <td>TV (English)</td>
      <td>137495714.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Squid Game: Season 1</td>
      <td>TV (Non-English)</td>
      <td>114475000.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Wednesday: Season 1</td>
      <td>TV (English)</td>
      <td>104369412.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Stranger Things 4</td>
      <td>TV (English)</td>
      <td>99332105.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Witcher: Season 2</td>
      <td>TV (English)</td>
      <td>86581667.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Ginny &amp; Georgia: Season 2</td>
      <td>TV (English)</td>
      <td>81215714.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>The Watcher: Season 1</td>
      <td>TV (English)</td>
      <td>79006000.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Sex Education: Season 3</td>
      <td>TV (English)</td>
      <td>74625000.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Cobra Kai: Season 4</td>
      <td>TV (English)</td>
      <td>73237500.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Bar graph of top 10 shows on basis of views
sns.barplot(y = 'season_title', x = 'weekly_hours_viewed', color = '#E50914',edgecolor = 'black', data = top_TV_shows)
plt.title('Top 10 TV Shows Worldwide (2021-07-04 to 2023-03-26)')
plt.xlabel('Weekly hours viewed \n (Per 100,000 million hours views)', labelpad = 10)
plt.ylabel('')
sns.despine()
```


    
![output_21_0](https://user-images.githubusercontent.com/125743309/229554034-18a9c14f-d144-47ad-8046-9491e5aa5767.png)
    


### The above findings so far are quite interesting: Nine out of the top 10 shows were English TV shows while only one (Squid Game) was a non-English TV show.
### It appears that globally, mankind prefers watching English TV shows.

## Average cumulative weeks in top 10 for the most popular TV shows


```python
season = world[world.season_title.isin(top_TV_shows.season_title.values)]
cum = season.groupby('season_title')['cumulative_weeks_in_top_10'].mean().sort_values(ascending = False)
cum = cum.reset_index()
cum
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>season_title</th>
      <th>cumulative_weeks_in_top_10</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Squid Game: Season 1</td>
      <td>10.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Stranger Things 4</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Wednesday: Season 1</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>DAHMER: Monster: The Jeffrey Dahmer Story</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ginny &amp; Georgia: Season 2</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Sex Education: Season 3</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>The Witcher: Season 2</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>The Watcher: Season 1</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Cobra Kai: Season 4</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>9</th>
      <td>The Night Agent: Season 1</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
sns.barplot(y = 'season_title' , x = 'cumulative_weeks_in_top_10', color = '#E50914', 
            edgecolor = 'black', data = cum)
plt.ylabel('')
plt.xlabel('Cumulative weeks')
sns.despine()
```


    
![output_25_0](https://user-images.githubusercontent.com/125743309/229554275-b742d5fb-764c-4802-98e7-94efce070f16.png)
    


### Out of the top 10 most watched shows worldwide, the show with the highest cumulative weeks as one of the top 10 watched show is Squid game: Season 1 with an average of 10.5 weeks which is fascinating particularly since The Night Agent : Season 1 which is the most watched show had only 1.5 cumulative weeks and Ginny & Georgia: Season 2 only 4.


```python
most_popular = pd.read_csv('most-popular.csv')
most_popular.head(5)
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>rank</th>
      <th>show_title</th>
      <th>season_title</th>
      <th>hours_viewed_first_28_days</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Films (English)</td>
      <td>1</td>
      <td>Red Notice</td>
      <td>NaN</td>
      <td>364020000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Films (English)</td>
      <td>2</td>
      <td>Don't Look Up</td>
      <td>NaN</td>
      <td>359790000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Films (English)</td>
      <td>3</td>
      <td>Bird Box</td>
      <td>NaN</td>
      <td>282020000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Films (English)</td>
      <td>4</td>
      <td>Glass Onion: A Knives Out Mystery</td>
      <td>NaN</td>
      <td>279740000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Films (English)</td>
      <td>5</td>
      <td>The Gray Man</td>
      <td>NaN</td>
      <td>253870000</td>
    </tr>
  </tbody>
</table>
</div>




```python
country = pd.read_csv('all-weeks-countries.csv')
country.head(5)
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country_name</th>
      <th>country_iso2</th>
      <th>week</th>
      <th>category</th>
      <th>weekly_rank</th>
      <th>show_title</th>
      <th>season_title</th>
      <th>cumulative_weeks_in_top_10</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Argentina</td>
      <td>AR</td>
      <td>2023-03-26</td>
      <td>Films</td>
      <td>1</td>
      <td>Still Time</td>
      <td>NaN</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Argentina</td>
      <td>AR</td>
      <td>2023-03-26</td>
      <td>Films</td>
      <td>2</td>
      <td>In His Shadow</td>
      <td>NaN</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Argentina</td>
      <td>AR</td>
      <td>2023-03-26</td>
      <td>Films</td>
      <td>3</td>
      <td>Luther: The Fallen Sun</td>
      <td>NaN</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Argentina</td>
      <td>AR</td>
      <td>2023-03-26</td>
      <td>Films</td>
      <td>4</td>
      <td>Noise</td>
      <td>NaN</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Argentina</td>
      <td>AR</td>
      <td>2023-03-26</td>
      <td>Films</td>
      <td>5</td>
      <td>All Quiet on the Western Front</td>
      <td>NaN</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
</div>




```python

```


```python
#select the rows with the top 10 most popular TV shows 

country_most_popular_TV = country[country.season_title.isin(most_popular.season_title.values)]

plt.figure (figsize = (10,18))
sns.barplot(x = 'cumulative_weeks_in_top_10', y = 'country_name', data = country_most_popular_TV.groupby('country_name')['cumulative_weeks_in_top_10'].mean().sort_values( ascending = False).reset_index())
plt.title('Average Cumulative Weeks Attained Per Country for the Top 10 Most Popular TV Shows')
plt.ylabel('')
plt.xlabel ('Cumulative Week')
sns.despine()
```


    
![output_30_0](https://user-images.githubusercontent.com/125743309/229554396-d8a39227-7d83-481e-af88-5c89f16da4cb.png)
    


### It appears that the top 10 countries that enjoyed the top 10 most popular TV shows the most were 
1. Ukrine
2. Brazil
3. Pakistan
4. India
5. Russia
6. Venezeula
7. Bangladesh
8. Spain
9. Bolivia
10. Servia

### While the countries that barely watched the top 10 most popular TV shows were 
1. Bahamas
2. Jamica
3. Trinidad and togobo
4. Martinique
5. Gaudeloupe
6. Reunion
7. Kenya
8. New Caledonia
9. South Africa
10. Romania

## Which TV show performed best per country?


```python
country_most_popular_TV = country[country.season_title.isin(most_popular.season_title.values)]
final_TV = country_most_popular_TV.groupby(['country_name','season_title'])[['weekly_rank','cumulative_weeks_in_top_10']].mean().round()
final_TV = final_TV.reset_index()
final_TV_perform =final_TV.sort_values(['cumulative_weeks_in_top_10'], ascending = [False]).reset_index(drop=True)
final_TV_perform.head(10)
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country_name</th>
      <th>season_title</th>
      <th>weekly_rank</th>
      <th>cumulative_weeks_in_top_10</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Venezuela</td>
      <td>Café con aroma de mujer: Season 1</td>
      <td>5.0</td>
      <td>32.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Spain</td>
      <td>Café con aroma de mujer: Season 1</td>
      <td>5.0</td>
      <td>28.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Dominican Republic</td>
      <td>Café con aroma de mujer: Season 1</td>
      <td>5.0</td>
      <td>25.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Nicaragua</td>
      <td>Café con aroma de mujer: Season 1</td>
      <td>6.0</td>
      <td>25.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>El Salvador</td>
      <td>Café con aroma de mujer: Season 1</td>
      <td>6.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Ecuador</td>
      <td>Café con aroma de mujer: Season 1</td>
      <td>6.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Paraguay</td>
      <td>Café con aroma de mujer: Season 1</td>
      <td>5.0</td>
      <td>23.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Honduras</td>
      <td>Café con aroma de mujer: Season 1</td>
      <td>6.0</td>
      <td>21.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Guatemala</td>
      <td>Café con aroma de mujer: Season 1</td>
      <td>5.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Ukraine</td>
      <td>Inventing Anna: Limited Series</td>
      <td>6.0</td>
      <td>19.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# This list cointains the TV shows which is most popular country wise
```

## And this concludes my review. English TV shows were very popular globally but one TV show Squid Game really  outperformed them all particularly when a standard metric was used.

## The country which spend most time for the top shows are Ukrine, Brazil, Pakistan and India whereas Bahamas, Jamica, Trinidad and togobo spend least time for the most popular TV shows

### Thank you


```python

```
