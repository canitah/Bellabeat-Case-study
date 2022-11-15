# Bellabeat-Case-study
##### Author: Qanitah Nizam

##### Date: November 15, 2022

##### [Tableau Dashboard](https://public.tableau.com/views/BellabeatFitnessDataAnalysis_16685263244340/Sleepanalysis?:language=en-US&:display_count=n&:origin=viz_share_link)

## Project Background
Bellabeat has grown rapidly and quickly positioned itself as a tech-driven wellness company for women. The company has 5 focus products: bellabeat app, leaf, time, spring and bellabeat membership. Bellabeat is a successful small company, but they have the potential to become a larger player in the global smart device market. Our team have been asked to analyze smart device data to gain insight into how consumers are using their smart devices. The insights we discover will then help guide marketing strategy for the company. 

## Objective
To grow and become a larger player in smart device industry.

## Business Task
1. What are some trends in smart device usage?
2. How could these trends could apply to bellabeat consumers?
3. How could these trends help influence bellabeat marketing strategy?

## Dataset
- Data Source: 33 participants FitBit Fitness Tracker Data from Mobius: https://www.kaggle.com/arashnic/fitbit
- The dataset has 18 CSV.
- Only 30 user data is available. The central limit theorem general rule of n≥30 applies.
- The data has limitations: 
           a) Outdated
           b) very few records

## Data Cleaning
1. Loaded Dataset to R
2. Split Date & Time into separate columns
3. Removed duplicate and null values
4. Corrected data types

## Useful Insights

### Sleep Analysis
By analyzing the sleep data of 24 users it turned out that on average a person sleeps for 7 hours per day whereas, the average bed time is 7 hours 39 minutes per day.

![image](https://github.com/canitah/Bellabeat-Case-study/blob/main/Insights/Sleep%20Analysis.PNG?raw=true)


On average users went to bed 39 minutes before they fall asleep. Average awake time is calculated by subtracting sleep time from bed time.

![image](https://github.com/canitah/Bellabeat-Case-study/blob/main/Insights/Awake%20Time.PNG?raw=true)


Looking at the visual it's clear that people sleep most on SUNDAY and least on Tuesdays and Thursdays.

![image](https://github.com/canitah/Bellabeat-Case-study/blob/main/Insights/Avg%20Sleep%20time%20weekly.PNG?raw=true)


## Steps Analysis
A/c to health associations on average 10,000 steps is considered healthy. From the fitbit data, we can see that on average users took 7876 steps. 

![image](https://github.com/canitah/Bellabeat-Case-study/blob/main/Insights/Steps.PNG?raw=true)


Users took most steps between 5PM - 7PM and more steps between 12PM - 2PM i.e. early evening and early afternoon.

![image](https://github.com/canitah/Bellabeat-Case-study/blob/main/Insights/hourlySteps.PNG?raw=true)


## People are Out & About on Saturday
From the following graphs we can see that users took most steps on SATURDAY, as well as the least sedentary minutes were spent on Saturday. We can conclude that People are out and about on Saturdays.

![image](https://github.com/canitah/Bellabeat-Case-study/blob/main/Insights/OutnAbout.PNG?raw=true)


## Percent of Active Minutes throughout the day
![image](https://github.com/canitah/Bellabeat-Case-study/blob/main/Insights/PercentofActMins.PNG?raw=true)


## Variation of Weight throughout the Month
As the data is limited we can't notice much variation in users' weight.

![image](https://github.com/canitah/Bellabeat-Case-study/blob/main/Insights/Weight%20Variation.PNG?raw=true)


## Active Min Vs. Calories Burn
Users who were most active burn most calories. We can say that very active minutes is correlated to calories burn. 

![image](https://github.com/canitah/Bellabeat-Case-study/blob/main/Insights/VeryActMin%20vs%20Calories.PNG?raw=true)


# Recommendations
- To reduce awake time in bed by 25% remind the user to go to sleep, when awake time in bed exceeds 20 mins.
- To increase very active minutes by 15% give a workout alert to the user everyday.
- To reach daily steps upto 10K remind users to take a walk every 15 min.
- Introducing the concept of leaderboard in Bellabeat App will create enthusiasm among the users to keep up the momentum.
 





