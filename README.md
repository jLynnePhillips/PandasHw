

Observed trend 1: There are significantly more purchases by male players than female playsers. 
Observed trend 2: Most of the purchases were by players between age 15 and 34.
Observed trend 3: The two most popular items, Betrayal Whisper of Grieving Widows and Arcane Gem, were not among the five most profitalbe items. 


```python
# Import Dependencies
import pandas as pd
import json

# File paths
file_path = "purchase_data.json"

# Read .json data to a pandas Dataframe
dataframe = pd.read_json(file_path)

# Check that the two have been added
dataframe.tail()
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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>775</th>
      <td>22</td>
      <td>Male</td>
      <td>98</td>
      <td>Deadline, Voice Of Subtlety</td>
      <td>3.62</td>
      <td>Eural50</td>
    </tr>
    <tr>
      <th>776</th>
      <td>14</td>
      <td>Male</td>
      <td>104</td>
      <td>Gladiator's Glaive</td>
      <td>1.36</td>
      <td>Lirtossa78</td>
    </tr>
    <tr>
      <th>777</th>
      <td>20</td>
      <td>Male</td>
      <td>117</td>
      <td>Heartstriker, Legacy of the Light</td>
      <td>4.15</td>
      <td>Tillyrin30</td>
    </tr>
    <tr>
      <th>778</th>
      <td>20</td>
      <td>Male</td>
      <td>75</td>
      <td>Brutality Ivory Warmace</td>
      <td>1.72</td>
      <td>Quelaton80</td>
    </tr>
    <tr>
      <th>779</th>
      <td>23</td>
      <td>Female</td>
      <td>107</td>
      <td>Splitter, Foe Of Subtlety</td>
      <td>3.61</td>
      <td>Alim85</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Player Count
# Find number of players
players = dataframe["SN"].unique()
total_number_of_players = len(players)
total_players_df = pd.DataFrame({"Total Players":[total_number_of_players]})
total_players_df
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




```python
# Purchasing Analysis (Total)
# Find number of unique items
unique_items = dataframe["Item ID"].unique()
number_of_unique_items = len(unique_items)

# Find the average purchase price
average_purchase_price = dataframe["Price"].mean()

# Find total number of purchases
total_number_of_purchases = dataframe["Age"].count()

# Find total revenue
total_revenue = dataframe["Price"].sum()

# Purchasing Analysis Results
purchasing_analysis = pd.DataFrame({"Number of Unique Items":[number_of_unique_items],
                                    "Average Purchase Price":["$"+str(round(average_purchase_price,2))],
                                    "Total Number of Purchases":[total_number_of_purchases],
                                    "Total Revenue":["$"+str(round(total_revenue,2))]})
purchasing_analysis
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
      <th>Average Purchase Price</th>
      <th>Number of Unique Items</th>
      <th>Total Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$2.93</td>
      <td>183</td>
      <td>780</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Gender Demographics
# Remove duplicate players
gender_df = dataframe.drop_duplicates(subset=["SN"])
# Count the number of each gender
gender_dict = gender_df["Gender"].value_counts().to_dict()
# Find the percent of each gender and round the number displayed to 2 decimal places
gender_percent_dict = {k: '%.2f' %(v / total_number_of_players) for k, v in gender_dict.items()}
# Create a DataFrame from the percentages
gender_result_df = pd.DataFrame.from_dict(gender_percent_dict,orient='index')
# Add the Total Count to the DataFrame
gender_result_df["Total Count"]=gender_dict.values()
# Change the column name from 0 to Percentage of Players
gender_result_df = gender_result_df.rename(columns={0:"Percentage of Players"})
gender_result_df
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
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>0.81</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>0.17</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>0.01</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Purchase Analysis (Gender)
# Group the dataframe by gender
gender_group = dataframe.groupby(["Gender"])
# Create a DataFrame for the count, average, and total value of the prices
gender_purchase_analysis = pd.DataFrame({"Purchase Count":gender_group["Price"].count(),
                                        "Average Purchase Price":gender_group["Price"].mean(),
                                        "Total Purchase Value":gender_group["Price"].sum(),
                                        "Normalized Totals":gender_group["Price"].sum()}) 
# Loop through and divide the purchase total by the number of each gender
for x in gender_dict.keys():
    gender_purchase_analysis.loc[x,"Normalized Totals"] = gender_purchase_analysis.loc[x,"Normalized Totals"]/gender_dict[x]

# Format the monetary values and reorder the columns
gender_purchase_analysis["Average Purchase Price"] = gender_purchase_analysis["Average Purchase Price"].map("${:.2f}".format)
gender_purchase_analysis["Total Purchase Value"] = gender_purchase_analysis["Total Purchase Value"].map("${:.2f}".format)
gender_purchase_analysis["Normalized Totals"] = gender_purchase_analysis["Normalized Totals"].map("${:.2f}".format)
gender_purchase_analysis = gender_purchase_analysis[["Purchase Count","Average Purchase Price","Total Purchase Value","Normalized Totals"]]
gender_purchase_analysis
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
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
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Age Demographics
bins = [0,9,14,19,24,29,34,39,dataframe["Age"].max()]
bin_names = ["<10","10-14","15-19","20-24","25-29","30-34","35-39","40+"]
age_dataframe = dataframe
age_dataframe["Age Range"] = pd.cut(age_dataframe["Age"], bins, labels=bin_names)
age_groups = age_dataframe.groupby("Age Range")

# DataFrame with duplacates removed
normalized_age_dataframe = gender_df
normalized_age_dataframe["Age Range"] = pd.cut(normalized_age_dataframe["Age"], bins, labels=bin_names)
normalized_age_groups = normalized_age_dataframe.groupby("Age Range")

age_demographics_df = pd.DataFrame({"Purchase Count":age_groups["Price"].count(),
                                        "Average Purchase Price":age_groups["Price"].mean(),
                                        "Total Purchase Value":age_groups["Price"].sum(),
                                   "Normalized Totals":age_groups["Price"].sum()}) 

# Count the number of each age range
age_range_dict = normalized_age_groups["Age"].count().to_dict()
# Loop through and divide the purchase total by the number of each gender
for x in age_range_dict.keys():
    age_demographics_df.loc[x,"Normalized Totals"] = age_demographics_df.loc[x,"Normalized Totals"]/age_range_dict[x]

# Format the monetary values and reorder the columns
age_demographics_df["Average Purchase Price"] = age_demographics_df["Average Purchase Price"].map("${:.2f}".format)
age_demographics_df["Total Purchase Value"] = age_demographics_df["Total Purchase Value"].map("${:.2f}".format)
age_demographics_df["Normalized Totals"] = age_demographics_df["Normalized Totals"].map("${:.2f}".format)
age_demographics_df = age_demographics_df[["Purchase Count","Average Purchase Price","Total Purchase Value","Normalized Totals"]]
age_demographics_df
```

    /anaconda3/lib/python3.6/site-packages/ipykernel/__main__.py:10: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy





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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Range</th>
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
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Top Spenders
top_spenders = dataframe.groupby(["SN"])
top_spender_df = pd.DataFrame({"Purchase Count":top_spenders["Price"].count(),
                                        "Average Purchase Price":top_spenders["Price"].mean(),
                                        "Total Purchase Value":top_spenders["Price"].sum()})
top_spender_df = top_spender_df.sort_values(["Total Purchase Value"],ascending=False)

# Format the monetary values and reorder the columns
top_spender_df["Average Purchase Price"] = top_spender_df["Average Purchase Price"].map("${:.2f}".format)
top_spender_df["Total Purchase Value"] = top_spender_df["Total Purchase Value"].map("${:.2f}".format)
top_spender_df = top_spender_df[["Purchase Count","Average Purchase Price","Total Purchase Value"]]
top_spender_df.head()
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Most Popular Items
popular_item = dataframe.groupby(["Item ID","Item Name"])
popular_item_df = pd.DataFrame({"Purchase Count":popular_item["Price"].count(),
                                        "Item Price":popular_item["Price"].mean(),
                                        "Total Purchase Value":popular_item["Price"].sum()})
most_popular_item_df = popular_item_df.sort_values(["Purchase Count"],ascending=False)

# Format the monetary values and reorder the columns
most_popular_item_df["Item Price"] = most_popular_item_df["Item Price"].map("${:.2f}".format)
most_popular_item_df["Total Purchase Value"] = most_popular_item_df["Total Purchase Value"].map("${:.2f}".format)
most_popular_item_df = most_popular_item_df[["Purchase Count","Item Price","Total Purchase Value"]]
most_popular_item_df.head(6)
# There is a four way tie between the second most popular item
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
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
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
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Most Profitable Items
most_profitable_item_df = popular_item_df.sort_values(["Total Purchase Value"],ascending=False)

# Format the monetary values and reorder the columns
most_profitable_item_df["Item Price"] = most_profitable_item_df["Item Price"].map("${:.2f}".format)
most_profitable_item_df["Total Purchase Value"] = most_profitable_item_df["Total Purchase Value"].map("${:.2f}".format)
most_profitable_item_df = most_profitable_item_df[["Purchase Count","Item Price","Total Purchase Value"]]
most_profitable_item_df.head(5)

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
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
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
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


