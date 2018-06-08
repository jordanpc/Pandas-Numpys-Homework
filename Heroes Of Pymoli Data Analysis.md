

```python
# Heroes Of Pymoli Data Analysis
```


```python
import pandas as pd
import numpy as np
import json
import os
```


```python
with open('purchase_data.json') as f:
    purchase_data = json.load(f)
```


```python
purchase_data_df = pd.DataFrame.from_dict(purchase_data, orient='columns')
```


```python
# Player Count
```


```python
players = purchase_data_df.groupby('SN').nunique()
total_players = players['SN'].count()
tp = {'Total Players': [str(total_players)]}
total_players_df = pd.DataFrame(data=tp)
total_players_df
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
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Purchasing Analysis
```


```python
unique_items = purchase_data_df.groupby('Item Name').nunique()
total_unique = unique_items['Item Name'].count()
```


```python
average_price = np.round(purchase_data_df['Price'].mean(), decimals=2)
```


```python
total_purchases = purchase_data_df['Item Name'].count()
```


```python
total_revenue = purchase_data_df['Price'].sum()
```


```python
p_a = {'Number of Unique Items': [str(total_unique)],
       'Average Price': [str(average_price)],
       'Number of Purchases': [str(total_purchases)],
       'Total Revenue': [str(total_revenue)]}
purchasing_analysis_df = pd.DataFrame(data=p_a)
purchasing_analysis_df
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
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>179</td>
      <td>2.93</td>
      <td>780</td>
      <td>2286.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Gender Demographics
```


```python
gender = purchase_data_df.drop_duplicates('SN')
gender_count = gender['Gender'].value_counts()
```


```python
gender_count = gender_count.to_frame().reset_index()
```


```python
gender_count = gender_count.rename(columns={'index': 'Genders', 'Gender': 'Total Count'})
```


```python
gender_count = gender_count.set_index('Genders')
```


```python
gender_final = (gender_count['Total Count']/573*100).round(2)
gender_count['Percentage of Players'] = gender_final
gender_count
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
      <th>Total Count</th>
      <th>Percentage of Players</th>
    </tr>
    <tr>
      <th>Genders</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>81.15</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>17.45</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>1.40</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Purchasing Analysis (Gender)
```


```python
g_a = purchase_data_df.groupby('Gender')
gender_analysis = g_a['Gender'].count()
gender_analysis_df = gender_analysis.to_frame(name=None)
ga = gender_analysis_df.rename(columns={'Gender': 'Purchase Count'})
```


```python
gender_average_price = np.round(purchase_data_df.pivot_table(purchase_data_df, 'Gender'), decimals = 2)
gap = gender_average_price.rename(columns={'Price': 'Average Purchase Price'})
gap = gap.drop(columns=['Age', 'Item ID'])
```


```python
gender_total_price = np.round(purchase_data_df.pivot_table(purchase_data_df, 'Gender', aggfunc=sum), decimals = 2)
gtp = gender_total_price.rename(columns={'Price': 'Total Purchase Price'})
gtp = gtp.drop(columns=['Age', 'Item ID'])
```


```python
final_gender = pd.concat([ga, gap, gtp], axis=1)
```


```python
final_gender['Normalized Totals']=np.round((final_gender['Total Purchase Price']/
                                            gender_count['Total Count']),decimals=2)
final_gender
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Price</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>2.82</td>
      <td>382.91</td>
      <td>3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>2.95</td>
      <td>1867.68</td>
      <td>4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>3.25</td>
      <td>35.74</td>
      <td>4.47</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Age Demographics
```


```python
unique_demo = purchase_data_df.pivot_table(purchase_data_df, 'SN')
```


```python
bins = [0, 9, 14, 19, 24, 29, 34, 39, 500]
age_group = ['<10', '10-14', '15-19', '20-24', '25-29', '30-34', '35-39', '40+']
unique_demo['Age Bracket'] = pd.cut(unique_demo['Age'], bins, labels = age_group)
age_bracket = unique_demo['Age Bracket'].value_counts()
age_bracket = age_bracket.to_frame(name=None)
age_bracket['Percentage of Players']=np.round(age_bracket['Age Bracket']/573*100, decimals=2)
age_bracket = age_bracket.rename(columns={'Age Bracket': 'Total Count'})
age_bracket
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
      <th>Total Count</th>
      <th>Percentage of Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>20-24</th>
      <td>259</td>
      <td>45.20</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>100</td>
      <td>17.45</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>87</td>
      <td>15.18</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>47</td>
      <td>8.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>27</td>
      <td>4.71</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>23</td>
      <td>4.01</td>
    </tr>
    <tr>
      <th>&lt;10</th>
      <td>19</td>
      <td>3.32</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>11</td>
      <td>1.92</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Purchasing Analysis (Age)
```


```python
bins1 = [0, 9, 14, 19, 24, 29, 34, 39, 500]
age_group1 = ['<10', '10-14', '15-19', '20-24', '25-29', '30-34', '35-39', '40+']
purchase_data_df['Age Bracket'] = pd.cut(purchase_data_df['Age'], bins1, labels = age_group1)
purchasing_item = purchase_data_df
```


```python
p_item1 = purchasing_item.groupby('Age Bracket').count()
p_item1 = p_item1.drop(columns=['Age', 'Gender','Item ID','Price','SN'])
p_item1 = p_item1.rename(columns={'Item Name': 'Purchase Count'})
```


```python
p_item2 = np.round(purchasing_item.groupby('Age Bracket').mean(),decimals=2)
p_item2 = p_item2.drop(columns=['Age','Item ID'])
p_item2 = p_item2.rename(columns={'Price': 'Average Purchase Price'})
```


```python
p_item3 = purchasing_item.groupby('Age Bracket').sum()
p_item3 = p_item3.drop(columns=['Age','Item ID'])
p_item3 = p_item3.rename(columns={'Price': 'Total Purchase Value'})
```


```python
p_final = pd.concat([p_item1,p_item2,p_item3], axis=1,sort=True)
p_final['Normalized Total']=np.round(p_final['Total Purchase Value']/age_bracket['Total Count'], decimals=2)
p_final
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Total</th>
    </tr>
    <tr>
      <th>Age Bracket</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>2.98</td>
      <td>83.46</td>
      <td>4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>2.77</td>
      <td>96.95</td>
      <td>4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>2.91</td>
      <td>386.42</td>
      <td>3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>2.91</td>
      <td>978.77</td>
      <td>3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>2.96</td>
      <td>370.33</td>
      <td>4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>3.08</td>
      <td>197.25</td>
      <td>4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>2.84</td>
      <td>119.40</td>
      <td>4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>3.16</td>
      <td>53.75</td>
      <td>4.89</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Top Spenders
```


```python
spender_sort = np.round(purchase_data_df.pivot_table(purchase_data_df, 'SN', aggfunc=sum), decimals=2)
spender_sort = spender_sort.sort_values(by=['Price'], ascending=False)
spender_sort = spender_sort.drop(columns=['Age', 'Item ID'])
spender_sort = spender_sort.rename(columns={'Price': 'Total Purchase Value'})
```


```python
spender = purchase_data_df.groupby('SN')
spender = spender['Price'].mean()
spender = np.round(spender.to_frame(name=None),decimals=2)
```


```python
spender2 = purchase_data_df.groupby('SN')
spender2 = spender2['SN'].count()
spender2 = np.round(spender2.to_frame(name=None),decimals=2)
```


```python
sp = pd.concat([spender_sort, spender, spender2], axis=1, sort=True)
final_sp = sp.sort_values(by=['Total Purchase Value'], ascending=False)
final_sp = final_sp.rename(columns={'Price': 'Average Purchase Price', 'SN':'Purchase Count'})
final_spenders = final_sp.head()
final_spenders
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
      <th>Total Purchase Value</th>
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>17.06</td>
      <td>3.41</td>
      <td>5</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>13.56</td>
      <td>3.39</td>
      <td>4</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>12.74</td>
      <td>3.18</td>
      <td>4</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>12.73</td>
      <td>4.24</td>
      <td>3</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>11.58</td>
      <td>3.86</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Most Popular Items
```


```python
popular_items = purchase_data_df.groupby(['Item ID','Item Name']).count().reset_index()
popular = popular_items.sort_values('Price', ascending=False)
popular = popular.set_index(['Item ID','Item Name'])
popular = popular.drop(columns=['Age', 'Gender', 'Price'])
popular = popular.rename(columns={'SN': 'Purchase Count'})
```


```python
popular_items1 = purchase_data_df.groupby(['Item ID', 'Item Name'])
popular_items1 = popular_items1['Price'].mean()
popular_items1 = np.round(popular_items1.to_frame(name=None),decimals=2)
popular_items1 = popular_items1.rename(columns={'Price':'Item Price'})
```


```python
popular_items2 = purchase_data_df.groupby(['Item ID', 'Item Name'])
popular_items2 = popular_items2['Price'].sum()
popular_items2 = np.round(popular_items2.to_frame(name=None),decimals=2)
popular_items2 = popular_items2.rename(columns={'Price':'Total Purchase Value'})
```


```python
popular_final = pd.concat([popular,popular_items1,popular_items2], axis=1,sort=True)
popular_final = popular_final.sort_values(by=['Purchase Count'], ascending=False)
popular_final = popular_final.head()
popular_final
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
      <th></th>
      <th>Purchase Count</th>
      <th>Age Bracket</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>11</td>
      <td>2.35</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>11</td>
      <td>2.23</td>
      <td>24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>9</td>
      <td>2.07</td>
      <td>18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>9</td>
      <td>1.24</td>
      <td>11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>9</td>
      <td>1.49</td>
      <td>13.41</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Most Profitable Items
```


```python
profitable_items = purchase_data_df.groupby(['Item ID','Item Name']).sum().reset_index()
profit = profitable_items.sort_values('Price', ascending=False)
profit = profit.set_index(['Item ID','Item Name']).head()
profit = profit.drop(columns=['Age'])
profit = profit.rename(columns={'Price': 'Total Purchase Value'})
```


```python
profit1 = purchase_data_df.groupby(['Item ID', 'Item Name'])
profit1 = profit1['Price'].mean()
profit1 = np.round(profit1.to_frame(name=None),decimals=2)
profit1 = profit1.rename(columns={'Price':'Item Price'})
```


```python
profit2 = purchase_data_df.groupby(['Item ID', 'Item Name'])
profit2 = profit2['SN'].count()
profit2 = np.round(profit2.to_frame(name=None),decimals=2)
profit2 = profit2.rename(columns={'SN':'Purchase Count'})
```


```python
profit_final = pd.concat([profit,profit1,profit2], axis=1,sort=True)
profit_final = profit_final.sort_values(by=['Total Purchase Value'], ascending=False)
profit_final = profit_final.head()
profit_final
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
      <th></th>
      <th>Total Purchase Value</th>
      <th>Item Price</th>
      <th>Purchase Count</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>37.26</td>
      <td>4.14</td>
      <td>9</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>29.75</td>
      <td>4.25</td>
      <td>7</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>29.70</td>
      <td>4.95</td>
      <td>6</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>29.22</td>
      <td>4.87</td>
      <td>6</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>28.88</td>
      <td>3.61</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>


