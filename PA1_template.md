---
title: "JHU Reproducible Research - Week 2 Project 1"
author: "Anonymous"
date: "2024-05-16"
output: html_document
---



# Introduction
This documents the Johns Hopkins "Reproducible Research" project assignment 1. The document reflects literate programming using R markdown. The assignment included questions to be answered by analyzing the data starting with the "activity monitoring data" taken from a personal activity monitoring device. 
This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

## Background
The personal activity monitoring device collects data at 5 minute intervals throughout the day. The data consists of two months of data from an anonymous individual collected furing the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

## Objective
There are four top level questions to be answered in this assignment:

1. What is the mean total number of steps taken per day?

2. What is the average daily activity pattern?

3. What is the impact of imputing missing data on the estimates of the total daily number of steps?

4. Are there differences in activity patterns between weekdays and weekends?

# Data Preparation
The data for this assignment is downloaded from the course web site.
* Dataset: Activity monitoring data [52K]
The variables included in this dataset are:
* steps: Number of steps taking in a 5-minute interval (missing values are coded as NA)
* date: The date on which the measurement was taken in YYYY-MM-DD format
* interval: Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset of 3 variables.

## Data Loading and preprocessing the data
### Downloading
1. Load the activity monitoring data 
2. Process/transfrom any necessary data into a format suitable for analysis.
Download the data into the current working directory for the assignment. The file required unzipping to extract the csv file into a working form.

```r
library(dplyr)
```

```
## Warning: package 'dplyr' was built under R version 4.3.3
```

```r
activity_data <- read.csv("activity.csv")
```
Previewing the data structure prior to later manipulation of the NA values:

```r
head(activity_data)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```
# Assignment Questions
## Question 1: What is the mean total number of steps taken per day?
For this part of the assignment, the missing values in the dataset are ignored.  

Remove missing steps data. clean_data has 15264 objects of 3 variables

```r
clean_data <- activity_data[!(is.na(activity_data$steps)),]
```
the steps values to be grouped by date

```r
total_steps_day <- aggregate(steps ~ date, data = clean_data, FUN = sum)
head(total_steps_day)
```

```
##         date steps
## 1 2012-10-02   126
## 2 2012-10-03 11352
## 3 2012-10-04 12116
## 4 2012-10-05 13294
## 5 2012-10-06 15420
## 6 2012-10-07 11015
```
1. Calculate the total number of steps taken per day

```r
steps_day <- total_steps_day$steps
```
2. Make a histogram of the total number of steps taken each day

```r
hist(steps_day,
     main="Histogram of the Total Number of Steps Per Day",
     breaks =10,
     xlab= "Number of Steps",
     ylab= "Frequency",
     col = "blue")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png)

3. Calculate and report the mean and median of the total number of steps taken per day

```r
total_steps_day %>% 
      summarise(mean_steps = mean(steps), 
                median_steps = median(steps))
```

```
##   mean_steps median_steps
## 1   10766.19        10765
```
For the total number of steps taken per day the mean = 10766.19 and the median = 10765.

## Question 2: What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 4.3.3
```

```r
mean_steps_interval <- aggregate(steps ~ interval,clean_data, mean)
 head(mean_steps_interval)
```

```
##   interval     steps
## 1        0 1.7169811
## 2        5 0.3396226
## 3       10 0.1320755
## 4       15 0.1509434
## 5       20 0.0754717
## 6       25 2.0943396
```
mean_steps_interval has 288 objects of 2 variables.
Create a line graph showing steps by 5 minute intervals.

```r
ggplot(mean_steps_interval, aes(x=interval, y=steps)) +
      geom_line(color="blue")+
      labs(x="5-Minute Interval", y="Average Number of Steps Across All Days",
         title="Time Series Plot: Average Steps Per Interval")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png)

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
mean_steps_interval[grep(max(mean_steps_interval$steps),mean_steps_interval$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```
The interval with the maximum number of steps is 835.

##  Question 3: Imputing missing values: What is the impact of imputing missing data on the estimates of the total daily number of steps? 
1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
## test to determine column(s) that contain missing data

```r
anyNA(activity_data$steps)
```

```
## [1] TRUE
```

```r
anyNA(activity_data$date)
```

```
## [1] FALSE
```

```r
anyNA(activity_data$interval)
```

```
## [1] FALSE
```
Only the steps column has NA values
Determine the number of jAN values in the steps column.

```r
num_na <- sum(is.na(activity_data$steps))
print(num_na)
```

```
## [1] 2304
```
The number of NA values in the steps column is 2304.

2. Devise a strategy for filling in all of the missing values in the dataset. 
The mean value for steps averaged across days is use to replace the missing NA values for the full activity_data set in the steps column to produce the imputed data.

```r
mean_steps <- mean(clean_data$steps)
```
The value to replace the NA values in the steps column is ~ 37.38256

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.
## mean_steps value is ~ 37.38256 that is used to replace each NA in the full dataset

```r
activity_data$steps[is.na(activity_data$steps)] <- mean_steps
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 
total daily number of steps?

```r
total_imputed_steps_day <- aggregate(steps ~ date, data = activity_data, FUN = sum)
imputed_steps_day <- total_imputed_steps_day$steps
hist(imputed_steps_day,
     main="Histogram of the Total Number of Steps Per Day (Includes Imputed Values",
     breaks =20,
     xlab= "Number of Steps",
     ylab= "Frequency",
     col = "blue")
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15-1.png)

Calculate the mean and median for total_imputed_steps_day$steps

```r
mean(total_imputed_steps_day$steps)
```

```
## [1] 10766.19
```

```r
median(total_imputed_steps_day$steps)
```

```
## [1] 10766.19
```

5. Do these values differ from the estimates from the first part of the assignment? 
## both values are 10766.19 for the imputed dataset

```r
mean(total_steps_day$steps)
```

```
## [1] 10766.19
```

```r
median(total_steps_day$steps)
```

```
## [1] 10765
```
For dataset without imputed values the mean = 10766.19 and the median = 10765 (see earlier values)

6. What is the impact of imputing missing data on the estimates of the total daily number of steps?
The mean values for the two datasets is the same (10766.19). The median values of the two datasets are very close (10765 and 10766.19)

In the histogram the frequency increases for the imputed dataset as a result of including the replaced NA values. However, the shape of the two graphs is the same.

## Question 4: Are there differences in activity patterns between weekdays and weekends?
Use the dataset with the filled-in missing values for this part.
1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
Note: function wday assumes week start on Sunday. Day 1 and Day 7 would be weekend days

```r
library(lubridate)
```

```
## Warning: package 'lubridate' was built under R version 4.3.3
```

```r
total_imputed_steps_day <- aggregate(steps ~ date + interval, data = activity_data, FUN = sum)
total_imputed_steps_day$weekday <- wday(total_imputed_steps_day$date)
```
Create a data frame for week day dates and a data frame for weekend days (1, 7).

```r
df_weekends <- total_imputed_steps_day[total_imputed_steps_day$weekday %in% c("1","7"),]
df_weekdays <- total_imputed_steps_day[total_imputed_steps_day$weekday %in% c("2","3","4","5","6"),]
mean_steps_interval_weekdays <- aggregate(steps ~ interval,df_weekdays, mean)
mean_steps_interval_weekends <- aggregate(steps ~ interval,df_weekends, mean)
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).
plot week days and weekend days by interval
set up plotting area
plot week days and weekend days by interval
set up plotting area

```r
par(mfrow =c(2,1), mar=c(4,4,3,3))
## png("plot4", width=800, height=500)
plot(mean_steps_interval_weekends$interval, mean_steps_interval_weekends$steps, type = "l",col = "red",
     xlab="Weekend 5-minute Intervals",ylab="Number of Steps",ylim=c(0, 220))
plot(mean_steps_interval_weekdays$interval, mean_steps_interval_weekdays$steps, type = "l",col = "blue",
     xlab="Weekday 5-minute Intervals",ylab="Number of Steps",ylim=c(0, 220))
```

![plot of chunk unnamed-chunk-20](figure/unnamed-chunk-20-1.png)

```r
## reset plot parameters
par(mfrow = c(1,1))
```
