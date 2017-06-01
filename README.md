#Make sure all data files are copied to c:/own/tidydata2
#The followind datafiles are used:
#subject_test.txt
#y_test.txt
#X_test.txt
#subject_train.txt
#y_train.txt
#X_train.txt
#features.txt
#activity_labels.txt
setwd("c:\own\tidydata")
#Load the 3 data files with the test data
subject_test <- read.table("subject_test.txt")
y_test <- read.table("y_test.txt")
X_test <- read.table("X_test.txt")
#Combine the 3 datasets into 1 test dataset
test <- cbind(subject_test,y_test,X_test)
#Load the 3 data files with the train data
subject_train <- read.table("subject_train.txt")
y_train <- read.table("y_train.txt")
X_train <- read.table("X_train.txt")
#Combine the 3 datasets into 1 train dataset
train <- cbind(subject_train,y_train,X_train)
#Merge the test and train datasets
full <- rbind(test,train)
#Load the features file to get the 561 variable names
features <- read.table("features.txt")
#Transform the features data frame to a factor that only has the variable names
x <- features[,2]
#Add 2 additional values for Subject and Activity to the variable names 
full_label <- c("SUBJ","ACT",as.character(x))
#Create column names for the merged test and train dataset
colnames(full) <- full_label
#Load the activity_labels file
activity_labels <- read.table("activity_labels.txt")
#Assign column names to the activity_labels dataset
colnames(activity_labels) <- c("ACTIV_CODE","ACTIVITY")
#Joins the full dataset to the activity_labels dataset to include the activity descriptions to each row
t1 <- merge(activity_labels,full,by.x="ACTIV_CODE",by.y="ACT")
#Identify the mean and standard deviation variables using grep("mean()|std()",names(t1))
#Create a new dataset that contains only the activity, subject and mean and standard dev variables
t2 <- t1[,c(1,2,grep("mean()|std()",full_label))]
#Order the narrow dataset by Activity and Subject
t2 <- t2[ order(t2$ACTIVITY, t2$SUBJ), ]
#Create a new dataset with the mean of each variable calculated per Activity and Subject
tidydata2 <- aggregate(t2[c(4:ncol(t2))],by=t2[c(2,3)],FUN=mean)
#Write the new dataset to a file - tidydata.txt
write.table(tidydata2,file="tidydata.csv",sep=",",row.name=FALSE)