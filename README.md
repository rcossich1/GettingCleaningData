# GettingCleaningData
Getting and cleaning data course project 
This repo has been created to complete the course Coursera/Getting and clean data's final project
The Codebook.md file includes the description of the variables, description of the data and transformation done 
to generate final "tidy_data.csv" file that includes the outcome of the attached script in R

run_analysis.R code runs to accomplish:
1. Merges the training and the test sets to create one data set.
2. Extracts only the measurements on the mean and standard deviation for each measurement.
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive variable names.
5. From the data set in step 4, creates a second, independent tidy data set with 
the average of each variable for each activity and each subject.

## libraries

library(plyr)

library(dplyr)

library(utils)

library(tibble)

## reading tables for data set for train and test; and feature names to name columns
features <- read.table(file="./UCI HAR Dataset/features.txt", header = FALSE, col.names = c("index","feature_name"))

X_train <- read.table(file="./UCI HAR Dataset/train/X_train.txt", header = FALSE)

X_test <- read.table(file="./UCI HAR Dataset/test/X_test.txt", header = FALSE)

## 4. Step done in advance as to keep work simple
## naming columns in training and test data sets
names(X_train) <- features$feature_name

names(X_test) <- features$feature_name

## 1. Merging data sets' rows
X_all<- rbind(X_train,X_test)

## 2.Extracting the mean() and standard deviation variables from the data set
X_all_meanstd <- select(X_all,grep("mean|std",names(X_all)))

## 3. Reading activity set and code for descriptive naming
activity <- read.table(file="./UCI HAR Dataset/activity_labels.txt", header = FALSE, col.names = c("act_code","activity"))

y_train <- read.table(file="./UCI HAR Dataset/train/y_train.txt")

y_test <- read.table(file="./UCI HAR Dataset/test/y_test.txt")

y_all <- rbind(y_train,y_test) ## merging rows

y_all<- mutate(y_all, activity=activity[y_all[,],2]) ## generating descriptive column

## Adding descriptive naming 
X_all_meanstd <- mutate(X_all_meanstd,activity=y_all$activity)

## 4. descriptive variable names  done at the beginning

## 5. Split by activity and subject who carried out the experiment
## Adding subject to data set
subject_train <- read.table(file="./UCI HAR Dataset/train/subject_train.txt", header = FALSE, col.names="subject")

subject_test <- read.table(file="./UCI HAR Dataset/test/subject_test.txt", header = FALSE, col.names="subject")


subject_all <- rbind(subject_train, subject_test)
## adding subject variable
X_all_meanstd <- mutate(X_all_meanstd,subject=subject_all$subject)

## Converting, grouping and creating the tidy df
all_df <- as_tibble(X_all_meanstd)

all_df <- group_by(all_df, activity, subject)

tidy_data <- summarise_each(all_df,funs = mean)

write.csv(tidy_data, file = "tidy_data.csv")

