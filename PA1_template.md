---
title: "Peer Assignment 1"
date: "April 1, 2018"
output:
 html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Loading and preprocessing the data

```{r}
assignment <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.

```{r}
q1 <- assignment %>% group_by(date) %>% summarise(total_steps = sum(steps))
```

Make a histogram of the total number of steps taken each day

```{r echo = T}
ggplot(q1,aes(x=date))+geom_bar(aes(y=total_steps), stat = "identity")+labs(x = "Date", y="Total Steps")
```

Calculate and report the mean and median total number of steps taken per day
```{recho = T}
mean(q1$total_steps, na.rm = T)
median(q1$total_steps, na.rm = T)
```

## What is the average daily activity pattern?
```{r echo = F}
q2 <- assignment %>% group_by(interval) %>% 
                     mutate (average_steps = mean(steps, na.rm = T))
```
1. Make a time sersies plot of the 5 minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```{r echo = T}
ggplot(q2,aes(x = interval, y = average_steps))+geom_line()+labs(x = "Interval", y="Average Steps")
```
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```{r echo = T}
q2$interval[max(q2$average_steps)]
```
## Imputing missing values
1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
```{r echo = T}
sum(is.na(assignment$steps))
```
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5 minutes etc.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```{r echo = F}
newset <- assignment
newset$steps[is.na(newset$steps)] <- 0
```
4. Make a histogram of the total number of steps taken each day. Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimate from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```{r echo = T}
q3 <- newset %>% group_by(date) %>% summarise(total_steps = sum(steps))
q3$date <- as.Date.character(q3$date, "%Y-%m-%d")

ggplot(q3,aes(x=date))+geom_bar(aes(y=total_steps), stat = "identity")+labs(x = "Date", y="Total Steps")

mean(q3$total_steps)
median(q3$total_steps)
```

## Are there differences in activity patterns between weekdays and weekends?
1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```{r echo = F}
q4 <- assignment
```
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using simulated data:

```{r echo = T}
q4$date <- as.Date.character(q4$date, "%Y-%m-%d")
q4$day <- format(q4$date, "%A")
weekend <- c("Saturday", "Sunday")

for (i in 1:nrow(q4))
{
  if (format(q4$date[i], "%A") %in% weekend)
  {
    q4$week[i] <- "Weekend"
  }
  else
  {
    q4$week[i] <- "Weekday"
  }
}

q4$week <- as.factor(q4$week)
q4 <- q4 %>% group_by(interval,week) %>% mutate (average_steps = mean(steps , na.rm = T))

library(lattice)
xyplot(average_steps ~ interval | week, q4, type = "l", xlab= "Interval", ylab="Average Steps", layout = c(1,2))
```
