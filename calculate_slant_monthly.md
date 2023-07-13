```python
import pandas as pd
```


```python
df1 = pd.read_csv("entre_tweets_all.csv")
```


```python
df1["time"] = df1["time"].astype(str)
```


```python
date_lst = []
for index, row in df1.iterrows():
    date_lst.append(row["time"][:10])
```


```python
df1["date"] = date_lst
```


```python
df1.head()
```




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
      <th>Unnamed: 0</th>
      <th>user_name</th>
      <th>text</th>
      <th>time</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>THCFilm</td>
      <td>Two years ago we release our #brightburn with ...</td>
      <td>2021-05-25T08:20:46</td>
      <td>2021-05-25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>THCFilm</td>
      <td>Very sad to hear that @ArcLightCinemas was clo...</td>
      <td>2021-04-13T22:32:42</td>
      <td>2021-04-13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>THCFilm</td>
      <td>Have you seen Zack Snyder’s justice league yet...</td>
      <td>2021-03-19T15:44:30</td>
      <td>2021-03-19</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>THCFilm</td>
      <td>Five types of the anti superhero, check out wh...</td>
      <td>2021-03-12T22:09:19</td>
      <td>2021-03-12</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>THCFilm</td>
      <td>Here are some positive reviews of our #Wrongtu...</td>
      <td>2021-03-05T23:09:23</td>
      <td>2021-03-05</td>
    </tr>
  </tbody>
</table>
</div>




```python
#extract month year 
year_lst = []
month_lst = []
for index, row in df1.iterrows():
    year_lst.append(row["date"][:4])
    month_lst.append(row["date"][5:7])
```


```python
df1["year"] = year_lst
df1["month"] = month_lst
```


```python
df1 = df1.drop_duplicates(subset= ["user_name", "text", "date"], keep = "first")
df1 = df1[["user_name", "text", "date", "year", "month"]]
```


```python
import csv
```


```python
with open('keywords_2005.csv', 'r') as f:
    keywords = set(row[0] for row in csv.reader(f)) #top1000 keywords
```


```python
polarization = {}
```


```python
keywords_found = []
```


```python
df1["text"] = df1["text"].astype(str)
for index, row in df1.iterrows():
    sentence = row["text"]
    username = row["user_name"]
    year = row["year"]
    month = row["month"]
    pair = (username, year, month)
    # Join all words in the tweet sentence into one string
    words = ''.join(sentence.split())
    words = words.replace("@","")
    words = words.replace("#","")
    for keyword in keywords:
        if keyword in words:
            if pair not in polarization:
                polarization[pair] = words.count(keyword)
            else:
                polarization[pair] += words.count(keyword)
            username_year_keyword = [keyword, year, month, username]
            keywords_found.append(username_year_keyword)
```


```python
df_keywords_found = pd.DataFrame(keywords_found, columns = ["keyword", "year", "month", "username"])
```


```python
username_lst = []
score = []
for key, value in polarization.items():
    lst = [key[0], key[1], key[2]]
    username_lst.append(lst)
    score.append(value)    
    
```


```python
df_score = pd.DataFrame(username_lst, columns = ["username", "year", "month"])
```


```python
df_score["score"] = score
```


```python
df_score
```




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
      <th>username</th>
      <th>year</th>
      <th>month</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>SanDiegoLoyal</td>
      <td>2023</td>
      <td>04</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>SanDiegoLoyal</td>
      <td>2022</td>
      <td>12</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>SanDiegoLoyal</td>
      <td>2022</td>
      <td>11</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>SanDiegoLoyal</td>
      <td>2022</td>
      <td>09</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>SanDiegoLoyal</td>
      <td>2022</td>
      <td>08</td>
      <td>1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>12768</th>
      <td>CarewellFamily</td>
      <td>2021</td>
      <td>08</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12769</th>
      <td>CarewellFamily</td>
      <td>2021</td>
      <td>03</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12770</th>
      <td>CarewellFamily</td>
      <td>2021</td>
      <td>02</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12771</th>
      <td>CarewellFamily</td>
      <td>2020</td>
      <td>02</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12772</th>
      <td>CarewellFamily</td>
      <td>2020</td>
      <td>01</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>12773 rows × 4 columns</p>
</div>




```python
polarization_2 = {}
```


```python
for index, row in df1.iterrows():
    sentence = row["text"].lower()
    username = row["user_name"]
    year = row["year"]
    month = row["month"]
    pair = (username, year, month)
    # Join all words in the tweet sentence into one string
    words = ''.join(sentence.split())
    # remove all marks
    words = words.replace("@","")
    words = words.replace("#","")
    words = words.replace("?","")
    words = words.replace("!","")
    a
    if pair not in polarization_2:
        polarization_2[pair] = {'total_tweets': 1, 'keywords': {}}
    else:
        polarization_2[pair]["total_tweets"] += 1
    for keyword in keywords:
        if keyword in words:
            if keyword not in polarization_2[pair]['keywords']:
                polarization_2[pair]['keywords'][keyword] = 1
            else:
                polarization_2[pair]['keywords'][keyword] += 1
    

```


```python
# Calculate the relative frequency of each keyword for each company for each year
for pair in polarization_2:
    total_keywords = sum(polarization_2[pair]['keywords'].values())
    for keyword in polarization_2[pair]['keywords']:
        keyword_frequency = polarization_2[pair]['keywords'][keyword] / total_keywords
        polarization_2[pair]['keywords'][keyword] = keyword_frequency
```


```python
company_lst = []
company_year = []
company_month = []
total_tweets = []
keywords_wrds = []
freq = []

for key, value in polarization_2.items():
    for i, q in value["keywords"].items():
        company_lst.append(key[0])
        company_year.append(key[1])
        company_month.append(key[2])
        total_tweets.append(value["total_tweets"])
        keywords_wrds.append(i)
        freq.append(q)
```


```python
#add year, month, keyword, and monthly total tweets
df_freq = pd.DataFrame(company_lst, columns = ["username"])
df_freq["company_year"] = company_year
df_freq["company_month"] = company_month
df_freq["total_tweets"] = total_tweets
df_freq["keyword"] = keywords_wrds
df_freq["frequency"] = freq
```


```python
df_freq.head()
```




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
      <th>username</th>
      <th>company_year</th>
      <th>company_month</th>
      <th>total_tweets</th>
      <th>keyword</th>
      <th>frequency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>THCFilm</td>
      <td>2019</td>
      <td>05</td>
      <td>15</td>
      <td>memorialday</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>THCFilm</td>
      <td>2019</td>
      <td>04</td>
      <td>4</td>
      <td>memorialday</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>THCFilm</td>
      <td>2019</td>
      <td>03</td>
      <td>11</td>
      <td>memorialday</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>THCFilm</td>
      <td>2019</td>
      <td>01</td>
      <td>14</td>
      <td>memorialday</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>THCFilm</td>
      <td>2018</td>
      <td>12</td>
      <td>9</td>
      <td>memorialday</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#read alpha beta value
df2 = pd.read_csv('keywords_alpha_beta_values_2005.csv')
```


```python
#create a dictionary that stores the alpha beta associates with each phrase
keyword_values = {}
for index, row in df2.iterrows():
    keyword_values[row["phrase_stub"]] = (row["alpha"], row["beta"])
```


```python
keyword_frequencies = {}
for index, row in df_freq.iterrows():
    # Create a dictionary to store the keyword frequency data, keyed by company, year, month and keyword
    company = row["username"]
    keyword = row["keyword"]
    year = row["company_year"]
    month = row["company_month"]
    frequency = row["frequency"]
    keyword_frequencies[(company, year, month, keyword)] = frequency
```


```python
#create a function to calculate the slant for each year-month
def get_slant_index(df1, df_freq, year, month, keyword_values):
    df3 = df_freq[(df_freq["company_year"] == year) & (df_freq["company_month"] == month)]
    if len(df3) > 0:
        keyword_frequencies = {}
        for index, row in df3.iterrows():
            company = row["username"]
            keyword = row["keyword"]
            keyword_frequencies[(company, keyword)] = row["frequency"]

        slant_indices = {}
        for company in set(df3["username"].to_list()):
            numerator = 0
            denominator = sum(beta ** 2 for alpha, beta in keyword_values.values())
            for keyword, (alpha, beta) in keyword_values.items():
                frequency = keyword_frequencies.get((company, keyword), 0)
                numerator += beta * (frequency - alpha)
            slant_index = numerator / denominator
            slant_indices[company] = slant_index

        df_slant = pd.DataFrame(slant_indices.items(), columns = ["username", "slant_index"])
        df_slant["year"] = [year] * len(df_slant)
        df_slant["month"] = [month] * len(df_slant)

        add_company = []
        #for companies which had tweet at that month but did not associate with any political attitude
        #insert 0 value
        for i in set(df1[(df1["year"] == year) & (df1["month"] == month)].user_name.to_list()): 
            if i not in df_slant.username.to_list():
                temp = [i, 0]
                add_company.append(temp)
        df_zero = pd.DataFrame(add_company, columns = ["username", "slant_index"])
        df_zero["year"] = [year] * len(df_zero)
        df_zero["month"] = [month] * len(df_zero)
        df_final = pd.concat([df_slant, df_zero])
    else:
        df_final = pd.DataFrame([], columns = ["username", "slant_index", "year", "month"])
    
    return df_final
    
```


```python
#get all dataframes and merge into one file
all_df = []
for i in ["2008", "2009", "2010", "2011", "2012", "2013", "2014", "2015", "2016", "2017", "2018", "2019", "2020", "2021", "2022", "2023"]:
    for q in ["01", "02", "03", "04", "05", "06", "07", "08", "09", "10", "11", "12"]:
        all_df.append(get_slant_index(df1, df_freq, i, q, keyword_values))
```


```python
df_panel_slant_month = pd.concat(all_df)
df_panel_slant_month.reset_index(drop=True, inplace=True)
```


```python

month_total_tweet = {}
for index, row in df_freq.iterrows():
    pair = (row["username"], row["company_year"], row["company_month"])
    month_total_tweet[pair] = row["total_tweets"]
```


```python
df1_descriptive = df1.groupby(["user_name", "year", "month"]).count().reset_index()
df1_descriptive = df1_descriptive[["user_name", "year", "month", "text"]]
df1_descriptive.rename(columns = {"text":"total_tweets_monthly", "user_name": "username"}, inplace = True)
```


```python
df1_descriptive.head()
```




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
      <th>username</th>
      <th>year</th>
      <th>month</th>
      <th>total_tweets_monthly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0xProject</td>
      <td>2017</td>
      <td>02</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0xProject</td>
      <td>2017</td>
      <td>03</td>
      <td>4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0xProject</td>
      <td>2017</td>
      <td>04</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0xProject</td>
      <td>2017</td>
      <td>05</td>
      <td>14</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0xProject</td>
      <td>2017</td>
      <td>06</td>
      <td>17</td>
    </tr>
  </tbody>
</table>
</div>




```python
#add monthly total tweets into the panel slant month data
df_panel_slant_month = df_panel_slant_month.merge(df1_descriptive, on = ["username", "year", "month"], how = "left")
```


```python
df_panel_slant_month[df_panel_slant_month["slant_index"]!=0]
```




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
      <th>username</th>
      <th>slant_index</th>
      <th>year</th>
      <th>month</th>
      <th>total_tweets_monthly</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>epicpivots</td>
      <td>-0.716784</td>
      <td>2008</td>
      <td>02</td>
      <td>22</td>
    </tr>
    <tr>
      <th>1</th>
      <td>epicpivots</td>
      <td>1.387239</td>
      <td>2008</td>
      <td>07</td>
      <td>70</td>
    </tr>
    <tr>
      <th>6</th>
      <td>epicpivots</td>
      <td>0.467693</td>
      <td>2008</td>
      <td>09</td>
      <td>34</td>
    </tr>
    <tr>
      <th>7</th>
      <td>EnlivenSoftware</td>
      <td>0.514525</td>
      <td>2008</td>
      <td>09</td>
      <td>20</td>
    </tr>
    <tr>
      <th>12</th>
      <td>epicpivots</td>
      <td>0.467693</td>
      <td>2009</td>
      <td>01</td>
      <td>38</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>86052</th>
      <td>centered_app</td>
      <td>0.641458</td>
      <td>2023</td>
      <td>06</td>
      <td>12</td>
    </tr>
    <tr>
      <th>86053</th>
      <td>SlingshotCrypto</td>
      <td>0.735649</td>
      <td>2023</td>
      <td>06</td>
      <td>14</td>
    </tr>
    <tr>
      <th>86054</th>
      <td>OXG_Esports</td>
      <td>0.800374</td>
      <td>2023</td>
      <td>06</td>
      <td>125</td>
    </tr>
    <tr>
      <th>86055</th>
      <td>amorusapp</td>
      <td>0.574569</td>
      <td>2023</td>
      <td>06</td>
      <td>17</td>
    </tr>
    <tr>
      <th>86056</th>
      <td>climate1stbank</td>
      <td>0.567193</td>
      <td>2023</td>
      <td>06</td>
      <td>10</td>
    </tr>
  </tbody>
</table>
<p>16525 rows × 5 columns</p>
</div>




```python
df_panel_slant_month.to_csv("slant_index_monthly.csv")
```


```python

```


```python

```


```python

```
