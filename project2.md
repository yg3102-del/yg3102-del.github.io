# Project II: Cardiovascular Mortality and Aging Population

Using datasets from the World Health Organization, or WHO, I will explore how cardiovascular disease deaths vary across countries and years, and examine one central demographic factor, the share of the population aged 60 and above.

The reason why I want to explore this topic is because cardiovascular disearses are one of the leading causes of death worldwide. What is more, population is widely considered as a key structural driver behind increasing mortality risk.

By conbining the cause-specific mortality data with detailed age-group population data, this projects inverstigates whether aging populations experience higher CVD mortality buredens.

Hypothesis: Countries with a larger proportion of older adults (aged 60+) will have higher cardiovascular mortality rates. Even within a short time window, we expect cross-country differences in age structure to align with differences in cardiovascular disease burden.


```python
import pandas as pd
import matplotlib.pyplot as plt
```

    Matplotlib is building the font cache; this may take a moment.


For the sake of this exploration, I will begin with the WHO Mortality Database (ICD-10).


```python
# import first dataset: WHO mortality data (ICD-10 causes)
morticd = pd.read_csv("data/Morticd10.csv", dtype=str)
morticd
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
      <th>Country</th>
      <th>Admin1</th>
      <th>SubDiv</th>
      <th>Year</th>
      <th>List</th>
      <th>Cause</th>
      <th>Sex</th>
      <th>Frmat</th>
      <th>IM_Frmat</th>
      <th>Deaths1</th>
      <th>...</th>
      <th>Deaths21</th>
      <th>Deaths22</th>
      <th>Deaths23</th>
      <th>Deaths24</th>
      <th>Deaths25</th>
      <th>Deaths26</th>
      <th>IM_Deaths1</th>
      <th>IM_Deaths2</th>
      <th>IM_Deaths3</th>
      <th>IM_Deaths4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1400</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2021</td>
      <td>101</td>
      <td>1000</td>
      <td>1</td>
      <td>07</td>
      <td>01</td>
      <td>481</td>
      <td>...</td>
      <td>155</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>5</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1400</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2021</td>
      <td>101</td>
      <td>1000</td>
      <td>2</td>
      <td>07</td>
      <td>01</td>
      <td>444</td>
      <td>...</td>
      <td>219</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>4</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1400</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2021</td>
      <td>101</td>
      <td>1001</td>
      <td>1</td>
      <td>07</td>
      <td>01</td>
      <td>14</td>
      <td>...</td>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1400</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2021</td>
      <td>101</td>
      <td>1001</td>
      <td>2</td>
      <td>07</td>
      <td>01</td>
      <td>8</td>
      <td>...</td>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1400</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2021</td>
      <td>101</td>
      <td>1002</td>
      <td>1</td>
      <td>07</td>
      <td>01</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>369235</th>
      <td>5020</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2023</td>
      <td>104</td>
      <td>Y86</td>
      <td>2</td>
      <td>00</td>
      <td>01</td>
      <td>38</td>
      <td>...</td>
      <td>4</td>
      <td>7</td>
      <td>8</td>
      <td>5</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>369236</th>
      <td>5020</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2023</td>
      <td>104</td>
      <td>Y881</td>
      <td>1</td>
      <td>00</td>
      <td>01</td>
      <td>2</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
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
      <th>369237</th>
      <td>5020</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2023</td>
      <td>104</td>
      <td>Y883</td>
      <td>1</td>
      <td>00</td>
      <td>01</td>
      <td>5</td>
      <td>...</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>369238</th>
      <td>5020</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2023</td>
      <td>104</td>
      <td>Y883</td>
      <td>2</td>
      <td>00</td>
      <td>01</td>
      <td>1</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>369239</th>
      <td>5020</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2023</td>
      <td>104</td>
      <td>Y890</td>
      <td>1</td>
      <td>00</td>
      <td>01</td>
      <td>1</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
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
<p>369240 rows × 39 columns</p>
</div>



This dataset reports annual death counts by country, year, sex, and cause of death.


```python
# convert key columns to numeric
morticd["Cause"] = morticd["Cause"].astype(str)
cvd = morticd[morticd["Cause"].str.startswith("I", na=False)]
cvd.shape
```




    (43655, 39)



To focus the analysis, I limit the dataset to cardiovascular diseases (ICD-10 cause codes 1000–1999). These causes include heart disease, stroke, and other major cardiovascular conditions. Summing the deaths across sexes gives the total number of cardiovascular deaths for each country and year.


```python
# ICD-10 cardiovascular diseases are 1000–1999
cvd["Year"] = pd.to_numeric(cvd["Year"], errors="coerce")
cvd["Deaths1"] = pd.to_numeric(cvd["Deaths1"], errors="coerce")
cvd["Country"] = pd.to_numeric(cvd["Country"], errors="coerce")

```

    /var/folders/wq/202j15cn72x_7mv_cy98qm300000gn/T/ipykernel_59348/903554875.py:2: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      cvd["Year"] = pd.to_numeric(cvd["Year"], errors="coerce")
    /var/folders/wq/202j15cn72x_7mv_cy98qm300000gn/T/ipykernel_59348/903554875.py:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      cvd["Deaths1"] = pd.to_numeric(cvd["Deaths1"], errors="coerce")
    /var/folders/wq/202j15cn72x_7mv_cy98qm300000gn/T/ipykernel_59348/903554875.py:4: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      cvd["Country"] = pd.to_numeric(cvd["Country"], errors="coerce")



```python
cvd_country_year = (
    cvd.groupby(["Country", "Year"], as_index=False)["Deaths1"]
       .sum()
       .rename(columns={"Deaths1": "CVD_deaths"})
)

cvd_country_year.head()

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
      <th>Country</th>
      <th>Year</th>
      <th>CVD_deaths</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1030</td>
      <td>2021</td>
      <td>1278</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1300</td>
      <td>2021</td>
      <td>4225</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1300</td>
      <td>2022</td>
      <td>4525</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1300</td>
      <td>2023</td>
      <td>3636</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1303</td>
      <td>2021</td>
      <td>198</td>
    </tr>
  </tbody>
</table>
</div>



The second dataset is the WHO Population and Live Births data.


```python
# import second dataset: WHO Population and Live Births data
pop = pd.read_csv("data/pop.csv", dtype=str)
pop.head()
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
      <th>Country</th>
      <th>Admin1</th>
      <th>SubDiv</th>
      <th>Year</th>
      <th>Sex</th>
      <th>Frmat</th>
      <th>Pop1</th>
      <th>Pop2</th>
      <th>Pop3</th>
      <th>Pop4</th>
      <th>...</th>
      <th>Pop18</th>
      <th>Pop19</th>
      <th>Pop20</th>
      <th>Pop21</th>
      <th>Pop22</th>
      <th>Pop23</th>
      <th>Pop24</th>
      <th>Pop25</th>
      <th>Pop26</th>
      <th>Lb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1030</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2021</td>
      <td>1</td>
      <td>04</td>
      <td>1175948</td>
      <td>26106</td>
      <td>87398</td>
      <td>NaN</td>
      <td>...</td>
      <td>27217</td>
      <td>21181</td>
      <td>13953</td>
      <td>17519</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>25765</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1030</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2021</td>
      <td>2</td>
      <td>04</td>
      <td>1217451</td>
      <td>25831</td>
      <td>86477</td>
      <td>NaN</td>
      <td>...</td>
      <td>34925</td>
      <td>27390</td>
      <td>17744</td>
      <td>25801</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>25197</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1060</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1980</td>
      <td>1</td>
      <td>07</td>
      <td>137100</td>
      <td>3400</td>
      <td>15800</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>5300</td>
      <td>NaN</td>
      <td>2900</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>6500</td>
      <td>5000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1060</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1980</td>
      <td>2</td>
      <td>07</td>
      <td>159000</td>
      <td>4000</td>
      <td>18400</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>6200</td>
      <td>NaN</td>
      <td>3400</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7500</td>
      <td>6000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1125</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1955</td>
      <td>1</td>
      <td>02</td>
      <td>5051500</td>
      <td>150300</td>
      <td>543400</td>
      <td>NaN</td>
      <td>...</td>
      <td>110200</td>
      <td>51100</td>
      <td>41600</td>
      <td>14300</td>
      <td>11800</td>
      <td>25300</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>253329</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>10205</th>
      <td>5198</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2017</td>
      <td>2</td>
      <td>03</td>
      <td>319192.6264</td>
      <td>8018.862999</td>
      <td>7989.670031</td>
      <td>7977.625751</td>
      <td>...</td>
      <td>6239.247979</td>
      <td>5062.873097</td>
      <td>3601.905772</td>
      <td>4347.471504</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>10206</th>
      <td>5198</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>1</td>
      <td>03</td>
      <td>340520.5176</td>
      <td>8630.00982</td>
      <td>8552.239421</td>
      <td>8515.408794</td>
      <td>...</td>
      <td>6587.270532</td>
      <td>5109.808634</td>
      <td>3482.798415</td>
      <td>4450.304553</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>10207</th>
      <td>5198</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>2</td>
      <td>03</td>
      <td>326036.483</td>
      <td>8015.096596</td>
      <td>7986.494468</td>
      <td>7975.603354</td>
      <td>...</td>
      <td>6422.052069</td>
      <td>5257.338854</td>
      <td>3744.042066</td>
      <td>4552.898314</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>10208</th>
      <td>5200</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2003</td>
      <td>1</td>
      <td>04</td>
      <td>51711</td>
      <td>1156</td>
      <td>5045</td>
      <td>NaN</td>
      <td>...</td>
      <td>1390</td>
      <td>1193</td>
      <td>859</td>
      <td>952</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>1433</td>
    </tr>
    <tr>
      <th>10209</th>
      <td>5200</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2003</td>
      <td>2</td>
      <td>04</td>
      <td>49693</td>
      <td>1065</td>
      <td>4713</td>
      <td>NaN</td>
      <td>...</td>
      <td>1406</td>
      <td>1122</td>
      <td>854</td>
      <td>1006</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>1348</td>
    </tr>
  </tbody>
</table>
<p>10210 rows × 33 columns</p>
</div>



This dataset contains the total population broken down into 26 age groups for each country and year.


```python
pop["Country"] = pd.to_numeric(pop["Country"], errors="coerce")
pop["Year"] = pd.to_numeric(pop["Year"], errors="coerce")

pop_cols = [c for c in pop.columns if c.startswith("Pop")]
elderly_cols = ["Pop13", "Pop14", "Pop15", "Pop16", "Pop17", "Pop18", "Pop19", "Pop20", "Pop21"]

```


```python
for c in pop_cols:
    pop[c] = pd.to_numeric(pop[c], errors="coerce")
    
pop["TotalPop"] = pop[pop_cols].sum(axis=1)
pop["Elderly"] = pop[elderly_cols].sum(axis=1)
pop["ElderlyRatio"] = pop["Elderly"] / pop["TotalPop"]

pop_simple = pop[["Country", "Year", "TotalPop", "Elderly", "ElderlyRatio"]]
pop_simple.head()
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
      <th>Country</th>
      <th>Year</th>
      <th>TotalPop</th>
      <th>Elderly</th>
      <th>ElderlyRatio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1030</td>
      <td>2021</td>
      <td>2351896.0</td>
      <td>409647.0</td>
      <td>0.174177</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1030</td>
      <td>2021</td>
      <td>2434902.0</td>
      <td>451969.0</td>
      <td>0.185621</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1060</td>
      <td>1980</td>
      <td>274100.0</td>
      <td>36800.0</td>
      <td>0.134258</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1060</td>
      <td>1980</td>
      <td>318000.0</td>
      <td>42800.0</td>
      <td>0.134591</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1125</td>
      <td>1955</td>
      <td>10103000.0</td>
      <td>1450300.0</td>
      <td>0.143551</td>
    </tr>
  </tbody>
</table>
</div>



Because this project focuses on population aging, I use age groups Pop20–Pop26, which correspond to adults aged 60 and above. Summing these values gives the older population, and comparing it to the total population provides the elderly population share.

The mortality and population data use numeric country codes. I merge a country‐code lookup table to attach readable country names.


```python
codes = pd.read_csv("data/country_codes.csv", dtype=str)
codes.head()
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
      <th>country</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1010</td>
      <td>Algeria</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1020</td>
      <td>Angola</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1025</td>
      <td>Benin</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1030</td>
      <td>Botswana</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1035</td>
      <td>Burkina Faso</td>
    </tr>
  </tbody>
</table>
</div>




```python
# merge mortality and population
merged = pd.merge(
    cvd_country_year,
    pop_simple,
    on=["Country", "Year"],
    how="inner"
)

# attach country names
merged["Country"] = merged["Country"].astype(int).astype(str)
codes["country"] = codes["country"].astype(str)

merged = merged.merge(codes, left_on="Country", right_on="country", how="left")
merged.drop(columns=["country"], inplace=True)

# compute CVD mortality rate
merged["CVD_mortality_rate"] = merged["CVD_deaths"] / merged["TotalPop"] * 100000

# keep recent years in your dataset
merged = merged[(merged["Year"] >= 2021) & (merged["Year"] <= 2023)]

merged.head()
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
      <th>Country</th>
      <th>Year</th>
      <th>CVD_deaths</th>
      <th>TotalPop</th>
      <th>Elderly</th>
      <th>ElderlyRatio</th>
      <th>name</th>
      <th>CVD_mortality_rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1030</td>
      <td>2021</td>
      <td>1278</td>
      <td>2351896.0</td>
      <td>409647.0</td>
      <td>0.174177</td>
      <td>Botswana</td>
      <td>54.339137</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1030</td>
      <td>2021</td>
      <td>1278</td>
      <td>2434902.0</td>
      <td>451969.0</td>
      <td>0.185621</td>
      <td>Botswana</td>
      <td>52.486712</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1300</td>
      <td>2021</td>
      <td>4225</td>
      <td>1252006.0</td>
      <td>318947.0</td>
      <td>0.254749</td>
      <td>Mauritius</td>
      <td>337.458447</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1300</td>
      <td>2021</td>
      <td>4225</td>
      <td>1280114.0</td>
      <td>334217.0</td>
      <td>0.261084</td>
      <td>Mauritius</td>
      <td>330.048730</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1300</td>
      <td>2022</td>
      <td>4525</td>
      <td>1247574.0</td>
      <td>319495.0</td>
      <td>0.256093</td>
      <td>Mauritius</td>
      <td>362.703936</td>
    </tr>
  </tbody>
</table>
</div>



Using country and year as common identifiers, I merge the cardiovascular mortality data with the population data and then attach country names from the code table.

Before selecting representative countries, I categorize all countries into aging levels based on their average elderly share (2021–2023):

High-aging: ≥ 30% aged 60+

Medium-aging: 15–30%

Low-aging: < 15%

This provides a global view of aging patterns and helps justify selecting one country from each group.


```python
# compute average elderly ratio per country (2021–2023)
aging_summary = (
    merged.groupby("Country")["ElderlyRatio"]
           .mean()
           .reset_index()
           .merge(codes, left_on="Country", right_on="country", how="left")
)

def classify(x):
    if x >= 0.30:
        return "High-aging"
    elif x >= 0.15:
        return "Medium-aging"
    else:
        return "Low-aging"

aging_summary["AgingGroup"] = aging_summary["ElderlyRatio"].apply(classify)

aging_summary.head()

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
      <th>Country</th>
      <th>ElderlyRatio</th>
      <th>country</th>
      <th>name</th>
      <th>AgingGroup</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1030</td>
      <td>0.179899</td>
      <td>1030</td>
      <td>Botswana</td>
      <td>Medium-aging</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1300</td>
      <td>0.259614</td>
      <td>1300</td>
      <td>Mauritius</td>
      <td>Medium-aging</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3080</td>
      <td>0.257565</td>
      <td>3080</td>
      <td>Cyprus</td>
      <td>Medium-aging</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3090</td>
      <td>0.312120</td>
      <td>3090</td>
      <td>Hong Kong SAR</td>
      <td>High-aging</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3150</td>
      <td>0.203881</td>
      <td>3150</td>
      <td>Israel</td>
      <td>Medium-aging</td>
    </tr>
  </tbody>
</table>
</div>



Bar Chart: Number of Countries by Aging Group


```python
count_by_group = aging_summary["AgingGroup"].value_counts()

plt.figure(figsize=(6,4))
plt.bar(count_by_group.index, count_by_group.values, color=["orange","skyblue","lightgreen"])
plt.xlabel("Aging Group")
plt.ylabel("Number of Countries")
plt.title("Distribution of Countries by Aging Level (2021–2023)")
plt.show()

```


    
![png](project2_files/project2_25_0.png)
    



```python
aging_summary["name"].sort_values().unique()[:50]

```




    array(['Armenia', 'Australia', 'Austria', 'Belgium',
           'Bosnia and Herzegovina', 'Botswana', 'Bulgaria', 'Croatia',
           'Cyprus', 'Czech Republic', 'Denmark', 'Finland', 'France',
           'Georgia', 'Hong Kong SAR', 'Hungary', 'Iceland', 'Ireland',
           'Israel', 'Italy', 'Japan', 'Kazakhstan', 'Kuwait', 'Latvia',
           'Lithuania', 'Luxembourg', 'Mauritius', 'Mongolia', 'Netherlands',
           'North Macedonia', 'Occupied Palestinian Territory', 'Oman',
           'Poland', 'Qatar', 'Republic of Korea', 'Saudi Arabia', 'Serbia',
           'Singapore', 'Slovakia', 'Spain', 'Sweden', 'Thailand', 'Turkey',
           'United Kingdom, England and Wales',
           'United Kingdom, Northern Ireland'], dtype=object)




```python
selected_names = ["Japan", "France", "Botswana"]

selected_rows = aging_summary[aging_summary["name"].isin(selected_names)]

selected_three = selected_rows.copy()

selected_three[["Country", "name", "ElderlyRatio"]]



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
      <th>Country</th>
      <th>name</th>
      <th>ElderlyRatio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1030</td>
      <td>Botswana</td>
      <td>0.179899</td>
    </tr>
    <tr>
      <th>5</th>
      <td>3160</td>
      <td>Japan</td>
      <td>0.296368</td>
    </tr>
    <tr>
      <th>25</th>
      <td>4080</td>
      <td>France</td>
      <td>0.266599</td>
    </tr>
  </tbody>
</table>
</div>




```python
country_list = selected_three["Country"].astype(str).tolist()
df_plot = merged[ merged["Country"].isin(country_list) ]

```


```python
country_list = selected_three["Country"].astype(str).tolist()

df_plot = merged[merged["Country"].isin(country_list)]

plt.figure(figsize=(10,6))

colors = {
    "Japan": "red",
    "France": "blue",
    "Botswana": "green"
}

for cid in country_list:
    df_c = df_plot[df_plot["Country"] == cid].sort_values("Year")
    cname = df_c["name"].iloc[0]

    plt.plot(df_c["ElderlyRatio"], df_c["CVD_mortality_rate"],
             marker="o", label=cname, color=colors.get(cname, None))

    for _, row in df_c.iterrows():
        plt.text(
            row["ElderlyRatio"] + 0.0003,
            row["CVD_mortality_rate"],
            str(int(row["Year"])),
            fontsize=9
        )

plt.xlabel("Share of Population Aged 60+")
plt.ylabel("CVD Mortality Rate (per 100,000)")
plt.title("Cardiovascular Mortality vs Aging Level (Japan, France, Botswana, 2021 or 2022)")
plt.legend()
plt.tight_layout()
plt.show()

```


    
![png](project2_files/project2_29_0.png)
    


Japan, the most aged population in the comparison, consistently shows the highest CVD mortality rates, supporting the expectation that older societies face a greater cardiovascular burden. France, with a moderate elderly share, exhibits mid-range mortality levels, while Botswana, the youngest population among the three, reports the lowest CVD mortality rates.

Despite small year-to-year fluctuations, the overall pattern suggests that differences in demographic age structure are strongly associated with differences in cardiovascular mortality, and this association appears more substantial than short-term variation within each country.

Summary: Using WHO mortality and population datasets, this project examines the relationship between a country’s aging structure and its burden of cardiovascular disease (CVD). Because data availability varies by country and year, the analysis relies on the most recent complete observations from 2021 or 2022, ensuring consistency across the three selected countries: Japan, France, and Botswana. These countries represent high-aging, medium-aging, and low-aging societies, respectively. 

By plotting the share of adults aged 60+ against CVD mortality rates, the visualization highlights a clear pattern: older societies tend to experience higher CVD burdens, even after accounting for differences in total population size. Although healthcare quality and socioeconomic factors also play important roles, the chart suggests that population aging alone remains a strong structural driver of cardiovascular mortality.

Refelction: Even though the dataset is limited, some countries only reported one or two recent years. I still learned a lot from working through the analysis. Cleaning the data, merging different sources, and visualizing cross-country patterns helped me better understand both the strengths and the gaps in real-world public health data. The project also showed me that even with imperfect data, it is still possible to identify meaningful patterns and develop reasonable insights, as long as the limitations are clearly acknowledged.
