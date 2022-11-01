---
layout: post
title:  Pandas常用語法
summary: 資料科學中最基礎的功夫!
date:   2021-07-18 18:05:55 +0800
image:  pandas.jpg
tags:   Python
---

最近在一個以資料科學為主的程式教學平台[datacamp](https://learn.datacamp.com)學習，

寫這篇文章一方面分享學習筆記，

也讓自己未來在操作時有地方翻語法。

[這裡](https://drive.google.com/file/d/1AE_XEhAtSxRu0QnMmnKmUP1bOUYEWVdT/view?usp=sharing)回憶一下matplotlib和dict等的扣。

***

# 目錄

0. [前言](#前言)

1. [Transforming Data](#1)

2. [Aggregating Data](#2)

3. [Slicing and Indexing](#3)

4. [Creating and Visualizing DataFrames](#4)

5. [Data Merging](#5)

6. [Advanced Merging and Concatenating](#6)

7. [Merging Ordered and Time-Series Data](#7)


<a name="1"/>
# Transforming Data

***

*Intro of df*
- df.head()  # 看前五rows
- df.info()  # 跳出各col是int/float/object/...
- df.shape   # 看幾x幾
- df.describe()  # mean/median...
- **df.values**  # 2D Numpy array，**丟入模型常用**
- df.coulmns  # 看cols的名稱
- df.index  # 看rows的名稱
    - len(df.index)  # total rows of dataframe
- df.sort_values("col_a", ascending=True/False)  # 針對col_a排序
    - 可以裝成list, eg. sort_values(["a","b"],ascending=[True,False])
- df["col_a"]  # print出col_a
    - df[["col_a", "col_b"]]  # print出col_a和col_b
    - **df[(~df["col_a"].isnull()) & (~df["col_b"].isnull())]  # 選出col_a和col_b(且都不是空值)**
- df["new"] = df["old1"] + df["old2"]  # 增加col

*Subsetting*
- df["height_cm"] > 60  # True/False
    - df[df["height_cm"] > 60]  # print出高度大於60
    - df[(dogs["height_cm"] > 60) & (dogs["color"] == "tan")]  # 多個條件
- 篩選欄位的值
```python
colors = ["brown", "black", "tan"]  # 只要這些顏色
condition = dogs["color"].isin(colors)
dogs[condition]  # only brown, black, tan dog
```

<a name="2"/>
# Aggregating Data

***

*Summary statistics*
- df["column"].mean()  # 取那個col的平均，類似的有max/min/median
- df["column"].agg(function)  # agg是用來套入function
```python
# Import NumPy and create custom IQR function
import numpy as np
def iqr(column):
   return column.quantile(0.75) - column.quantile(0.25)
# Update to print IQR and median of temperature_c, fuel_price_usd_per_l, & unemployment
print(sales[["temperature_c", "fuel_price_usd_per_l", "unemployment"]].agg([iqr, np.median]))
```
```
              temperature_c   fuel_price_usd_per_l  unemployment
    iqr           16.583               0.073            0.565
    median        16.967               0.743            8.099
```

- df["column"].cumsum()  # 累加column的值
- df["column"].cummax()  # 依照每一列記錄下當前的max
```
             date     weekly_sales  cum_weekly_sales  cum_max_sales
    0  2010-02-05      24924.50          24924.50       24924.50
    1  2010-03-05      21827.90          46752.40       24924.50
    2  2010-04-02      57258.43         104010.83       57258.43
    3  2010-05-07      17413.94         121424.77       57258.43
    4  2010-06-04      17558.09         138982.86       57258.43
    5  2010-07-02      16333.14         155316.00       57258.43
```
- df.drop("col1", axis=0)  # axis=0表示刪除row，axis=1表示刪除column，此處為把col1刪掉
- df.drop_duplicates(subset="column")  # 針對column把重複項刪除
- df.drop_duplicates(subset=["column1","column2"])  # 兩個col都一樣才刪
- df["column"].value_counts()  # 算各項出現幾次
    - df["column"].value_counts(normalize=True)  # 算出比例
    - df["column"].value_counts(sort=True)  # sort
- df.groupby("color")["weight"].mean()  # 算出在不同顏色下不同的平均重量
- df.groupby("color")["weight"].agg([min, max, sum])  # 一次有三個數值
- df.groupby(["color", "breed"])["weight"].mean() 

*Pivot tables*
- df.pivot_table(values="kg", index="color", aggfunc=[np.mean, np.median])  # 跑出像是**樞紐分析表**的東西(aggfunc預設是平均)
- df.pivot_table(values="kg", index="color", columns="breed")  # 空值會是NaN，若想空值補零就加 fill_value=0，多加margins=True會多出一列與一行，顯示各row/column的平均值


<a name="3"/>
# Slicing and Indexing

***

*Setting Index*
- df.index = list  # 把df的index換成list裡面的element
    - pd_ind = pd.set_index("name")  # 設定index，此處把name當作index
    - df.sort_index(level="column1", ascending=False)  # 對index中的column1做sort(descending)
    - pd_ind.loc[["name1", "name2"]]  # 設定index的好處就是能快速找到name1和name2的rows
    - pd_ind1 = pd.set_index(["breed", "color"])  # 也可以同時有兩個
        - pd_ind1 = pd.set_index(["breed", "color"], ascending=[True, False])  # sort
        - pd_ind1.loc[("breed1", "Brown"), ("breed2", "Tan")]  # 用tuple來找交集的rows
- pd_ind.reset_index()  # 重新回到原本pd的樣子
    - pd_ind.reset_index(drop=True)  # 多把name那個col刪掉

*Slicing the df*
- pd.loc["index_x":"index_y"]  # 這也是為什麼要先sort過，再來slice，這會得到index_x到index_y的資料
- df.loc[("country1", "city1"):("country2", "city2")]  # 仍然針對index來做篩選，值得注意適用在多個index的情況下
```
                          date  avg_temp_c
    country  city                         
    Pakistan Lahore 2000-01-01      12.792
             Lahore 2000-02-01      14.339
             Lahore 2000-03-01      20.309
             Lahore 2000-04-01      29.072
             Lahore 2000-05-01      34.845
    ...                    ...         ...
    Russia   Moscow 2013-05-01      16.152
             Moscow 2013-06-01      18.718
             Moscow 2013-07-01      18.136
             Moscow 2013-08-01      17.485
             Moscow 2013-09-01         NaN
```
- df.iloc[:, "col_x":"col_y"]  # 針對column來slice
- df.iloc[2:5, 1:4]  # 對row與column同時來slice(i=index，**適合列標籤太長不容易記得**)

*pivot tables & index*
- df.mean(axis="index")  # 替index算平均
- df.mean(axis="columns")  # 替columns算平均


<a name="4"/>
# Creating and Visualizing DataFrames

***

*Using matplotlib.pyplot to visualize*
- df["column1"].hist()  # histograms(**要先group完!!!**)
    - df["column1"].hist(bins=k)  # 設定bar的數量
    - df["column1"].hist(alpha=0.7)  # 設定透明度，0為完全透明，1為完全不透明
    - df[df["sex"]=="F"]["cm"].hist()  # 篩選出女生高度
    - plt.legend(["F","M"])  # 使用時機: 在一張圖同時有兩個變量時給予顏色(此例為兩變量為男性與女性)
- df.plot(kind="bar", title="test")  # bar plot
- df.plot(x="date", y="kg", kind="line")  # line plots:顯現時間趨勢
- df.plot(x="cm", y="kg", kind="scatter")  # scatter plot:適合用在檢視兩變數關係
- plt.show()  # print

*Dealing with missing data*
- df.isna()  # return True/False, True 代表缺失
    - df.isna().any()  # 看整個column中，有沒有任何缺失值
    - df.isna().sum()  # 算整個column中，缺失值共有幾個
    - df.isna().sum().plot(kind="bar")
- df.dropna()  # 直接不要那行資料
- df.fillna(0)  # NaN填0

*Two Ways of Creating DataFrames*
- From a list of dicts
    - row by row
```python
list_of_dicts = [
    {"name": "A", "grade": 90},
    {"name": "B" "grade": 85},
    {"name": "C", "grade": 80}
]
```
    - pd.DataFrame(list_of_dicts)
- From a dict of lists
    - col by col
```python
dict_of_lists = {
    "name": ["A", "B", "C"],
    "grade": [90, 85, 80]
}
```
    - key = column name
    - value = list of column values
    - pd.DataFrame(dict_of_lists)

*Reading and writing CSVs*
```python
# CSV to DataFrame
import pandas as pd
newfile = pd.read_csv("newfile.csv", index_col=0)   # make the first col as index
print(newfile)
# DataFrame to CSV
newfile.to_csv("newfile.csv")
```

<a name="5"/>
# Data Merging

***

*inner join*
- df1.merge(df2, on='**common**')  # on 是用在兩個df交集的地方，u**也就是共同部分**
- suffixes is to avoid multiple columns w/ same name(在兩個df有同樣的名字，因此要作出區別)
```python
# Merge the taxi_owners and taxi_veh tables setting a suffix
taxi_own_veh = taxi_owners.merge(taxi_veh, on='vid', suffixes=('_own','_veh'))
```

*Merging multiple DataFrames*
- one-to-many relationships: every row in left table is related to one or more rows in the right table (一對多)
- 同時merge三個df：
    - cal有 year, month, day, day_type 三個columns
    - ridership有 station_id, year, month, day, rides 五個columns
    - stations有 station_id, staton_name, location 三個columns
```python
# Merge the ridership, cal, and stations tables
ridership_cal_stations = ridership.merge(cal, on=['year','month','day']) \
							.merge(stations, on='station_id')
```
- 另外一個範例，搭配groupby和agg
```python
# Merge licenses and zip_demo, on zip; and merge the wards on ward
licenses_zip_ward = licenses.merge(zip_demo, on='zip') \
            			.merge(wards, on='ward')
# Print the results by alderman and show median income
print(licenses_zip_ward.groupby('alderman').agg({'income':'median'}))
```

*left join*
- 僅取left table，意思是**left table有n個row，join後的結果還是n個rows**，只有columns的數量會改變，以左邊為主。
- how 的預設是inner join，若要改inner為how，則要多打how，範例如下。
```python
# Merge the movies table with the financials table with a left join
movies_financials = movies.merge(financials, on='id', how='left')
# Count the number of rows in the budget column that are missing
number_of_missing_fin = movies_financials['budget'].isnull().sum()
# Print the number of movies missing financials
print(number_of_missing_fin)
```

*right join*
- 以右邊為主(被加入的)
```python
# Merge action_movies to the scifi_movies with right join
action_scifi = action_movies.merge(scifi_movies, on='movie_id', how='right',
                                   suffixes=('_act','_sci'))
# From action_scifi, select only the rows where the genre_act column is null
scifi_only = action_scifi[action_scifi['genre_act'].isnull()]
# Merge the movies and scifi_only tables with an inner join
movies_and_scifi_only = movies.merge(scifi_only, left_on='id', right_on='movie_id', how='inner')
```

*outer join*
- 最後的table會是兩個table最大的樣子，意思是指假設table1為3*4，table2為2*5，則合併出來是3*5(row和column都取最大)，**保留所有部分**。
```python
# Merge iron_1_actors to iron_2_actors on id with outer join using suffixes
iron_1_and_2 = iron_1_actors.merge(iron_2_actors, on='id', how='outer', suffixes=('_1','_2'))
```

*Merging a table to itself*
- 使用時機: 在同個table裡，某一row跟另一個row有關係，這時要把有關係的抓出來，下例是一個director底下很多crew。
```python
# Merge the crews table to itself
crews_self_merged = crews.merge(crews, on='id', how='inner',
                                suffixes=('_dir','_crew'))
# Create a boolean index to select the appropriate rows
boolean_filter = ((crews_self_merged['job_dir'] == 'Director') & 
                  (crews_self_merged['job_crew'] != 'Director'))
direct_crews = crews_self_merged[boolean_filter]
# Print the first few rows of direct_crews
print(direct_crews.head())
```
```
            id   job_dir       name_dir        job_crew          name_crew
    156  19995  Director  James Cameron          Editor  Stephen E. Rivkin
    157  19995  Director  James Cameron  Sound Designer  Christopher Boyes
    158  19995  Director  James Cameron         Casting          Mali Finn
    160  19995  Director  James Cameron          Writer      James Cameron
    161  19995  Director  James Cameron    Set Designer    Richard F. Mays
```

*Merging on indexes*
- Example 1:
```python
# Merge to the movies table the ratings table on the index
movies_ratings = movies.merge(ratings, on='id')
# Print the first few rows of movies_ratings
print(movies_ratings.head())
```
- Example 2: 看續集有沒有賺比較多(**當兩邊index不一樣時可以使用left_on和right_on來指定merge的index**)
```python
# Merge sequels and financials on index id
sequels_fin = sequels.merge(financials, on='id', how='left')
# Self merge with suffixes as inner join with left on sequel and right on id
orig_seq = sequels_fin.merge(sequels_fin, how='inner', left_on='sequel', 
                             right_on='id', right_index=True,
                             suffixes=('_org','_seq'))
# Add calculation to subtract revenue_org from revenue_seq 
orig_seq['diff'] = orig_seq['revenue_seq'] - orig_seq['revenue_org']
# Select the title_org, title_seq, and diff 
titles_diff = orig_seq[['title_org','title_seq','diff']]
```
```
# sequels_fin.head()
              title sequel       budget       revenue
id                                                   
19995        Avatar    nan  237000000.0  2.787965e+09
862       Toy Story    863   30000000.0  3.735540e+08
863     Toy Story 2  10193   90000000.0  4.973669e+08
597         Titanic    nan  200000000.0  1.845034e+09
24428  The Avengers    nan  220000000.0  1.519558e+09
-
# orig_seq.head()
    sequel                                          title_org sequel_org   budget_org  revenue_org                                      title_seq sequel_seq   budget_seq   revenue_seq         diff
id                                                                                                                                                                                                  
862    863                                          Toy Story        863   30000000.0  373554033.0                                    Toy Story 2      10193   90000000.0  4.973669e+08  123812836.0
863  10193                                        Toy Story 2      10193   90000000.0  497366869.0                                    Toy Story 3        nan  200000000.0  1.066970e+09  569602834.0
675    767          Harry Potter and the Order of the Phoenix        767  150000000.0  938212738.0         Harry Potter and the Half-Blood Prince        nan  250000000.0  9.339592e+08   -4253541.0
121    122              The Lord of the Rings: The Two Towers        122   79000000.0  926287400.0  The Lord of the Rings: The Return of the King        nan   94000000.0  1.118889e+09  192601579.0
120    121  The Lord of the Rings: The Fellowship of the Ring        121   93000000.0  871368364.0          The Lord of the Rings: The Two Towers        122   79000000.0  9.262874e+08   54919036.0
-
# titles_diff.sort_values('diff', ascending=False).head()
               title_org        title_seq          diff
id                                                     
331    Jurassic Park III   Jurassic World  1.144748e+09
272        Batman Begins  The Dark Knight  6.303398e+08
10138         Iron Man 2       Iron Man 3  5.915067e+08
863          Toy Story 2      Toy Story 3  5.696028e+08
10764  Quantum of Solace          Skyfall  5.224703e+08
```

<a name="6"/>
# Advanced Merging and Concatenating

***

*Filtering joins*
- semi-join:
    - returns the intersection
    - returns only columns from the left teble and **not** the right
    - no duplicates
    - Example: 一堆發票中，找出是non-music的
```python
# Merge the non_mus_tck and top_invoices tables on tid
tracks_invoices = non_mus_tcks.merge(top_invoices, on='tid')
# Use .isin() to subset non_mus_tcsk to rows with tid in tracks_invoices
top_tracks = non_mus_tcks[non_mus_tcks['tid'].isin(tracks_invoices['tid'])]
# Group the top_tracks by gid and count the tid rows
cnt_by_gid = top_tracks.groupby(['gid'], as_index=False).agg({'tid':'count'})
# Merge the genres table to cnt_by_gid on gid and print
print(cnt_by_gid.merge(genres, on='gid'))
```
```
# tracks_invoices.head()
    tid       name  aid  mtid  gid  u_price  ilid  iid  uprice  quantity
0  2850    The Fix  228     3   21     1.99   473   88    1.99         1
1  2850    The Fix  228     3   21     1.99  2192  404    1.99         1
2  2868  Walkabout  230     3   19     1.99   476   88    1.99         1
3  2868  Walkabout  230     3   19     1.99  2194  404    1.99         1
4  3177   Hot Girl  249     3   19     1.99  1668  306    1.99         1
-
# top_tracks.heads()
       tid                          name  aid  mtid  gid  u_price
2849  2850                       The Fix  228     3   21     1.99
2867  2868                     Walkabout  230     3   19     1.99
3176  3177                      Hot Girl  249     3   19     1.99
3199  3200                Gay Witch Hunt  251     3   19     1.99
3213  3214             Phyllis's Wedding  251     3   22     1.99
```
- anti-join:
    - returns the left table, excluding the intersection
    - returns only columns from the left teble and **not** the right
    - Example: 一個員工管理許多顧客，目標為找出那些沒有對應到高品質顧客的員工
```python
# Merge employees and top_cust(indicator generate the column "_merge", ,meaning there's no info from the right table)
empl_cust = employees.merge(top_cust, on='srid', 
                                 how='left', indicator=True)
# Select the srid column where _merge is left_only
srid_list = empl_cust.loc[empl_cust['_merge'] == 'left_only', 'srid']
```
```
# empl_cust.head() (前面是員工，後面是高品質顧客)
    srid  lname_x fname_x                title  hire_date  ...    lname_y               phone                 fax                        email_y     _merge
0     1    Adams  Andrew      General Manager 2002-08-14  ...        NaN                 NaN                 NaN                            NaN  left_only
1     2  Edwards   Nancy        Sales Manager 2002-05-01  ...        NaN                 NaN                 NaN                            NaN  left_only
2     3  Peacock    Jane  Sales Support Agent 2002-04-01  ...  Gonçalves  +55 (12) 3923-5555  +55 (12) 3923-5566           luisg@embraer.com.br       both
3     3  Peacock    Jane  Sales Support Agent 2002-04-01  ...   Tremblay   +1 (514) 721-4711                 NaN            ftremblay@gmail.com       both
4     3  Peacock    Jane  Sales Support Agent 2002-04-01  ...    Almeida  +55 (21) 2271-7000  +55 (21) 2271-7070  roberto.almeida@riotur.gov.br       both
-
# srid_list
     srid
0     1
1     2
61    6
62    7
63    8
-
# employees[employees['srid'].isin(srid_list)]
        srid     lname    fname            title  hire_date                    email
    0     1     Adams   Andrew  General Manager 2002-08-14   andrew@chinookcorp.com
    1     2   Edwards    Nancy    Sales Manager 2002-05-01    nancy@chinookcorp.com
    5     6  Mitchell  Michael       IT Manager 2003-10-17  michael@chinookcorp.com
    6     7      King   Robert         IT Staff 2004-01-02   robert@chinookcorp.com
    7     8  Callahan    Laura         IT Staff 2004-03-04    laura@chinookcorp.com
```

*Concatenate DataFrames together vertically*
- 只要ignore_index=True就能讓原本的index不見，取而代之的是整齊數列。
```python
# Concatenate the tracks so the index goes from 0 to n-1
tracks_from_albums = pd.concat([tracks_master, tracks_ride, tracks_st],
                               ignore_index=True,
                               sort=True)
```
- 設定index，並且看在不同月份下營業額的平均(由於月份是index，所以level=0)
```python
# Concatenate the tables and add keys
inv_jul_thr_sep = pd.concat([inv_jul, inv_aug,inv_sep], 
                            keys=['7Jul', '8Aug', '9Sep'])
# Group the invoices by the index keys and find avg of the total column
avg_inv_by_month = inv_jul_thr_sep.groupby(level=0).agg({'total':'mean'})
```

*Verifying integrity*(避免重複出現，實務上的資料並不乾淨!!)
- validate='one_to_many'代表on的東西是一對多
```python
albums.merge(tracks, on='aid', validate='one_to_many')
```
- verify_integrity True代表同一index不能有不同值
```python
pd.concat([inv_feb, inv_mar], verify_integrity=False)
```

<a name="7"/>
# Merging Ordered and Time-Series Data

***

*merge_ordered()*
- 適合用在有時間相關的資料
- 可以填入missing data
```python
# Use merge_ordered() to merge gdp and sp500, interpolate missing value
gdp_sp500 = pd.merge_ordered(gdp, sp500, left_on='year', right_on='date', 
                             how='left',  fill_method='ffill')
# Subset the gdp and returns columns
gdp_returns = gdp_sp500[['gdp', 'returns']]
# Print gdp_returns correlation
print (gdp_returns.corr())
```

*merge_asof()*
- similar to left-join
- match on the nearset key column and not exact match(可以設定為大於等於/小於等於/最靠近的)
- 預設是backward，也就是選小於等於最靠近的
```python
# Use merge_asof() to merge jpm and wells
jpm_wells = pd.merge_asof(jpm, wells, on='date_time', 
                          suffixes=('', '_wells'), direction='nearest')
# Use merge_asof() to merge jpm_wells and bac
jpm_wells_bac = pd.merge_asof(jpm_wells, bac, on='date_time', 
                              suffixes=('_jpm', '_bac'), direction='nearest')
```
```python
# Merge gdp and recession on date using merge_asof()
gdp_recession = pd.merge_asof(gdp, recession, on='date')
# Create a list based on the row value of gdp_recession['econ_status']
is_recession = ['r' if s=='recession' else 'g' for s in gdp_recession['econ_status']]
# Plot a bar chart of gdp_recession
gdp_recession.plot(kind='bar', y='gdp', x='date', color=is_recession, rot=90)
plt.show()
```

*Selecting data with .query()*
- Example: 資料長這樣
```
            financial  company  year    value
0       total_revenue  twitter  2019  3459329
1     cost_of_revenue  twitter  2019  1137041
2        gross_profit  twitter  2019  2322288
3  operating_expenses  twitter  2019  1955915
4          net_income  twitter  2019  1465659
```
```python
# 找NI<0的公司
social_fin.query('financial == "net_income" and value < 0')
# 找FB的rev
social_fin.query('company == "facebook" and financial == "total_revenue"')
```

*Reshaping data with .melt()*
- 可以讓表格更好讀
```python
# Unpivot everything besides the year column
ur_tall = ur_wide.melt(id_vars=['year'], var_name='month', 
                       value_name='unempl_rate')
```
```
ur_wide(old):
        year  jan  feb  mar  apr  ...  aug  sep  oct  nov  dec
    0   2010  9.8  9.8  9.9  9.9  ...  9.5  9.5  9.4  9.8  9.3
    1   2011  9.1  9.0  9.0  9.1  ...  9.0  9.0  8.8  8.6  8.5
    2   2012  8.3  8.3  8.2  8.2  ...  8.1  7.8  7.8  7.7  7.9
    3   2013  8.0  7.7  7.5  7.6  ...  7.2  7.2  7.2  6.9  6.7
    4   2014  6.6  6.7  6.7  6.2  ...  6.1  5.9  5.7  5.8  5.6
    5   2015  5.7  5.5  5.4  5.4  ...  5.1  5.0  5.0  5.1  5.0
    6   2016  4.9  4.9  5.0  5.0  ...  4.9  5.0  4.9  4.7  4.7
    7   2017  4.7  4.6  4.4  4.4  ...  4.4  4.2  4.1  4.2  4.1
    8   2018  4.1  4.1  4.0  4.0  ...  3.8  3.7  3.8  3.7  3.9
    9   2019  4.0  3.8  3.8  3.6  ...  3.7  3.5  3.6  3.5  3.5
    10  2020  3.6  3.5  4.4  NaN  ...  NaN  NaN  NaN  NaN  NaN
ur_tall(new):
         year  month     unempl_rate
    0    2010   jan          9.8
    1    2011   jan          9.1
    2    2012   jan          8.3
    3    2013   jan          8.0
    4    2014   jan          6.6
    5    2015   jan          5.7
    6    2016   jan          4.9
    7    2017   jan          4.7
    8    2018   jan          4.1
    9    2019   jan          4.0
    10   2020   jan          3.6
    11   2010   feb          9.8
    12   2011   feb          9.0
    13   2012   feb          8.3
    14   2013   feb          7.7
    15   2014   feb          6.7
```
