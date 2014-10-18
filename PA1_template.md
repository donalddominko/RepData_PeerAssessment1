---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---

## Loading and preprocessing the data


```r
        unzip("activity.zip")
        dfRead = read.csv("activity.csv", sep = ",", header=T)
        
        # Calculating sum
        agStepsSum = aggregate(dfRead$steps, list(date=dfRead$date), sum)
```

## What is mean total number of steps taken per day?

```r
        # Histogram of steps
        hist(agStepsSum$x, col = "green", breaks=10)
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

```r
        # Calculating mean
        mean(agStepsSum$x, na.rm=T)
```

```
## [1] 10766.19
```

```r
        # Calculating median
        inMedian = median(agStepsSum$x, na.rm=T)
        inMedian
```

```
## [1] 10765
```



## What is the average daily activity pattern?

```r
        agStepsMeanInt = aggregate(dfRead$steps, list(dfRead$interval), mean, na.rm=T)
        names(agStepsMeanInt) = c("interval","daymean")

        #dfMaxDayMean = agStepsMeanInt[max(agStepsMeanInt$daymean), ] # One BUG down :)
        dfMaxDayMean = agStepsMeanInt[which.max(agStepsMeanInt$daymean), ]

        # Time to plot
        library(ggplot2)
        ggplot(agStepsMeanInt, aes(x=interval, y=daymean)) + 
                geom_line() + 
                xlab("Interval (5 minute)") + 
                ylab("Average steps per day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

```r
        #agStepsSumInt = aggregate(dfRead$steps, list(dfRead$interval), sum, na.rm=T)
        #colnames(agStepsSumInt) = c("inteval","sumsteps")
```
The interval with maximum number of average steps is interval number 835 and it contains on average maximum number of 206.1698113 steps.

## Imputing missing values

```r
        inNas = sum(is.na(dfRead))
```
The number of missing values is 2304.

For imputing missing values I'll use median of an entire dataset

```r
        # First let's make a copy to work with
        dfReadImpute = dfRead
        
        # Now we set that to the median which was previously calculated
        dfReadImpute[is.na(dfReadImpute$steps),]$steps = inMedian

        # We do the computation again
        agStepsSumImpute = aggregate(dfReadImpute$steps, list(date=dfRead$date), sum)

        # Creating histogram
        hist(agStepsSumImpute$x, col = "green", breaks=20)
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 

```r
        # Printing out mean values before and after
        mean(agStepsSum$x, na.rm=T)
```

```
## [1] 10766.19
```

```r
        mean(agStepsSumImpute$x)
```

```
## [1] 415953.6
```

```r
        median(agStepsSum$x, na.rm=T)
```

```
## [1] 10765
```

```r
        median(agStepsSumImpute$x)
```

```
## [1] 11458
```
Imputing median values into dataset had huge effect on the mean value and some effect 
(about +10%) on median value.

## Are there differences in activity patterns between weekdays and weekends?

