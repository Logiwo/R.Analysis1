# R.Analysis

Coursera Course 3 final project



##### read activity labels and features

activity_labels<-read.table("UCI HAR Dataset/activity_labels.txt")
features <- read.table("UCI HAR Dataset/features.txt")

##### read in  training sets

subject_train<-read.table("UCI HAR Dataset/train/subject_train.txt")
x_train <- read.table("UCI HAR Dataset/train/x_train.txt")
y_train <-read.table("UCI HAR Dataset/train/y_train.txt")

##### read in  test sets

subject_test<-read.table("UCI HAR Dataset/test/subject_test.txt")
x_test <- read.table("UCI HAR Dataset/test/x_test.txt")
y_test <-read.table("UCI HAR Dataset/test/y_test.txt")


##### add colnames

colnames(x_train) <- features[,2] 
colnames(y_train) <-"activityId"
colnames(subject_train) <- "subjectId"

colnames(x_test) <- features[,2] 
colnames(y_test) <- "activityId"
colnames(subject_test) <- "subjectId"

colnames(activity_labels) <- c("activityId","activityType")


#### merging data together

train <- cbind(subject_train, x_train, y_train)
test <- cbind(subject_test, x_test, y_test)
train_test <- rbind(train, test)


#####Extracts only the measurements on the mean and standard deviation for each measurement.

cols <- colnames(train_test)

  mean_std <- (grepl("activityId" , cols) | 
                     grepl("subjectId" , cols) | 
                     grepl("mean.." , cols) | 
                     grepl("std.." , cols) 
  )


train_test_mean_std<- train_test[ , mean_std == TRUE]
 
# add activity names 
train_test_mean_std_activity_names <- merge(train_test_mean_std, activity_labels,
                              by='activityId',
                              all.x=TRUE)


View(train_test_mean_std_activity_names)

clean_data <- aggregate(. ~subjectId + activityId, train_test_mean_std_activity_names, mean)
clean_data <- clean_data[order(clean_data$subjectId, clean_data$activityId),]


# save file
write.table(clean_data, "clean_data.txt", row.name=FALSE)



