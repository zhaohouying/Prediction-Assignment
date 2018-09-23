---
title: "Prediction Assignment"
author: "Zhao"
date: "September 23, 2018"
output: 
  html_document:
    keep_md: true
---
## Executive Summary
The goal of this project is to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants to predict the manner in which they did the exercise. This is the "classe" variable in the training set. 

A validation set is sub-setted from the original training set to validate the modle built based on the training subset, with a random forest model using 54 of the predictors in the training sub-set. accuracy is above 99% for both the training sub-set and validation sub-set.

I also use your prediction model to predict 20 different test cases in the test set, with a 100% accuracy.



### 1. Load libraries needed,create a d folder and download the data into the folder. Enable prrallel running to speedup the modeling

```r
library(parallel)
library(doParallel)
registerDoParallel(makeCluster(detectCores()- 1))


library(caret)
library(randomForest)

trainURL <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
download.file(trainURL,destfile = "./pml-training.csv")

testURL <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
download.file(testURL,destfile = "./pml-testing.csv")
```

### 2. Load the training and testing data set, select the preedictors by removing columns with NA as majority, then sub-set the training set

```r
trainset <- read.csv("pml-training.csv",na.strings = c("",NA))
trainset <- trainset[,colSums(is.na(trainset))<19000][,-(1:5)]
inTrain = createDataPartition(trainset$classe, p = 0.6,list=FALSE)
training = trainset[inTrain,]
validation = trainset[-inTrain,]
testing <- read.csv("pml-testing.csv")
```

### 3. Build the random forest model based on the training set and check the accuracy

```r
modrf <- train(classe~.,method="rf",data=training,trControl=trainControl(method = "cv",number = 2,allowParallel = TRUE))
confusionMatrix(modrf)
```

```
## Cross-Validated (2 fold) Confusion Matrix 
## 
## (entries are percentual average cell counts across resamples)
##  
##           Reference
## Prediction    A    B    C    D    E
##          A 28.4  0.2  0.0  0.0  0.0
##          B  0.0 19.0  0.2  0.0  0.0
##          C  0.0  0.2 17.2  0.3  0.0
##          D  0.0  0.0  0.0 16.1  0.1
##          E  0.0  0.0  0.0  0.0 18.3
##                             
##  Accuracy (average) : 0.9901
```

### 4. Validate the modle with the validation set and confirm the accuracy

```r
validate <- predict(modrf,validation)
confusionMatrix(validate,validation$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 2232    7    0    0    0
##          B    0 1509    3    0    0
##          C    0    2 1362    5    0
##          D    0    0    3 1280    1
##          E    0    0    0    1 1441
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9972          
##                  95% CI : (0.9958, 0.9982)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9965          
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            1.0000   0.9941   0.9956   0.9953   0.9993
## Specificity            0.9988   0.9995   0.9989   0.9994   0.9998
## Pos Pred Value         0.9969   0.9980   0.9949   0.9969   0.9993
## Neg Pred Value         1.0000   0.9986   0.9991   0.9991   0.9998
## Prevalence             0.2845   0.1935   0.1744   0.1639   0.1838
## Detection Rate         0.2845   0.1923   0.1736   0.1631   0.1837
## Detection Prevalence   0.2854   0.1927   0.1745   0.1637   0.1838
## Balanced Accuracy      0.9994   0.9968   0.9973   0.9974   0.9996
```

### 5. Predict 20 different test cases in the test set

```r
predTestrf <- predict(modrf,testing)
print(predTestrf)
```

```
##  [1] B A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```

### 6. Stop the parallel running

```r
stopCluster(makeCluster(detectCores()- 1))
registerDoSEQ()
```


