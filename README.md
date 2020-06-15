# Job-Aggregator
A dataset built out of job APIs and Web scraping job listings from various Job Boards and Aggregators and Staffing firms

## **Abstract**

Job aggregators are essentially search engines for jobs. Job aggregators gather job postings from job boards and other internet sites and consolidate them into a single searchable interface. 

This readme describes how the code for our job aggregator prototype was designed and should be engaged.

## **Obejective** 

Our main goal is to build a job aggregator which will be used by every job applicant who is seeking for a job but mostly aimed at International applicants. We are fetching job data by scraping data from Randstad website, and API's of Adzuna, GitHub jobs, and The Muse and merging it with USCIS 2019 H1B employer data which will provide the data of all companies that offer sponsorship and how many applications were filed which were approved and rejected.

## **Process Outline** 


![Process Outline](data/process.png?raw=true)


## **Data Source and Access Rights**<br>
### **Randstad: Web Scraping**<br>
Link: https://www.randstad.com/jobs/united-states/q-data-science/<br>
1. Check robot.txt of Randstad | It allows scraping with crawl delay of 5 seconds. <br>

### **Adzuna API**<br>
Link: https://developer.adzuna.com/overview <br>
We need to create a developer account to get an app_key and aap_id to access the API.<br>
Quering the API : https://api.adzuna.com/v1/api/jobs/us/search/1?app_id={YOUR_APP_ID}&app_key={YOUR_APP_KEY}<br>
**Important Terms and Conditions** <br>
1. There are no rate limits present.<br>
Detailed verion of the terms and conditions : https://www.adzuna.co.uk/terms-and-conditions.html<br>

### **GitHub Jobs API**<br>
Link: https://jobs.github.com/api<br>
GitHub's API doesn't require an "access token". <br>
Quering the API: https://jobs.github.com/positions.json?description=data+science&location=US <br>
**Important Terms and Conditions** <br>
Detailed version of the API terms: https://help.github.com/en/github/site-policy/github-terms-of-service#h-api-terms <br>

### **The Muse API**<br>
Link: https://www.themuse.com/developers/api/v2<br>
Quering the API: https://www.themuse.com/api/public/jobs?category=Data%20Science&location=United%20States&page=1 <br>
**Important Terms and Conditions** <br>
1. If we don't register our app, we are limited to 500 requests per hour. We have created developer account and recieved an app_key which will allow us to make up to 3600 requests per hour.<br>
Detailed version of the API terms: https://www.themuse.com/developers/api/v2/terms

### **United States Citizenship and Immigration Services (USCIS) H1B data**<br>
Link: https://www.uscis.gov/tools/reports-studies/h-1b-employer-data-hub-files<br>
Data is openly available on the above site.

## **Libraries Used**
import urllib.request<br>
import robotexclusionrulesparser<br>
from bs4 import BeautifulSoup<br>
import requests<br>
import time<br>
import random<br>
import re<br>
from collections import defaultdict<br>
from pprint import pprint<br>
import csv<br>
import dateutil.parser as dateparser<br>
import os<br>
import datetime<br>
import pandas as pd<br>
from dateutil import tz<br>

## **Data Acquistion and Preprocessing** 

### **Randstand: Web Scraping**

1. scrapeDataFromPage(page, writer) | Function to fetch required data from a page for a particular job.<br>
2. fetchDataFromRandstad() | Function to get link of every jobs present on https://www.randstad.com/jobs/united-states/q-data-science/ calling above function scrapeDataFromPage() <br> 
3. fetchDataFromRandstadRSS() | To perform scraping once hourly from the RSS feeds<br>

### **Adzuna API** 

1. fetchDataFromAdzuna() | Checking for all the number of pages present in API and fetching all job listings from each page. <br>
2. fetchDataFromAdzuna(1) | It will fetch all data from previous day till today from the API.<br>

### **GitHub API**

1. fetchDataFromGithub() | Checking for all the number of pages present and fetching all job listings from each page.<br>

### **The Muse API**

1. fetchDataFromMuse() | Checking for all the number of pages present and fetching all job listings from each page.<br>

### **United States Citizenship and Immigration Services (USCIS) H1B data** 

1. fetchDataFromUSCIS() | We are fetching 2019 H-1B Employer Data which is openly available on USCIS Website.<br>

### **Merging Job data and USCIS data**

1. mergeWithUSCISData(company) | Merges jobs data with USCIS data to get the H1B statistics for each company in jobs data. <br>

### **Exporting the final results to a CSV file**

### **Accessing Final Job data**

1. searchForJobs(title, location, job_posted) | Function for the User to query for jobs. <br>


## File Sizes
Dated on : 06/14/2019

 File Name | Size |
 --- | --- |
 randstad.csv 							                   | 0.99 MB
 adzuna.csv 					           | 11.4 MB
 github.csv 		        | 256 KB
 muse.csv 					               	   	| 124 KB
 uscis.csv 					               	   	| 3.63 MB
 job_database.csv 					               	   	| 13 MB


## Column information of the final merge file
Columns | Definition |
 --- | --- |
 company  							                   |Name of the company
 title 					           |Job Title
 location 		        |Location of the posting
 link  					               	   	|Link to apply
 job_posted							                	      |Date posted 
 description  					                	|Job description 
 catergory 								                     |Job Category
 source 					           |Source from whether the job was fetched
 initial_approvals_2019 			       |H-1B petitions with “New employment” or “New concurrent employment” whose first decision is an approval.
 initial_denial_2019 							                  |H-1B petitions with “New employment” or “New concurrent employment” whose first decision is an denial.
 continuing_approvals_2019							                      	|H-1B petitions with anything other than “New employment” or “New concurrent employment” whose first decision is approval.
 continuing_denials_2019					                	|H-1B petitions with anything other than “New employment” or “New concurrent employment” whose first decision is denial.

## **How to Access the above final_job data file** <br>
searchForJobs(title, location, job_posted) | Function for the User to query for jobs.Filters availabe: title: Job title, location: Job location, and job_posted: Date of job posting will return all the records from that date to current date.<br>

## **Data Directories** <br>
1. './data/ranstad/randstad.csv' | Randstad Job's Data
2. './data/adzuna/adzuna.csv' | Adzuna Job's Data
3. './data/github/github.csv' | Github Job's Data
4. './data/muse/muse.csv' | The Muse Job's Data
5. './data/uscis/uscis.csv' | USCIS HIB Data
6. './data/job_database/job_database.csv' | Final Job Data

## **Who might be interested in this data**

1. Job seekers, majorly international applicants. 
2. Job Board Applications.
3. Educational institutes.
4. Data scientists who want to perform further analysis.  
5. Consultancies. 
6. Goverment agencies to generate various employement related surveys. 

## **Challenges Faced**
1. Since different source different job features, some source which are present in one data source may not be availaable in the other one. <br> 
2. Company name present in jobs data may not match with USCIS Employer name since it may have been registered with a different name. We are using 'contains()' function. <br>
The quality of the data can be improved by web scrapping from multiple sources of websites. But most of the sites don’t allow scrapping. We need to get permission. <br> 
3. Many APIs have rate limit which limits the data we can access from that site.<br> 



