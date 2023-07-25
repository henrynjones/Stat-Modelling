---
title: "TP 6 Henry Jones"
author: "Henry Jones"
date: '2022-04-18'
output: 
  html_document: 
    keep_md: yes
---



# Test Problems 6

```r
library(tree)
library(randomForest)
```

```
## randomForest 4.7-1.1
```

```
## Type rfNews() to see new features/changes/bug fixes.
```

```r
library(ISLR2)
Auto$origin <- factor(Auto$origin)
```

Build a model that can predict mpg using trees.

## a
"Fit a simple tree model (prune it if appropriate) and report the cross-validation MSE."

```r
set.seed(1)
#remove names as a predictor
reg.tree <- tree(mpg ~., data = Auto[,-9])
summary(reg.tree)
```

```
## 
## Regression tree:
## tree(formula = mpg ~ ., data = Auto[, -9])
## Variables actually used in tree construction:
## [1] "displacement" "horsepower"   "year"         "weight"      
## Number of terminal nodes:  8 
## Residual mean deviance:  9.346 = 3589 / 384 
## Distribution of residuals:
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
## -9.4170 -1.5190 -0.2855  0.0000  1.7150 18.5600
```

```r
#plot(reg.tree)
#text(reg.tree, pretty = 0)
summary(reg.tree)
```

```
## 
## Regression tree:
## tree(formula = mpg ~ ., data = Auto[, -9])
## Variables actually used in tree construction:
## [1] "displacement" "horsepower"   "year"         "weight"      
## Number of terminal nodes:  8 
## Residual mean deviance:  9.346 = 3589 / 384 
## Distribution of residuals:
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
## -9.4170 -1.5190 -0.2855  0.0000  1.7150 18.5600
```

```r
cv.auto <- cv.tree(reg.tree)
#plot(cv.auto$size, cv.auto$dev, type = "b")
min(cv.auto$dev) / nrow(Auto)
```

```
## [1] 12.88698
```

The lowest cross validation MSE of 12.88698 was found when the tree had eight terminal nodes which means the original unpruned tree is the best tree to use for prediction.

## b
"Fit a random forest model and report the out-of-bag MSE"

```r
rf.lAuto2 <- randomForest(mpg ~., data = Auto[,-9], mtry = 2)
rf.lAuto2
```

```
## 
## Call:
##  randomForest(formula = mpg ~ ., data = Auto[, -9], mtry = 2) 
##                Type of random forest: regression
##                      Number of trees: 500
## No. of variables tried at each split: 2
## 
##           Mean of squared residuals: 7.293626
##                     % Var explained: 88
```

```r
rf.Auto3 <- randomForest(mpg ~., data = Auto[,-9], mtry = 3)
rf.Auto3
```

```
## 
## Call:
##  randomForest(formula = mpg ~ ., data = Auto[, -9], mtry = 3) 
##                Type of random forest: regression
##                      Number of trees: 500
## No. of variables tried at each split: 3
## 
##           Mean of squared residuals: 7.215569
##                     % Var explained: 88.13
```

```r
rf.Auto4 <- randomForest(mpg ~., data = Auto[,-9], mtry = 4)
rf.Auto4
```

```
## 
## Call:
##  randomForest(formula = mpg ~ ., data = Auto[, -9], mtry = 4) 
##                Type of random forest: regression
##                      Number of trees: 500
## No. of variables tried at each split: 4
## 
##           Mean of squared residuals: 7.356904
##                     % Var explained: 87.89
```

I fitted three random forest models with m = 2,3,4 and found the lowest out of bag MSE to be 7.158916 for the model with m = 4.

## c
"Fit a boosting tree model and report the cross-validation MSE."

```r
set.seed(1)
library(gbm)
```

```
## Loaded gbm 2.1.8.1
```

```r
boost.auto <- gbm(mpg ~ ., data = Auto[,-9], distribution = "gaussian", n.trees = 1000, interaction.depth = 2, cv.folds = 10)
boost.auto$cv.error[1000]
```

```
## [1] 8.07093
```

```r
#min(boost.auto$cv.error)
#cv$delta[1]
#class(cv)
```

A boosting model with interaction depth set to 1 yields a CV MSE of 8.836237, while an Interaction depth set to 2 yields a CV MSE of 8.07093 As interaction depth increases beyond 2, we see that the CV MSE continues to decreases to 7.846761, 7.695694, etc.

## d
"Compare the MSEs from parts (a), (b), and (s) with the cross-validation MSE obtained from the best model in the previous test problem (TP5). Which model (or models) seem to perform best in predicting mpg? Explain the strengths of the best model (or models) that may have led to its good performance."

As can be seen from the CV MSE's for the three tree models, the random forest model with m =3 produced the lowest CV MSE of 7.19355. Most notably, the CV MSE's of random forests and boosting are somewhat comparable, differing by less than about 0.9, while the CV MSE for the simple regression tree is much larger (12.89) and indicates the dramatic improvements random forests and boosting makes on simple regression trees.

The strength of random forests that the algorithm allow for randomized learning to regularize data to not miss out on deeper trends which would be missed in the greedy algorithm of a simple tree. In the case of boosting, trees learn based on the previous trees so that the model is more attuned to previous information already obtained.
