---
layout: single
title:  "Bigdata homework 04"
categories: R
tags : [PNU, bigdata]
author_profile : false
---



# Bigdata_homework_04

Open the data set College in the R package ‘ISLR’. The data information is available with ?College. All
of 17 variables except the response variable Private are used for predictors. We randomly generate 600
training samples and 177 test samples. This random generation repeats 100 times such that
 RNGkind(sample.kind = "Rounding")
 set.seed(1111)
 id <- rep(c("tr", "te"), c(600, 177))
 index <- matrix(id, length(id), 100)
 index <- apply(index, 2, sample)
In the i-th replicate, "tr" and "te" of index[,i] stand for 600 training samples and 177 test samples, respectively.



```R
install.packages('ISLR')
install.packages('MASS')
install.packages('e1071')
install.packages('tree')
install.packages('randomForest')

library(ISLR)
library(MASS) ## QDA,LDA ?궗?슜
library(e1071) ## svm
library(tree) ## tree
library(randomForest)
```

    
      There is a binary version available but the source version is later:
         binary source needs_compilation
    ISLR    1.2    1.4             FALSE
    
    

    installing the source package 'ISLR'
    
    

    
      There is a binary version available but the source version is later:
         binary source needs_compilation
    MASS 7.3-54 7.3-56              TRUE
    
      Binaries will be installed
    package 'MASS' successfully unpacked and MD5 sums checked
    

    Warning message:
    "cannot remove prior installation of package 'MASS'"Warning message in file.copy(savedcopy, lib, recursive = TRUE):
    "C:\Users\marri\anaconda3\envs\r-tutorial\Lib\R\library\00LOCK\MASS\libs\x64\MASS.dll를 C:\Users\marri\anaconda3\envs\r-tutorial\Lib\R\library\MASS\libs\x64\MASS.dll로 복사하는데 문제가 발생했습니다: Permission denied"Warning message:
    "restored 'MASS'"

    
    The downloaded binary packages are in
    	C:\Users\marri\AppData\Local\Temp\RtmpAVbxHY\downloaded_packages
    
      There is a binary version available but the source version is later:
          binary source needs_compilation
    e1071  1.7-6  1.7-9              TRUE
    
      Binaries will be installed
    package 'e1071' successfully unpacked and MD5 sums checked
    

    Warning message:
    "cannot remove prior installation of package 'e1071'"Warning message in file.copy(savedcopy, lib, recursive = TRUE):
    "C:\Users\marri\anaconda3\envs\r-tutorial\Lib\R\library\00LOCK\e1071\libs\x64\e1071.dll를 C:\Users\marri\anaconda3\envs\r-tutorial\Lib\R\library\e1071\libs\x64\e1071.dll로 복사하는데 문제가 발생했습니다: Permission denied"Warning message:
    "restored 'e1071'"

    
    The downloaded binary packages are in
    	C:\Users\marri\AppData\Local\Temp\RtmpAVbxHY\downloaded_packages
    
      There is a binary version available but the source version is later:
         binary source needs_compilation
    tree 1.0-40 1.0-41              TRUE
    
      Binaries will be installed
    package 'tree' successfully unpacked and MD5 sums checked
    
    The downloaded binary packages are in
    	C:\Users\marri\AppData\Local\Temp\RtmpAVbxHY\downloaded_packages
    

    Warning message:
    "package 'randomForest' is not available (for R version 3.6.1)"Warning message:
    "package 'MASS' was built under R version 3.6.3"Warning message:
    "package 'e1071' was built under R version 3.6.3"Warning message:
    "package 'tree' was built under R version 3.6.3"randomForest 4.6-14
    Type rfNews() to see new features/changes/bug fixes.
    


```R
data(College)
RNGkind(sample.kind = "Rounding")
set.seed(1111)
id <- rep(c("tr", "te"), c(600, 177))
index <- matrix(id, length(id), 100)
index <- apply(index, 2, sample)
rep <- dim(index)[2]
```

    Warning message in RNGkind(sample.kind = "Rounding"):
    "non-uniform 'Rounding' sampler used"

1. Develop an R function to compute accuracy (ACC), Matthews correlation coefficient (MCC), and F1 score (F1), where two input variables are predicted class labels of 177 test samples, and real class labels of the test set. e.g., Private=="Yes" or Private=="No". Note that ACC is equivalent to “1−misclassification rate” and the formula of MCC and F1 are available in your Homework Assignment3. To answer the following questions (Q2 ∼ Q6), ACC, MCC, and F1 should be first computed each replicate and then they should be averaged over 100 different replicates.


```R
###Q1
omnibus.fun <- function(pred.class,test.class){
  acc <- 1-mean(test.class[index[,i]=='te',1]!=pred.class)
  tp <- sum(pred.class[test.class[index[,i]=='te',1]=='Yes']=='Yes')
  tn <- sum(pred.class[test.class[index[,i]=='te',1]=='No']=='No')
  fp <- sum(pred.class[test.class[index[,i]=='te',1]=='No']=='Yes')
  fn <- sum(pred.class[test.class[index[,i]=='te',1]=='Yes']=='No')
  num <- tp*tn-fp*fn
  denum <- sqrt((tp+fp)*(tp+fn)*(tn+fp)*(tn+fn))
  if(denum==0){
    mcc <- 0
  }else{
    mcc <- num/denum
  }
  f1 <- 2*tp/(2*tp+fp+fn)
  res <- c(acc,mcc,f1)
  return(res)
}
```

2. For each replicate, build 3 classification models such as LDA(Linear Discriminant Analysis), QDA(Quadratic Discriminant Analysis) and NB(Naive Bayes) for the training set, and then predict the class labels of the test set. Compute averaged ACC, MCC, and F1 of the test set, using three models. (Just use a threshold of 0.5 for classification)


```R
###Q2
### lda,qda,nb ?씠?슜?빐?꽌 媛? 援ы븯?뒗 寃껋엫!!
lda.res <- qda.res <- nb.res <- matrix(NA,rep,3)
for(i in 1:rep){
  ###LDA
  lda.tran <- lda(Private~.,data=College[index[,i]=='tr',])
  lda.pred <- predict(lda.tran,College[index[,i]=='te',])$class
  lda.res[i,] <- omnibus.fun(pred.class=lda.pred,test.class=College)
  ###QDA
  qda.tran <- qda(Private~.,data=College[index[,i]=='tr',])
  qda.pred <- predict(qda.tran,College[index[,i]=='te',])$class
  qda.res[i,] <- omnibus.fun(pred.class=qda.pred,test.class=College)
  ###NB
  nb.tran <- naiveBayes(Private~.,data=College[index[,i]=='tr',])
  nb.pred <- predict(nb.tran,College[index[,i]=='te',])
  nb.res[i,] <- omnibus.fun(pred.class=nb.pred,test.class=College)
}
ans2 <- rbind(apply(lda.res,2,mean),apply(qda.res,2,mean),apply(nb.res,2,mean))
colnames(ans2) <- c('ACC','MCC','F1')
rownames(ans2) <- c('LDA','QDA','NB')
ans2

```


<table>
<thead><tr><th></th><th scope=col>ACC</th><th scope=col>MCC</th><th scope=col>F1</th></tr></thead>
<tbody>
	<tr><th scope=row>LDA</th><td>0.9368927</td><td>0.8374779</td><td>0.9572309</td></tr>
	<tr><th scope=row>QDA</th><td>0.9025989</td><td>0.7444609</td><td>0.9352927</td></tr>
	<tr><th scope=row>NB</th><td>0.9056497</td><td>0.7531308</td><td>0.9370299</td></tr>
</tbody>
</table>



3. For each replicate, build a classification tree for the training set, and then predict the class labels of the test set. Compute averaged ACC, MCC, and F1 of the test set, using the estimated tree. In this question, do not prune the tree.


```R
###Q3
tree.res <- matrix(NA,rep,3)
for(i in 1:rep){
  tree.tran <- tree(Private~.,data=College[index[,i]=='tr',])
  tree.pred <- predict(tree.tran,College[index[,i]=='te',],type='class')
  tree.res[i,] <- omnibus.fun(pred.class=tree.pred,test.class=College)
}
ans3 <- matrix(apply(tree.res,2,mean),1,3)
colnames(ans3) <- c('ACC','MCC','F1')
rownames(ans3) <- 'Tree'
ans3
```


<table>
<thead><tr><th></th><th scope=col>ACC</th><th scope=col>MCC</th><th scope=col>F1</th></tr></thead>
<tbody>
	<tr><th scope=row>Tree</th><td>0.9193785</td><td>0.7966309</td><td>0.9444246</td></tr>
</tbody>
</table>



4. For each replicate, perform 10-fold cross validation (CV) for the training set to find the optimal tree
size. You must use the following group id matrix cv.index to separate 10 groups for each replicate.
 RNGkind(sample.kind = "Rounding")
 set.seed(4444)
 cv.id <- rep(seq(10), 60)
 cv.index <- matrix(cv.id, length(cv.id), 100)
 cv.index <- apply(cv.index, 2, sample)
The function cv.tree(..., rand=cv.index[,i], FUN=prune.misclass) should be used for 10-fold CV for the i-th replicate. If you have the exactly same deviance values for different sizes of trees, you should pick up the largest tree for your optimal tree. Compute averaged ACC, MCC, and F1 of the test set, using the optimal tree.


```R
###Q4
### using cv.tree
### to use this, we should use tree to make maximum tree
### and adapt cv.tree and do the prune.misclass

RNGkind(sample.kind = "Rounding")
set.seed(4444)
cv.id <- rep(seq(10), 60)
cv.index <- matrix(cv.id, length(cv.id), 100)
cv.index <- apply(cv.index, 2, sample)
cv.tree.res <- matrix(NA,rep,3)
for(i in 1:rep){
  tree.tran <- tree(Private~.,data=College[index[,i]=='tr',])
  cvtree.tran <- cv.tree(tree.tran,rand=cv.index[,i],FUN=prune.misclass)
  w <- max(cvtree.tran$size[which(cvtree.tran$dev==min(cvtree.tran$dev))])
  prune.college <- prune.misclass(tree.tran, best=w)
  tree.pred <- predict(prune.college,College[index[,i]=='te',],type='class')
  cv.tree.res[i,] <- omnibus.fun(pred.class=tree.pred,test.class=College)
}
ans4 <- matrix(apply(cv.tree.res,2,mean),1,3)
colnames(ans4) <- c('ACC','MCC','F1'); rownames(ans4) <- 'CV Tree'
ans4
```

    Warning message in RNGkind(sample.kind = "Rounding"):
    "non-uniform 'Rounding' sampler used"


<table>
<thead><tr><th></th><th scope=col>ACC</th><th scope=col>MCC</th><th scope=col>F1</th></tr></thead>
<tbody>
	<tr><th scope=row>CV Tree</th><td>0.9189831</td><td>0.7951673</td><td>0.9442658</td></tr>
</tbody>
</table>



5. For each replicate, build a classification tree for the training set using the bagging method. For each replicate, 300 bootstrap samples for the training set can be generated by
 RNGkind(sample.kind = "Rounding")
 set.seed(5555)
 bt.index <- array(0, c(600, 300, 100))
 for (t in 1:100) {
 for (b in 1:300) {
 u <- unique(sample(1:600, replace=TRUE))
 bt.index[u, b, t] <- 1
 }
 }
For the i-th replicate, the matrix bt.index[,,i] consists of either 1 or 0 for 600 training samples and 300 bootstrap replications, where 1 stands for ‘selected’ and 0 stands for ‘not selected’ in the bootstrap replicate. The prediction of the bagging tree should follow the majority vote rule.
For example, if the jth test observation obtains at least 150 votes for Private=="Yes" among 300 classification trees (generated by the bootstrap replications), the predicted class label of the jth test observation is "Yes"; otherwise "No". Compute averaged ACC, MCC, and F1 of the test set, using the bagging tree.


```R
###Q5
### bagging method is randomforest using all predictors

RNGkind(sample.kind = "Rounding")
set.seed(5555)
bt.index <- array(0, c(600, 300, 100))
for (t in 1:100) {
  for (b in 1:300) {
    u <- unique(sample(1:600, replace=TRUE))
    bt.index[u, b, t] <- 1
  }
}

num.test <- sum(id=='te')
## number of test
bagging <- matrix(NA,rep,3)
for(i in 1:rep){
  mat.rep <- bt.index[,,i]
  ## 600*300
  bt.rep <- dim(mat.rep)[2]
  ## making 300 models
  bagging.res <- matrix(NA,bt.rep,num.test)
  ## row is the number of boosting 
  for(b in 1:bt.rep){
    College.temp <- College[index[,i]=='tr',][mat.rep[,b]==1,]
    ## using 2/3 data to do the bagging from training set
    bagging.tran <- tree(Private~.,data=College.temp)
    ## making a tree
    bagging.pred <- predict(bagging.tran,College[index[,i]=='te',],type='class')
    ## predict
    bagging.res[b,] <- bagging.pred
  }
  vote <- apply(bagging.res,2,function(x) sum(x==2))
  ## sum Yes
  decision <- rep('No',num.test)
  decision[vote >= 150] <- 'Yes'
  bagging[i,] <- omnibus.fun(pred.class=decision,test.class=College)
  ## use function 
  cat(paste0("i : ", i, "\n"))
}
ans5 <- matrix(apply(bagging,2,mean),1,3)
colnames(ans5) <- c('ACC','MCC','F1')
rownames(ans5) <- 'Bagging'
ans5
```

    Warning message in RNGkind(sample.kind = "Rounding"):
    "non-uniform 'Rounding' sampler used"

    i : 1
    i : 2
    i : 3
    i : 4
    i : 5
    i : 6
    i : 7
    i : 8
    i : 9
    i : 10
    i : 11
    i : 12
    i : 13
    i : 14
    i : 15
    i : 16
    i : 17
    i : 18
    i : 19
    i : 20
    i : 21
    i : 22
    i : 23
    i : 24
    i : 25
    i : 26
    i : 27
    i : 28
    i : 29
    i : 30
    i : 31
    i : 32
    i : 33
    i : 34
    i : 35
    i : 36
    i : 37
    i : 38
    i : 39
    i : 40
    i : 41
    i : 42
    i : 43
    i : 44
    i : 45
    i : 46
    i : 47
    i : 48
    i : 49
    i : 50
    i : 51
    i : 52
    i : 53
    i : 54
    i : 55
    i : 56
    i : 57
    i : 58
    i : 59
    i : 60
    i : 61
    i : 62
    i : 63
    i : 64
    i : 65
    i : 66
    i : 67
    i : 68
    i : 69
    i : 70
    i : 71
    i : 72
    i : 73
    i : 74
    i : 75
    i : 76
    i : 77
    i : 78
    i : 79
    i : 80
    i : 81
    i : 82
    i : 83
    i : 84
    i : 85
    i : 86
    i : 87
    i : 88
    i : 89
    i : 90
    i : 91
    i : 92
    i : 93
    i : 94
    i : 95
    i : 96
    i : 97
    i : 98
    i : 99
    i : 100
    


<table>
<thead><tr><th></th><th scope=col>ACC</th><th scope=col>MCC</th><th scope=col>F1</th></tr></thead>
<tbody>
	<tr><th scope=row>Bagging</th><td>0.9369492</td><td>0.8395196</td><td>0.9567852</td></tr>
</tbody>
</table>



6. For each replicate, build random forest for the training set where the number of predictors (m) is considered as 1, 2, 3, 4 ,5 or 6. To find the optimal value of m, perform 10-fold cross validation (CV) using cv.index in Q4. The optimal value of m should minimize an averaged misclassification rate of 10 folds. If multiple m values have the exactly same misclassification rate, you must select a smaller value of m. In order to fit random forest, use the randomForest function with ntree=1000. If you find the optimal value of m each replicate, compute ACC, MCC, and F1 of the test set using random forest with the optimal m. Finally, provide the averaged ACC, MCC, and F1 over 100 replicates.



```R
###Q6
m <- 1:6
K <- 10
rf.res <- matrix(NA,rep,3)
for(i in 1:rep){
  College.tran <- College[index[,i]=='tr',]
  ## tran
  gr <- cv.index[,i]
  rf.miss <- matrix(NA,K,length(m))
  ## 10*6 
  for(k in 1:K){
    train <- which(gr!=k)
    test <- which(gr==k)
    for(j in 1:length(m)){
      rf.model <-
        randomForest(x=College.tran[train,-1],y=College.tran[train,1],xtest=College.tran[test,-1]
                     , ytest=College.tran[test,1],mtry=m[j],importance=TRUE,ntree=1000)
      rf.conf <- rf.model$test$confusion[1:2,1:2]
      rf.miss[k,j] <- 1- sum(diag(rf.conf))/sum(rf.conf)
    }
  }
  cv.ave <- apply(rf.miss,2,mean)
  m.best <- min(m[which(cv.ave==min(cv.ave))])
  rf.refit <-
    randomForest(x=College.tran[,-1],y=College.tran[,1],xtest=College[index[,i]=='te',-1],
                 ytest=College[index[,i]=='te',1],mtry=m.best,importance=TRUE,ntree=1000)
  rf.refit.pred <- rf.refit$test$predicted
  ## if you use predicted, you can get class variables
  rf.res[i,] <- omnibus.fun(pred.class=rf.refit.pred,test.class=College)
}
ans6 <- matrix(apply(rf.res,2,mean),1,3)
colnames(ans6) <- c('ACC','MCC','F1')
rownames(ans6) <- 'RandomForest'
ans6
```


<table>
<thead><tr><th></th><th scope=col>ACC</th><th scope=col>MCC</th><th scope=col>F1</th></tr></thead>
<tbody>
	<tr><th scope=row>RandomForest</th><td>0.9402825</td><td>0.8465852</td><td>0.9593863</td></tr>
</tbody>
</table>



7. Summarize your result using the following table
ACC MCC F1
Q2 (LDA)
Q2 (QDA)
Q2 (NB)
Q3 (Tree)
Q4 (Cross-validated tree)
Q5 (Bagging)
Q6 (Random Forest)
Who is winner?
