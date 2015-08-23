# Qualitative Activity Recognition
Victor Herrera  
August 23, 2015  
##Data load and clean

The training data for this project are pml-training.csv available here: https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv and the test data are pml-testing.csv available here: https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv. The data for this project come from this source: http://groupware.les.inf.puc-rio.br/har.


```r
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```r
library(randomForest)
```

```
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

```r
traindf <- read.csv(file = "pml-training.csv", header = TRUE)
selfeat <- read.csv(file = "sel.csv", header = TRUE)
selfeat$feat
```

```
##  [1]   7   8   9  10  11  37  38  39  40  41  42  43  44  45  46  47  48
## [18]  49  60  61  62  63  64  65  66  67  68  84  85  86 102 113 114 115
## [35] 116 117 118 119 120 121 122 123 124 140 151 152 153 154 155 156 157
## [52] 158 159 160
```

##Training model

```r
featseldf <- traindf[traindf$new_window=="no", selfeat$feat]
trainIndex <- createDataPartition(featseldf$classe, p= .5, list = FALSE)
trainData <- featseldf[trainIndex,]
testData <- featseldf[-trainIndex,]
rf1 <- randomForest(classe ~., trainData, ntree = 25)
rf1
```

```
## 
## Call:
##  randomForest(formula = classe ~ ., data = trainData, ntree = 25) 
##                Type of random forest: classification
##                      Number of trees: 25
## No. of variables tried at each split: 7
## 
##         OOB estimate of  error rate: 1.22%
## Confusion matrix:
##      A    B    C    D    E  class.error
## A 2734    2    0    0    0 0.0007309942
## B   14 1819   19    4    3 0.0215169446
## C    1   22 1646    6    1 0.0178997613
## D    3    1   20 1546    4 0.0177890724
## E    1    5    3    8 1747 0.0096371882
```

##Prediction

```r
topredictdf <- read.csv(file = "pml-testing.csv", header = TRUE)
res <- predict(rf1, newdata = topredictdf)
res
```

```
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  A  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E
```
## Write Up

```r
pml_write_files = function(x){
  n = length(x)
  for(i in 1:n){
    filename = paste0("problem_id_",i,".txt")
    write.table(x[i],file=filename,quote=FALSE,row.names=FALSE,col.names=FALSE)
  }
}
pml_write_files(res)
```
