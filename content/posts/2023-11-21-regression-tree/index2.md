---
title: "Tree-based classification and regression"
author: "Oskar Vilhelmsson"
date: "2023-11-21"
output: pdf_document
categories: Machine Learning
tags:
- R
- Classification
- CART
- Regression
slug: []
---



# Decision trees

- The goal is to classify or predict an outcome based on a set of predictors.


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

## Classification Tree: Detecting email spam data



```r
data <- spam7
str(data)  
## 'data.frame':	4601 obs. of  7 variables:
##  $ crl.tot: num  278 1028 2259 191 191 ...
##  $ dollar : num  0 0.18 0.184 0 0 0 0.054 0 0.203 0.081 ...
##  $ bang   : num  0.778 0.372 0.276 0.137 0.135 0 0.164 0 0.181 0.244 ...
##  $ money  : num  0 0.43 0.06 0 0 0 0 0 0.15 0 ...
##  $ n000   : num  0 0.43 1.16 0 0 0 0 0 0 0.19 ...
##  $ make   : num  0 0.21 0.06 0 0 0 0 0 0.15 0.06 ...
##  $ yesno  : Factor w/ 2 levels "n","y": 2 2 2 2 2 2 2 2 2 2 ...
table(data$yesno)
## 
##    n    y 
## 2788 1813
```

The data consists of 4601 emails, 1813 of them were considered as spam emails.

The variables in the dataset are:
 - crl.tot: total length of words in capitals
 - dollar: number of occurrences of the $ symbol.
 - bang: number of occurrences of the ! symbol.
 - money: number of occurrences of the word 'money'
 - n000: number of occurrences of the string '000'
 - make: number of occurrences of the word 'make'
 - yesno: outcome variable; y = if spam, no = if not spam

- The goal is to classify each email as spam or not spam using the decision tree.

# Splitting data

We need to split data into training and testing for our model. 


```r
set.seed(1337)
index <- sample(2, nrow(data), replace = T, prob = c(0.5, 0.5))
train <- data[index == 1,]
test <- data[index == 2,]
```

# The tree  

```r
tree <- rpart(yesno ~., data = train)
rpart.plot(tree)
```

<img src="/posts/2023-11-21-regression-tree/index2_files/figure-html/unnamed-chunk-4-1.png" width="672" />


The root node is dollar and if dollar is > 0.046 then the email is classified as spam. 27% of the data has dollar > 0.046 and 88% of that data is spam.

The second node is bang. 73% percent of the data goes through this splitting criteria that is is bang < 0.18. If bang > 0.18 and crl.tot > 78 the email is classified as spam. If bang > 0.18 and crl.tot < 78 then the data goes through an another node: bang < 1.1. If bang > 1.1 then the classification is spam. If bang < 1.1 the classification is not spam.

The remaining data that has bang < 0.18 goes through the last node: money < 0.01. If money is > 0.01 the classification becomes spam, and if money is less than 0.01 the classification becomes no spam.


# The complexity parameter


```r
printcp(tree)
## 
## Classification tree:
## rpart(formula = yesno ~ ., data = train)
## 
## Variables actually used in tree construction:
## [1] bang    crl.tot dollar  money  
## 
## Root node error: 905/2267 = 0.39921
## 
## n= 2267 
## 
##         CP nsplit rel error  xerror     xstd
## 1 0.498343      0   1.00000 1.00000 0.025765
## 2 0.070718      1   0.50166 0.50166 0.021055
## 3 0.059669      2   0.43094 0.45746 0.020327
## 4 0.026519      3   0.37127 0.39227 0.019120
## 5 0.014365      4   0.34475 0.37569 0.018785
## 6 0.010000      5   0.33039 0.35359 0.018318
```






```r
plotcp(tree)
```

<img src="/posts/2023-11-21-regression-tree/index2_files/figure-html/unnamed-chunk-6-1.png" width="672" />



```r
p <- predict(tree, train, type = 'class')
confusionMatrix(p, train$yesno)
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    n    y
##          n 1228  165
##          y  134  740
##                                           
##                Accuracy : 0.8681          
##                  95% CI : (0.8535, 0.8818)
##     No Information Rate : 0.6008          
##     P-Value [Acc > NIR] : < 2e-16         
##                                           
##                   Kappa : 0.7235          
##                                           
##  Mcnemar's Test P-Value : 0.08275         
##                                           
##             Sensitivity : 0.9016          
##             Specificity : 0.8177          
##          Pos Pred Value : 0.8816          
##          Neg Pred Value : 0.8467          
##              Prevalence : 0.6008          
##          Detection Rate : 0.5417          
##    Detection Prevalence : 0.6145          
##       Balanced Accuracy : 0.8596          
##                                           
##        'Positive' Class : n               
## 

pp <- predict(tree, test, type = 'class')
confusionMatrix(pp, test$yesno)
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    n    y
##          n 1274  200
##          y  152  708
##                                          
##                Accuracy : 0.8492         
##                  95% CI : (0.834, 0.8635)
##     No Information Rate : 0.611          
##     P-Value [Acc > NIR] : < 2e-16        
##                                          
##                   Kappa : 0.6797         
##                                          
##  Mcnemar's Test P-Value : 0.01224        
##                                          
##             Sensitivity : 0.8934         
##             Specificity : 0.7797         
##          Pos Pred Value : 0.8643         
##          Neg Pred Value : 0.8233         
##              Prevalence : 0.6110         
##          Detection Rate : 0.5458         
##    Detection Prevalence : 0.6315         
##       Balanced Accuracy : 0.8366         
##                                          
##        'Positive' Class : n              
## 
```

