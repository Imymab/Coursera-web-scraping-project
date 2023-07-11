# Introduction
This is my "Coursera web scraping Project" using python in jupyter notebook.
# What is Web scraping ? 
Web scraping is like using a magic vacuum cleanerfor the internet. It's a clever technique where you can teach a computer to automatically gather information from websites. Just like a vacuum cleaner sucks up dirt, web scraping sucks up data from web pages and stores it for you to use.
In this project, I used a library called "BeautifulSoup" which is a popular Python library used for web scraping tasks. It provides convenient methods and features for parsing HTML and XML documents, making it easier to extract data from web pages. 
# My purpose of this project:
The main purpose behind this was to automate the collecting process of the different data about the course 'Fundamentals of Visualization with Tableau' offered by Coursera, then send email notifications to myself whenever there is a change in the enrollment count. 

```python
#import libraries:

from bs4 import BeautifulSoup
import requests
import smtplib
import time
import datetime
```


```python
#Getting the data from the website of Coursera:

URL = 'https://www.coursera.org/learn/data-visualization-tableau?='

headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36","Accept-Encoding": "gzip, deflate, br", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", 
    "Upgrade-Insecure-Requests": "1"}

page = requests.get(URL, headers = headers)

soup1 = BeautifulSoup(page.content, "html.parser")

soup2 = BeautifulSoup(soup1.prettify(), "html.parser")

try:
# Try to find and extract the desired element
   title = soup2.find(class_='banner-title banner-title-without--subtitle m-b-0')
   title_content = title.get_text().strip()
   print(title_content)
except AttributeError:
    # Handle the case when the element is not found
    print('Element not found or attribute error occurred.')
```

    Fundamentals of Visualization with Tableau
    


```python
instructor_name= soup2.find(class_="instructor-name headline-3-text bold").get_text().strip()
print(instructor_name)
```

    Desiree' Abbott
    


```python
Ratings = soup2.find(class_="_16ni8zai m-b-0 rating-text number-rating m-l-1s m-r-1").get_text().strip()
Ratings_course = Ratings[:3]+" stars"
#print(Ratings[:3]," stars")
print(Ratings_course)
```

    4.5 stars
    


```python
university_course= soup2.find(class_="headline-4-text bold rc-Partner__title").get_text().strip()
print(university_course)
```

    University of California, Davis
    


```python
#In this code, the HTML content <strong>217 856</strong> is passed to BeautifulSoup for parsing. The find() method is used to locate the <strong> tag within the parsed HTML.

enrollement = soup2.find('strong').get_text().strip()

enrollement_status = enrollement + ' Learners'

print(enrollement_status)
```

    207,749 Learners
    


```python
div_elements = soup2.find_all('div', class_='_16ni8zai m-b-0')

print(div_elements)
```

    [<div class="_16ni8zai m-b-0">
                     Flexible deadlines
                    </div>, <div class="_16ni8zai m-b-0">
                     Shareable Certificate
                    </div>, <div class="_16ni8zai m-b-0">
                     100% online
                    </div>, <div class="_16ni8zai m-b-0">
    <span>
                      Course 1 of 5 in the
                     </span>
    </div>, <div class="_16ni8zai m-b-0">
                     Beginner Level
                    </div>, <div class="_16ni8zai m-b-0">
                     English
                    </div>, <div class="_16ni8zai m-b-0">
                     Flexible deadlines
                    </div>, <div class="_16ni8zai m-b-0">
                     Shareable Certificate
                    </div>, <div class="_16ni8zai m-b-0">
                     100% online
                    </div>, <div class="_16ni8zai m-b-0">
    <span>
                      Course 1 of 5 in the
                     </span>
    </div>, <div class="_16ni8zai m-b-0">
                     Beginner Level
                    </div>, <div class="_16ni8zai m-b-0">
                     English
                    </div>]
    


```python
#In my case, When scraping some additional information about the course, I found that the desired information have the same class, with some extra keywords...
```


```python
for div_element in div_elements:
    # Check if the text content contains 'Beginner Level'
    if 'Beginner Level' in div_element.text:
        difficulty_level = div_element.text.strip()
        
    if 'English' in div_element.text:
        language = div_element.text.strip()
      
print(difficulty_level)
print(language)
```

    Beginner Level
    English
    


```python
import datetime
today = datetime.date.today()
print(today)
```

    2023-07-11
    


```python
#Saving the collected data from Coursera into a Csv file:

import csv

header = ['Course Title', 'Instructor', 'University', 'Ratings', 'Date', 'Difficulty_level ', 'Language', "Enrollement Status"]

data_course = [title_content, instructor_name, university_course, Ratings_course, today, difficulty_level, language, enrollement_status ]

with open('CourseraWebScraperDataset.csv', 'w', newline='', encoding='UTF8') as f:
    writer = csv.writer(f)
    writer.writerow(header)
    writer.writerow(data_course)
```


```python
import pandas as pd

df = pd.read_csv(r'C:\Users\hp\CourseraWebScraperDataset.csv')
print(df)
```

                                     Course Title       Instructor  \
    0  Fundamentals of Visualization with Tableau  Desiree' Abbott   
    
                            University    Ratings        Date Difficulty_level   \
    0  University of California, Davis  4.5 stars  2023-07-11    Beginner Level   
    
      Language Enrollement Status  
    0  English   207,749 Learners  
    


```python
def scrape_and_append():
    
    URL = 'https://www.coursera.org/learn/data-visualization-tableau?='

    headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36","Accept-Encoding": "gzip, deflate, br", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", 
    "Upgrade-Insecure-Requests": "1"}

    page = requests.get(URL, headers = headers)

    soup1 = BeautifulSoup(page.content, "html.parser")
    
    soup2 = BeautifulSoup(soup1.prettify(), "html.parser")

    
    try:
# Try to find and extract the desired element
      title = soup2.find(class_='banner-title banner-title-without--subtitle m-b-0')
      title_content = title.get_text().strip()
   
    except AttributeError:
       # Handle the case when the element is not found
       print('Element not found or attribute error occurred.')
        
    instructor_name= soup2.find(class_="instructor-name headline-3-text bold").get_text().strip()
    
    Ratings = soup2.find(class_="_16ni8zai m-b-0 rating-text number-rating m-l-1s m-r-1").get_text().strip()
    Ratings_course = Ratings[:3]+" stars"
    
    university_course= soup2.find(class_="headline-4-text bold rc-Partner__title").get_text().strip()
    
    div_elements = soup2.find_all('div', class_='_16ni8zai m-b-0')
    
    for div_element in div_elements:
    
       if 'Beginner Level' in div_element.text:
          difficulty_level = div_element.text.strip()
        
       if 'English' in div_element.text:
          language = div_element.text.strip()
   
    enrollement = soup2.find('strong').get_text().strip()
    enrollement_status = enrollement + ' Learners'
    
    import datetime
    today = datetime.date.today()
    
    import csv

    header = ['Course Title', 'Instructor', 'University', 'Ratings', 'Date', 'Difficulty_level ', 'Language',"Enrollement Status"]


    data_course = [title_content, instructor_name, university_course, Ratings_course, today, difficulty_level, language, enrollement_status ]

    
    with open('CourseraWebScraperDataset.csv', 'a+', newline='', encoding='UTF8') as f:
        writer = csv.writer(f)
        writer.writerow(data_course)
        
    # Retrieve the previous enrollment count from the CSV file
    with open('CourseraWebScraperDataset.csv', 'r') as f:
        reader = csv.reader(f)
        rows = list(reader)
        previous_enrollment = rows[-1][-1].split()[0]  # Assuming the enrollment status is in the last column

    # Compare the previous enrollment count with the current count
    #if previous_enrollment != enrollement:
        # Enrollment count has changed, send email notification
        #send_mail(enrollement)
        
        
```


```python

scrape_and_append()

```


```python
import pandas as pd

df = pd.read_csv(r'C:\Users\hp\CourseraWebScraperDataset.csv')
print(df)
```

                                     Course Title       Instructor  \
    0  Fundamentals of Visualization with Tableau  Desiree' Abbott   
    1  Fundamentals of Visualization with Tableau  Desiree' Abbott   
    
                            University    Ratings        Date Difficulty_level   \
    0  University of California, Davis  4.5 stars  2023-07-11    Beginner Level   
    1  University of California, Davis  4.5 stars  2023-07-11    Beginner Level   
    
      Language Enrollement Status  
    0  English   207,749 Learners  
    1  English   207,749 Learners  
    


```python
#To keep our web scraping running ie to keep track the number of subscribers to the course:

#This is a while Loop to keep the web scraping running after a defined interval of time in sec:
#while(True):
  #scrape_and_append()
  #time.sleep(5) 

```


```python
#sending mail to myself:

def send_mail():
    smtp_server = 'smtp.gmail.com'
    smtp_port = 587
    sender_email = 'imanemabrouk3002@gmail.com'
    password = 'xxxxxxxxxx'

    subject = "Number of learners has changed!"
    body = f'The enrollment count has changed. New count: {enrollement}'

    message = f"Subject: {subject}\n\n{body}"

    with smtplib.SMTP(smtp_server, smtp_port) as server:
        server.starttls()
        server.login(sender_email, password)
        server.sendmail(sender_email, sender_email, message)
```


```python

```


```python

```


```python

```


```python

```
