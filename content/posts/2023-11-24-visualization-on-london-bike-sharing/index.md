---
title: Visualization on London Bike Sharing
author: Oskar Mikko
date: '2023-11-24'
slug: []
categories:
  - Visualization
tags:
  - Visulization
---




# get data    

```r
library(tidyverse)
data <- read.csv("C:/Users/Oskar/Documents/R/OskarMikko/OskarMikko/data/london_bike_sharing_data/london_merged.csv")

head(data)
##             timestamp cnt  t1  t2   hum wind_speed weather_code is_holiday
## 1 2015-01-04 00:00:00 182 3.0 2.0  93.0        6.0            3          0
## 2 2015-01-04 01:00:00 138 3.0 2.5  93.0        5.0            1          0
## 3 2015-01-04 02:00:00 134 2.5 2.5  96.5        0.0            1          0
## 4 2015-01-04 03:00:00  72 2.0 2.0 100.0        0.0            1          0
## 5 2015-01-04 04:00:00  47 2.0 0.0  93.0        6.5            1          0
## 6 2015-01-04 05:00:00  46 2.0 2.0  93.0        4.0            1          0
##   is_weekend season
## 1          1      3
## 2          1      3
## 3          1      3
## 4          1      3
## 5          1      3
## 6          1      3
data$date <- as.Date(data$timestamp) 
str(data)
## 'data.frame':	17414 obs. of  11 variables:
##  $ timestamp   : chr  "2015-01-04 00:00:00" "2015-01-04 01:00:00" "2015-01-04 02:00:00" "2015-01-04 03:00:00" ...
##  $ cnt         : int  182 138 134 72 47 46 51 75 131 301 ...
##  $ t1          : num  3 3 2.5 2 2 2 1 1 1.5 2 ...
##  $ t2          : num  2 2.5 2.5 2 0 2 -1 -1 -1 -0.5 ...
##  $ hum         : num  93 93 96.5 100 93 93 100 100 96.5 100 ...
##  $ wind_speed  : num  6 5 0 0 6.5 4 7 7 8 9 ...
##  $ weather_code: num  3 1 1 1 1 1 4 4 4 3 ...
##  $ is_holiday  : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ is_weekend  : num  1 1 1 1 1 1 1 1 1 1 ...
##  $ season      : num  3 3 3 3 3 3 3 3 3 3 ...
##  $ date        : Date, format: "2015-01-04" "2015-01-04" ...

?as.Date

data %>%
  group_by(date,as.factor(weather_code)) %>%
  summarise(cnt)
## # A tibble: 17,414 × 3
## # Groups:   date, as.factor(weather_code) [2,654]
##    date       `as.factor(weather_code)`   cnt
##    <date>     <fct>                     <int>
##  1 2015-01-04 1                           138
##  2 2015-01-04 1                           134
##  3 2015-01-04 1                            72
##  4 2015-01-04 1                            47
##  5 2015-01-04 1                            46
##  6 2015-01-04 3                           182
##  7 2015-01-04 3                           301
##  8 2015-01-04 3                           528
##  9 2015-01-04 3                           727
## 10 2015-01-04 3                           916
## # ℹ 17,404 more rows

ggplot(data,
       aes(x=date,y=cnt,group=as.factor(weather_code))) + geom_line()
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="672" />


# How does weather affect bike usage?





