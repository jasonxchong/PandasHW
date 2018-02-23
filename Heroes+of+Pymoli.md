

```python
import json
import csv
import pandas as pd
import numpy as np

file = "purchase_data.json" #data set 1
file_df = pd.read_json(file) # data set 1

#file = "purchase_data2.json" # data set 2
#file_df = pd.read_json(file) # data set 2



#file_df.head()
file_df.columns

```

# Player Count


```python
total_players = pd.DataFrame(
                        {"Total Players": [file_df["SN"].nunique()]})
total_players


```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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



# Purchasing Analysis


```python
purchase = pd.DataFrame(
                {"Number of unique items": [file_df["Item ID"].nunique()],
                    "Average purchase price": [file_df["Price"].mean()],
                    "Total number of purchases": [file_df["Price"].count()],
                    "Total revenue": [file_df["Price"].sum()]})
purchase
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average purchase price</th>
      <th>Number of unique items</th>
      <th>Total number of purchases</th>
      <th>Total revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2.931192</td>
      <td>183</td>
      <td>780</td>
      <td>2286.33</td>
    </tr>
  </tbody>
</table>
</div>



# Gender Demographics


```python
gender = file_df.groupby("Gender")["SN"].nunique().reset_index()
gender["Percentage"] = 100 * gender["SN"] / gender["SN"].sum()
gender = gender.rename(columns={"SN" : "Count"})
gender
#gender.groupby("Gender").mean().head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Count</th>
      <th>Percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Female</td>
      <td>100</td>
      <td>17.452007</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Male</td>
      <td>465</td>
      <td>81.151832</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>8</td>
      <td>1.396161</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis Gender


```python
genderp = file_df[["Gender","Price"]]
genderp = genderp.groupby("Gender").count()
genderp["Avg Price"] = file_df.groupby(["Gender"])["Price"].mean() #wont let me calculate normalized if formatted
genderp["Total Purchase"] = file_df.groupby(["Gender"])["Price"].sum().map('${:,.2f}'.format)
genderp["Normalized Totals"] = (genderp["Price"] - genderp["Avg Price"])/genderp["Price"].std(ddof=0)
genderp = genderp.rename(columns={"Price" : "Count"})
genderp
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Count</th>
      <th>Avg Price</th>
      <th>Total Purchase</th>
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
      <td>2.815515</td>
      <td>$382.91</td>
      <td>0.495769</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>2.950521</td>
      <td>$1,867.68</td>
      <td>2.345310</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>3.249091</td>
      <td>$35.74</td>
      <td>0.028852</td>
    </tr>
  </tbody>
</table>
</div>



# AGE DEMOGRAPHICS


```python
#maxed = file_df["Age"].max()
#maxed - 45
bins = [0,10,15,20,25,30,35,40,50]
age = ["<10","10-14","15-19","20-24","25-29","30-34","35-39","40-45"]

pd.cut(file_df["Age"],bins,labels=age).head()

aged_df=file_df

aged_df["Age Group"] = pd.cut(aged_df["Age"],bins,labels=age)
#aged_df.head()

age_demo = aged_df[["Age Group","Price"]]
age_demo = age_demo.groupby("Age Group").count()
age_demo["Avg Price"] = aged_df.groupby(["Age Group"])["Price"].mean() #wont let me calculate normalized if formatted
age_demo["Total Purchase"] = aged_df.groupby(["Age Group"])["Price"].sum().map('${:,.2f}'.format)
age_demo["Normalized Totals"] = (age_demo["Price"]-age_demo["Avg Price"])/age_demo["Price"].std(ddof=0)
age_demo = age_demo.rename(columns={"Price" : "Count"})
age_demo
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Count</th>
      <th>Avg Price</th>
      <th>Total Purchase</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>32</td>
      <td>3.019375</td>
      <td>$96.62</td>
      <td>0.311999</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>78</td>
      <td>2.873718</td>
      <td>$224.15</td>
      <td>0.808792</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>184</td>
      <td>2.873587</td>
      <td>$528.74</td>
      <td>1.949965</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>305</td>
      <td>2.959377</td>
      <td>$902.61</td>
      <td>3.251699</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>76</td>
      <td>2.892368</td>
      <td>$219.82</td>
      <td>0.787060</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>58</td>
      <td>3.073448</td>
      <td>$178.26</td>
      <td>0.591326</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>44</td>
      <td>2.897500</td>
      <td>$127.49</td>
      <td>0.442500</td>
    </tr>
    <tr>
      <th>40-45</th>
      <td>3</td>
      <td>2.880000</td>
      <td>$8.64</td>
      <td>0.001292</td>
    </tr>
  </tbody>
</table>
</div>



# Top Spender


```python
baller = file_df[["SN","Price"]]
baller = baller.groupby("SN").count()
baller["Avg Price"] = file_df.groupby(["SN"])["Price"].mean().map('${:,.2f}'.format)
baller["Total Purchase"] = file_df.groupby(["SN"])["Price"].sum().map('${:,.2f}'.format)
baller = baller.rename(columns={"Price" : "Count"})
biggest_baller = baller.sort_values(["Total Purchase"], ascending=False)
biggest_baller.reset_index(inplace=True)
biggest_baller.head(5)

#baller.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SN</th>
      <th>Count</th>
      <th>Avg Price</th>
      <th>Total Purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Qarwen67</td>
      <td>4</td>
      <td>$2.49</td>
      <td>$9.97</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Sondim43</td>
      <td>3</td>
      <td>$3.13</td>
      <td>$9.38</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Tillyrin30</td>
      <td>3</td>
      <td>$3.06</td>
      <td>$9.19</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Lisistaya47</td>
      <td>3</td>
      <td>$3.06</td>
      <td>$9.19</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Tyisriphos58</td>
      <td>2</td>
      <td>$4.59</td>
      <td>$9.18</td>
    </tr>
  </tbody>
</table>
</div>



# popular


```python
pop = file_df[["Item ID","Item Name","Price"]]
pop = pop.groupby(["Item ID","Item Name"]).count()
pop["Avg Price"] = file_df.groupby(["Item ID", "Item Name"])["Price"].mean().map('${:,.2f}'.format)
pop["Total Purchase"] = file_df.groupby(["Item ID", "Item Name"])["Price"].sum().map('${:,.2f}'.format)
pop = pop.rename(columns={"Price" : "Count"})

most_pop = pop.sort_values(["Count"], ascending=False)
most_pop.reset_index(inplace=True)
most_pop.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Count</th>
      <th>Avg Price</th>
      <th>Total Purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>39</td>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>1</th>
      <td>84</td>
      <td>Arcane Gem</td>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>2</th>
      <td>31</td>
      <td>Trickster</td>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>3</th>
      <td>175</td>
      <td>Woeful Adamantite Claymore</td>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>4</th>
      <td>13</td>
      <td>Serenity</td>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>



# money maker


```python
profit = file_df[["Item ID","Item Name","Price"]]
profit = profit.groupby(["Item ID","Item Name"]).count()
profit["Avg Price"] = file_df.groupby(["Item ID", "Item Name"])["Price"].mean().map('${:,.2f}'.format)
profit["Total Purchase Value"] = file_df.groupby(["Item ID", "Item Name"])["Price"].sum().map('${:,.2f}'.format)
profit = profit.rename(columns={"Price" : "Count"})

most_profit = profit.sort_values(["Total Purchase Value"], ascending=False)
most_profit.reset_index(inplace=True)
most_profit.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Count</th>
      <th>Avg Price</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>170</td>
      <td>Shadowsteel</td>
      <td>5</td>
      <td>$1.98</td>
      <td>$9.90</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Souleater</td>
      <td>3</td>
      <td>$3.27</td>
      <td>$9.81</td>
    </tr>
    <tr>
      <th>2</th>
      <td>37</td>
      <td>Shadow Strike, Glory of Ending Hope</td>
      <td>5</td>
      <td>$1.93</td>
      <td>$9.65</td>
    </tr>
    <tr>
      <th>3</th>
      <td>127</td>
      <td>Heartseeker, Reaver of Souls</td>
      <td>3</td>
      <td>$3.21</td>
      <td>$9.63</td>
    </tr>
    <tr>
      <th>4</th>
      <td>120</td>
      <td>Agatha</td>
      <td>5</td>
      <td>$1.91</td>
      <td>$9.55</td>
    </tr>
  </tbody>
</table>
</div>



# Observations


```python
Observations:
    #1 - as expected most of the age group falls between the 15-19 and 20-24 age range
    #2 - males dominate purchasing volume accounting for over 80% of volume
    #3 - There doesnt appear to be one item that has high volume and the item volume appears to be more distributed amongst the 183 items
    
```
