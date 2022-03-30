---
layout: post
title:  "Third guided project"
---

# Third guided project

With this project, we will see which app is helpful for our company and let develpers know what to concentrate on.



```python
def explore_data(dataset, start, end, rows_and_columns=False):
    dataset_slice = dataset[start:end]    
    for row in dataset_slice:
        print(row)
        print('\n') # adds a new (empty) line after each row

    if rows_and_columns:
        print('Number of rows:', len(dataset))
        print('Number of columns:', len(dataset[0]))
```


```python
opened_file = open('AppleStore.csv',encoding = 'utf8')
from csv import reader
read_file = reader(opened_file)
apps_data = list(read_file)


```


```python
opened_file = open('googleplaystore.csv',encoding = 'utf8')
from csv import reader
read_file = reader(opened_file)
google_data = list(read_file)

```


```python
explore_data(apps_data,1,3,True)
```

    ['284882215', 'Facebook', '389879808', 'USD', '0.0', '2974676', '212', '3.5', '3.5', '95.0', '4+', 'Social Networking', '37', '1', '29', '1']
    
    
    ['389801252', 'Instagram', '113954816', 'USD', '0.0', '2161558', '1289', '4.5', '4.0', '10.23', '12+', 'Photo & Video', '37', '0', '29', '1']
    
    
    Number of rows: 7198
    Number of columns: 16
    


```python
explore_data(google_data,1,3,True)
```

    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    ['Coloring book moana', 'ART_AND_DESIGN', '3.9', '967', '14M', '500,000+', 'Free', '0', 'Everyone', 'Art & Design;Pretend Play', 'January 15, 2018', '2.0.0', '4.0.3 and up']
    
    
    Number of rows: 10842
    Number of columns: 13
    


```python
print(apps_data[0])
print(google_data[0])
```

    ['id', 'track_name', 'size_bytes', 'currency', 'price', 'rating_count_tot', 'rating_count_ver', 'user_rating', 'user_rating_ver', 'ver', 'cont_rating', 'prime_genre', 'sup_devices.num', 'ipadSc_urls.num', 'lang.num', 'vpp_lic']
    ['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']
    

### descriptive of data
We can see some factors which will be helpful for our developers such as price, rating, type.

google_app : https://www.kaggle.com/datasets/lava18/google-play-store-apps
apple_app : https://www.kaggle.com/datasets/ramamet4/app-store-apple-data-set-10k-apps




```python
google_data[10473]
```




    ['Life Made WI-Fi Touchscreen Photo Frame',
     '1.9',
     '19',
     '3.0M',
     '1,000+',
     'Free',
     '0',
     'Everyone',
     '',
     'February 11, 2018',
     '1.0.19',
     '4.0 and up']



10472 row in google_data is the wrong data because it has wrong data type in category column. Therefore, I need to delete it for corretness of data science.


```python
del google_data[10473]
```


```python
app_name = []
app_unique = []

for row in google_data[1:]:
    name = row[0]
    if name in app_unique:
        app_name.append(name)
    else:
        app_unique.append(name)
```


```python
print(app_name[:10])
```

    ['Quick PDF Scanner + OCR FREE', 'Box', 'Google My Business', 'ZOOM Cloud Meetings', 'join.me - Simple Meetings', 'Box', 'Zenefits', 'Google Ads', 'Google My Business', 'Slack']
    

In app_name, there are the names of duplicated in google_app. As the guide suggested, I am going to remove rows with duplicated except the newest row.


```python
review_max = {}

for row in google_data[1:]:
    name = row[0]
    n_reviews = float(row[3])
    
    if name in review_max and review_max[name] < n_reviews:
        review_max[name] = n_reviews
    elif name not in review_max:
        review_max[name] = n_reviews
        
```


```python
len(review_max)
```




    9659



We made a dictionary with name and n_reviews without duplications above. Next step is to create a list of whole data without duplicate.


```python
android_clean = []
already_added = []

for row in google_data[1:]:
    name = row[0]
    n_reviews = float(row[3])
    
    if n_reviews == review_max[name] and name not in already_added:
        android_clean.append(row)
        already_added.append(name)
    
```


```python
len(android_clean)
```




    9659



We just did data clean in google apps data. With appropriate for and if syntax, we can clean raw data easily.


```python
def detector(stt) :
    plus = 0
    for row in stt:
        if ord(row) > 127:
            plus = plus + 1
    if plus > 3 :
        return False
    return True
```


```python
detector('Instagram')
```




    True




```python
detector('p爱奇艺PPS -《欢乐颂2》电视剧热播')
```




    False




```python
detector('Docs To Go™ Free Office Suite')
```




    True




```python
detector('Instachat 😜')
```




    True



With new function, I will filter out non-English apps from both datasets.


```python
new_google_data = []
non_english_google_data = []

for row in android_clean:
    name = row[0]
    if detector(name) == True:
        new_google_data.append(row)
    else:
        non_english_google_data.append(row)
```


```python
print(non_english_google_data[:3])
```

    [['Flame - درب عقلك يوميا', 'EDUCATION', '4.6', '56065', '37M', '1,000,000+', 'Free', '0', 'Everyone', 'Education', 'July 26, 2018', '3.3', '4.1 and up'], ['သိင်္ Astrology - Min Thein Kha BayDin', 'LIFESTYLE', '4.7', '2225', '15M', '100,000+', 'Free', '0', 'Everyone', 'Lifestyle', 'July 26, 2018', '4.2.1', '4.0.3 and up'], ['РИА Новости', 'NEWS_AND_MAGAZINES', '4.5', '44274', '8.0M', '1,000,000+', 'Free', '0', 'Everyone', 'News & Magazines', 'August 6, 2018', '4.0.6', '4.4 and up']]
    


```python
new_apple_data = []
non_english_apple_data = []

for row in apps_data[1:]:
    name = row[1]
    if detector(name) == True:
        new_apple_data.append(row)
    else:
        non_english_apple_data.append(row)
```


```python
print(non_english_apple_data[:3])
```

    [['445375097', '爱奇艺PPS -《欢乐颂2》电视剧热播', '224617472', 'USD', '0.0', '14844', '0', '4.0', '0.0', '6.3.3', '17+', 'Entertainment', '38', '5', '3', '1'], ['405667771', '聚力视频HD-人民的名义,跨界歌王全网热播', '90725376', 'USD', '0.0', '7446', '8', '4.0', '4.5', '5.0.8', '12+', 'Entertainment', '24', '4', '1', '1'], ['336141475', '优酷视频', '204959744', 'USD', '0.0', '4885', '0', '3.5', '0.0', '6.7.0', '12+', 'Entertainment', '38', '0', '2', '1']]
    


```python
print(len(new_google_data),len(new_apple_data))
```

    9614 6183
    

Till now, we have been filtering out inaccrate data, duplicate app entries, and non-English apps. On this step, we will isolate free apps from non-free apps.


```python
apps_data[0]

```




    ['id',
     'track_name',
     'size_bytes',
     'currency',
     'price',
     'rating_count_tot',
     'rating_count_ver',
     'user_rating',
     'user_rating_ver',
     'ver',
     'cont_rating',
     'prime_genre',
     'sup_devices.num',
     'ipadSc_urls.num',
     'lang.num',
     'vpp_lic']




```python
#price is located at column 5

free_apps_apple = []
non_free_apps_apple = []

for row in new_apple_data[1:]:
    price = float(row[4])
    if price == 0 :
        free_apps_apple.append(row)
    else :
        non_free_apps_apple.append(row)
```


```python
# price is located at column 8
free_apps_google = []
non_free_apps_google = []

for row in new_google_data[1:]:
    price = row[7]
    if price == '0' :
        free_apps_google.append(row)
    else :
        non_free_apps_google.append(row)
```


```python
print(len(free_apps_apple),len(free_apps_google))
```

    3221 8863
    

## Our validation strategy
1. Build a minimal Android version of the app, and add it to Google Play
2. If the app has a good response from users, we develop it further
3. If the app is profitable after six months, we build an iOS version of the app and add it to the App Store


```python
# find the most common genres in each market
# First apple_data
print(apps_data[0])
print(free_apps_apple[:5])
```

    ['id', 'track_name', 'size_bytes', 'currency', 'price', 'rating_count_tot', 'rating_count_ver', 'user_rating', 'user_rating_ver', 'ver', 'cont_rating', 'prime_genre', 'sup_devices.num', 'ipadSc_urls.num', 'lang.num', 'vpp_lic']
    [['389801252', 'Instagram', '113954816', 'USD', '0.0', '2161558', '1289', '4.5', '4.0', '10.23', '12+', 'Photo & Video', '37', '0', '29', '1'], ['529479190', 'Clash of Clans', '116476928', 'USD', '0.0', '2130805', '579', '4.5', '4.5', '9.24.12', '9+', 'Games', '38', '5', '18', '1'], ['420009108', 'Temple Run', '65921024', 'USD', '0.0', '1724546', '3842', '4.5', '4.0', '1.6.2', '9+', 'Games', '40', '5', '1', '1'], ['284035177', 'Pandora - Music & Radio', '130242560', 'USD', '0.0', '1126879', '3594', '4.0', '4.5', '8.4.1', '12+', 'Music', '37', '4', '1', '1'], ['429047995', 'Pinterest', '74778624', 'USD', '0.0', '1061624', '1814', '4.5', '4.0', '6.26', '12+', 'Social Networking', '37', '5', '27', '1']]
    


```python
numbers_genre_apple = {}

for row in free_apps_apple:
    genre = row[-5]
    if genre in numbers_genre_apple :
        numbers_genre_apple[genre] += 1
    else :
        numbers_genre_apple[genre] = 1
        
print(numbers_genre_apple)

# You can see in the table that Games is the one which is the most common genre in apple app
```

    {'Photo & Video': 160, 'Games': 1874, 'Music': 66, 'Social Networking': 105, 'Reference': 18, 'Health & Fitness': 65, 'Weather': 28, 'Utilities': 81, 'Travel': 40, 'Shopping': 84, 'News': 43, 'Navigation': 6, 'Lifestyle': 51, 'Entertainment': 254, 'Food & Drink': 26, 'Sports': 69, 'Book': 14, 'Finance': 36, 'Education': 118, 'Productivity': 56, 'Business': 17, 'Catalogs': 4, 'Medical': 6}
    


```python
# for google apps
print(google_data[0])
```

    ['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']
    


```python
print(google_data[:5][-4])
```

    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    


```python
numbers_genre_google = {}

for row in free_apps_google:
    genre = row[-4]
    if genre in numbers_genre_google :
        numbers_genre_google[genre] += 1
    else :
        numbers_genre_google[genre] = 1
        
print(numbers_genre_google)

# It is really hard to know which genre is the most common among these data
```

    {'Art & Design': 52, 'Art & Design;Creativity': 6, 'Auto & Vehicles': 82, 'Beauty': 53, 'Books & Reference': 190, 'Business': 407, 'Comics': 54, 'Comics;Creativity': 1, 'Communication': 287, 'Dating': 165, 'Education': 474, 'Education;Creativity': 4, 'Education;Education': 30, 'Education;Pretend Play': 5, 'Education;Brain Games': 3, 'Entertainment': 538, 'Entertainment;Brain Games': 7, 'Entertainment;Creativity': 3, 'Entertainment;Music & Video': 15, 'Events': 63, 'Finance': 328, 'Food & Drink': 110, 'Health & Fitness': 273, 'House & Home': 73, 'Libraries & Demo': 83, 'Lifestyle': 345, 'Lifestyle;Pretend Play': 1, 'Card': 40, 'Arcade': 164, 'Puzzle': 100, 'Racing': 88, 'Sports': 307, 'Casual': 156, 'Simulation': 181, 'Adventure': 60, 'Trivia': 37, 'Action': 275, 'Word': 23, 'Role Playing': 83, 'Strategy': 81, 'Board': 34, 'Music': 18, 'Action;Action & Adventure': 9, 'Casual;Brain Games': 12, 'Educational;Creativity': 3, 'Puzzle;Brain Games': 15, 'Educational;Education': 35, 'Casual;Pretend Play': 21, 'Educational;Brain Games': 6, 'Art & Design;Pretend Play': 1, 'Educational;Pretend Play': 8, 'Entertainment;Education': 1, 'Casual;Education': 2, 'Casual;Creativity': 6, 'Casual;Action & Adventure': 12, 'Music;Music & Video': 2, 'Arcade;Pretend Play': 1, 'Adventure;Action & Adventure': 3, 'Role Playing;Action & Adventure': 3, 'Simulation;Pretend Play': 2, 'Puzzle;Creativity': 2, 'Simulation;Action & Adventure': 7, 'Racing;Action & Adventure': 15, 'Sports;Action & Adventure': 2, 'Educational;Action & Adventure': 3, 'Arcade;Action & Adventure': 11, 'Entertainment;Action & Adventure': 3, 'Art & Design;Action & Adventure': 1, 'Puzzle;Action & Adventure': 3, 'Education;Action & Adventure': 3, 'Strategy;Action & Adventure': 1, 'Music & Audio;Music & Video': 1, 'Health & Fitness;Education': 1, 'Board;Action & Adventure': 2, 'Board;Brain Games': 7, 'Casual;Music & Video': 1, 'Education;Music & Video': 3, 'Role Playing;Pretend Play': 4, 'Entertainment;Pretend Play': 2, 'Medical': 313, 'Social': 236, 'Shopping': 199, 'Photography': 261, 'Travel & Local': 206, 'Travel & Local;Action & Adventure': 1, 'Tools': 749, 'Tools;Education': 1, 'Personalization': 294, 'Productivity': 345, 'Parenting': 44, 'Parenting;Music & Video': 6, 'Parenting;Education': 7, 'Parenting;Brain Games': 1, 'Weather': 71, 'Video Players & Editors': 157, 'Video Players & Editors;Music & Video': 2, 'Video Players & Editors;Creativity': 1, 'News & Magazines': 248, 'Maps & Navigation': 124, 'Health & Fitness;Action & Adventure': 1, 'Educational': 33, 'Casino': 38, 'Trivia;Education': 1, 'Lifestyle;Education': 1, 'Card;Action & Adventure': 1, 'Books & Reference;Education': 1, 'Simulation;Education': 1, 'Puzzle;Education': 1, 'Adventure;Education': 1, 'Role Playing;Brain Games': 1, 'Strategy;Education': 1, 'Racing;Pretend Play': 1, 'Communication;Creativity': 1, 'Strategy;Creativity': 1}
    


```python
# Using loop, let me extract the most common genre
aa = 0
common = []
for row in numbers_genre_google :
    if numbers_genre_google[row] > aa :
       common = row
       aa = numbers_genre_google[row]
    
```


```python
print(common)
numbers_genre_google[common]
# Tools is the most common genre in google app store, which is 749
```

    Tools
    




    749




```python
def freq_table(dataset,index) :
    numbers_genre = {}

    for row in dataset:
        genre = row[index]
        if genre in numbers_genre :
            numbers_genre[genre] += 1
        else :
            numbers_genre[genre] = 1
    return(numbers_genre)    
    
    
```


```python
def display_table(dataset, index):
    table = freq_table(dataset, index)
    table_display = []
    for key in table:
        key_val_as_tuple = (table[key], key)
        table_display.append(key_val_as_tuple)

    table_sorted = sorted(table_display, reverse = True)
    for entry in table_sorted:
        print(entry[1], ':', entry[0])
```


```python
display_table(free_apps_apple,-5)
```

    Games : 1874
    Entertainment : 254
    Photo & Video : 160
    Education : 118
    Social Networking : 105
    Shopping : 84
    Utilities : 81
    Sports : 69
    Music : 66
    Health & Fitness : 65
    Productivity : 56
    Lifestyle : 51
    News : 43
    Travel : 40
    Finance : 36
    Weather : 28
    Food & Drink : 26
    Reference : 18
    Business : 17
    Book : 14
    Navigation : 6
    Medical : 6
    Catalogs : 4
    

According to the table above, Games is the most common genre in Apple Store. Generally, entertainment apps are more popular than pratical purposes apps.
We could think that it would be more beneficial to develope entertainment apps because of the result above. However, we don't know how long people use the apps in general. More analization will seem to be needed.


```python
display_table(free_apps_google,-4)
```

    Tools : 749
    Entertainment : 538
    Education : 474
    Business : 407
    Productivity : 345
    Lifestyle : 345
    Finance : 328
    Medical : 313
    Sports : 307
    Personalization : 294
    Communication : 287
    Action : 275
    Health & Fitness : 273
    Photography : 261
    News & Magazines : 248
    Social : 236
    Travel & Local : 206
    Shopping : 199
    Books & Reference : 190
    Simulation : 181
    Dating : 165
    Arcade : 164
    Video Players & Editors : 157
    Casual : 156
    Maps & Navigation : 124
    Food & Drink : 110
    Puzzle : 100
    Racing : 88
    Role Playing : 83
    Libraries & Demo : 83
    Auto & Vehicles : 82
    Strategy : 81
    House & Home : 73
    Weather : 71
    Events : 63
    Adventure : 60
    Comics : 54
    Beauty : 53
    Art & Design : 52
    Parenting : 44
    Card : 40
    Casino : 38
    Trivia : 37
    Educational;Education : 35
    Board : 34
    Educational : 33
    Education;Education : 30
    Word : 23
    Casual;Pretend Play : 21
    Music : 18
    Racing;Action & Adventure : 15
    Puzzle;Brain Games : 15
    Entertainment;Music & Video : 15
    Casual;Brain Games : 12
    Casual;Action & Adventure : 12
    Arcade;Action & Adventure : 11
    Action;Action & Adventure : 9
    Educational;Pretend Play : 8
    Simulation;Action & Adventure : 7
    Parenting;Education : 7
    Entertainment;Brain Games : 7
    Board;Brain Games : 7
    Parenting;Music & Video : 6
    Educational;Brain Games : 6
    Casual;Creativity : 6
    Art & Design;Creativity : 6
    Education;Pretend Play : 5
    Role Playing;Pretend Play : 4
    Education;Creativity : 4
    Role Playing;Action & Adventure : 3
    Puzzle;Action & Adventure : 3
    Entertainment;Creativity : 3
    Entertainment;Action & Adventure : 3
    Educational;Creativity : 3
    Educational;Action & Adventure : 3
    Education;Music & Video : 3
    Education;Brain Games : 3
    Education;Action & Adventure : 3
    Adventure;Action & Adventure : 3
    Video Players & Editors;Music & Video : 2
    Sports;Action & Adventure : 2
    Simulation;Pretend Play : 2
    Puzzle;Creativity : 2
    Music;Music & Video : 2
    Entertainment;Pretend Play : 2
    Casual;Education : 2
    Board;Action & Adventure : 2
    Video Players & Editors;Creativity : 1
    Trivia;Education : 1
    Travel & Local;Action & Adventure : 1
    Tools;Education : 1
    Strategy;Education : 1
    Strategy;Creativity : 1
    Strategy;Action & Adventure : 1
    Simulation;Education : 1
    Role Playing;Brain Games : 1
    Racing;Pretend Play : 1
    Puzzle;Education : 1
    Parenting;Brain Games : 1
    Music & Audio;Music & Video : 1
    Lifestyle;Pretend Play : 1
    Lifestyle;Education : 1
    Health & Fitness;Education : 1
    Health & Fitness;Action & Adventure : 1
    Entertainment;Education : 1
    Communication;Creativity : 1
    Comics;Creativity : 1
    Casual;Music & Video : 1
    Card;Action & Adventure : 1
    Books & Reference;Education : 1
    Art & Design;Pretend Play : 1
    Art & Design;Action & Adventure : 1
    Arcade;Pretend Play : 1
    Adventure;Education : 1
    


```python
display_table(free_apps_google,1)
```

    FAMILY : 1676
    GAME : 862
    TOOLS : 750
    BUSINESS : 407
    LIFESTYLE : 346
    PRODUCTIVITY : 345
    FINANCE : 328
    MEDICAL : 313
    SPORTS : 301
    PERSONALIZATION : 294
    COMMUNICATION : 287
    HEALTH_AND_FITNESS : 273
    PHOTOGRAPHY : 261
    NEWS_AND_MAGAZINES : 248
    SOCIAL : 236
    TRAVEL_AND_LOCAL : 207
    SHOPPING : 199
    BOOKS_AND_REFERENCE : 190
    DATING : 165
    VIDEO_PLAYERS : 159
    MAPS_AND_NAVIGATION : 124
    FOOD_AND_DRINK : 110
    EDUCATION : 103
    ENTERTAINMENT : 85
    LIBRARIES_AND_DEMO : 83
    AUTO_AND_VEHICLES : 82
    HOUSE_AND_HOME : 73
    WEATHER : 71
    EVENTS : 63
    PARENTING : 58
    ART_AND_DESIGN : 56
    COMICS : 55
    BEAUTY : 53
    

Unlike Apple apps, apps for entertainment shows it is balanced. However, Like I said, we need more analysis for better result.


```python
freq_table(free_apps_apple,-5)
```




    {'Photo & Video': 160,
     'Games': 1874,
     'Music': 66,
     'Social Networking': 105,
     'Reference': 18,
     'Health & Fitness': 65,
     'Weather': 28,
     'Utilities': 81,
     'Travel': 40,
     'Shopping': 84,
     'News': 43,
     'Navigation': 6,
     'Lifestyle': 51,
     'Entertainment': 254,
     'Food & Drink': 26,
     'Sports': 69,
     'Book': 14,
     'Finance': 36,
     'Education': 118,
     'Productivity': 56,
     'Business': 17,
     'Catalogs': 4,
     'Medical': 6}




```python

```
