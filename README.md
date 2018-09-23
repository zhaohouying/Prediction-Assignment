# Prediction-Assignment

The goal of this project is to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants to predict the manner in which they did the exercise. This is the "classe" variable in the training set. 

A validation set is sub-setted from the original training set to validate the modle built based on the training subset, with a random forest model using 54 of the predictors in the training sub-set. accuracy is above 99% for both the training sub-set and validation sub-set.

I also use your prediction model to predict 20 different test cases in the test set, with a 100% accuracy.