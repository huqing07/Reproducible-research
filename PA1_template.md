# PA1_template.Rmd


Peer Assessment 1 in "Reproducible Research"
=================================================

This assignment is to analyze the data from a personal activity monitoring device.

**Loading the data**


```r
setwd("~/Desktop")
activity<-read.csv('./activity.csv', header=TRUE)
```

**Total number of steps taken per day**

1. Histogram of the total number of steps taken each day


```r
steps.by.day<-tapply(activity$steps, activity$date, sum, na.rm=TRUE)
steps.by.day.1<-data.frame(date=row.names(steps.by.day), steps=steps.by.day)
hist(steps.by.day.1$steps, xlab='Daily steps', main='Histogram of total number of steps taken each day')
```

![](./PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

2. Mean and median of total number of steps taken per day


```r
mean.total.steps.by.day<-mean(steps.by.day)
mean.total.steps.by.day
```

```
## [1] 9354.23
```

```r
median.total.steps.by.day<-median(steps.by.day)
median.total.steps.by.day
```

```
## [1] 10395
```

**Average daily activity pattern**

1. Time series plot of the 5-minute interval (x-axis) and the average number of steps taken, average across all days (y-axis)


```r
avg.steps<-tapply(activity$steps, activity$interval, mean, na.rm=TRUE)
plot(row.names(avg.steps), avg.steps, type = 'l', xlab='5-minute interval', ylab = 'average number of steps taken')
```

![](./PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

2. It is "835" 5-minute interval that contains the maximum number of steps on average across all the days in the dataset


```r
avg.steps[which(avg.steps==max(avg.steps))]
```

```
##      835 
## 206.1698
```


**Imputing missing values**

1. Total number of missing values in the dataset (the total number of rows with NAs) = 2304


```r
length(which(is.na(activity$steps)))
```

```
## [1] 2304
```

2. Filling in all of the missing values in the dataset by the mean for that 5-minute interval


```r
uni.interval<-unique(activity$interval[which(is.na(activity$steps))])
activity1<-activity
for (i in uni.interval) {
activity1$steps[which(is.na(activity$steps)&activity$interval==i)]<-avg.steps[as.numeric(row.names(avg.steps))==i]
}
```

3. "activity1" above is a new dataset that is equal to the original dataset but with the missing data filled in

4. Histogram of the total number of steps taken per day and the mean and median of total number of steps taken per day. In contrast to the original dataset, imputing missing data has shifted up the mean and median of total number of steps taken per day.


```r
steps.by.day.imput<-tapply(activity1$steps, activity1$date, sum, na.rm=TRUE)

steps.by.day.1.imput<-data.frame(date=row.names(steps.by.day.imput), steps=steps.by.day.imput)

hist(steps.by.day.1.imput$steps, xlab='Daily steps', main= 'with missing values filled in')
```

![](./PA1_template_files/figure-html/unnamed-chunk-8-1.png) 

```r
mean(steps.by.day.imput)
```

```
## [1] 10766.19
```

```r
median(steps.by.day.imput)
```

```
## [1] 10766.19
```

**Activity patterns between weekdays and wenkends**

1. Create a new factor variable "date.type" indicating whether a given date is a weekday or weekend day


```r
activity1$date.type<-factor('weekday', levels=c('weekday', 'weekend'))
activity1$date.type[weekdays(as.Date(activity$date))%in%c("Saturday","Sunday")]<-'weekend'
```

2. A panel plot containing a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The panel plot shows weekday's average number of steps is fewer from 0 to 1200 5-minute interval but more from 2000 to 2330.


```r
library(lattice)
avg.steps.by.datetype<-tapply(activity1$steps, list(activity1$interval, activity1$date.type), mean)
avg.steps.by.datetype.1<-data.frame(interval=row.names(avg.steps.by.datetype), avg.steps=as.vector(avg.steps.by.datetype), date.type=rep(c('weekday','weekend'), each=nrow(avg.steps.by.datetype)))

xyplot(avg.steps~interval|date.type, data=avg.steps.by.datetype.1, layout=c(1,2), type='a', ylab='Number of steps', scales=list(x=list(at=seq(1, 277, 12), labels=c('0', '100', '200', '300', '400', '500', '600', '700', '800', '900', '1000', '1100', '1200', '1300', '1400', '1500', '1600', '1700', '1800', '1900', '2000', '2100', '2200', '2300'))))
```

![](./PA1_template_files/figure-html/unnamed-chunk-10-1.png) 

