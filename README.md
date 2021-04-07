# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary
**Problem Statement**
This dataset is a banking dataset, where we are seeking to classify whether or not a load is worth while pursuing, with the output being a binary Yes (Y) or No (N).  There are 21 variables including the target variable "y".  The analyis was more about using custom SCIKIT learn python scripts, vs using AutoML on the same dataset and whether on method would converge more easily.


**Solution**
The best performing model was the AutoML Voting Ensemble classifier with an accuracy of 0.9170

## Scikit-learn Pipeline
**Pipeline Architecture**
The pipeline architectre loosely followed the below order.
1. Download dataset, in this case there was a predefined URL with CSV
2. Preprocess the data.  The hyperdrive process included a custom script that allowed for pre-processing and cleaning of the data, including dropping NA's, missing data, encoding, splitting into test/train portions and then running a logistic model based on a hyperdrive parameter sampler, which is passed from the python sdk.  The accuracy of the model is measured and used as the defining metric for classification.  The hyperdrive ran through a controlled search to optimise the model through various iterations, including an early termination rule (in this case bandit). 

**What are the benefits of the parameter sampler you chose?**
Random Parameter sampling has the advantage of being quick and cost effective, particuarly for a system like MS Azure where VM space is a premium outlay.  Grid Search is another method, however it is more time consuming and resoucre ($) intensive, and if the data shows no convergence you committ a lot of resources initially to get a similar output.  Another method not explored was bayesian sampling which uses the previous model performed.  Again, this is quite resource intensive and does not support early termination.

**What are the benefits of the early stopping policy you chose?**
Bandit was chosen as it ends the run, when the primary metric isn't within the defined slack_amount at a certain interval.  Again this was chosen to limit the compute resources vs median policy where it compares against the model population median to compare whether or not to terminate.

## AutoML
**Describe the model and hyperparameters generated by AutoML.**

The AutoML model, uses the above clean_data script for preprocessing, and then is passed into a AutoML sequence for classification, using the same target metric, accuracty.  The AutoML unlike the hyperdrive, explores a multitude of model types, including XGBoost, LightGBM and RandomForests.  It also scales or normalises the data as default through some of the following : MaxAbsScalar, SparseNormalizer.

## Pipeline comparison
**Compare the two models and their performance. What are the differences in accuracy? In architecture? If there was a difference, why do you think there was one?**

They Hyperdrive using Logistic Regression scored 0.908 accuracy
They AutoML Classification scored best score was LightGBM classifier with an accuracy of 0.91142.

Some of the architectural differences in this case was that cross fold validation was used in AutoML vs Test Train only, which can increase the model learning.  Additionally, logistic regression isn't typically one of the highest performing model types.  Having a substantial amount of high performing classification engines particauraly XGBoost and other ensemble techniques will typically work better than a weak learner such as logistic classification.

## Future work
**What are some areas of improvement for future experiments? Why might these improvements help the model?**

Future work could consider grid sampling for the data network, longer time for termination checks and a larger range of input variables for the Hyperdrive.  Simiarly for the AutoML work, understanding the architecture and performance, consideration to the experiment timeout duration (i.e. make it greater than 30 minutes).  There was minor feature engineering included in the work however this could also be further explored.
