# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
1. Load the data (i.e. read.csv())
2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
library(plyr)
unzip("activity.zip")
data<-read.csv("activity.csv", header=TRUE)
data$date <-as.Date(data$date)
dim(data)
```

```
## [1] 17568     3
```



## What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.

1. Calculate the total number of steps taken per day
2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day
3. Calculate and report the mean and median of the total number of steps taken per day

```r
steps_per_day<-aggregate(data$steps, by=list(data$date), sum,na.rm=T)
hist(steps_per_day$x, xlab="Steps per day", main="Steps per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

```r
print(mean(steps_per_day$x))
```

```
## [1] 9354.23
```

```r
print(median(steps_per_day$x))
```

```
## [1] 10395
```

## What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
steps_per_interval<-aggregate(data$steps, by=list(data$interval), mean,na.rm=T)
plot(steps_per_interval$Group.1, steps_per_interval$x,type = "l", xlab="5 minutes time interval", ylab="Steps", main="Average number of steps per 5 minutes")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

```r
print(steps_per_interval[which.max(steps_per_interval$x),1])
```

```
## [1] 835
```
## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
print(sum(is.na(data$steps)))
```

```
## [1] 2304
```
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```r
averageSteps<-mean(steps_per_interval$x,na.rm=T)
```
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
data[,1][is.na(data[,1])]<-averageSteps
```
4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
steps_by_day<-aggregate(data$steps, by=list(data$date), sum)
hist(steps_by_day$x, xlab="Steps per day", main="Steps per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 

```r
print(mean(steps_by_day$x))
```

```
## [1] 10766.19
```

```r
print(median(steps_by_day$x))
```

```
## [1] 10766.19
```
Mean and median are now in the same position. Compared to the first results the number of mean and median increases a bit, which is expected.

## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels ??? ???weekday??? and ???weekend??? indicating whether a given date is a weekday or weekend day.

```r
data$weekdays<-weekdays(data$date)
data$weekday = with(data,ifelse(weekdays=="Sunday" | weekdays=="Saturday","weekend", "weekday"))
data$weekday = as.factor(data$weekday)
```
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.1.3
```

```r
steps_per_interval<-aggregate(data$steps, by=list(data$interval), mean,na.rm=T)
ggplot(data, aes(x=data$interval, y=data$steps)) + geom_line() + facet_grid(. ~ weekday) + xlab("Interval") + ylab("Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png) 
