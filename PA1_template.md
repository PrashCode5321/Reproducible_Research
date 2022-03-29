## Introduction

It is now possible to collect a large amount of data about personal
movement using activity monitoring devices such as a Fitbit, Nike
Fuelband, or Jawbone Up. These type of devices are part of the
“quantified self” movement – a group of enthusiasts who take
measurements about themselves regularly to improve their health, to find
patterns in their behavior, or because they are tech geeks. But these
data remain under-utilized both because the raw data are hard to obtain
and there is a lack of statistical methods and software for processing
and interpreting the data.

This assignment makes use of data from a personal activity monitoring
device. This device collects data at 5 minute intervals through out the
day. The data consists of two months of data from an anonymous
individual collected during the months of October and November, 2012 and
include the number of steps taken in 5 minute intervals each day.

The data for this assignment can be downloaded from the course web site:

-   Dataset: [Activity monitoring
    data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip)

The variables included in this dataset are:

steps: Number of steps taking in a 5-minute interval (missing values are
coded as 𝙽𝙰) </br> date: The date on which the measurement was taken in
YYYY-MM-DD format </br> interval: Identifier for the 5-minute interval
in which measurement was taken </br> The dataset is stored in a
comma-separated-value (CSV) file and there are a total of 17,568
observations in this dataset.

## Loading and preprocessing the data

Unzip data to obtain a csv file.

    library(dplyr)

    ## Warning: package 'dplyr' was built under R version 4.1.3

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    library(ggplot2)
    fileUrl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
    download.file(fileUrl, destfile = paste0(getwd(), '/repdata%2Fdata%2Factivity.zip'), method = "curl")
    unzip("repdata%2Fdata%2Factivity.zip",exdir = "data")

Reading csv data.

    df <- read.csv("activity.csv")
    head(df, 10)

    ##    steps       date interval
    ## 1     NA 2012-10-01        0
    ## 2     NA 2012-10-01        5
    ## 3     NA 2012-10-01       10
    ## 4     NA 2012-10-01       15
    ## 5     NA 2012-10-01       20
    ## 6     NA 2012-10-01       25
    ## 7     NA 2012-10-01       30
    ## 8     NA 2012-10-01       35
    ## 9     NA 2012-10-01       40
    ## 10    NA 2012-10-01       45

## Total number of steps taken per day

Transforming the data into a suitable format and calculating number of
steps taken per day.

    edit_df <- df %>% group_by(date) %>% summarise(steps = sum(steps))
    head(edit_df, 10)

    ## # A tibble: 10 x 2
    ##    date       steps
    ##    <chr>      <int>
    ##  1 2012-10-01    NA
    ##  2 2012-10-02   126
    ##  3 2012-10-03 11352
    ##  4 2012-10-04 12116
    ##  5 2012-10-05 13294
    ##  6 2012-10-06 15420
    ##  7 2012-10-07 11015
    ##  8 2012-10-08    NA
    ##  9 2012-10-09 12811
    ## 10 2012-10-10  9900

Making a histogram of the total number of steps taken each day.

    ggplot(edit_df, aes(x = steps)) + 
      geom_histogram(fill = "red", binwidth = 1000) + 
      labs(title = "Total Number of Steps per day", x = "Steps", y = "Counts")

    ## Warning: Removed 8 rows containing non-finite values (stat_bin).

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-4-1.png)

Calculating and reporting the mean of the total number of steps taken
per day

    mean(edit_df$steps, na.rm = TRUE)

    ## [1] 10766.19

Calculating and reporting the median of the total number of steps taken
per day

    median(edit_df$steps, na.rm = TRUE)

    ## [1] 10765

## Average daily activity pattern

Making a time series plot (i.e. `𝚝𝚢𝚙𝚎 = "𝚕"`) of the 5-minute interval
(x-axis) and the average number of steps taken, averaged across all days
(y-axis)

    df_interval <- df %>% group_by(interval) %>% summarise(steps = mean(steps, na.rm = TRUE))

    ggplot(df_interval , aes(x = interval , y = steps)) + 
      geom_line(color="blue", size=1) + 
      labs(title = "Time series plot of the average number of steps taken", x = "Interval", y = "Avg. Steps per day")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-7-1.png)

Finding the 5-minute interval which contains the maximum number of steps

    df_interval[which(df_interval$steps == max(df_interval$steps)), ]$interval

    ## [1] 835

## Imputing missing values

Calculating the total number of missing values in the dataset:

    nrow(df[is.na(df$steps), ])

    ## [1] 2304

Choosing to replace all missing values with mean of the `steps` column

    # Filling in missing values with mean of dataset. 
    df[is.na(df$steps), "steps"] <- mean(df$steps, na.rm = TRUE)

Creating a new dataset that is equal to the original dataset but with
the missing data filled in.

    write.csv(x = df, file = "newdata.csv", quote = FALSE)

    # total number of steps taken per day
    edit_df2 <- df %>% group_by(date) %>% summarise(steps = sum(steps))

Calculating and reporting the mean of the total number of steps taken
per day

    mean(edit_df2$steps, na.rm = TRUE)

    ## [1] 10766.19

Calculating and reporting the median of the total number of steps taken
per day

    median(edit_df2$steps, na.rm = TRUE)

    ## [1] 10766.19

Summarizing Means and Medians to show how much these values differ
before and after imputation:

    # mean and median total number of steps taken per day
    def <- matrix(c(mean(edit_df2$steps, na.rm = TRUE), median(edit_df2$steps, na.rm = TRUE), mean(edit_df$steps, na.rm = TRUE), median(edit_df$steps, na.rm = TRUE)),ncol=2,byrow=TRUE)

    colnames(def) <- c("Mean","Median")
    rownames(def) <- c("Without Na", "With Na")

    def

    ##                Mean   Median
    ## Without Na 10766.19 10766.19
    ## With Na    10766.19 10765.00

Making a histogram of the total number of steps taken each day

    ggplot(edit_df2, aes(x = steps)) + geom_histogram(fill = "red", binwidth = 1000) + labs(title = "Total Number of Steps per day", x = "Steps", y = "Counts")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-16-1.png)

Comparing this histogram with the histogram made before imputation,
there is a difference noticed in both the plots.  
The histogram made after imputation has more counts than the one made
before imputation.

## Differences in activity patterns between weekdays and weekends

Creating a new factor variable in the dataset with two levels –
`weekday` and `weekend` indicating whether a given date is a weekday or
weekend day.

    # Just recreating activityDT from scratch then making the new factor variable. (No need to, just want to be clear on what the entire process is.) 
    df[ , "date"] <- as.POSIXct(df$date, format = "%Y-%m-%d")
    df[, "Day of the Week"] <- weekdays(x = df$date)

    days <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")
    df[, "check"] <- df[, "Day of the Week"] %in% days
    df[df$check == TRUE, "weekday_or_weekend"] <- "weekday"
    df[df$check == FALSE, "weekday_or_weekend"] <- "weekend"

    head(df)

    ##     steps       date interval Day of the Week check weekday_or_weekend
    ## 1 37.3826 2012-10-01        0          Monday  TRUE            weekday
    ## 2 37.3826 2012-10-01        5          Monday  TRUE            weekday
    ## 3 37.3826 2012-10-01       10          Monday  TRUE            weekday
    ## 4 37.3826 2012-10-01       15          Monday  TRUE            weekday
    ## 5 37.3826 2012-10-01       20          Monday  TRUE            weekday
    ## 6 37.3826 2012-10-01       25          Monday  TRUE            weekday

Making a panel plot containing a time series plot (i.e. `𝚝𝚢𝚙𝚎 = "𝚕"`) of
the 5-minute interval (x-axis) and the average number of steps taken,
averaged across all weekday days or weekend days (y-axis).

    df_interval2 <- df %>% group_by(interval, weekday_or_weekend) %>% summarise(steps = mean(steps, na.rm = TRUE), .groups = "drop")

    ggplot(df_interval2 , aes(x = interval , y = steps, color=weekday_or_weekend)) + geom_line() + labs(title = "Avg. Daily Steps by Weektype", x = "Interval", y = "No. of Steps") + facet_wrap(~weekday_or_weekend , ncol = 1, nrow=2)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-18-1.png)
