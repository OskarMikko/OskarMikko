---
title: Analysis of Hotel Booking Cancellations
author: Oskar Mikko
date: '2023-11-28'
slug: []
categories:
  - Data Analysis
tags:
  - Visulization
  - Data Analysis
---



# Business Problem

In recent years, City Hotel and Resort Hotel in Portugal have seen high cancellation rates. Each hotel is now dealing with a number of issues as a result, including fewer revenues and less than ideal hotel room use. Consequently, lowering cancellation rates is both hotels' primary goel in order to increase their efficiency in generating revenue, and for us to offer thorough business advice to adress this problem. 

The analysis of hotel booking cancellations as well as other factors that have no bearing on their business and yearly revenue generation are the main topics of this report. 

The data used in this case can be found at: https://www.kaggle.com/datasets/mathsian/hotel-bookings. 

# Data Cleaning and Exploratory Data Analysis

## An Overview of the Data

```r
library(tidyverse)
library(knitr)
data = read.csv("C:/Users/Oskar/Documents/R/OskarMikko/OskarMikko/static/hotel_bookings.csv")
str(data)
## 'data.frame':	119390 obs. of  32 variables:
##  $ hotel                         : chr  "Resort Hotel" "Resort Hotel" "Resort Hotel" "Resort Hotel" ...
##  $ is_canceled                   : int  0 0 0 0 0 0 0 0 1 1 ...
##  $ lead_time                     : int  342 737 7 13 14 14 0 9 85 75 ...
##  $ arrival_date_year             : int  2015 2015 2015 2015 2015 2015 2015 2015 2015 2015 ...
##  $ arrival_date_month            : chr  "July" "July" "July" "July" ...
##  $ arrival_date_week_number      : int  27 27 27 27 27 27 27 27 27 27 ...
##  $ arrival_date_day_of_month     : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ stays_in_weekend_nights       : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ stays_in_week_nights          : int  0 0 1 1 2 2 2 2 3 3 ...
##  $ adults                        : int  2 2 1 1 2 2 2 2 2 2 ...
##  $ children                      : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ babies                        : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ meal                          : chr  "BB" "BB" "BB" "BB" ...
##  $ country                       : chr  "PRT" "PRT" "GBR" "GBR" ...
##  $ market_segment                : chr  "Direct" "Direct" "Direct" "Corporate" ...
##  $ distribution_channel          : chr  "Direct" "Direct" "Direct" "Corporate" ...
##  $ is_repeated_guest             : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ previous_cancellations        : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ previous_bookings_not_canceled: int  0 0 0 0 0 0 0 0 0 0 ...
##  $ reserved_room_type            : chr  "C" "C" "A" "A" ...
##  $ assigned_room_type            : chr  "C" "C" "C" "A" ...
##  $ booking_changes               : int  3 4 0 0 0 0 0 0 0 0 ...
##  $ deposit_type                  : chr  "No Deposit" "No Deposit" "No Deposit" "No Deposit" ...
##  $ agent                         : chr  "NULL" "NULL" "NULL" "304" ...
##  $ company                       : chr  "NULL" "NULL" "NULL" "NULL" ...
##  $ days_in_waiting_list          : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ customer_type                 : chr  "Transient" "Transient" "Transient" "Transient" ...
##  $ adr                           : num  0 0 75 75 98 ...
##  $ required_car_parking_spaces   : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ total_of_special_requests     : int  0 0 0 0 1 1 0 1 1 0 ...
##  $ reservation_status            : chr  "Check-Out" "Check-Out" "Check-Out" "Check-Out" ...
##  $ reservation_status_date       : chr  "2015-07-01" "2015-07-01" "2015-07-02" "2015-07-02" ...
```


```r
reservations = as.data.frame(table(data$hotel))
colnames(reservations) = c("Hotel","Number of Reservations")
kable(reservations,caption = "Number of Reservations per Hotel")
```



Table: <span id="tab:unnamed-chunk-2"></span>Table 1: Number of Reservations per Hotel

|Hotel        | Number of Reservations|
|:------------|----------------------:|
|City Hotel   |                  79330|
|Resort Hotel |                  40060|



The data contains 119390 observations and 32 different variables. 79 330 of the observations are City Hotel's reservations and 40 060 are Resort Hotel reservations. 



```r
data$reservation_status_date = as.Date(data$reservation_status_date)
```

### Looking for Outliers


```r
ggplot(data, aes(x=hotel, y=adr)) + 
  geom_boxplot() + theme_bw() +
  ggtitle("Boxplot for Average Daily Rate per hotel") +
  theme(plot.title = element_text(hjust = 0.5))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="672" />
When looking at the average daily rate we can see that there are a few outliers in the data. I'm choosing to remove the most obvious outlier which is over 5 000.


```r
index = data[,"adr"]<2000
data = data[index,]
```
# Data Analysis and Visualizations


```r
kable(data %>%
  group_by(hotel) %>%
  summarise(canceled_percentage = round(mean(is_canceled),3)),
  caption = "Canceling percentage")
```



Table: <span id="tab:unnamed-chunk-6"></span>Table 2: Canceling percentage

|hotel        | canceled_percentage|
|:------------|-------------------:|
|City Hotel   |               0.417|
|Resort Hotel |               0.278|



```r

data$is_canceled <- as.factor(data$is_canceled)
data %>%
  group_by(hotel) %>%
  count(is_canceled) %>%
  ggplot(aes(x=hotel, y=n, fill=is_canceled)) +
  geom_bar(stat="identity", position=position_dodge())+
  scale_fill_brewer(palette="Paired")+
  theme_minimal() + ggtitle("Reservation Status") +
  theme(plot.title = element_text(hjust = 0.5))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-6-1.png" width="672" />
The percentage of canceled reservations for City Hotel is 41.7% and for Resort Hotel it is 27.8%.


```r
data %>%
  group_by(reservation_status_date,hotel) %>%
  summarise(Average_Daily_Rate = mean(adr)) %>%
  ggplot(aes(x=reservation_status_date,y=Average_Daily_Rate,
             group= hotel, color=hotel)) + geom_line() + theme_minimal() +
  ggtitle("Average Daily Rate in City and Resort Hotel") +
  theme(plot.title = element_text(hjust = 0.5)) + 
  scale_x_date(date_labels = "%Y (%b)")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-1.png" width="672" />

The average daily rate for Resort Hotel changes more by the season; Resort Hotel typically have lower rates than City Hotel during the winter, and higher rates during the summer. Both hotels had their highest average during July 2017. 


```r
data %>%
  group_by(Month=month(reservation_status_date)) %>%
  count(is_canceled) %>%
  ggplot(aes(x=Month, y=n, fill=is_canceled)) +
  geom_bar(stat="identity", position=position_dodge())+
  scale_fill_brewer(palette="Paired")+
  theme_minimal() + ggtitle("Reservation Status per Month") +
  theme(plot.title = element_text(hjust = 0.5)) +
  scale_x_continuous(breaks=seq(1,12,1),expand = c(0, 0)) +
  ylab("Number of Reservations")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="672" />

During the winter months, December January and February the cancellation rates are the highest relative to the reservations, and in January the cancellation rate is even over 50%. July had the second highest cancellation rate. In September and August the cancellation rates are the lowest. The highest confirmed reservations are in August.



```r
ggplot(mapping = aes(x = data$market_segment,
                     y = after_stat(count/sum(count)))) +
  geom_bar() + scale_y_continuous(labels = scales::percent) + theme_minimal() +
  ggtitle("Reservations by Market Segment") + xlab("Market Segment") + ylab("Percentage")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-1.png" width="672" />

Here we've analyzed from where the reservations have been made. More than 45% of the reservations come from online bookings. About 20% come from offline travel agents. About 16% of the reservations are from group reservations and about 10% are direct reservations made at the hotel. 



















