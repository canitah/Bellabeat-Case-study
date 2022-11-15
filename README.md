# Bellabeat-Case-study

title: "Bellabeat Case Study"
author: "Qanitah Nizam"
date: "2022-10-28"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = FALSE)
```

```{r}
knitr::opts_chunk$set(error = TRUE)
```


## Install & Load Packages

```{r}
install.packages("tidyverse",  repos = "http://cran.us.r-project.org") 
install.packages("ggplot2",  repos = "http://cran.us.r-project.org")
install.packages("plotly",  repos = "http://cran.us.r-project.org")
install.packages("dplyr",  repos = "http://cran.us.r-project.org")
install.packages("skimr",  repos = "http://cran.us.r-project.org")

library(tidyverse) #wrangle data
library(ggplot2) #graphs
library(plotly) #pie chart
library(dplyr) #clean data
library(lubridate) #datetime
library(readr) #save csv
library(skimr) #get summary

```

## Importing Data sets

```{r}
daily_activity <- read_csv("dailyActivity_merged.csv")

sleep_day <- read.csv("sleepDay_merged.csv")

weight_log <- read.csv("weightLogInfo_merged.csv")

hourly_steps <- read.csv("hourlySteps_merged.csv")
```

## Examining Data


```{r}
dim(daily_activity)
dim(sleep_day)
dim(weight_log)
```

## Checking for Null & Duplicate values


```{r}
sum(is.na(daily_activity))
sum(is.na(sleep_day))
sum(is.na(weight_log))
colSums(is.na(weight_log))

# dropping column with null values
weight_log <- weight_log[-5]

head(weight_log)

# removing duplicates
sum(duplicated(daily_activity))

sum(duplicated(sleep_day))
sleep_day <- sleep_day[!duplicated(sleep_day), ]

sum(duplicated(weight_log))
```

## Extracting days from date


```{r}
daily_activity <- daily_activity %>% 
  mutate( Weekday = weekdays(as.Date(ActivityDate, "%m/%d/%Y")))
str(daily_activity)
```

## Unique users


```{r}
n_distinct(daily_activity$Id)
n_distinct(sleep_day$Id)
n_distinct(weight_log$Id)
```
## Manual Recordings


```{r}
IsManual <- weight_log %>% 
  filter(IsManualReport == "TRUE") %>% 
  group_by(Id) %>% 
  summarise("Manual Weight Report"=n()) %>%
  distinct()

IsManual
```
## Merging Data Sets


```{r}
#Merging data
merged_activity_sleep <- merge(daily_activity, sleep_day, by.x = 'Id', by.y = 'Id')

merged_data <- merge(merged_activity_sleep, weight_log, by.x = 'Id', by.y = 'Id')
```

```{r}
#Order from Monday to Sunday for plot later
merged_data$Weekday <- factor(merged_data$Weekday, 
                              levels= c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", 
                                        "Saturday", "Sunday"))

head(merged_data[order(merged_data$Weekday), ])

#Save CSV for Tableau presentation
write_csv(merged_data, "merged_data.csv")
```


## Data Recorded during the week

### From the bar graph the data is greatest from Tuesday to Thursday. Monday and Friday are both weekdays, why isn't the data recordings as much as the other weekdays?


```{r}
ggplot(data = merged_data) +
  geom_bar(mapping = aes(x = Weekday), fill = "steelblue") +
  labs(title = "Data Recording During the Week",
       x= "Weekday",
       y= "Count")
```

## Total Minutes Asleep during the week


```{r}
ggplot(data=merged_data, aes(x=Weekday, y=TotalMinutesAsleep, fill=Weekday))+ 
  geom_bar(stat="identity", fill="steelblue")+
  labs(title="Total Minutes Asleep During the Week", y="Total Minutes Asleep")
```

## Summary

```{r}
merged_data %>%
  select(Weekday,
                TotalSteps,
                TotalDistance,
                VeryActiveMinutes,
                FairlyActiveMinutes,
                LightlyActiveMinutes,
                SedentaryMinutes,
                Calories,
                TotalMinutesAsleep,
                TotalTimeInBed,
                WeightPounds
  ) %>%
  summary()
```


## Active Minutes Analysis

### Percentage of active minutes in the four categories: very active, fairly active, lightly active and sedentary. From the pie chart, we can see that most users spent 81.3% of their daily activity in sedentary minutes and only 1.74% in very active minutes.


```{r}
Sum_active_min <- daily_activity %>% 
  summarise(sum_sedentary = sum(SedentaryMinutes),
            sum_fairly = sum(FairlyActiveMinutes),
            sum_active = sum(VeryActiveMinutes),
            sum_lightly = sum(LightlyActiveMinutes),
            total_active_min = sum(sum_sedentary,sum_fairly,sum_active,sum_lightly))

sedentary_percentage <- Sum_active_min$sum_sedentary / Sum_active_min$total_active_min * 100
lightly_percentage <- Sum_active_min$sum_lightly / Sum_active_min$total_active_min * 100
fairly_percentage <- Sum_active_min$sum_fairly/ Sum_active_min$total_active_min * 100
active_percentage <- Sum_active_min$sum_active / Sum_active_min$total_active_min * 100
  
percentage <- data.frame(
  level =c("Sedentary", "Lightly", "Fairly", "Very Active"),
  minutes=c(sedentary_percentage,lightly_percentage,fairly_percentage,active_percentage)
)

plot_ly(percentage, labels = ~level, values = ~minutes, type = 'pie',textposition = 'outside',textinfo = 'label+percent') %>%
  layout(title = 'Percentage of Active Minutes')
```

### A/c to American Health Association daily goal of fairly_active_minutes = 21.4 or 10.7 minutes of Very_Active_Minutes.


```{r}
active_users <- daily_activity %>%
  filter(FairlyActiveMinutes >= 21.4 | VeryActiveMinutes >= 10.7) %>% 
  group_by(Id) %>% 
  count(Id)

head(active_users)
```


## Noticeable day

### The bar graph shows that there is a jump on Saturday user spent LESS time in sedentary minutes and take MORE steps. Users are out and about on Saturday.


```{r}
ggplot(data = merged_data, aes(x=Weekday, y=SedentaryMinutes, fill=Weekday))+ 
  geom_bar(stat="identity", fill="steelblue")+
  labs(title="Less Sedentary Minutes on Saturday", y="Sedentary Minutes")

```

```{r}
ggplot(data=merged_data, aes(x=Weekday, y=TotalSteps, fill=Weekday))+ 
  geom_bar(stat="identity", fill="steelblue")+
  labs(title="More Steps on Saturday", y="Total Steps")
```

## Hourly Steps

### From 5PM to 7PM the users take the most steps.


```{r}
hourly_steps$ActivityHour=as.POSIXct(hourly_steps$ActivityHour,format="%m/%d/%Y %I:%M:%S %p")
hourly_steps$Hour <-  format(hourly_steps$ActivityHour,format= "%H")
head(hourly_steps)

ggplot(data=hourly_steps, aes(x=Hour, y=StepTotal, fill=Hour))+
  geom_bar(stat="identity")+
  labs(title="Hourly Steps")
```

## Weekly steps

### Tuesday and Saturdays the users take the most steps.


```{r}
ggplot(data=merged_data, aes(x=Weekday, y=TotalSteps, fill=Weekday))+ 
  geom_bar(stat="identity")+
  labs(title = "Weekly Steps",
  y = "Total Steps")
```

## Total Steps Vs. Calories

### Here we see that some users who are sedentary, take minimal steps, but still able to burn over 1500 to 2500 calories compare to users who are more active, take more steps, but still burn similar calories.


```{r}
ggplot(data = daily_activity, aes(x=TotalSteps, y = Calories, color=SedentaryMinutes))+ 
  geom_point()+ 
  stat_smooth(method=lm)+
  scale_color_gradient(low="steelblue", high="orange")+
  labs(title = "Total Steps Vs. Calories")
```

## Steps Vs. Active Minutes

### Comparing the four active levels to the total steps, we see most data is concentrated on users who take about 5000 to 15000 steps a day. These users spent an average between 8 to 13 hours in sedentary, 5 hours in lightly active, and 1 to 2 hour for fairly and very active.


```{r}
active_minutes_vs_steps <- ggplot(data = merged_data) + 
  geom_point(mapping=aes(x=TotalSteps, y=FairlyActiveMinutes), color = "maroon", alpha = 1/3) +
  geom_smooth(method = loess,formula =y ~ x, mapping=aes(x=TotalSteps, y=FairlyActiveMinutes, color=FairlyActiveMinutes), color = "maroon", se = FALSE) +
  
  geom_point(mapping=aes(x=TotalSteps, y=VeryActiveMinutes), color = "forestgreen", alpha = 1/3) +
  geom_smooth(method = loess,formula =y ~ x,mapping=aes(x=TotalSteps, y=VeryActiveMinutes, color=VeryActiveMinutes), color = "forestgreen", se = FALSE) +
  
  geom_point(mapping=aes(x=TotalSteps, y=LightlyActiveMinutes), color = "orange", alpha = 1/3) +
  geom_smooth(method = loess,formula =y ~ x,mapping=aes(x=TotalSteps, y=LightlyActiveMinutes, color=LightlyActiveMinutes), color = "orange", se = FALSE) +
  
   geom_point(mapping=aes(x=TotalSteps, y=SedentaryMinutes), color = "steelblue", alpha = 1/3) +
  geom_smooth(method = loess,formula =y ~ x,mapping=aes(x=TotalSteps, y=SedentaryMinutes, color=SedentaryMinutes), color = "steelblue", se = FALSE) +
  
  annotate("text", x=35000, y=150, label="Very Active", color="black", size=3)+
  annotate("text", x=35000, y=50, label="Fairly Active", color="black", size=3)+
  annotate("text", x=35000, y=1350, label="Sedentary", color="black", size=3)+
  annotate("text", x=35000, y=380, label="Lightly  Active", color="black", size=3)+
  labs(x = "Total Steps", y = "Active Minutes", title="Steps vs Active Minutes")

active_minutes_vs_steps
```

## Sleep Vs. Calories Burnt

### Do people sleep more burn less calories? Plotting the two variables we can see that there is not much a correlation.


```{r}
ggplot(data=merged_data, aes(x=TotalMinutesAsleep, y = Calories, color=TotalMinutesAsleep))+ 
  geom_point()+ 
  labs(title="Total Minutes Asleep vs Calories")+
  xlab("Total Minutes Alseep")+
  stat_smooth(method=lm)+
  scale_color_gradient(low="orange", high="steelblue")
```
