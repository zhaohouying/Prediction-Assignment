---
title Prediction Assignment
author Zhao
date September 23, 2018
output html_document
---
## Executive Summary
The goal of this project is to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants to predict the manner in which they did the exercise. This is the classe variable in the training set. 

A validation set is sub-setted from the original training set to validate the modle built based on the training subset, with a random forest model using 54 of the predictors in the training sub-set. accuracy is above 99% for both the training sub-set and validation sub-set.

I also use your prediction model to predict 20 different test cases in the test set, with a 100% accuracy.

```{r setup, include=FALSE}
knitropts_chunk$set(echo = TRUE,cache = TRUE)
```

### 1. Load libraries needed,create a d folder and download the data into the folder. Enable prrallel running to speedup the modeling
```{r}
library(parallel)
library(doParallel)
registerDoParallel(makeCluster(detectCores()- 1))


library(caret)
library(randomForest)

trainURL - httpsd396qusza40orc.cloudfront.netpredmachlearnpml-training.csv
download.file(trainURL,destfile = .pml-training.csv)

testURL - httpsd396qusza40orc.cloudfront.netpredmachlearnpml-testing.csv
download.file(testURL,destfile = .pml-testing.csv)
```

### 2. Load the training and testing data set, select the preedictors by removing columns with NA as majority, then sub-set the training set
```{r}
trainset - read.csv(pml-training.csv,na.strings = c(,NA))
trainset - trainset[,colSums(is.na(trainset))19000][,-(15)]
inTrain = createDataPartition(trainset$classe, p = 0.6,list=FALSE)
training = trainset[inTrain,]
validation = trainset[-inTrain,]
testing - read.csv(pml-testing.csv)
```

### 3. Build the random forest model based on the training set and check the accuracy
```{r}
modrf - train(classe~.,method=rf,data=training,trControl=trainControl(method = cv,number = 2,allowParallel = TRUE))
confusionMatrix(modrf)
```
Cross-Validated (2 fold) Confusion Matrix 

(entries are percentual average cell counts across resamples)
 
          Reference
Prediction    A    B    C    D    E
         A 28.4  0.2  0.0  0.0  0.0
         B  0.0 19.1  0.2  0.0  0.0
         C  0.0  0.1 17.2  0.2  0.0
         D  0.0  0.0  0.1 16.2  0.1
         E  0.0  0.0  0.0  0.0 18.3
                           
 Accuracy (average) : 0.991

### 4. Validate the modle with the validation set and confirm the accuracy
```{r}
validate - predict(modrf,validation)
confusionMatrix(validate,validation$classe)
```

Confusion Matrix and Statistics

          Reference
Prediction    A    B    C    D    E
         A 2230    1    0    0    0
         B    1 1512    2    0    0
         C    0    5 1366    9    0
         D    0    0    0 1277    5
         E    1    0    0    0 1437

Overall Statistics
                                         
               Accuracy : 0.9969         
                 95% CI : (0.9955, 0.998)
    No Information Rate : 0.2845         
    P-Value [Acc > NIR] : < 2.2e-16      
                                         
                  Kappa : 0.9961         
 Mcnemar's Test P-Value : NA             

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9991   0.9960   0.9985   0.9930   0.9965
Specificity            0.9998   0.9995   0.9978   0.9992   0.9998
Pos Pred Value         0.9996   0.9980   0.9899   0.9961   0.9993
Neg Pred Value         0.9996   0.9991   0.9997   0.9986   0.9992
Prevalence             0.2845   0.1935   0.1744   0.1639   0.1838
Detection Rate         0.2842   0.1927   0.1741   0.1628   0.1832
Detection Prevalence   0.2843   0.1931   0.1759   0.1634   0.1833
Balanced Accuracy      0.9995   0.9978   0.9982   0.9961   0.9982

### 5. Predict 20 different test cases in the test set
```{r}
predTestrf - predict(modrf,testing)
print(predTestrf)
```

 [1] B A B A A E D B A A B C B A E E A B B B
Levels: A B C D E

### 6. Stop the parallel running
```{r}
stopCluster(makeCluster(detectCores()- 1))
registerDoSEQ()
```

