---
title: "CodeBook"
author: "Alyssa Weinstein"
date: "February 2, 2017"
output: html_document
---


##Getting and Cleaning Data Course Project

This is an R Markdown document to describe the variables, data, summaries, units, and transformations done to complete the run_analysis.R script.

#IMPORTANT Variables w/ units: 
  "all"  - a combined and labeled dataset of all testing and training data organized by Subject Id and Activity ID (10299 obs x 563 vars)
  "subjectAvg" -  a second combined data set that averages (means) EACH activity (walking, running,standing etc) for EACH subject (1-30)

# Part 1:

 Create data set to tidy from the "Human Activity Recognition Using Smartphones Dataset V 1.0" in the UCI HAR Dataset:
 https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

 read training, testing, and naming files into tables
```
testSet <- read.table("/Users/aweinste/Downloads/UCI HAR Dataset/test/X_test.txt")
testActLabel <- read.table("/Users/aweinste/Downloads/UCI HAR Dataset/test/y_test.txt")
testSubject <- read.table("/Users/aweinste/Downloads/UCI HAR Dataset/test/subject_test.txt")

trainSet <- read.table("/Users/aweinste/Downloads/UCI HAR Dataset/train/X_train.txt")
trainActLabel <- read.table("/Users/aweinste/Downloads/UCI HAR Dataset/train/y_train.txt")
trainSubject <- read.table("/Users/aweinste/Downloads/UCI HAR Dataset/train/subject_train.txt")
```
Then I loaded activity labels 
```
ActLabel <- read.table("/Users/aweinste/Downloads/UCI HAR Dataset/activity_labels.txt")
```
Then I loaded the "features" list , which become column names for the data set
```
features <- read.table("/Users/aweinste/Downloads/UCI HAR Dataset/features.txt")
```
Then I combined  data to form a data table for the testing and training data
```
test <- cbind(testSubject, testActLabel, testSet)
train <- cbind(trainSubject, trainActLabel, trainSet)
```
Then I combined the test and the training data for ALL THE DATA STORAGE 
```
all <- rbind(test,train)
```
#Part 4: 
(labelling now makes things so much easier), added labels to the columns in "All", including the features from the features.txt file which needs to be turned into a list before it can be added as a column name.
```
colnames(all) <- c("SubjectID", "ActivityID", as.list(levels(features[,2])))
colnames(copyall) <- c("SubjectID", "ActivityID", as.list(levels(features[,2])))
```
#Part 2:
Extract a data set for *just* the measurements of mean (means) and standard deviation (stds)
```
means <- all[,grep("*mean",colnames(all))]
stds <- all[,grep("*std",colnames(all))]
```
make sure to add the labels for the rows ("Subject ID" & "Activity ID")
```
labels <- all[,c(1,2)]
```
combine it
```
meanAndStd <- cbind(labels, means,stds)
```
#Part 3:
Use descriptive activity names to name the activities in the data set
```
ActLabel <- as.character(ActLabel[,2]) # get just the names of the labels as characters

# now append them to the dataset in the Activity ID column. I could have used a loop -- but f it. this works.

all$ActivityID[all$ActivityID == "1"] <- ActLabel[1];
all$ActivityID[all$ActivityID == "2"] <- ActLabel[2];
all$ActivityID[all$ActivityID == "3"] <- ActLabel[3];
all$ActivityID[all$ActivityID == "4"] <- ActLabel[4];
all$ActivityID[all$ActivityID == "5"] <- ActLabel[5];
all$ActivityID[all$ActivityID == "6"] <- ActLabel[6];
```
# Part 5:
Create a second data set that averages (mean) EACH variable for EACH subject (1-30)
```
all$SubjectID <- as.factor(all$SubjectID)
all$ActivityID <- as.factor(all$ActivityID)
```
melt the data to create a castable image of the dataset
```
melted <- melt(all, ids = c("SubjectID", "ActivityID"))
```
Cast the data frame into determining the mean by subjectid and activity id together
```
subjectAvg <- dcast(melted, SubjectID + ActivityID ~ variable, mean)
```
 
# DONE
