# Task #1: Scrapping from Instagram

## Overview
This Python script uses Selenium and Pandas to scrape data from an Instagram profile and its posts.

## Requirements
- Python 3.x
- Pandas
- Selenium
- ChromeDriver (ensure it's installed and its path is set correctly)

## Description of the Code
### Importing required libraries

```
import pandas as pd
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui.WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time

```
### Setting up driver 
```
url = 'https://www.instagram.com/bbc/'
options = webdriver.ChromeOptions()
service = Service("D:\\backup\\chromedriver.exe")
driver = webdriver.Chrome(service=service, options=options)
driver.get(url)
time.sleep(2)
```
### Extract Profile Information
``` 
try:
    # Extract the title of the page (username)
    title = driver.find_element(By.CLASS_NAME,"x1qjc9v5.x78zum5.x1q0g3np.x2lah0s.x1n2onr6.x1qsaojo.xc2v4qs.x1xl8k2i.x1ez9qw7.x1kcpa7z")
    title_of_page = driver.find_elements(By.CLASS_NAME,"x58vhm7")[0].text.split()[0]
    
    # Extract the profile image link
    image_link = driver.find_elements(By.CLASS_NAME,"x58vhm7")[0].find_element(By.CLASS_NAME,"xh8yej3").get_attribute("src")
    
    # Extract the bio/description
    desc_element = driver.find_elements(By.CLASS_NAME,"x58vhm7")[0].find_element(By.CLASS_NAME,"xbjc6do")
    if desc_element:
        desc = " ".join(desc_element.text.split()[2:])
    
    # Extract stats (posts, followers, following)
    stats = title.find_elements(By.CLASS_NAME,"x78zum5.x1q0g3np.xieb3on")
    if len(stats) > 0:
        stats_text = stats[0].text.split()
        posts = stats_text[0]
        followers = stats_text[2]
        following = stats_text[4]
except Exception as e:
    print(f"Error extracting data from the profile: {e}")

```
 - Script Explanation
The script opens an Instagram profile, extracts profile information (username, image link, bio, stats).</br>
It uses Selenium to navigate through the Instagram pages and extract the required data.</br>
The extracted data is stored in Pandas DataFrames and then exported to an Excel file.</br>

### Interact with Posts
```
# Click to load more posts
for_more_post = driver.find_elements(By.CLASS_NAME,"x1lugfcp")
len(for_more_post)
for_more_post[0].click()
```
 - Script Explanation
This section handles the interaction needed to load more posts by clicking the button that loads additional content.

### Extract Likes from Each Post
``` 
likes = []
for link in range(len(links_1)):
    driver.get(links_1[link])
    time.sleep(3)
    try:
        likes.append(driver.find_elements(By.CLASS_NAME,"html-span.xdj266r.x11i5rnm.xat24cr")[0].text)
    except:
        likes.append(" ")
```
 - Script Explanation
In this section, the code iterates over each post link, navigates to the post, and extracts the number of likes. If an error occurs during extraction, it appends a blank space.

### Storing and Exporting 

``` 
data = pd.DataFrame(
    {
        "User Name": [title_of_page],
        "User Image": [image_link],
        "Bio": [desc],
        "Number of Posts": [posts],
        "Number of followers": [followers],
        "Number of following": [following]
    }
)

data_50_posts = pd.DataFrame(
    {"Links":links_1,
     "Likes/Interaction" :likes}
)

with pd.ExcelWriter('output.xlsx') as writer:
    data.to_excel(writer, sheet_name='Account Info')
    data_50_posts.to_excel(writer, sheet_name='Interactions of 50 posts')

```

## Files Included
task1.ipynb: Python script for scraping Instagram data.
output.xlsx: Excel file containing scraped data.
