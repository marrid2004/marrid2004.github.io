---
layout: single
title:  "Exploring Hacker News Posts"
categories: Python
tags : [dataquest, bigdata]
author_profile : false
---



#  4th guided project
### The goal of this projec

1) Do Ask HN or Show Hn receive more comments on average.
2) Do posts created at a certain time receive more the dataset into a list of lists.


```python
from csv import reader
data = open("hacker_news.csv")
hn = reader(data)
hn = list(hn)
```


```python
hn[:5]
```




    [['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at'],
     ['12224879',
      'Interactive Dynamic Video',
      'http://www.interactivedynamicvideo.com/',
      '386',
      '52',
      'ne0phyte',
      '8/4/2016 11:52'],
     ['10975351',
      'How to Use Open Source and Shut the Fuck Up at the Same Time',
      'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/',
      '39',
      '10',
      'josep2',
      '1/26/2016 19:30'],
     ['11964716',
      "Florida DJs May Face Felony for April Fools' Water Joke",
      'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/',
      '2',
      '1',
      'vezycash',
      '6/23/2016 22:20'],
     ['11919867',
      'Technology ventures: From Idea to Enterprise',
      'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429',
      '3',
      '1',
      'hswarna',
      '6/17/2016 0:01']]




```python
header = hn[0]
hn = hn[1:]
print(header)
print(hn[:6])
```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
    [['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52'], ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30'], ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20'], ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01'], ['10301696', 'Note by Note: The Making of Steinway L1037 (2007)', 'http://www.nytimes.com/2007/11/07/movies/07stein.html?_r=0', '8', '2', 'walterbell', '9/30/2015 4:12'], ['10482257', 'Title II kills investment? Comcast and other ISPs are now spending more', 'http://arstechnica.com/business/2015/10/comcast-and-other-isps-boost-network-investment-despite-net-neutrality/', '53', '22', 'Deinos', '10/31/2015 9:48']]
    


```python
ask_posts = []
show_posts = []
other_posts = []

for row in hn :
    title = row[1]
    title = title.lower()
    if title.startswith("ask hn"):
        ask_posts.append(row)
    elif title.startswith("show hn"):
        show_posts.append(row)
    else :
        other_posts.append(row)
        
for row in [ask_posts,show_posts,other_posts]:
    print(len(row))
```

    1744
    1162
    17194
    


```python
total_ask_comments = 0

for row in ask_posts:
    num = int(row[4])
    total_ask_comments += num
    
avg_ask_comments = total_ask_comments/ len(ask_posts)
print(avg_ask_comments)

total_show_comments = 0

for row in show_posts :
    num = int(row[4])
    total_show_comments += num

avg_show_comments = total_show_comments / len(show_posts)
print(avg_show_comments)
```

    14.038417431192661
    10.31669535283993
    

According to the results above, it says that `ask_posts` usually get more comments than `show_posts`. So, we will focus on `ask_posts` more afterwards.


```python
import datetime as dt
```


```python
result_list = []

for row in ask_posts :
    data = row[6]
    num = int(row[4])
    result_list.append([data,num])
print(result_list[:5])
```

    [['8/16/2016 9:55', 6], ['11/22/2015 13:43', 29], ['5/2/2016 10:14', 1], ['8/2/2016 14:20', 3], ['10/15/2015 16:38', 17]]
    


```python
counts_by_hour = {}
comments_by_hour = {}

for row in result_list :
    aa = dt.datetime.strptime(row[0],'%m/%d/%Y %H:%M')
    hour = aa.strftime('%H')
    comment = row[1]
    if hour not in counts_by_hour :
        counts_by_hour[hour] = 1
        comments_by_hour[hour] = comment
    else :
        counts_by_hour[hour] += 1
        comments_by_hour[hour] += comment
    
## aa = dt.datetime.strptime('8/16/2016 9:55','%m/%d/%Y %H:%M')
## aa.strftime('%H')
```


```python
print(counts_by_hour)
print(comments_by_hour)
```

    {'09': 45, '13': 85, '10': 59, '14': 107, '16': 108, '23': 68, '12': 73, '17': 100, '15': 116, '21': 109, '20': 80, '02': 58, '18': 109, '03': 54, '05': 46, '19': 110, '01': 60, '22': 71, '08': 48, '04': 47, '00': 55, '06': 44, '07': 34, '11': 58}
    {'09': 251, '13': 1253, '10': 793, '14': 1416, '16': 1814, '23': 543, '12': 687, '17': 1146, '15': 4477, '21': 1745, '20': 1722, '02': 1381, '18': 1439, '03': 421, '05': 464, '19': 1188, '01': 683, '22': 479, '08': 492, '04': 337, '00': 447, '06': 397, '07': 267, '11': 641}
    

`counts_by_hour` : contains the number of ask posts created during each hour of the day
`comments_by_hour` : contains the corresponding number of comments ask posts created at each hour received


```python
avg_by_hour =[]

for row in counts_by_hour:
    avg_by_hour.append([row, comments_by_hour[row] / counts_by_hour[row]])
    
print(avg_by_hour)
```

    [['09', 5.5777777777777775], ['13', 14.741176470588234], ['10', 13.440677966101696], ['14', 13.233644859813085], ['16', 16.796296296296298], ['23', 7.985294117647059], ['12', 9.41095890410959], ['17', 11.46], ['15', 38.5948275862069], ['21', 16.009174311926607], ['20', 21.525], ['02', 23.810344827586206], ['18', 13.20183486238532], ['03', 7.796296296296297], ['05', 10.08695652173913], ['19', 10.8], ['01', 11.383333333333333], ['22', 6.746478873239437], ['08', 10.25], ['04', 7.170212765957447], ['00', 8.127272727272727], ['06', 9.022727272727273], ['07', 7.852941176470588], ['11', 11.051724137931034]]
    


```python
swap_avg_by_hour = []

for row, row1 in avg_by_hour:
    swap_avg_by_hour.append([row1,row])
print(swap_avg_by_hour)
```

    [[5.5777777777777775, '09'], [14.741176470588234, '13'], [13.440677966101696, '10'], [13.233644859813085, '14'], [16.796296296296298, '16'], [7.985294117647059, '23'], [9.41095890410959, '12'], [11.46, '17'], [38.5948275862069, '15'], [16.009174311926607, '21'], [21.525, '20'], [23.810344827586206, '02'], [13.20183486238532, '18'], [7.796296296296297, '03'], [10.08695652173913, '05'], [10.8, '19'], [11.383333333333333, '01'], [6.746478873239437, '22'], [10.25, '08'], [7.170212765957447, '04'], [8.127272727272727, '00'], [9.022727272727273, '06'], [7.852941176470588, '07'], [11.051724137931034, '11']]
    


```python
sorted_swap = sorted(swap_avg_by_hour,reverse = True)
```


```python
print("Top 5 Houus for ask Posts Comments")

for avg, hour in sorted_swap[:5] :
    string = '{}:00: {:.2f} average comments per post'
    ###time = dt.datetime.strptime(hours = hour)
    
    result = string.format(hour,avg)
    print(result)
    
    
    
```

    Top 5 Houus for ask Posts Comments
    15:00: 38.59 average comments per post
    02:00: 23.81 average comments per post
    20:00: 21.52 average comments per post
    16:00: 16.80 average comments per post
    21:00: 16.01 average comments per post
    

According to the result above, we will be most likely t
