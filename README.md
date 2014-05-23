Getting-and-Cleaning-Data-Course-Project
========================================

## 1.Merges the training and the test sets to create one data set.

### Read in data  

    trainx<-read.table("UCI HAR Dataset/train/X_train.txt")  
    trainy<-read.table("UCI HAR Dataset/train/Y_train.txt")  
    trains<-read.table("UCI HAR Dataset/train/subject_train.txt")  
    
    testx<-read.table("UCI HAR Dataset/test/X_test.txt")  
    testy<-read.table("UCI HAR Dataset/test/Y_test.txt")  
    tests<-read.table("UCI HAR Dataset/test/subject_test.txt")  

### Column bind x, y and subject data for both train and test  

    train<-cbind(trainx, trainy, trains)  
    test<-cbind(testx, testy, tests)  

### Row bind train and test to get all of the data  

    all<-rbind(train,test)  

### Giving names to columns

    features<-read.table("UCI HAR Dataset/features.txt")  
    names(all)<-features[,2]  
    names(all)[562]<-"activity_id"  
    names(all)[563]<-"subject"  

## 2.Extracts only the measurements on the mean and standard deviation for each measurement.   
### also keep the subject and activity_id fields in addition to mean std fields.

    mean_and_std_indexes<-grepl(".*[Mm]ean.*|.*std.*|subject|activity_id",names(all))    
    mean_and_std_extract<-all[mean_and_std_indexes]  

## 3.Uses descriptive activity names to name the activities in the data set

    activity_labels<-read.table("UCI HAR Dataset/activity_labels.txt")  

### Prepare the activity_labels table by giving it column names, so that we can join using activity_id  

    names(activity_labels)<-c("activity_id","activity")  
    data_with_descriptive_activity_names<-merge(data_without_mean_and_std,activity_labels)  

### after the merge we have both the activity_id and activity columns so we get rid of activity_id column

    data_with_descriptive_activity_names<-data_with_descriptive_activity_names[,names(data_with_descriptive_activity_names)!="activity_id"]  

## 4.Appropriately labels the data set with descriptive activity names.

### Following the course video the column names satisfy the following criteria  
### All lower case when possible  
### Descriptive (Diagnosis versus Dx)  
### Not duplicated  
### Not have underscores or dots or white spaces 

### so just lowercasing is enough   

    names(data_with_descriptive_activity_names)<-tolower(names(data_with_descriptive_activity_names))  

## 5.Creates a second, independent tidy data set with the average of each variable for each activity and each subject. 

    tidy<-aggregate(. ~ activity+subject,data=data_with_descriptive_activity_names,FUN=mean)  

# Append "-mean" to the end of all variables except subject and activity  

    names(tidy)[!grepl("activity|subject",names(tidy))]<-paste0(names(tidy)[!grepl("activity|subject",names(tidy))],"-mean")
    write.table(tidy,"tidy.txt")  
