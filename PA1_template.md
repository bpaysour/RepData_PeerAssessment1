---
title: "Coursera Project for Human Activity - Steps Data"
author: "R. Paysour"
date: "May 11, 2019"
output: html_document
---

Load the required libraries and set the working directory for accessing the data.


```r
options(stringsAsFactors = FALSE);
library(ggplot2);
```

```
## Warning: package 'ggplot2' was built under R version 3.5.3
```

```
## Use suppressPackageStartupMessages() to eliminate package startup
## messages.
```

```r
setwd("~/JHU_DataScienceCourse/CourseProjects/Course5/Week2");
echo = TRUE;
```

Read in the file with the human activity data, and check the extent to which each of the fields contains missing values.  Then create a new variable that indicates the appropriate day of the week for each date and interval.


```r
data1 <- read.csv("activity.csv");
table(is.na(data1$steps));
```

```
## 
## FALSE  TRUE 
## 15264  2304
```

```r
table(is.na(data1$date));
```

```
## 
## FALSE 
## 17568
```

```r
table(is.na(data1$interval));
```

```
## 
## FALSE 
## 17568
```

```r
data1$date <- as.Date(data1$date);
data1$dayname <- weekdays(data1$date, abbreviate = TRUE);
echo = TRUE;
```

Calculate the total number of steps taken each day, produce a frequency distribution (histogram) of the	daily number of steps, and compute the mean and median number of daily steps.


```r
daySteps <- aggregate(data1$steps, by = list(Date = data1$date), FUN = "sum");
hist(daySteps$x, main = "Histogram of Daily Number of Steps", xlab = "Daily Steps", plot = TRUE);
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)

```r
print(c("The mean number of daily steps is", mean(daySteps$x, na.rm = TRUE)), quote = FALSE);
```

```
## [1] The mean number of daily steps is 10766.1886792453
```

```r
print(c("The median number of daily steps is", median(daySteps$x, na.rm = TRUE)), quote = FALSE);
```

```
## [1] The median number of daily steps is 10765
```

```r
echo = TRUE;
```

Calculate the average number of steps taken during each 5 minute interval, and plot the time series of these values. Then determine and report the interval with the highest average number of steps.


```r
intervalSteps <- aggregate(data1$steps, by = list(Interval = data1$interval), FUN = "mean", 
	na.rm = TRUE);
plot(intervalSteps$Interval, intervalSteps$x, type = "l", 
	col = "black", main = "Mean Steps by Time Interval", xlab = "Time Interval",
	ylab = "Mean Number of Steps", cex.axis = 0.8, cex.lab = 0.8);
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)

```r
maxMeanSteps <- max(intervalSteps$x);
print(c("The time interval with the highest average number of steps is", 
	intervalSteps$Interval[intervalSteps$x == maxMeanSteps]), quote = FALSE);
```

```
## [1] The time interval with the highest average number of steps is
## [2] 835
```

```r
echo = TRUE;
```

Compute and report the number of rows in the data set that contain missing values.  For all observations with missing numbers of steps, impute a steps value from the mean number of steps of all observations that occur during the same interval.  Create a new data set that contains these imputed values for all of the missing cases, and make a frequency distribution of the daily number of steps in the new data set, then compute the mean and median number of daily steps using the imputed values.


```r
missingRows <- is.na(data1[,1]) | is.na(data1[,2]) | is.na(data1[,3]);
numMissing <- sum(missingRows);
print(c("The number of rows in the data set with missing values is", numMissing), quote = FALSE);
```

```
## [1] The number of rows in the data set with missing values is
## [2] 2304
```

```r
allsteps <- integer();
numobs <- dim(data1)[1];
for (i in 1:numobs) {
	curr_int <- data1$interval[i];
	if (is.na(data1$steps[i])) {
		allsteps[i] <- mean(data1$steps[data1$interval == curr_int], na.rm = TRUE);
		}
	else {
		allsteps[i] <- data1$steps[i];
		}
	rm(curr_int);
	}
data2 <- data1;
data2$steps <- allsteps;
daySteps2 <- aggregate(data2$steps, by = list(Date = data2$date), FUN = "sum");
hist(daySteps2$x, main = "Histogram of Daily Number of Steps", xlab = "Daily Steps", plot = TRUE);
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)

```r
print(c("The mean number of daily steps (with imputed values) is",
	mean(daySteps2$x, na.rm = TRUE)), quote = FALSE);
```

```
## [1] The mean number of daily steps (with imputed values) is
## [2] 10766.1886792453
```

```r
print(c("The median number of daily steps (with imputed values) is",
	median(daySteps2$x, na.rm = TRUE)), quote = FALSE);
```

```
## [1] The median number of daily steps (with imputed values) is
## [2] 10766.1886792453
```

```r
echo = TRUE;
```

Create a factor variable in the data set with imputed values, indicating whether the day is during the	work week or during the weekend.  Next plot charts showing the time series of average steps taken throughout a day, first only for work days, then only for weekends.


```r
data2$daytype <- factor(data2$dayname, levels = c("Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"),
	labels = c("Work", "Work", "Work", "Work", "Work", "Weekend", "Weekend"));	
intervalSteps2 <- aggregate(data2$steps, by = list(Interval = data2$interval, dayType = data2$daytype),
	FUN = "mean", na.rm = TRUE);
qplot(Interval, x, data = intervalSteps2, facets = . ~ dayType, geom = "line", 
	ylab = "Mean Number of Steps");
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png)

```r
echo = TRUE;
```
This completes the requirements for the assignment.
