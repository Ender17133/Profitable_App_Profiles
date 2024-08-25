[![Open in Jupyter](https://img.shields.io/badge/Open%20in-Jupyter-blue.svg?logo=jupyter)](https://github.com/Ender17133/Profitable_App_Profiles/blob/main/Profitable_App_Profiles.ipynb)
# Profitable App Profiles for the App Store and Google Play Markets
(an imaginary situation for the project as if I am working in a company). 

At our company, we only build apps that are free to download and install, and our main source of revenue consists of in-app ads. This means that our revenue for any given app is mostly influenced by the number of users that use our app. 

I am working as data analyst for a company that builds Android and iOS mobile apps, and my goal in this project is to make a sufficient data analysis to help companies understand which applications will be most profitable by attracting more people. 


## Opening and Exploring the Data 
As of September 2018, there were approximately 2 million iOS apps available on the App Store, and 2.1 million Android apps on Google Play.

![img](https://s3.amazonaws.com/dq-content/350/py1m8_statista.png) Source: [Statista](https://www.statista.com/statistics/276623/number-of-apps-available-in-leading-app-stores/)

Collecting data for over 4 million apps requires a significant amount of time and money, so I will try to analyze a sample of the data instead. Fortunately, there are two data sets that seem suitable for my goals of this project: 

- A [dataset](https://www.kaggle.com/lava18/google-play-store-apps) containing data about approximately 10,000 Android apps from Google Play; the data was collected in August 2018. You can download the data set directly from this [link](https://dq-content.s3.amazonaws.com/350/googleplaystore.csv)

- A [dataset](https://www.kaggle.com/ramamet4/app-store-apple-data-set-10k-apps) containing data about approximately 7,000 iOS apps from the App Store; the data was collected in July 2017. You can download the data set directly from this [link](https://dq-content.s3.amazonaws.com/350/AppleStore.csv)

First, these data sets should be opened and explored. 




```python
from csv import reader

### The Google Play data set ###
opened_file = open('googleplaystore.csv')
read_file = reader(opened_file)
android = list(read_file)
android_header = android[0]
android = android[1:]

### The App Store data set ###
opened_file = open('AppleStore.csv')
read_file = reader(opened_file)
ios = list(read_file)
ios_header = ios[0]
ios = ios[1:]
```

In order to make the data sets easier to explore, I created a function called explore_data() that can be used to print rows in a readable way. First let's look on first three rows of the android dataset.


```python
def explore_data(dataset, start, end, rows_and_columns = False): 
    dataset_slice = dataset[start:end]
    for row in dataset_slice: 
        print(row)
        print('\n') # adds a new (empty) line after each row 
    
    if rows_and_columns: 
        print('Number of rows:', len(dataset))
        print('Number of columns:', len(dataset[0]))
        
print(android_header)
print('\n')
print(explore_data(android, 0, 3, True))
```

    ['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']
    
    
    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    ['Coloring book moana', 'ART_AND_DESIGN', '3.9', '967', '14M', '500,000+', 'Free', '0', 'Everyone', 'Art & Design;Pretend Play', 'January 15, 2018', '2.0.0', '4.0.3 and up']
    
    
    ['U Launcher Lite – FREE Live Cool Themes, Hide Apps', 'ART_AND_DESIGN', '4.7', '87510', '8.7M', '5,000,000+', 'Free', '0', 'Everyone', 'Art & Design', 'August 1, 2018', '1.2.4', '4.0.3 and up']
    
    
    Number of rows: 10841
    Number of columns: 13
    None
    

We can see that this data set has 10841 applications and 13 columns. We can also see important columns that can be used for our data analysis: `'App'`, `'Category'`, `'Rating'`, `'Reviews'`, `'Price'`, `'Genres'`. Documentation for the columns in data set can be found on this [link](https://www.kaggle.com/lava18/google-play-store-apps)

Now let's look on Apple data set. 


```python
print(ios_header)
print('\n')
print(explore_data(ios, 0, 3, True))
```

    ['id', 'track_name', 'size_bytes', 'currency', 'price', 'rating_count_tot', 'rating_count_ver', 'user_rating', 'user_rating_ver', 'ver', 'cont_rating', 'prime_genre', 'sup_devices.num', 'ipadSc_urls.num', 'lang.num', 'vpp_lic']
    
    
    ['284882215', 'Facebook', '389879808', 'USD', '0.0', '2974676', '212', '3.5', '3.5', '95.0', '4+', 'Social Networking', '37', '1', '29', '1']
    
    
    ['389801252', 'Instagram', '113954816', 'USD', '0.0', '2161558', '1289', '4.5', '4.0', '10.23', '12+', 'Photo & Video', '37', '0', '29', '1']
    
    
    ['529479190', 'Clash of Clans', '116476928', 'USD', '0.0', '2130805', '579', '4.5', '4.5', '9.24.12', '9+', 'Games', '38', '5', '18', '1']
    
    
    Number of rows: 7197
    Number of columns: 16
    None
    

iOS data set has 7197 applications and 16 columns. Same as in previous data set, we can see useful columns in this data set for our data analysis: `'id'`, `'price'`, `'rating_count_tot'`, `'user_rating'`, `'prime_genre'`. Not all columns in the data set are clear, documentation for this data set can be accessed through this [link](https://www.kaggle.com/ramamet4/app-store-apple-data-set-10k-apps) as well.  

## Deleting Wrong Data 
The Google Play data set has a dedicated [discussion section](https://www.kaggle.com/lava18/google-play-store-apps/discussion), and we can see that [one of the discussions](https://www.kaggle.com/lava18/google-play-store-apps/discussion/66015) describes an error for row 10472


```python
print(android_header)
print('\n')
print(android[10472])
print('\n')
print(android[0])
```

    ['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']
    
    
    ['Life Made WI-Fi Touchscreen Photo Frame', '1.9', '19', '3.0M', '1,000+', 'Free', '0', 'Everyone', '', 'February 11, 2018', '1.0.19', '4.0 and up']
    
    
    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    

The row with the mistake correspond to the *Life made WI-Fi Touchscreen Photo Frame* application. We can see that the rating is 19, while the maximum value is 5 ( as mentioned in the [discussions section](https://www.kaggle.com/lava18/google-play-store-apps/discussion/66015), this problem is caused by a missing value in the 'Category' column). We will delete this row. 


```python
print(len(android))
del android[10472]
print(len(android))
```

    10841
    10840
    

I didn't find any errors in the [discussion](https://www.kaggle.com/datasets/ramamet4/app-store-apple-data-set-10k-apps/discussion) of iOS data set, however we can check the error by coding.


```python
for row in ios: 
    if len(row) != len(ios_header):
        print(row) # It will print the row with error
        print(ios.index(row)) # It will print the index number of the row with mistake
```

As you see, there are no row mistakes in the Apple data set. 

## Removing Duplicate Entries
### Part One
If you explore the Google Play data set long enough or look at the [discussions](https://www.kaggle.com/lava18/google-play-store-apps/discussion) section, you'll notice some apps have duplicate entries. For instance, Instagram has four entries:


```python
for app in android: 
    name = app[0]
    if name == 'Instagram':
        print(app)
```

    ['Instagram', 'SOCIAL', '4.5', '66577313', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']
    ['Instagram', 'SOCIAL', '4.5', '66577446', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']
    ['Instagram', 'SOCIAL', '4.5', '66577313', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']
    ['Instagram', 'SOCIAL', '4.5', '66509917', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']
    

In total, there are 1,181 cases where an app occurs more than once. 



```python
duplicate_apps = []
unique_apps = []

for app in android: 
    name = app[0]
    if name in unique_apps: 
        duplicate_apps.append(name)
    else: 
        unique_apps.append(name)
        
print('Number of duplicate apps:', len(duplicate_apps))
print('\n')
print('Examples of duplicate apps:', duplicate_apps[:15])
```

    Number of duplicate apps: 1181
    
    
    Examples of duplicate apps: ['Quick PDF Scanner + OCR FREE', 'Box', 'Google My Business', 'ZOOM Cloud Meetings', 'join.me - Simple Meetings', 'Box', 'Zenefits', 'Google Ads', 'Google My Business', 'Slack', 'FreshBooks Classic', 'Insightly CRM', 'QuickBooks Accounting: Invoicing & Expenses', 'HipChat - Chat Built for Teams', 'Xero Accounting Software']
    

We don't want to count certain apps more than once when we analyze data, so we need to remove the duplicate entries and keep only one entry per app. One thing we could do is remove the duplicate rows randomly, but we could probably find a better way. 

If you examine the rows we printed for the Instagram app, the main difference happens on the fourth position of each row, which corresponds to the number of reviews. The different numbers show the data was collected at different times. We can use this to build a criterion for removing or keeping rows. We are not going to remove rows randomly, but we'll only keep the row with the highest number of reviews and remove the other entries for any given app. 

To remove the duplicates, we will do the following: 
- Create a dictionary, where each dictionary key is a unique app name and the corresponsding dictionary value is the highest number of reviews of that app.
- Use the information stored in dictionary and create a new dataset, which will have only one entry per app. 

### Part Two
Let's start by creating dictionary 



```python
reviews_max = {}
for app in android:
    name = app[0]
    n_reviews = float(app[3])
    
    if name in reviews_max and reviews_max[name] < n_reviews:
        reviews_max[name] = n_reviews
    
    elif name not in reviews_max: 
        reviews_max[name] = n_reviews 
        


```

In a previous code cell, we found that there are 1,181 cases where an app occurs more than once, so the length of our dictionary (of unique apps) should be equal to the difference between the length of our data set and 1,181.


```python
print('Expected length:', len(android) - 1181)
print('Actual length:', len(reviews_max))
```

    Expected length: 9659
    Actual length: 9659
    

Now we should use the dictionary `'reviews_max'` we created to remove the duplicate rows. 


```python
android_clean = []
already_added = []
for app in android: 
    name = app[0]
    n_reviews = float(app[3])
    
    if (reviews_max[name] == n_reviews) and (name not in already_added): 
        android_clean.append(app)
        already_added.append(name)
        
```

Now let's check the `'android_clean'` data set to check if everything went as we wanted. The data set should havve 9659 rows. 


```python
explore_data(android_clean, 0, 3, True)
```

    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    ['U Launcher Lite – FREE Live Cool Themes, Hide Apps', 'ART_AND_DESIGN', '4.7', '87510', '8.7M', '5,000,000+', 'Free', '0', 'Everyone', 'Art & Design', 'August 1, 2018', '1.2.4', '4.0.3 and up']
    
    
    ['Sketch - Draw & Paint', 'ART_AND_DESIGN', '4.5', '215644', '25M', '50,000,000+', 'Free', '0', 'Teen', 'Art & Design', 'June 8, 2018', 'Varies with device', '4.2 and up']
    
    
    Number of rows: 9659
    Number of columns: 13
    

Everything went good as we expected. 

## Removing Non-English Apps
### Part One
If we explore the data carefully, we'll find that some names of applications in data sets are not directed towards English audience. 


```python
print(ios[813][1])
print(ios[6731][1])
print('\n')
print(android_clean[4412][0])
print(android_clean[7940][0])
```

    爱奇艺PPS -《欢乐颂2》电视剧热播
    【脱出ゲーム】絶対に最後までプレイしないで 〜謎解き＆ブロックパズル〜
    
    
    中国語 AQリスニング
    لعبة تقدر تربح DZ
    

We're not interested in keeping these apps, so we'll remove them. One option to remove non-english applications is to remove each app with a name containing a symbol not commonly used in English text - English text usually includes letters from the English alphabet, digit numbers from 0 to 9, punctuation marks (., !, ?, ;), and other symbols (+, *, /)

All characters we use in an English text are encoded using ASCII standard. Each ASCII standard has a specific number from 0 to 127 associated with it. We could use this range of numbers to build a function that checks app name and tells us whether it contains non-ASCII characters. 


```python
def is_english(string): 
    for character in string:
        if ord(character) > 127:
            return False
    return True
# Examples of the function 
print(is_english('Instagram'))
print(is_english('爱奇艺PPS -《欢乐颂2》电视剧热播'))
```

    True
    False
    

Our function seems to work fine, however some English applications might contain use of emojis or other symbols which will cause a huge data loss of useful apps. In order to prevent data loss, we will make a slight modifications to the program. 


```python
print(is_english('Docs To Go™ Free Office Suite'))
print(ord('™'), '\n')
print(is_english('Instachat 😜'))
print(ord('😜'))

```

    False
    8482 
    
    False
    128540
    

### Part Two 
To minimize the impact of data loss, we'll only remove an app if its name has more than three characters with corresponding numbers falling outside the ASCII range. This means all English apps with up to three emoji or other special characters will still be labeled as English.




```python
def is_english(string): 
    non_ascii = 0
    for character in string:
        if ord(character) > 127:
            non_ascii += 1
        
    if non_ascii > 3: 
        return False 
    else: 
        return True

print(is_english('Docs To Go™ Free Office Suite'))
print(is_english('Instachat 😜'))

```

    True
    True
    

Our function is still not perfect, but it should be effective for filtering out non-English apps. 

Below, we used our created function to filter out the non-English apps from both data sets: 


```python
android_english = []
ios_english = []
for app in android_clean: 
    name = app[0]
    if is_english(name): 
        android_english.append(app)

for app in ios: 
    name = app[1]
    if is_english(name): 
        ios_english.append(app)
        
explore_data(android_english, 0, 3, True)
print('\n')
explore_data(ios_english, 0, 3, True)
```

    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    ['U Launcher Lite – FREE Live Cool Themes, Hide Apps', 'ART_AND_DESIGN', '4.7', '87510', '8.7M', '5,000,000+', 'Free', '0', 'Everyone', 'Art & Design', 'August 1, 2018', '1.2.4', '4.0.3 and up']
    
    
    ['Sketch - Draw & Paint', 'ART_AND_DESIGN', '4.5', '215644', '25M', '50,000,000+', 'Free', '0', 'Teen', 'Art & Design', 'June 8, 2018', 'Varies with device', '4.2 and up']
    
    
    Number of rows: 9614
    Number of columns: 13
    
    
    ['284882215', 'Facebook', '389879808', 'USD', '0.0', '2974676', '212', '3.5', '3.5', '95.0', '4+', 'Social Networking', '37', '1', '29', '1']
    
    
    ['389801252', 'Instagram', '113954816', 'USD', '0.0', '2161558', '1289', '4.5', '4.0', '10.23', '12+', 'Photo & Video', '37', '0', '29', '1']
    
    
    ['529479190', 'Clash of Clans', '116476928', 'USD', '0.0', '2130805', '579', '4.5', '4.5', '9.24.12', '9+', 'Games', '38', '5', '18', '1']
    
    
    Number of rows: 6183
    Number of columns: 16
    

Now we can see that we are left with 6183 iOS applications and 9614 Android applications. 

## Isolating the Free Apps

As we mentioned before, we only make apps that are free to download and install and our main source or revenue consists of in-app ads. Our datasets contain both free and non-free apps. We'll need to isolate only the free apps for our analysis.


```python
print(android_header)
explore_data(android_english, 0, 1)
print('\n')
print(ios_header)
explore_data(ios_english, 0, 1)
# We need to see where is the price column located in each data set. 

android_final = []
ios_final = []
for app in android_english: 
    price = app[7]
    if price == '0':
        android_final.append(app)

for app in ios_english: 
    price = app[4]
    if price == '0.0':
        ios_final.append(app)

print(len(android_final))
print(len(ios_final))
        
        
    
```

    ['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']
    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    
    
    ['id', 'track_name', 'size_bytes', 'currency', 'price', 'rating_count_tot', 'rating_count_ver', 'user_rating', 'user_rating_ver', 'ver', 'cont_rating', 'prime_genre', 'sup_devices.num', 'ipadSc_urls.num', 'lang.num', 'vpp_lic']
    ['284882215', 'Facebook', '389879808', 'USD', '0.0', '2974676', '212', '3.5', '3.5', '95.0', '4+', 'Social Networking', '37', '1', '29', '1']
    
    
    8864
    3222
    

After filtering out some of the apps, we are left with 8864 Android apps and 3222 iOS apps. 

## Most Common Apps by Genre
### Part One
As it was mentioned in the introduction, goal of this data analysis is to identify apps that are likely to attract more users because the number of people using our apps affect our revenues. 

Our final goal after the data analysis is to create an app that will be profitable both in Google Play and App Sotre, that is why we need to find apps that are already successful in both markets. 

To minimize risks and overhead, our validation strategy for an app idea has three steps: 
1. Build a minimal Android version of the app, and add it to Google Play.
2. If the app has a good response from users, we develop it further.
3.If the app is profitable after six months, we build an iOS version of the app and add it to the App Store.

Let's begin data analysis by determining the most common genres for each market. For this I will build frequency tables for the `'prime_genre'` column of the iOS data set and `'Genres'` and `'Category'` columns of the Android data set.


### Part Two
We'll build two functions that can be used to analyze the frequency tables. 
- One function to create frequency tables that show percentages 
- Another function we can use to display the percentages in a descending order


```python
def freq_table(dataset, index): 
    table = {}
    total = 0
    
    for row in dataset: 
        total += 1
        value = row[index]
        if value in table:
            table[value] += 1
        else: 
            table[value] = 1
        
    table_percentages = {}
    for key in table: 
        percentage = (table[key] / total) * 100 
        table_percentages[key] = percentage
    return table_percentages 

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

### Part Three 
We will start by looking on frequency table for the `'prime_genre'` column of iOS data set. 



```python
display_table(ios_final, -5)
```

    Games : 58.16263190564867
    Entertainment : 7.883302296710118
    Photo & Video : 4.9658597144630665
    Education : 3.662321539416512
    Social Networking : 3.2898820608317814
    Shopping : 2.60707635009311
    Utilities : 2.5139664804469275
    Sports : 2.1415270018621975
    Music : 2.0484171322160147
    Health & Fitness : 2.0173805090006205
    Productivity : 1.7380509000620732
    Lifestyle : 1.5828677839851024
    News : 1.3345747982619491
    Travel : 1.2414649286157666
    Finance : 1.1173184357541899
    Weather : 0.8690254500310366
    Food & Drink : 0.8069522036002483
    Reference : 0.5586592178770949
    Business : 0.5276225946617008
    Book : 0.4345127250155183
    Navigation : 0.186219739292365
    Medical : 0.186219739292365
    Catalogs : 0.12414649286157665
    

According to the data, greater than half(58.16%) of the applications in the Apple Store are games. Entertainment(8%) is the second most common genre after Games followed by Photo & Video applications that are close to 5%.

The general impression is that Apple Store is controlled by apps created designed for fun (games, photo and video, social networking, sports, music). While application designed for practical purposes (education, shopping, utilities, productivity, lifestyle) are more rare. However, we can't say that the most common applications in Apple Store have the highest number of users as Supply can be not same as Demand.  

Now we should look on `'Category'` and `'Genres'` columns of the Android data set.


```python
display_table(android_final, 1) # Category
```

    FAMILY : 18.907942238267147
    GAME : 9.724729241877256
    TOOLS : 8.461191335740072
    BUSINESS : 4.591606498194946
    LIFESTYLE : 3.9034296028880866
    PRODUCTIVITY : 3.892148014440433
    FINANCE : 3.7003610108303246
    MEDICAL : 3.531137184115524
    SPORTS : 3.395758122743682
    PERSONALIZATION : 3.3167870036101084
    COMMUNICATION : 3.2378158844765346
    HEALTH_AND_FITNESS : 3.0798736462093865
    PHOTOGRAPHY : 2.944494584837545
    NEWS_AND_MAGAZINES : 2.7978339350180503
    SOCIAL : 2.6624548736462095
    TRAVEL_AND_LOCAL : 2.33528880866426
    SHOPPING : 2.2450361010830324
    BOOKS_AND_REFERENCE : 2.1435018050541514
    DATING : 1.861462093862816
    VIDEO_PLAYERS : 1.7937725631768955
    MAPS_AND_NAVIGATION : 1.3989169675090252
    FOOD_AND_DRINK : 1.2409747292418771
    EDUCATION : 1.1620036101083033
    ENTERTAINMENT : 0.9589350180505415
    LIBRARIES_AND_DEMO : 0.9363718411552346
    AUTO_AND_VEHICLES : 0.9250902527075812
    HOUSE_AND_HOME : 0.8235559566787004
    WEATHER : 0.8009927797833934
    EVENTS : 0.7107400722021661
    PARENTING : 0.6543321299638989
    ART_AND_DESIGN : 0.6430505415162455
    COMICS : 0.6204873646209386
    BEAUTY : 0.5979241877256317
    

The patterns are not really different in Google Play market. The most common category is Family (18.9 ~ 19% of all apps), which is mostly games designed for kids. Family category is followed by Games with 9.72% and tools with 8.46%.

However, Google Play market has a better representation of practical applications compared to App Store. 



```python
display_table(android_final, -4)
```

    Tools : 8.449909747292418
    Entertainment : 6.069494584837545
    Education : 5.347472924187725
    Business : 4.591606498194946
    Productivity : 3.892148014440433
    Lifestyle : 3.892148014440433
    Finance : 3.7003610108303246
    Medical : 3.531137184115524
    Sports : 3.463447653429603
    Personalization : 3.3167870036101084
    Communication : 3.2378158844765346
    Action : 3.1024368231046933
    Health & Fitness : 3.0798736462093865
    Photography : 2.944494584837545
    News & Magazines : 2.7978339350180503
    Social : 2.6624548736462095
    Travel & Local : 2.3240072202166067
    Shopping : 2.2450361010830324
    Books & Reference : 2.1435018050541514
    Simulation : 2.0419675090252705
    Dating : 1.861462093862816
    Arcade : 1.8501805054151623
    Video Players & Editors : 1.7712093862815883
    Casual : 1.7599277978339352
    Maps & Navigation : 1.3989169675090252
    Food & Drink : 1.2409747292418771
    Puzzle : 1.128158844765343
    Racing : 0.9927797833935018
    Role Playing : 0.9363718411552346
    Libraries & Demo : 0.9363718411552346
    Auto & Vehicles : 0.9250902527075812
    Strategy : 0.9138086642599278
    House & Home : 0.8235559566787004
    Weather : 0.8009927797833934
    Events : 0.7107400722021661
    Adventure : 0.6768953068592057
    Comics : 0.6092057761732852
    Beauty : 0.5979241877256317
    Art & Design : 0.5979241877256317
    Parenting : 0.4963898916967509
    Card : 0.45126353790613716
    Casino : 0.42870036101083037
    Trivia : 0.41741877256317694
    Educational;Education : 0.39485559566787
    Board : 0.3835740072202166
    Educational : 0.3722924187725632
    Education;Education : 0.33844765342960287
    Word : 0.2594765342960289
    Casual;Pretend Play : 0.236913357400722
    Music : 0.2030685920577617
    Racing;Action & Adventure : 0.16922382671480143
    Puzzle;Brain Games : 0.16922382671480143
    Entertainment;Music & Video : 0.16922382671480143
    Casual;Brain Games : 0.13537906137184114
    Casual;Action & Adventure : 0.13537906137184114
    Arcade;Action & Adventure : 0.12409747292418773
    Action;Action & Adventure : 0.10153429602888085
    Educational;Pretend Play : 0.09025270758122744
    Simulation;Action & Adventure : 0.078971119133574
    Parenting;Education : 0.078971119133574
    Entertainment;Brain Games : 0.078971119133574
    Board;Brain Games : 0.078971119133574
    Parenting;Music & Video : 0.06768953068592057
    Educational;Brain Games : 0.06768953068592057
    Casual;Creativity : 0.06768953068592057
    Art & Design;Creativity : 0.06768953068592057
    Education;Pretend Play : 0.056407942238267145
    Role Playing;Pretend Play : 0.04512635379061372
    Education;Creativity : 0.04512635379061372
    Role Playing;Action & Adventure : 0.033844765342960284
    Puzzle;Action & Adventure : 0.033844765342960284
    Entertainment;Creativity : 0.033844765342960284
    Entertainment;Action & Adventure : 0.033844765342960284
    Educational;Creativity : 0.033844765342960284
    Educational;Action & Adventure : 0.033844765342960284
    Education;Music & Video : 0.033844765342960284
    Education;Brain Games : 0.033844765342960284
    Education;Action & Adventure : 0.033844765342960284
    Adventure;Action & Adventure : 0.033844765342960284
    Video Players & Editors;Music & Video : 0.02256317689530686
    Sports;Action & Adventure : 0.02256317689530686
    Simulation;Pretend Play : 0.02256317689530686
    Puzzle;Creativity : 0.02256317689530686
    Music;Music & Video : 0.02256317689530686
    Entertainment;Pretend Play : 0.02256317689530686
    Casual;Education : 0.02256317689530686
    Board;Action & Adventure : 0.02256317689530686
    Video Players & Editors;Creativity : 0.01128158844765343
    Trivia;Education : 0.01128158844765343
    Travel & Local;Action & Adventure : 0.01128158844765343
    Tools;Education : 0.01128158844765343
    Strategy;Education : 0.01128158844765343
    Strategy;Creativity : 0.01128158844765343
    Strategy;Action & Adventure : 0.01128158844765343
    Simulation;Education : 0.01128158844765343
    Role Playing;Brain Games : 0.01128158844765343
    Racing;Pretend Play : 0.01128158844765343
    Puzzle;Education : 0.01128158844765343
    Parenting;Brain Games : 0.01128158844765343
    Music & Audio;Music & Video : 0.01128158844765343
    Lifestyle;Pretend Play : 0.01128158844765343
    Lifestyle;Education : 0.01128158844765343
    Health & Fitness;Education : 0.01128158844765343
    Health & Fitness;Action & Adventure : 0.01128158844765343
    Entertainment;Education : 0.01128158844765343
    Communication;Creativity : 0.01128158844765343
    Comics;Creativity : 0.01128158844765343
    Casual;Music & Video : 0.01128158844765343
    Card;Action & Adventure : 0.01128158844765343
    Books & Reference;Education : 0.01128158844765343
    Art & Design;Pretend Play : 0.01128158844765343
    Art & Design;Action & Adventure : 0.01128158844765343
    Arcade;Pretend Play : 0.01128158844765343
    Adventure;Education : 0.01128158844765343
    

The difference between category and genre is not really clear, however we can see that Tools is most common genre with 8.45%, followed by Entertainment with 6% and Education reaching only 5.35%. The one potential difference between category and genre columns is that genre has more variations, so it is better to continue focusing only on category column for now. 

To summarize, it was found that App Store is dominated by applications designed for fun, while Google Play market has a more balanced distribution between for-fun and practical apps. 

## Most Popular Apps by Genre on the App Store 
Frequency tables that we created showed us that apps designed for fun dominate the App Store, while Google Play has a mar balanced market of applications. Now we need to determine applications with the most number of users. 

We could calculate the mean value of installs per each app genre in order to accomplish this task. We can easily do it in Google Play data set by using `'Installs'`column, however we don't have such data in App Store data set. We could use `'Rating_countr_tot'` column instead in iOS data set. 

First, we need to isolate the apps of each genre and calculate average number of user ratings per app genre.


```python
ios_genre = freq_table(ios_final, -5)

for genre in ios_genre: 
    total = 0 
    len_genre = 0
    for app in ios_final: 
        genre_app = app[-5]
        if genre_app == genre:
            n_ratings = float(app[5])
            total += n_ratings
            len_genre += 1
    average_ratings = total / len_genre
    print(genre, ':', average_ratings)
```

    Social Networking : 71548.34905660378
    Photo & Video : 28441.54375
    Games : 22788.6696905016
    Music : 57326.530303030304
    Reference : 74942.11111111111
    Health & Fitness : 23298.015384615384
    Weather : 52279.892857142855
    Utilities : 18684.456790123455
    Travel : 28243.8
    Shopping : 26919.690476190477
    News : 21248.023255813954
    Navigation : 86090.33333333333
    Lifestyle : 16485.764705882353
    Entertainment : 14029.830708661417
    Food & Drink : 33333.92307692308
    Sports : 23008.898550724636
    Book : 39758.5
    Finance : 31467.944444444445
    Education : 7003.983050847458
    Productivity : 21028.410714285714
    Business : 7491.117647058823
    Catalogs : 4004.0
    Medical : 612.0
    

Navigation applications have the highest average number of user reviews. We could go even further and check which apps in this genre are the most popular in App Store.



```python
for app in ios_final: 
    if app[-5] == 'Navigation': 
        print(app[1], ':', app[5])
```

    Waze - GPS Navigation, Maps & Real-time Traffic : 345046
    Google Maps - Navigation & Transit : 154911
    Geocaching® : 12811
    CoPilot GPS – Car Navigation & Offline Maps : 3582
    ImmobilienScout24: Real Estate Search in Germany : 187
    Railway Route Search : 5
    

It is clear that average number of revie in Navigation genre is dominated by applications from giant firms such as Waze - GPS Navigation and Google Maps, while other applications in this genre can't even reach more than 20 thousand reviews.  

The second most popular genre is Reference. We could check this genre too. 


```python
for app in ios_final: 
    if app[-5] == 'Reference': 
        print(app[1], ':', app[5])
```

    Bible : 985920
    Dictionary.com Dictionary & Thesaurus : 200047
    Dictionary.com Dictionary & Thesaurus for iPad : 54175
    Google Translate : 26786
    Muslim Pro: Ramadan 2017 Prayer Times, Azan, Quran : 18418
    New Furniture Mods - Pocket Wiki & Game Tools for Minecraft PC Edition : 17588
    Merriam-Webster Dictionary : 16849
    Night Sky : 12122
    City Maps for Minecraft PE - The Best Maps for Minecraft Pocket Edition (MCPE) : 8535
    LUCKY BLOCK MOD ™ for Minecraft PC Edition - The Best Pocket Wiki & Mods Installer Tools : 4693
    GUNS MODS for Minecraft PC Edition - Mods Tools : 1497
    Guides for Pokémon GO - Pokemon GO News and Cheats : 826
    WWDC : 762
    Horror Maps for Minecraft PE - Download The Scariest Maps for Minecraft Pocket Edition (MCPE) Free : 718
    VPN Express : 14
    Real Bike Traffic Rider Virtual Reality Glasses : 8
    教えて!goo : 0
    Jishokun-Japanese English Dictionary & Translator : 0
    

This genre is not dominated by famous companies and there is some potential for creating our application. We could focus on this side of the market in App Store. For example, we could create an application which will allow users to install any books freely, including Dictionary, Bible or other popular books. This will allow our users to read any books they want without closing our application and installing others. 


Other most popular genres are: 
 - Social Networking
  - Music
  - Weather
  - Finance
 


```python
for app in ios_final:
    if app[-5] == 'Social Networking': 
        print(app[1], ':', app[5])
```

    Facebook : 2974676
    Pinterest : 1061624
    Skype for iPhone : 373519
    Messenger : 351466
    Tumblr : 334293
    WhatsApp Messenger : 287589
    Kik : 260965
    ooVoo – Free Video Call, Text and Voice : 177501
    TextNow - Unlimited Text + Calls : 164963
    Viber Messenger – Text & Call : 164249
    Followers - Social Analytics For Instagram : 112778
    MeetMe - Chat and Meet New People : 97072
    We Heart It - Fashion, wallpapers, quotes, tattoos : 90414
    InsTrack for Instagram - Analytics Plus More : 85535
    Tango - Free Video Call, Voice and Chat : 75412
    LinkedIn : 71856
    Match™ - #1 Dating App. : 60659
    Skype for iPad : 60163
    POF - Best Dating App for Conversations : 52642
    Timehop : 49510
    Find My Family, Friends & iPhone - Life360 Locator : 43877
    Whisper - Share, Express, Meet : 39819
    Hangouts : 36404
    LINE PLAY - Your Avatar World : 34677
    WeChat : 34584
    Badoo - Meet New People, Chat, Socialize. : 34428
    Followers + for Instagram - Follower Analytics : 28633
    GroupMe : 28260
    Marco Polo Video Walkie Talkie : 27662
    Miitomo : 23965
    SimSimi : 23530
    Grindr - Gay and same sex guys chat, meet and date : 23201
    Wishbone - Compare Anything : 20649
    imo video calls and chat : 18841
    After School - Funny Anonymous School News : 18482
    Quick Reposter - Repost, Regram and Reshare Photos : 17694
    Weibo HD : 16772
    Repost for Instagram : 15185
    Live.me – Live Video Chat & Make Friends Nearby : 14724
    Nextdoor : 14402
    Followers Analytics for Instagram - InstaReport : 13914
    YouNow: Live Stream Video Chat : 12079
    FollowMeter for Instagram - Followers Tracking : 11976
    LINE : 11437
    eHarmony™ Dating App - Meet Singles : 11124
    Discord - Chat for Gamers : 9152
    QQ : 9109
    Telegram Messenger : 7573
    Weibo : 7265
    Periscope - Live Video Streaming Around the World : 6062
    Chat for Whatsapp - iPad Version : 5060
    QQ HD : 5058
    Followers Analysis Tool For Instagram App Free : 4253
    live.ly - live video streaming : 4145
    Houseparty - Group Video Chat : 3991
    SOMA Messenger : 3232
    Monkey : 3060
    Down To Lunch : 2535
    Flinch - Video Chat Staring Contest : 2134
    Highrise - Your Avatar Community : 2011
    LOVOO - Dating Chat : 1985
    PlayStation®Messages : 1918
    BOO! - Video chat camera with filters & stickers : 1805
    Qzone : 1649
    Chatous - Chat with new people : 1609
    Kiwi - Q&A : 1538
    GhostCodes - a discovery app for Snapchat : 1313
    Jodel : 1193
    FireChat : 1037
    Google Duo - simple video calling : 1033
    Fiesta by Tango - Chat & Meet New People : 885
    Google Allo — smart messaging : 862
    Peach — share vividly : 727
    Hey! VINA - Where Women Meet New Friends : 719
    Battlefield™ Companion : 689
    All Devices for WhatsApp - Messenger for iPad : 682
    Chat for Pokemon Go - GoChat : 500
    IAmNaughty – Dating App to Meet New People Online : 463
    Qzone HD : 458
    Zenly - Locate your friends in realtime : 427
    League of Legends Friends : 420
    豆瓣 : 407
    Candid - Speak Your Mind Freely : 398
    知乎 : 397
    Selfeo : 366
    Fake-A-Location Free ™ : 354
    Popcorn Buzz - Free Group Calls : 281
    Fam — Group video calling for iMessage : 279
    QQ International : 274
    Ameba : 269
    SoundCloud Pulse: for creators : 240
    Tantan : 235
    Cougar Dating & Life Style App for Mature Women : 213
    Rawr Messenger - Dab your chat : 180
    WhenToPost: Best Time to Post Photos for Instagram : 158
    Inke—Broadcast an amazing life : 147
    Mustknow - anonymous video Q&A : 53
    CTFxCmoji : 39
    Lobi : 36
    Chain: Collaborate On MyVideo Story/Group Video : 35
    botman - Real time video chat : 7
    BestieBox : 0
    MATCH ON LINE chat : 0
    niconico ch : 0
    LINE BLOG : 0
    bit-tube - Live Stream Video Chat : 0
    


```python
for app in ios_final: 
    if app[-5] == 'Music': 
        print(app[1], ':', app[5])
```

    Pandora - Music & Radio : 1126879
    Spotify Music : 878563
    Shazam - Discover music, artists, videos & lyrics : 402925
    iHeartRadio – Free Music & Radio Stations : 293228
    SoundCloud - Music & Audio : 135744
    Magic Piano by Smule : 131695
    Smule Sing! : 119316
    TuneIn Radio - MLB NBA Audiobooks Podcasts Music : 110420
    Amazon Music : 106235
    SoundHound Song Search & Music Player : 82602
    Sonos Controller : 48905
    Bandsintown Concerts : 30845
    Karaoke - Sing Karaoke, Unlimited Songs! : 28606
    My Mixtapez Music : 26286
    Sing Karaoke Songs Unlimited with StarMaker : 26227
    Ringtones for iPhone & Ringtone Maker : 25403
    Musi - Unlimited Music For YouTube : 25193
    AutoRap by Smule : 18202
    Spinrilla - Mixtapes For Free : 15053
    Napster - Top Music & Radio : 14268
    edjing Mix:DJ turntable to remix and scratch music : 13580
    Free Music - MP3 Streamer & Playlist Manager Pro : 13443
    Free Piano app by Yokee : 13016
    Google Play Music : 10118
    Certified Mixtapes - Hip Hop Albums & Mixtapes : 9975
    TIDAL : 7398
    YouTube Music : 7109
    Nicki Minaj: The Empire : 5196
    Sounds app - Music And Friends : 5126
    SongFlip - Free Music Streamer : 5004
    Simple Radio - Live AM & FM Radio Stations : 4787
    Deezer - Listen to your Favorite Music & Playlists : 4677
    Ringtones for iPhone with Ringtone Maker : 4013
    Bose SoundTouch : 3687
    Amazon Alexa : 3018
    DatPiff : 2815
    Trebel Music - Unlimited Music Downloader : 2570
    Free Music Play - Mp3 Streamer & Player : 2496
    Acapella from PicPlayPost : 2487
    Coach Guitar - Lessons & Easy Tabs For Beginners : 2416
    Musicloud - MP3 and FLAC Music Player for Cloud Platforms. : 2211
    Piano - Play Keyboard Music Games with Magic Tiles : 1636
    Boom: Best Equalizer & Magical Surround Sound : 1375
    Music Freedom - Unlimited Free MP3 Music Streaming : 1246
    AmpMe - A Portable Social Party Music Speaker : 1047
    Medly - Music Maker : 933
    Bose Connect : 915
    Music Memos : 909
    UE BOOM : 612
    LiveMixtapes : 555
    NOISE : 355
    MP3 Music Player & Streamer for Clouds : 329
    Musical Video Maker - Create Music clips lip sync : 320
    Cloud Music Player - Downloader & Playlist Manager : 319
    Remixlive - Remix loops with pads : 288
    QQ音乐HD : 224
    Blocs Wave - Make & Record Music : 158
    PlayGround • Music At Your Fingertips : 150
    Music and Chill : 135
    The Singing Machine Mobile Karaoke App : 130
    radio.de - Der Radioplayer : 64
    Free Music -  Player & Streamer  for Dropbox, OneDrive & Google Drive : 46
    NRJ Radio : 38
    Smart Music: Streaming Videos and Radio : 17
    BOSS Tuner : 13
    PetitLyrics : 0
    

 - Social Networking - genre is also dominated by giant firms and there is no potential for us to develop our application which could give us profit. 
 -  Weather - Users usually do not spend long time looking on the weather. We are trying to make free app which will give us profit solely from advertisements, so there are very low chances that we could benefit from this genre.
 - Music - This genre is also occupied by the biggest firms that we know. For example: Pandora, Spotify, Amazon, Shazam. This genre is also not a good to choice for us to make profit from advertisements. 
 
 It seems that Reference genre is the most suitable for our goal.
 Now, let's analyze Google Play data set.

## Most Popular Apps by Genre on Google Play
It will be easier to analyze the Google Play market data as we have the actual number of installs in this data set. However, the only issue is that the values in `'installs'` column are open_ended (100+, 500+, 1000+, 5000+, etc.)


```python
display_table(android_final, 5)
```

    1,000,000+ : 15.726534296028879
    100,000+ : 11.552346570397113
    10,000,000+ : 10.548285198555957
    10,000+ : 10.198555956678701
    1,000+ : 8.393501805054152
    100+ : 6.915613718411552
    5,000,000+ : 6.825361010830325
    500,000+ : 5.561823104693141
    50,000+ : 4.7721119133574
    5,000+ : 4.512635379061372
    10+ : 3.5424187725631766
    500+ : 3.2490974729241873
    50,000,000+ : 2.3014440433213
    100,000,000+ : 2.1322202166064983
    50+ : 1.917870036101083
    5+ : 0.78971119133574
    1+ : 0.5076714801444043
    500,000,000+ : 0.2707581227436823
    1,000,000,000+ : 0.22563176895306858
    0+ : 0.04512635379061372
    0 : 0.01128158844765343
    

Luckily, we don't need very precise data as we only want to find out which app genres in Google Play marker attract the most number of users. 

We will consider that an app with 100,000+ installs has 100,000 installs, and an app with 50,000,000+ installs has 50,000,00 installs. But we still need to convert this numbers into float for out data analysis. We will have to remove the commas and the plus characters. 

Below, we are isolating the apps by genre and computing average value of apps installations per each genre.


```python
android_categories = freq_table(android_final, 1)

for category in android_categories: 
    total = 0 
    len_category = 0 
    for app in android_final: 
        category_app = app[1]
        if category_app == category: 
            n_installs = app[5]
            n_installs = n_installs.replace(',', '')
            n_installs = n_installs.replace('+', '' )
            total += float(n_installs)
            len_category += 1
    average_number = total / len_category
    print(category, ":", average_number)
```

    ART_AND_DESIGN : 1986335.0877192982
    AUTO_AND_VEHICLES : 647317.8170731707
    BEAUTY : 513151.88679245283
    BOOKS_AND_REFERENCE : 8767811.894736841
    BUSINESS : 1712290.1474201474
    COMICS : 817657.2727272727
    COMMUNICATION : 38456119.167247385
    DATING : 854028.8303030303
    EDUCATION : 1833495.145631068
    ENTERTAINMENT : 11640705.88235294
    EVENTS : 253542.22222222222
    FINANCE : 1387692.475609756
    FOOD_AND_DRINK : 1924897.7363636363
    HEALTH_AND_FITNESS : 4188821.9853479853
    HOUSE_AND_HOME : 1331540.5616438356
    LIBRARIES_AND_DEMO : 638503.734939759
    LIFESTYLE : 1437816.2687861272
    GAME : 15588015.603248259
    FAMILY : 3695641.8198090694
    MEDICAL : 120550.61980830671
    SOCIAL : 23253652.127118643
    SHOPPING : 7036877.311557789
    PHOTOGRAPHY : 17840110.40229885
    SPORTS : 3638640.1428571427
    TRAVEL_AND_LOCAL : 13984077.710144928
    TOOLS : 10801391.298666667
    PERSONALIZATION : 5201482.6122448975
    PRODUCTIVITY : 16787331.344927534
    PARENTING : 542603.6206896552
    WEATHER : 5074486.197183099
    VIDEO_PLAYERS : 24727872.452830188
    NEWS_AND_MAGAZINES : 9549178.467741935
    MAPS_AND_NAVIGATION : 4056941.7741935486
    

On average, communications apps have the most installs in the Google Play market : 38,456,119. However, number of installs is heavily influenced by giant firms. 


```python
for app in android_final:
    if app[1] == 'COMMUNICATION' and (app[5] == '1,000,000,000+'
                                      or app[5] == '500,000,000+'
                                      or app[5] == '100,000,000+'):
        print(app[0], ':', app[5])
```

    WhatsApp Messenger : 1,000,000,000+
    imo beta free calls and text : 100,000,000+
    Android Messages : 100,000,000+
    Google Duo - High Quality Video Calls : 500,000,000+
    Messenger – Text and Video Chat for Free : 1,000,000,000+
    imo free video calls and chat : 500,000,000+
    Skype - free IM & video calls : 1,000,000,000+
    Who : 100,000,000+
    GO SMS Pro - Messenger, Free Themes, Emoji : 100,000,000+
    LINE: Free Calls & Messages : 500,000,000+
    Google Chrome: Fast & Secure : 1,000,000,000+
    Firefox Browser fast & private : 100,000,000+
    UC Browser - Fast Download Private & Secure : 500,000,000+
    Gmail : 1,000,000,000+
    Hangouts : 1,000,000,000+
    Messenger Lite: Free Calls & Messages : 100,000,000+
    Kik : 100,000,000+
    KakaoTalk: Free Calls & Text : 100,000,000+
    Opera Mini - fast web browser : 100,000,000+
    Opera Browser: Fast and Secure : 100,000,000+
    Telegram : 100,000,000+
    Truecaller: Caller ID, SMS spam blocking & Dialer : 100,000,000+
    UC Browser Mini -Tiny Fast Private & Secure : 100,000,000+
    Viber Messenger : 500,000,000+
    WeChat : 100,000,000+
    Yahoo Mail – Stay Organized : 100,000,000+
    BBM - Free Calls & Messages : 100,000,000+
    

Another popular genre among Android users is Video Players. Same patter can be viewed for Video Players too, which means it is not a profitable area to create our application.


```python
for app in android_final:
    if app[1] == 'VIDEO_PLAYERS' and (app[5] == '1,000,000,000+'
                                      or app[5] == '500,000,000+'
                                      or app[5] == '100,000,000+'):
        print(app[0], ':', app[5])
```

    YouTube : 1,000,000,000+
    Motorola Gallery : 100,000,000+
    VLC for Android : 100,000,000+
    Google Play Movies & TV : 1,000,000,000+
    MX Player : 500,000,000+
    Dubsmash : 100,000,000+
    VivaVideo - Video Editor & Photo Movie : 100,000,000+
    VideoShow-Video Editor, Video Maker, Beauty Camera : 100,000,000+
    Motorola FM Radio : 100,000,000+
    

Book and Reference genre is very popular as well. Let's see if the Google Play market has a potential for this genre, so we could make profit from advertisements there both in Google Play and App Store market.


```python
for app in android_final:
    if app[1] == 'BOOKS_AND_REFERENCE' and (app[5] == '1,000,000,000+'
                                      or app[5] == '500,000,000+'
                                      or app[5] == '100,000,000+'):
        print(app[0], ':', app[5])
```

    Google Play Books : 1,000,000,000+
    Bible : 100,000,000+
    Amazon Kindle : 100,000,000+
    Wattpad 📖 Free Books : 100,000,000+
    Audiobooks from Audible : 100,000,000+
    

There are only few applications that have the most popularity in Books and Reference genre. This area might have a potential for us in Google Play market as well. However,  it is also important to check number of applications somewhere in the middle of popularity (greater or equal to 1,000,000 installations). 


```python
for app in android_final:
    if app[1] == 'BOOKS_AND_REFERENCE' and (app[5] == '1,000,000+'
                                      or app[5] == '5,000,000+'
                                      or app[5] == '10,000,000+'
                                        or app[5] == '50,000,000+'):
        print(app[0], ':', app[5])
```

    Wikipedia : 10,000,000+
    Cool Reader : 10,000,000+
    Book store : 1,000,000+
    FBReader: Favorite Book Reader : 10,000,000+
    Free Books - Spirit Fanfiction and Stories : 1,000,000+
    AlReader -any text book reader : 5,000,000+
    FamilySearch Tree : 1,000,000+
    Cloud of Books : 1,000,000+
    ReadEra – free ebook reader : 1,000,000+
    Ebook Reader : 5,000,000+
    Read books online : 5,000,000+
    eBoox: book reader fb2 epub zip : 1,000,000+
    All Maths Formulas : 1,000,000+
    Ancestry : 5,000,000+
    HTC Help : 10,000,000+
    Moon+ Reader : 10,000,000+
    English-Myanmar Dictionary : 1,000,000+
    Golden Dictionary (EN-AR) : 1,000,000+
    All Language Translator Free : 1,000,000+
    Aldiko Book Reader : 10,000,000+
    Dictionary - WordWeb : 5,000,000+
    50000 Free eBooks & Free AudioBooks : 5,000,000+
    Al-Quran (Free) : 10,000,000+
    Al Quran Indonesia : 10,000,000+
    Al'Quran Bahasa Indonesia : 10,000,000+
    Al Quran Al karim : 1,000,000+
    Al Quran : EAlim - Translations & MP3 Offline : 5,000,000+
    Koran Read &MP3 30 Juz Offline : 1,000,000+
    Hafizi Quran 15 lines per page : 1,000,000+
    Quran for Android : 10,000,000+
    Satellite AR : 1,000,000+
    Oxford A-Z of English Usage : 1,000,000+
    Dictionary.com: Find Definitions for English Words : 10,000,000+
    English Dictionary - Offline : 10,000,000+
    Bible KJV : 5,000,000+
    NOOK: Read eBooks & Magazines : 10,000,000+
    Brilliant Quotes: Life, Love, Family & Motivation : 1,000,000+
    Stats Royale for Clash Royale : 1,000,000+
    Dictionary : 10,000,000+
    wikiHow: how to do anything : 1,000,000+
    EGW Writings : 1,000,000+
    My Little Pony AR Guide : 1,000,000+
    Spanish English Translator : 10,000,000+
    Dictionary - Merriam-Webster : 10,000,000+
    JW Library : 10,000,000+
    Oxford Dictionary of English : Free : 10,000,000+
    English Hindi Dictionary : 10,000,000+
    English to Hindi Dictionary : 5,000,000+
    

It looks like this area is dominated by reading ebooks and software processing applications, as well as lots of libraries and dictionaries. It is probably not a good idea to build similar apps like here as there will be a significant competition.

However, if we create an application with free access to all past and recent new books, it should work well. But we also should make some original features to our application to avoid making it similar to others. For example: audio version of the books, popular quotes in the books, discussion groups or quizzes.

## Conclusions 
In this project we analyzed data about App Store and Google Play mobile applications with the goal of creating an application that will be profitable for both markets.

It was concluded that creating a universal mobile app that will have all recent and past books will be profitable for our company. However for sakes of avoiding the competition, we should add additional features for our mobile application, such as audio version of the books, quizzes or discussion groups where people can interact and discuss books.
