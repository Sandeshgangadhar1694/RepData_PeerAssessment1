<br/>

### Loading and preprocessing the data

    activity <- read.csv("activity/activity.csv")
    head(activity)

    ##   steps       date interval
    ## 1    NA 2012-10-01        0
    ## 2    NA 2012-10-01        5
    ## 3    NA 2012-10-01       10
    ## 4    NA 2012-10-01       15
    ## 5    NA 2012-10-01       20
    ## 6    NA 2012-10-01       25

    activity$date <- as.Date(activity$date)

### What is mean total number of steps taken per day?

1.  Calculate the total number of steps taken per day

<!-- -->

    library(dplyr)

    totalsteps <- activity %>% group_by(date) %>% summarize("Total Num of Steps per day" = sum(steps,na.rm = TRUE))

1.  Make a histogram of the total number of steps taken each day

<!-- -->

    hist(totalsteps$`Total Num of Steps per day`,xlab = "Total Number of Steps per day")

![](PA1_template_v2_files/figure-markdown_strict/unnamed-chunk-4-1.png)

1.  Calculate and report the mean and median of the total number of
    steps taken per day

<!-- -->

    steps_mean = mean(totalsteps$`Total Num of Steps per day`,na.rm = TRUE)
    steps_median = median(totalsteps$`Total Num of Steps per day`,na.rm = TRUE)

<h6>
Mean = 9354.2295082
</h6>
<h6>
Median = 10395
</h6>
### What is the average daily activity pattern?

1.  Make a time series plot (i.e. type = "l") of the 5-minute
    interval (x-axis) and the average number of steps taken, averaged
    across all days (y-axis)

<!-- -->

    average_steps <- activity %>% group_by(interval) %>% summarise("Average Steps" = mean(steps,na.rm = TRUE))
    with(average_steps,plot(interval , `Average Steps`,type = "l"))

![](PA1_template_v2_files/figure-markdown_strict/unnamed-chunk-6-1.png)

1.  Which 5-minute interval, on average across all the days in the
    dataset, contains the maximum number of steps?

<!-- -->

    max_interval = average_steps$interval[which(average_steps$`Average Steps` == max(average_steps$`Average Steps`))]

<center>
<h4>
Interval with max Average step = 835
</center>
</h4>
### Imputing missing values

1.  Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with NAs)

<!-- -->

    missing_steps = sum(is.na(activity$steps))

<center>
<h4>
Number of missing rows = 2304
</h4>
</center>
1.  Devise a strategy for filling in all of the missing values in
    the dataset. The strategy does not need to be sophisticated. For
    example, you could use the mean/median for that day, or the mean for
    that 5-minute interval, etc.

<!-- -->

    #Replacing NAs with 0 assuming the subject to be inactive(Sleeping,Standing,Sitting)
    activity$steps[is.na(activity$steps)] <- 0

1.  Create a new dataset that is equal to the original dataset but with
    the missing data filled in.

<!-- -->

    activity.imputed <- activity
    activity.imputed$steps[is.na(activity.imputed$steps)] <- 0

1.  Make a histogram of the total number of steps taken each day and
    Calculate and report the mean and median total number of steps taken
    per day. Do these values differ from the estimates from the first
    part of the assignment? What is the impact of imputing missing data
    on the estimates of the total daily number of steps? Histogram of
    the total number of steps taken each day

<!-- -->

    hist(activity.imputed$steps)

![](PA1_template_v2_files/figure-markdown_strict/unnamed-chunk-11-1.png)

    imputed.steps.mean <- mean(activity.imputed$steps)
    imputed.steps.median <- median(activity.imputed$steps)

<h6>
Mean = 32.4799636
</h6>
<h6>
Median = 0
</h6>
<center>
<h4>
Drastic difference in Mean and Median are noticed after replacing NAs
with 0s in the dataset.
</h4>
</center>
### Are there differences in activity patterns between weekdays and weekends?

1.  Create a new factor variable in the dataset with two levels –
    “weekday” and “weekend” indicating whether a given date is a weekday
    or weekend day.

<!-- -->

    activity.imputed$DayType <- as.factor(ifelse(weekdays(activity.imputed$date) %in% c("Saturday","Sunday"),"weekend","weekday"))

1.  Make a panel plot containing a time series plot (i.e. type = "l") of
    the 5-minute interval (x-axis) and the average number of steps
    taken, averaged across all weekday days or weekend days (y-axis).
    See the README file in the GitHub repository to see an example of
    what this plot should look like using simulated data.

<!-- -->

    library(ggplot2)
    activity.imputed.toplot <- activity.imputed %>% group_by(DayType,interval) %>% summarise("Average steps taken" = mean(steps))
    ggplot(data = activity.imputed.toplot,aes(x = interval,y = `Average steps taken`)) + geom_line() + facet_grid(DayType ~ .)

![](PA1_template_v2_files/figure-markdown_strict/unnamed-chunk-13-1.png)
