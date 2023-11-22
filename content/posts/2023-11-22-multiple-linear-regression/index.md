---
title: Tree-based classification
author: Oskar Vilhelmsson
date: '2023-11-22'
slug: []
categories:
  - Machine Learning
tags:
  - R
  - CART
  - Regression
---




```r
library(tidyverse)
library(DAAG)
library(party)
library(rpart)
library(rpart.plot)
library(mlbench)
library(pROC)
library(tree)
library(caret)
```

# Decision trees

- The goal is to predict an outcome based on a set of predictors.

## Regression Tree: Predicting median value of Boston homes


```r
data('BostonHousing')
data <- BostonHousing
str(data)
## 'data.frame':	506 obs. of  14 variables:
##  $ crim   : num  0.00632 0.02731 0.02729 0.03237 0.06905 ...
##  $ zn     : num  18 0 0 0 0 0 12.5 12.5 12.5 12.5 ...
##  $ indus  : num  2.31 7.07 7.07 2.18 2.18 2.18 7.87 7.87 7.87 7.87 ...
##  $ chas   : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
##  $ nox    : num  0.538 0.469 0.469 0.458 0.458 0.458 0.524 0.524 0.524 0.524 ...
##  $ rm     : num  6.58 6.42 7.18 7 7.15 ...
##  $ age    : num  65.2 78.9 61.1 45.8 54.2 58.7 66.6 96.1 100 85.9 ...
##  $ dis    : num  4.09 4.97 4.97 6.06 6.06 ...
##  $ rad    : num  1 2 2 3 3 3 5 5 5 5 ...
##  $ tax    : num  296 242 242 222 222 222 311 311 311 311 ...
##  $ ptratio: num  15.3 17.8 17.8 18.7 18.7 18.7 15.2 15.2 15.2 15.2 ...
##  $ b      : num  397 397 393 395 397 ...
##  $ lstat  : num  4.98 9.14 4.03 2.94 5.33 ...
##  $ medv   : num  24 21.6 34.7 33.4 36.2 28.7 22.9 27.1 16.5 18.9 ...
```

The data contains 506 observations and 14 different variables:

- crim: per capita crime rate by town
- zn: proportion of residential land zoned for lots over 25,000 sq.ft
- indus: proportion of non-retail business acres per town
- chas: Charles River dummy varaible(1= if tract bounds river, 0 otherwise)
- nox: nitric oxides concentraion
- rm: averigare number of rooms per dwelling
- age: prortion of owner-occupied units built prior to 1940
- dis: weighted distances to five Boston employment centres
- rad: index of accessibility to radial highways
- tax: full-value property-tax rate per USD 10,000
- ptratio: pupil-teacher ratio by town
- b: `\(1000*(B-0.63)^2\)` where B is the proportion of blacks by town
- lstat: percentage of lower status of the population
- medv: median value of owner-occupied homes in USD 1000's.

where medv is the predictor variable.


