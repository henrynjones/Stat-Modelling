---
title: "HW_4_Henry_Jones"
author: "Henry Jones"
date: '2022-04-11'
output: 
  html_document: 
    keep_md: yes
---



## Chapter 5 Exercise 6
"We continue to consider the use of a logistic regression model to predict the probability of default using income and balance on the Default data set. In particular, we will now compute estimates for the standard errors of the income and balance logistic regression coefficients in two different ways: (1) using the bootstrap, and (2) using the standard formula for computing the standard errors in the glm() function. Do not forget to set a random seed before beginning your analysis."

```r
library(boot)
library(ISLR2)
set.seed(1)
#View(Default)
```


### a
"Using the summary() and glm() functions, determine the estimated standard errors for the coefficients associated with income and balance in a multiple logistic regression model that uses both predictors."

```r
log_mod <- glm(default ~ income + balance, data = Default, family = "binomial")
summary(log_mod)
```

```
## 
## Call:
## glm(formula = default ~ income + balance, family = "binomial", 
##     data = Default)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -2.4725  -0.1444  -0.0574  -0.0211   3.7245  
## 
## Coefficients:
##               Estimate Std. Error z value Pr(>|z|)    
## (Intercept) -1.154e+01  4.348e-01 -26.545  < 2e-16 ***
## income       2.081e-05  4.985e-06   4.174 2.99e-05 ***
## balance      5.647e-03  2.274e-04  24.836  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 2920.6  on 9999  degrees of freedom
## Residual deviance: 1579.0  on 9997  degrees of freedom
## AIC: 1585
## 
## Number of Fisher Scoring iterations: 8
```
So the estimated standard errors for income and balance are 4.985e-06 and 2.274e-04 respectively.

### b
"Write a function, boot.fn(), that takes as input the Default data set as well as an index of the observations, and that outputs the coefficient estimates for income and balance in the multiple logistic regression model."

```r
set.seed(1)
boot.fn <- function(data, index){
  coef(glm(default ~ income + balance, data = data, family = "binomial", subset = index))[-1]
}
boot.fn(Default, 1:10000)
```

```
##       income      balance 
## 2.080898e-05 5.647103e-03
```


### c
"Use the boot() function together with your boot.fn() function to estimate the standard errors of the logistic regression coefficients for income and balance."


```r
boot(Default, boot.fn, R = 1000)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = Default, statistic = boot.fn, R = 1000)
## 
## 
## Bootstrap Statistics :
##         original       bias     std. error
## t1* 2.080898e-05 1.680317e-07 4.866284e-06
## t2* 5.647103e-03 1.855765e-05 2.298949e-04
```
So the standard error for income and balance coefficients as computed by the boot strap are 4.866284e-06 and 2.298949e-04 respectively.

### d
"Comment on the estimated standard errors obtained using the glm() function and using your bootstrap function."

The glm() function yielded standard errors of 4.985e-06 and 2.274e-04 for income and balance respectively, which shows that these estimates are very similar. The Boostrap estimated standard error was slighly lower for income than that of the glm() function, but was slightly higher for the balance coefficient.

## Exercise 1, Chapter 6
"We perform best subset, forward stepwise, and backward stepwise selection on a single data set. For each approach, we obtain p + 1 models, containing 0, 1, 2, . . . , p predictors. Explain your answers:"

### a 
"Which of the three models with k predictors has the smallest training RSS?"

The model that makes use of best subset selection because the best subset selection simply tries all possible combinations. There is no way to beat the best subset selection for low training RSS.

### b
"Which of the three models with k predictors has the smallest test RSS?"
Just based on the selection method we will not be able to tell which method will yield the lowest test RSS. In order to assess the test RSS for each, we must either directly estimate test error with validation set or cross validation techniques, or we must indirectly estimate test error by making an adjustment to the training error to account for bias due to overfitting.

### c True or False:

### i
"The predictors in the k-variable model identified by forward stepwise are a subset of the predictors in the (k+1)-variable model identified by forward stepwise selection."

This is true because the (k+1) variable model looks to add the next most useful predictor to the predictors already in the k - variable model.


### ii 
"The predictors in the k-variable model identified by backward stepwise are a subset of the predictors in the (k + 1)- variable model identified by backward stepwise selection."

This is true because backward selection removes the least useful predictor in the k+1 predictor model to produce the k - predictor model.

### iii
"The predictors in the k-variable model identified by backward stepwise are a subset of the predictors in the (k + 1)- variable model identified by forward stepwise selection."

This is false because backward and forward selection don't nicely overlap, so we cannot say that this is always true.

### iv
"The predictors in the k-variable model identified by forward stepwise are a subset of the predictors in the (k+1)-variable model identified by backward stepwise selection."

This is false it could be the case that the kth predictor added to create the k-variable forward selection is the least useful predictor in any of the  (K+ i) variable backward selection and then we would not have a subset relationship. 

### v
"The predictors in the k-variable model identified by best subset are a subset of the predictors in the (k + 1)-variable model identified by best subset selection."

This is false because it could be that there is some other more accurate predictor couple to use in the (k+1)-variable model, in which case we would not have this subset relationship.

## Exercise 9 (a-d), Chapter 6
"In this exercise, we will predict the number of applications received using the other variables in the College data set."

```r
library(ISLR2)
```


### (a) 
Split the data set into a training set and a test set.

```r
set.seed(10)
train <- sample(c(1:600), replace =F)
training <- College[train,]
testing <- College[-train,]
```


### (b)
Fit a linear model using least squares on the training set, and report the test error obtained.


```r
set.seed(10)
blm <- lm(Apps ~., data = training)
summary(blm)
```

```
## 
## Call:
## lm(formula = Apps ~ ., data = training)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -5502.0  -361.2     7.4   276.9  7378.3 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -5.798e+02  4.475e+02  -1.296  0.19566    
## PrivateYes  -4.935e+02  1.585e+02  -3.113  0.00194 ** 
## Accept       1.723e+00  4.456e-02  38.675  < 2e-16 ***
## Enroll      -1.227e+00  2.047e-01  -5.995 3.58e-09 ***
## Top10perc    4.086e+01  6.224e+00   6.566 1.15e-10 ***
## Top25perc   -1.040e+01  4.889e+00  -2.128  0.03375 *  
## F.Undergrad  6.413e-02  3.671e-02   1.747  0.08121 .  
## P.Undergrad -1.202e-03  4.951e-02  -0.024  0.98064    
## Outstate    -9.924e-02  2.071e-02  -4.793 2.09e-06 ***
## Room.Board   1.463e-01  5.214e-02   2.805  0.00520 ** 
## Books        9.764e-02  2.522e-01   0.387  0.69874    
## Personal     4.263e-02  7.000e-02   0.609  0.54275    
## PhD         -6.814e+00  4.958e+00  -1.374  0.16988    
## Terminal    -3.957e+00  5.415e+00  -0.731  0.46525    
## S.F.Ratio    2.364e+01  1.460e+01   1.619  0.10596    
## perc.alumni  1.939e+00  4.496e+00   0.431  0.66636    
## Expend       1.047e-01  1.470e-02   7.123 3.12e-12 ***
## Grad.Rate    5.727e+00  3.150e+00   1.818  0.06952 .  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1000 on 582 degrees of freedom
## Multiple R-squared:  0.9271,	Adjusted R-squared:  0.925 
## F-statistic: 435.6 on 17 and 582 DF,  p-value: < 2.2e-16
```

```r
m1.1 <- lm(Apps ~ ., data = training)
#tests data that is not training data
pred1.1 <- predict.lm(m1.1, testing, type = "response")
err1.1 <- mean((testing$Apps - pred1.1)^2, na.rm=T)
err1.1
```

```
## [1] 1502077
```


### (c) 
Fit a ridge regression model on the training set, with λ chosen by cross-validation. Report the test error obtained.


```r
set.seed(10)
library(glmnet)
```

```
## Loading required package: Matrix
```

```
## Loaded glmnet 4.1-7
```

```r
train_mod <- model.matrix(Apps ~., data = training)
test_mod = model.matrix(Apps~., data = testing)
cv.train = cv.glmnet(train_mod, training$Apps, alpha = 0)
best_lambda = cv.train$lambda.min
best_lambda
```

```
## [1] 343.3316
```

```r
ridge_model = glmnet(train_mod, training$Apps, alpha = 0)
ridge_pred = predict(ridge_model, s = best_lambda, newx = test_mod)
mean((ridge_pred - testing$Apps)^2)
```

```
## [1] 1224221
```


### (d) 
Fit a lasso model on the training set, with λ chosen by cross- validation. Report the test error obtained, along with the num- ber of non-zero coefficient estimates.

```r
set.seed(10)
cv.train2 = cv.glmnet(train_mod, training$Apps, alpha = 1)
best_lambda2 = cv.train2$lambda.min
best_lambda2
```

```
## [1] 2.0109
```

```r
lasso_model = glmnet(train_mod, training$Apps, alpha = 1)
lasso_pred = predict(lasso_model, s = best_lambda2, newx = test_mod)
mean((lasso_pred - testing$Apps)^2)
```

```
## [1] 1491124
```

