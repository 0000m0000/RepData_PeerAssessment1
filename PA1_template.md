---
title: "Reproducible Research: Peer Assessment 1"

---
### Introduction

This report presents the results of the analysis of data about personal movement using activity monitoring devices, as an example of Reproducible Research based on R markdown and the knitr package.  

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.



### Loading and preprocessing the data

The data for the assignment can be downloaded [from the course website](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip).
The following code can be used to:  
- check if the file is already download and unzipped in the working directory  
- if not, download and unzip the data  
- read the file and load the data into a dataframe


```r
# check that we have the packages that will be needed later
library(data.table)
```

```
## Warning: package 'data.table' was built under R version 3.1.2
```

```
## data.table 1.9.4  For help type: ?data.table
## *** NB: by=.EACHI is now explicit. See README to restore previous behaviour.
```

```r
library(ggplot2) 
```

```
## Warning: package 'ggplot2' was built under R version 3.1.2
```


```r
#check if file was already downloaded, if not download and extract the zip file
if (!file.exists("activity.csv")) {
        print("downloading data")
        fileUrl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
        download.file(fileUrl, destfile = "activity.zip", method = "curl")
}
```

```
## Warning: running command 'curl
## "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip" -o
## "activity.zip"' had status 127
```

```
## Warning in download.file(fileUrl, destfile = "activity.zip", method =
## "curl"): download had nonzero exit status
```

```r
#load the data with read.table function
unzip("activity.zip")
```

```
## Warning in unzip("activity.zip"): error 1 in extracting from zip file
```

```r
print("Loading existing data")
activity_data<- read.table("./activity.csv", header=T, sep=',', na.strings="NA", colClasses=c("numeric", "character", "numeric"))
```

```
## Warning in file(file, "rt"): cannot open file './activity.csv': No such
## file or directory
```

```
## Error in file(file, "rt"): cannot open the connection
```

```r
print("Loading completed")
```

```
## [1] "downloading data"
## [1] "Loading existing data"
## [1] "Loading completed"
```

Following additional steps are needed:
- dates need to be converted into suitable date format


```r
#adjust date format
activity_data$date <- as.Date(activity_data$date, format="%Y-%m-%d")
```

```
## Error in as.Date(activity_data$date, format = "%Y-%m-%d"): object 'activity_data' not found
```

We can use the head() function to have a look at the loaded data that will look as follows:


```
## Error in head(activity_data): object 'activity_data' not found
```


### What is mean total number of steps taken per day?

1. Make a histogram of the total number of steps taken each day
2. Calculate and report the mean and median total number of steps taken per day  

For this part of the assignment, we can ignore the missing values in the data set. First we aggregate the total number of steps by day, then we plot it by day.


```r
tot_steps_day <- aggregate(steps ~ date, data=activity_data, FUN=sum)
```

```
## Error in eval(expr, envir, enclos): object 'activity_data' not found
```

```r
barplot(tot_steps_day$steps, names.arg=tot_steps_day$date, xlab="date", ylab="steps")
```

```
## Error in barplot(tot_steps_day$steps, names.arg = tot_steps_day$date, : object 'tot_steps_day' not found
```

Mean and median value can be calculate as follows:


```r
mean(tot_steps_day$steps,na.rm=TRUE)
```

```
## Error in mean(tot_steps_day$steps, na.rm = TRUE): object 'tot_steps_day' not found
```

```r
median(tot_steps_day$steps,na.rm=TRUE)
```

```
## Error in median(tot_steps_day$steps, na.rm = TRUE): object 'tot_steps_day' not found
```


### What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

In this case we need to aggregate by interval ID, and for each one calculate the average number of steps over the days.


```r
av_steps_interval <- aggregate(steps ~ interval, data=activity_data, FUN=mean)
```

```
## Error in eval(expr, envir, enclos): object 'activity_data' not found
```

```r
plot(av_steps_interval, type="l")
```

```
## Error in plot(av_steps_interval, type = "l"): object 'av_steps_interval' not found
```

The interval ID with the highest average of steps is then calculated with the following:


```r
av_steps_interval$interval[which.max(av_steps_interval$steps)]
```

```
## Error in eval(expr, envir, enclos): object 'av_steps_interval' not found
```

### Imputing missing values

The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

The total number of rows with NAs is given by:


```r
sum(is.na(activity_data))
```

```
## Error in eval(expr, envir, enclos): object 'activity_data' not found
```

Assuming the average for the interval ID is good enough for replacing NAs, we can write a new dataset with NAs replaced:


```r
activity_temp <- merge(activity_data, av_steps_interval, by="interval", suffixes=c("",".y"))
```

```
## Error in merge(activity_data, av_steps_interval, by = "interval", suffixes = c("", : object 'activity_data' not found
```

```r
nas <- is.na(activity_temp$steps)
```

```
## Error in eval(expr, envir, enclos): object 'activity_temp' not found
```

```r
activity_temp$steps[nas] <- activity_temp$steps.y[nas]
```

```
## Error in eval(expr, envir, enclos): object 'activity_temp' not found
```

```r
activity_replaced <- activity_temp[,c(1:3)]
```

```
## Error in eval(expr, envir, enclos): object 'activity_temp' not found
```

For the modified dataset we can calculate mean and median and the difference from the original dataset in %.


```r
tot_steps_day2 <- aggregate(steps ~ date, data=activity_data, FUN=sum)
```

```
## Error in eval(expr, envir, enclos): object 'activity_data' not found
```

```r
barplot(tot_steps_day2$steps, names.arg=tot_steps_day2$date, xlab="date", ylab="steps")
```

```
## Error in barplot(tot_steps_day2$steps, names.arg = tot_steps_day2$date, : object 'tot_steps_day2' not found
```

```r
mean(tot_steps_day2$steps,na.rm=TRUE)
```

```
## Error in mean(tot_steps_day2$steps, na.rm = TRUE): object 'tot_steps_day2' not found
```

```r
median(tot_steps_day2$steps,na.rm=TRUE)
```

```
## Error in median(tot_steps_day2$steps, na.rm = TRUE): object 'tot_steps_day2' not found
```

```r
diff_mean<-mean(tot_steps_day2$steps,na.rm=TRUE)/mean(tot_steps_day$steps,na.rm=TRUE)-1
```

```
## Error in mean(tot_steps_day2$steps, na.rm = TRUE): object 'tot_steps_day2' not found
```

```r
diff_mean<-median(tot_steps_day2$steps,na.rm=TRUE)/median(tot_steps_day$steps,na.rm=TRUE)-1
```

```
## Error in median(tot_steps_day2$steps, na.rm = TRUE): object 'tot_steps_day2' not found
```

### Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

    Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

    Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).
    
Add a factor column for whether a day is a weekday or weekend.



```r
daytype <- function(date) {
    if (weekdays(date) %in% c("Saturday", "Sunday")) {
        return("weekend")
    } else {
        return("weekday")
    }
}
activity_replaced$daytype <- as.factor(sapply(activity_replaced$date, daytype))
```

```
## Error in lapply(X = X, FUN = FUN, ...): object 'activity_replaced' not found
```
   
Then can be plotted.


```r
par(mfrow=c(2,1))
for (d_type in c("weekend", "weekday")) {
    steps_type <- aggregate(steps ~ interval,
                            data=activity_replaced,
                            subset=activity_replaced$daytype==d_type,
                            FUN=mean)
    plot(steps_type, type="l", main=d_type)
}
```

```
## Error in eval(expr, envir, enclos): object 'activity_replaced' not found
```

