# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

# 1. Read the data into a dataframe 
stepdata <- read.csv("./RepData_PeerAssessment1/activity/activity.csv")

# 2. No further processing

## What is mean total number of steps taken per day?

# 1a. Convert the dataset: Create a new dataframe 'steps_per_day' that sums 'steps' by the factor 'date'
steps_per_day <- aggregate(stepdata$steps, list(stepdata$date), sum, na.rm = TRUE)
# 1b. Name the variables of the new object 'date' and 'steps'
names(steps_per_day) <- c("date","steps")
# 1c. Print the head of the new dataframe
head(steps_per_day)

# 2. Print a histogram of steps per day
hist(steps_per_day$steps, breaks = 20, col = "wheat")

# 3a. Print the mean and median of 'steps' with the summary function (ANSWER: mean = 9354, median = 10400)
summary(steps_per_day$steps)

## What is the average daily activity pattern?

# 1a. Create a new dataframe 'steps_per_5m' that averages 'steps' by the factor 'interval'
steps_per_5m <- aggregate(stepdata$steps, list(stepdata$interval), mean, na.rm = TRUE)
# 1b. Name the variables 'interval' and 'steps'
names(steps_per_5m) <- c("interval","steps")
# 1c. plot the two variables
plot(x = steps_per_5m$interval, y = steps_per_5m$steps, type = "l")

# 2a. Assign a variable max_steps to the maximum average steps per interval
max_steps <- max(steps_per_5m$steps)
# 2b. Assign a variable max_interval as the subset for every interval at the maximum amount of steps 
max_interval <- subset(steps_per_5m, steps == maxsteps)
# 2c. Print the variable to find the interval(s) with the maximum amount of average steps --> ANSWER: interval 835
print(max_interval)

## Imputing missing values

# 1. The number of missing rows can be found with the summary function
summary(stepdata)

# 2. Strategy to replace missing values: Replace every missing NA with the average for that interval

# 3a. Make a copy "stepdata2" of the original "stepdata" dataframe.
stepdata2 <- stepdata
# 3b. Use a loop to test every 'steps' value for NA and if true, replace it with the average value for that interval
for(i in 1:length(stepdata2$interval)) {
    if(is.na(stepdata2[i, "steps"])) {
        stepdata2[i,"steps"] <- steps_per_5m[steps_per_5m$interval == stepdata2[i,"interval"],"steps"]
    }
}

# 4a. Create 'steps_per_day2' by aggregating 'stepdata2' just like 'steps_per_day' was created from 'stepdata'.
steps_per_day2 <- aggregate(stepdata2$steps, list(stepdata2$date), sum, na.rm = TRUE)
names(steps_per_day2) <- c("date","steps")
head(steps_per_day2)
# 4b. Plot a histogram and calculate means with summary. ANSWER: mean = 10770, median = 10770 
hist(steps_per_day2$steps, breaks = 20, col = "wheat")
summary(steps_per_day2$steps)

## Are there differences in activity patterns between weekdays and weekends?

# 1a. Create a new variable weekday that holds the name of the weekday of the variable 'date' in stepdata2
weekday <- weekdays(as.Date(stepdata2$date))
# 1b. Run a loop through weekday to assign a new value 'weekday' or 'weekend' according to the value within ('monday' - 'sunday')
for(i in 1:length(weekday)) {
    if(weekday[i] == weekdays(as.Date("2016-01-02"))) {weekday[i] <- "weekend"}
    else if(weekday[i] == weekdays(as.Date("2016-01-02"))) {weekday[i] <- "weekend"}
    else {weekday[i] <- "weekday"}
}
# 1c. Create a new datafram 'stepdata3' that combines the vector 'weekday' with 'stepdata2'
stepdata3 <- cbind(stepdata2, weekday)

# 2a. Create a new variable 'avg_steps_5m_weekday' that averages the steps over two factors: 'ínterval' and 'weekday'
avg_steps_5m_weekday <- aggregate(stepdata3$steps, list(stepdata3$interval, stepdata3$weekday), mean)
# 2b. Add names to the variables
names(avg_steps_5m_weekday) <- c("interval", "weekday", "steps")
# 2c. Create an emtpty plot of steps versus interval of avg_steps_5m_weekday
with(avg_steps_5m_weekday, plot(interval, steps, type = "n", main = "Average number of steps throughout the day "))
# 2d. Plot the points where factor 'weekday' equals 'weekend' in red
with(avg_steps_5m_weekday, points(interval[weekday == "weekend"], steps[weekday == "weekend"], type = "l", col = "red"))
# 2e. Plot the points where factor 'weekday' equals 'weekday' in blue
with(avg_steps_5m_weekday, points(interval[weekday == "weekday"], steps[weekday == "weekday"], type = "l", col = "blue"))
# 2f. Plot a legend
legend("topright", pch = "-", col = c("red", "blue"), legend = c("weekend", "weekday"))
                
