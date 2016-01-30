# getting-and-cleaning-data-week4-assignment

setwd("C:/1. HP- Work files/HP WORK 1st Feb 2015/COURSERA_COURSES/Getting and Cleaning Data/assignment_week4/UCI HAR Dataset")

library(dplyr)

#Read train and test files:
x_train<- read.table("C:/1. HP- Work files/HP WORK 1st Feb 2015/COURSERA_COURSES/Getting and Cleaning Data/assignment_week4/UCI HAR Dataset/train/X_train.txt")

x_test<- read.table("C:/1. HP- Work files/HP WORK 1st Feb 2015/COURSERA_COURSES/Getting and Cleaning Data/assignment_week4/UCI HAR Dataset/test/X_test.txt")

y_train<- read.table("C:/1. HP- Work files/HP WORK 1st Feb 2015/COURSERA_COURSES/Getting and Cleaning Data/assignment_week4/UCI HAR Dataset/train/y_train.txt")

y_test<- read.table("C:/1. HP- Work files/HP WORK 1st Feb 2015/COURSERA_COURSES/Getting and Cleaning Data/assignment_week4/UCI HAR Dataset/test/y_test.txt")

subject_train<- read.table("C:/1. HP- Work files/HP WORK 1st Feb 2015/COURSERA_COURSES/Getting and Cleaning Data/assignment_week4/UCI HAR Dataset/train/subject_train.txt")

subject_test<- read.table("C:/1. HP- Work files/HP WORK 1st Feb 2015/COURSERA_COURSES/Getting and Cleaning Data/assignment_week4/UCI HAR Dataset/test/subject_test.txt")

dim(x_train)
dim(y_train)
dim(subject_train)
dim(x_test)
dim(y_test)
dim(subject_test)

#Read variable column names files:

features<- read.table("C:/1. HP- Work files/HP WORK 1st Feb 2015/COURSERA_COURSES/Getting and Cleaning Data/assignment_week4/UCI HAR Dataset/features.txt")

activity_labels<- read.table("C:/1. HP- Work files/HP WORK 1st Feb 2015/COURSERA_COURSES/Getting and Cleaning Data/assignment_week4/UCI HAR Dataset/activity_labels.txt")

head(features)
head(activity_labels)

################################################################

#Analysis of the Volunteers under Training dataset

colnames(activity_labels)<- c("V1", "Activity")
subject<- rename(subject_train, subject=V1)

#y_train- Merging :
train<- cbind(y_train,subject)
y_train2<- merge(train,activity_labels, by="V1")
View(y_train2)

#replace column names in x_train from features:
colnames(x_train)<- features[,2]

View(x_train)

#Combining y_train, activity_labels and x_train:
train2<- cbind(y_train2,x_train)
View(train2)
#eliminating the first column from train2 to avoid error "duplicate column name"
train3<- train2[,-1]
View(train3)

#selecting only the columns that contains means and std
train4<- select(train3,contains("subject"), contains("Activity"), contains("mean"), contains("std"))
View(train4)

################################################################

#Analysis of the Volunteers under Test dataset:

colnames(activity_labels)<- c("V1", "Activity")
subject2<- rename(subject_test, subject=V1)

#y_test- Merging :
test<- cbind(y_test,subject2)
y_test2<- merge(test,activity_labels, by="V1")
View(y_train2)

#replace column names in x_test from features:
colnames(x_test)<- features[,2]

View(x_test)

#Combining y_train, activity_labels and x_train:
test2<- cbind(y_test2,x_test)
View(test2)
#eliminating the first column from train2 to avoid error "duplicate column name"
test3<- test2[,-1]
View(test3)

#selecting only the columns that contains means and std
test4<- select(test3,contains("subject"), contains("Activity"), contains("mean"), contains("std"))
View(test4)

################################################################

#Combine Train and Test datasets:
run_analysis2<- rbind(train4,test4)

#Summarizing the data:
run_analysis<- (run_analysis2%>%
                        group_by(subject,Activity) %>%
                        summarise_each(funs( mean)))

print(run_analysis)

write.table(run_analysis,"run_analysis.txt",sep=" ",row.name=FALSE)
