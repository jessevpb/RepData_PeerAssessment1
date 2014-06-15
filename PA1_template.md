Title
========================================================

This is an R Markdown document. Markdown is a simple formatting syntax for authoring web pages (click the **MD** toolbar button for help on Markdown).

When you click the **Knit HTML** button a web page will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:


```r
download.file("https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip", 
    "steps data.zip", method = "curl")
```

Make sure your working directory is set to the right folder.


```r
setwd("~/Documents/Jesse's Docs/R Data")

activity <- read.csv("activity.csv")

StepsPerDay <- tapply(activity$steps, activity$date, sum)

mean(StepsPerDay[!is.na(StepsPerDay)])
```

```
## [1] 10766
```

```r

median(StepsPerDay[!is.na(StepsPerDay)])
```

```
## [1] 10765
```

```r

hist(StepsPerDay, breaks = 20, main = "Steps Per Day")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 


This chunk calculates the mean and median number of steps per day. The histogram shows their frequency.



```r
activity.no.NA <- activity[!is.na(activity$steps), ]

StepsPerInterval <- tapply(activity.no.NA$steps, activity.no.NA$interval, mean)

plot(levels(factor(activity$interval)), StepsPerInterval, type = "l", main = "Steps Throughout the Day", 
    xlab = "time", ylab = "steps")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 


Next, we calculate steps per interval and graph them linearly.


```r

which.max(StepsPerInterval)
```

```
## 835 
## 104
```


On average, our sample take the most steps between 8:35 and 8:40 am.


```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

```r

activity2 <- activity

NAIndex <- c(1:length(activity2$steps))[is.na(activity2$steps)]

for (i in NAIndex) {
    activity2$steps[i] <- StepsPerInterval[paste(activity2$interval[i])]
}
```


Replace the NAs with the average for the appropriate interval.


```r
StepsPerDay2 <- tapply(activity2$steps, activity2$date, sum)

mean(StepsPerDay2)
```

```
## [1] 10766
```

```r

median(StepsPerDay2)
```

```
## [1] 10766
```


Calculate the mean and median again. Mean is unchanged, median has gone up.


```r
days <- weekdays(strptime(activity$date, "%Y-%m-%d"))

weekdays <- character()

for (i in 1:length(days)) {
    if (days[i] == "Sunday" | days[i] == "Saturday") {
        weekdays[i] <- "weekend"
    } else {
        weekdays[i] <- "weekday"
    }
}

activity2[, 4] <- factor(weekdays)

colnames(activity2)[4] <- "weekdays"
```


Adding a "weekdays" factor.


```r

weekdayActivity <- split(activity2, activity2$weekdays)[[1]]

weekendActivity <- split(activity2, activity2$weekdays)[[2]]

weStpsInt <- tapply(weekendActivity$steps, weekendActivity$interval, mean)

wdStpsInt <- tapply(weekdayActivity$steps, weekdayActivity$interval, mean)

IntMeans <- cbind(wdStpsInt, weStpsInt, levels(activity2$interval))
```


Calculating means for weekend and weekday intervals and storing them in one data frame.
