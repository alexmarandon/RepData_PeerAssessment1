# Reproducible Research: Peer Assessment 1




## Loading and preprocessing the data


```r
Sys.setlocale("LC_TIME", "en_US.UTF-8")
library(ggplot2)
library(dplyr, quietly=TRUE, warn.conflicts = FALSE)

data <- tbl_df(read.csv("activity.csv", colClasses=c("integer", "Date", "integer")))
```

## What is mean total number of steps taken per day?


```r
data_gb_dt <- group_by(data, date)
data_dt <- summarize(data_gb_dt, nb_steps = sum(steps, na.rm=TRUE))

mean <- mean(data_dt$nb_steps, na.rm=TRUE)
median <- median(data_dt$nb_steps, na.rm=TRUE)

hist(data_dt$nb_steps, breaks=20, col="#005555"
     , main="Total number of steps taken each day", xlab = "Sum of steps")
```

![](PA1_template_files/figure-html/nbsteps-1.png) 

## What is the average daily activity pattern?


```r
data_gb_int<- group_by(data, interval)
data_int <- summarize(data_gb_int, avg_steps = mean(steps,na.rm=TRUE))

max_interval <- data_int$interval[which.max(data_int$avg_steps)]

plot(data_int$interval, data_int$avg_steps, type="l", xlab="Intervals", ylab="Average steps", main="Average number of steps across all days")
```

![](PA1_template_files/figure-html/avg_daily_act-1.png) 



## Imputing missing values


```r
data_nafill <- data
data_nafill$steps[is.na(data_nafill$steps)] <- sapply(data_nafill$interval[is.na(data_nafill$steps)], function(x) data_int$avg_steps[data_int$interval==x])

data_gb_dt_nafill <- group_by(data_nafill, date)
data_dt_nafill <- summarize(data_gb_dt_nafill, nb_steps = sum(steps, na.rm=TRUE))

mean_nafill <- mean(data_dt_nafill$nb_steps, na.rm=TRUE)
median_nafill <- median(data_dt_nafill$nb_steps, na.rm=TRUE)

hist(data_dt_nafill$nb_steps, breaks=20, col="#005555"
     , main="Total number of steps taken each day", xlab = "Sum of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-1-1.png) 


## Are there differences in activity patterns between weekdays and weekends?


```r
data_nafill <- mutate(data_nafill, isWE = as.factor(ifelse(weekdays(date, abbreviate=TRUE) %in% c("Sat","Sun"),"Weekend","Weekday")))

data_gb_intwe_nafill<- group_by(data_nafill, interval, isWE)
data_intwe_nafill<- summarize(data_gb_intwe_nafill, avg_steps = mean(steps,na.rm=TRUE))

qplot(interval, avg_steps, data=data_intwe_nafill, geom = "line", xlab = "Intervals", ylab = "Average Steps", main = "Average number of steps split between weekends and weekdays", facets = isWE ~ .)
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 
