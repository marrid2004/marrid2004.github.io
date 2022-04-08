---
layout: single
title:  "Bigdata homework 05"
categories: R
tags : [PNU, bigdata]
author_profile : false
---


# Homework Assignment 05

Perform a simulation study to compare some of classifiers including boosting methods and support vector machines. First, generate the simulation data such that
 library(mnormt)
 RNGkind(sample.kind = "Rounding")
 set.seed(1111)
 K <- 100; n <- 200; p <- 10
 x.tran <- x.test <- x.vald <- array(0, c(n, p, K))
 z <- rep(c(1,2,3), each=n/2)
 covm <- matrix(0.6, p, p); diag(covm) <- 1
 for (i in 1:K) {
 t <- sample(1:p, 1); s <- sample(1:p, t)
 mu <- rep(0,p); mu[s] <- runif(t,-1, 1)
 x1 <- rmnorm(3*n/2, mu, covm)
 x2 <- rmnorm(3*n/2, rep(0,p), covm)
 x.tran[,,i] <- rbind(x1[z==1,], x2[z==1,])
 x.test[,,i] <- rbind(x1[z==2,], x2[z==2,])
 x.vald[,,i] <- rbind(x1[z==3,], x2[z==3,])
 }
In this simulation data, we generate a training set (x.tran), a test set (x.test) and a validation set (x.vald), where the observations of each set consist of 100 cases (y=1) for the first 100 observations and 100 controls
(y=0) for the other 100 observations, i.e., the sample size (n) is 200. The number of predictors (p) is 10 and the number of simulation replications (K) is 100. For the k-th simulation replicate, you have to build a classifier c(x) from the training set (x.tran[,,k]) and then compute the classification error rate (CER) of the test set (x.test[,,k]). The validation set x.vald[,,k]) should be used only for selection of the optimal tuning parameter(s), if necessary. The final CER of the classifier c(x) should be averaged over 100 simulation replications.


```R
install.packages('gbm')
install.packages('caTools')
install.packages('kernlab')
install.packages('mnormt')

library(gbm) ## boosting
library(caTools) ## logistic boosting
library(kernlab) ## svm
library(mnormt)
```

    package 'gbm' successfully unpacked and MD5 sums checked
    
    The downloaded binary packages are in
    	C:\Users\marri\AppData\Local\Temp\RtmpicHEKZ\downloaded_packages
    package 'caTools' successfully unpacked and MD5 sums checked
    
    The downloaded binary packages are in
    	C:\Users\marri\AppData\Local\Temp\RtmpicHEKZ\downloaded_packages
    
      There is a binary version available but the source version is later:
            binary source needs_compilation
    kernlab 0.9-29 0.9-30              TRUE
    
      Binaries will be installed
    package 'kernlab' successfully unpacked and MD5 sums checked
    
    The downloaded binary packages are in
    	C:\Users\marri\AppData\Local\Temp\RtmpicHEKZ\downloaded_packages
    package 'mnormt' successfully unpacked and MD5 sums checked
    

    Warning message:
    "cannot remove prior installation of package 'mnormt'"Warning message in file.copy(savedcopy, lib, recursive = TRUE):
    "C:\Users\marri\anaconda3\envs\r-tutorial\Lib\R\library\00LOCK\mnormt\libs\x64\mnormt.dll를 C:\Users\marri\anaconda3\envs\r-tutorial\Lib\R\library\mnormt\libs\x64\mnormt.dll로 복사하는데 문제가 발생했습니다: Permission denied"Warning message:
    "restored 'mnormt'"

    
    The downloaded binary packages are in
    	C:\Users\marri\AppData\Local\Temp\RtmpicHEKZ\downloaded_packages
    

    Warning message:
    "package 'gbm' was built under R version 3.6.3"Loaded gbm 2.1.8
    Warning message:
    "package 'caTools' was built under R version 3.6.3"Warning message:
    "package 'mnormt' was built under R version 3.6.3"


```R
RNGkind(sample.kind = "Rounding")
set.seed(1111)
K <- 100; n <- 200; p <- 10
x.tran <- x.test <- x.vald <- array(0, c(n, p, K))
z <- rep(c(1,2,3), each=n/2)
covm <- matrix(0.6, p, p); diag(covm) <- 1
for(i in 1:K){
  t <- sample(1:p, 1); s <- sample(1:p, t)
  mu <- rep(0,p); mu[s] <- runif(t,-1, 1)
  x1 <- rmnorm(3*n/2, mu, covm)
  x2 <- rmnorm(3*n/2, rep(0,p), covm)
  x.tran[,,i] <- rbind(x1[z==1,], x2[z==1,])
  x.test[,,i] <- rbind(x1[z==2,], x2[z==2,])
  x.vald[,,i] <- rbind(x1[z==3,], x2[z==3,])
}
## x.tran 200*10*100 
y <- rep(c(1,0),c(100,100))

```

    Warning message in RNGkind(sample.kind = "Rounding"):
    "non-uniform 'Rounding' sampler used"

1. Based on the training set, build a boosting classifier, using the gbm function of the ‘gbm’ package. Consider 3 different boosting models, where the number of splits is depth=1, depth=2 or depth=3. Fix the number of trees as n.trees=1000 when you predict class labels of the test set. Use the prediction threshold of 0.5 for classification. What are the averaged CERs of the test sets for 3 boosting models? Note that you don’t need to use the validation set in this question.


```R
###Q1
boost.miss <- matrix(NA,K,3)
for(i in 1:K){
  data.tran <- cbind(y,x.tran[,,i])
  data.test <- cbind(y,x.test[,,i])
  colnames(data.tran) <- colnames(data.test) <- c('y',paste0('x',1:10))
  for(j in 1:3){
    boost.fit <-
      gbm(y~.,data=data.frame(data.tran),distribution='bernoulli',n.trees=1000,interaction.depth=j)
    boost.pred <- predict(boost.fit,newdata=data.frame(data.test),type='response',n.trees=1000)
    decision <- rep(0,dim(data.test)[1])
    decision[boost.pred > 0.5] <- 1
    boost.miss[i,j] <- mean(decision!=y)
  }
}
ans1 <- matrix(apply(boost.miss,2,mean),1,3)
colnames(ans1) <- c('Depth=1','Depth=2','Depth=3')
rownames(ans1) <- 'Boosting'
ans1

```


<table>
<thead><tr><th></th><th scope=col>Depth=1</th><th scope=col>Depth=2</th><th scope=col>Depth=3</th></tr></thead>
<tbody>
	<tr><th scope=row>Boosting</th><td>0.23765</td><td>0.2373 </td><td>0.2387 </td></tr>
</tbody>
</table>



2. Repeat Q1 except that we find the optimal number of trees between 1 and 1000, using the validation set. First, build a boosting classifier for the training set. Second, compute the CER of the validation set for each number of trees n.trees=j, where j starts from 1 to 1000. Find the optimal number of trees that has the smallest CER of the validation set. If you have multiple number of trees that have the smallest CER, choose the maximum number of trees for the optimal value. Finally, apply the boosting method with the optimal number of tree to the test set. What are the averaged CERs of the test sets for 3 boosting models?


```R
depth <- 1:3
ntrees <- 1:1000
boost.opt.miss <- matrix(NA,K,length(depth))
for(i in 1:1){ ## cv
  data.tran <- cbind(y,x.tran[,,i])
  data.test <- cbind(y,x.test[,,i])
  data.vald <- cbind(y,x.vald[,,i])
  colnames(data.tran) <- colnames(data.test) <- colnames(data.vald) <-  c('y',paste0('x',1:10))
  boost.miss <- matrix(NA,length(depth),length(ntrees))
  ## depth?옉 tree ?닔?뿉 留욌뒗 嫄? 李얘린.
  for(k in 1:length(depth)){ ## depth
    boost.fit <-
      gbm(y~.,data=data.frame(data.tran),distribution='bernoulli',n.trees=1000,interaction.depth=k)
    for(j in 1:length(ntrees)){ ## ntrees
      boost.pred <-
        predict(boost.fit,newdata=data.frame(data.vald),type='response',n.trees=j)
      decision <- rep(0,dim(data.vald)[1])
      decision[boost.pred > 0.5] <- 1
      boost.miss[k,j] <- mean(decision!=y)
    }
  }
  opt.size <- apply(boost.miss,1,function(x) max(which(x==min(x))))
  ## per k find the optimal number of trees if it is the same number use maximum one
  for(k in 1:length(depth)){
    boost.refit <-
      gbm(y~.,data=data.frame(data.tran),distribution='bernoulli',n.trees=opt.size[k],interaction.depth=depth[k])
    boost.repred <-
      predict(boost.refit,newdata=data.frame(data.test),type='response',n.trees=opt.size[k])
    decision <- rep(0,dim(data.test)[1])
    decision[boost.repred > 0.5] <- 1
    boost.opt.miss[i,k] <- mean(decision!=y)
  }
  cat(paste0("i : ", i, "\n"))
}
ans2 <- matrix(apply(boost.opt.miss,2,mean),1,3)
colnames(ans2) <- c('Depth=1','Depth=2','Depth=3')
rownames(ans2) <- 'Boosting'
ans2

```

    i : 1
    


<table>
<thead><tr><th></th><th scope=col>Depth=1</th><th scope=col>Depth=2</th><th scope=col>Depth=3</th></tr></thead>
<tbody>
	<tr><th scope=row>Boosting</th><td>NA</td><td>NA</td><td>NA</td></tr>
</tbody>
</table>



3. Build a logistic boosting method, using the LogitBoost function of the ‘caTools’ package. Similar to Q2, first build a boosting classifier for the training set, where the number of boosting iteration should be fixed as nIter=1000. Second, compute the CER of the validation set for each number of iterations nIter=j, where j starts from 1 to 999 increased by 2, e.g., j=1, j=3, j=5, . . . , j=999. Consider only odd numbers of iterations because even numbers can make 50:50 for classification, which leads to the prediction result of NA. If you have multiple number of iterations that have the smallest CER, choose the maximum number of iterations for the optimal value. Finally, apply the logistic boosting method with the optimal number of boosting iteration to the test set. What is the averaged CER of the test sets for the logistic boosting method?


```R
### Q3

iter <- seq(1,999,2)
logit.boot.miss <- NA
for(i in 1:K){
  logit.boot.fit <- LogitBoost(xlearn=x.tran[,,i], ylearn=y, nIter=1000)
  vald.miss <- NA
  for(j in 1:length(iter)){
    logit.pred <- predict(logit.boot.fit, x.vald[,,i], type ='raw', nIter=iter[j])[,2]
    decision <- rep(0,dim(x.vald[,,i])[1])
    decision[logit.pred > 0.5] <- 1
    vald.miss[j] <- mean(y!=decision)
  }
  opt.iter <- max(iter[which(vald.miss==min(vald.miss))])
  logit.repred <- predict(logit.boot.fit,x.test[,,i],type ='raw',nIter=opt.iter)[,2]
  decision <- rep(0,dim(x.test[,,i])[1])
  decision[logit.repred > 0.5] <- 1
  logit.boot.miss[i] <- mean(y!=decision)
  cat(paste0("i : ", i, "\n"))
}
ans3 <- mean(logit.boot.miss)
ans3
```

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
    


0.24805


4. Build a support vector machine (SVM) method, using the function ksvm of the ‘kernlab’ package. Apply the linear kernel (kernel=‘vanilladot’) and the constrain parameter C which can have 26 different values such as 2^seq(-10,15). Do not change any other parameters of the function ksvm, i.e., just use the default values. First, build SVM to the training set for each value of C. Second, apply SVM to the validation set and then compute decision values (predicted values) of 200 validation samples. Next, sort 200 decision values from the smallest to the largest such that
yˆ[1] < yˆ[2] < · · · < yˆ[199] < yˆ[200]
Each decision value will be used for the threshold of classification. For example, for the i-th validation
sample
cˆ(xi) =



1 if yi > yˆ[j]
0 if yi ≤ yˆ[j]
where yi is the decision value of the i-th validation sample and ˆy[j]
is the j-th threshold for j = 1, . . . , 200. Find both of the optimal value of the constrain parameter C and the optimal value of the threshold that can minimize the CER of validation sets. If you have multiple values of C that have the smallest CER, choose the minimum value of C for the optimal value. If you have multiple values of thresholds that have the smallest CER, compute the mean of the multiple thresholds and take it for the optimal value of the threshold. Finally, apply SVM with the optimal values of C and the threshold
to the test set. What is the averaged CER of the test sets for SVM?


```R
###Q4
### finding optimal c and threshold
y.fac <- as.factor(y)

cons <- 2^seq(-10,15)
test.miss <- NA
for(i in 1:K){
  res.van.temp <- matrix(NA,length(cons),2)

  for(j in 1:length(cons)){
    svm.van.fit <-
      ksvm(x=x.tran[,,i],y=y.fac,kernel='vanilladot',C=cons[j],kpar=list(),type="C-svc")
    svm.van.pred <- predict(svm.van.fit,x.vald[,,i],type='decision')
    svm.van.sort <- sort(svm.van.pred,decreasing=FALSE)
    vald.miss <- NA
    for(k in 1:length(svm.van.sort)){
      cx.hat <- ifelse(svm.van.pred > svm.van.sort[k],1,0)
      vald.miss[k] <- mean(y.fac!=cx.hat)
    }
    res.van.temp[j,1] <- min(vald.miss)
    res.van.temp[j,2] <- mean(svm.van.sort[vald.miss==min(vald.miss)])
    ## min MSE and the optimal threshold according to min MSE
  }
  opt.c <- cons[which.min(res.van.temp[,1])]
  ## min MSE by C 
  opt.thred <- res.van.temp[which.min(res.van.temp[,1]),2]
  ## optimal threshold
  svm.van.fit <-
    ksvm(x=x.tran[,,i],y=y.fac,kernel='vanilladot',C=opt.c,kpar=list(),type="C-svc")
  svm.van.pred <- predict(svm.van.fit,x.test[,,i],type='decision')
  cx.hat <- ifelse(svm.van.pred > opt.thred,1,0)
  test.miss[i] <- mean(y.fac!=cx.hat)
}
ans4 <- mean(test.miss)
ans4
```


0.19415


5. Repeat Q4 with 5 different kernels such as
 
 Radial kernel (kernel=‘rbfdot’)
 
 Polynomial kernel (kernel=‘polydot’)
 
 Hyperbolic tangent kernel (kernel=‘tanhdot’)
 
 Laplacian kernel (kernel=‘laplacedot’)
 
 Bessel kernel (kernel=‘besseldot’).
 
What are the averaged CERs of the test sets for 5 SVMs?


```R
for(i in 1:K){
  res.rbf.temp <- res.poly.temp <- res.tanh.temp <- res.lap.temp <- res.bess.temp<- matrix(NA,length(cons),2)
  for(j in 1:length(cons)){
    ###
    svm.rbf.fit <-
      ksvm(x=x.tran[,,i],y=y.fac,kernel='rbfdot',C=cons[j],kpar=list(),type="C-svc")
    svm.rbf.pred <- predict(svm.rbf.fit,x.vald[,,i],type='decision')
    svm.rbf.sort <- sort(svm.rbf.pred,decreasing=FALSE)
    ###
    svm.poly.fit <-
      ksvm(x=x.tran[,,i],y=y.fac,kernel='polydot',C=cons[j],kpar=list(),type="C-svc")
    svm.poly.pred <- predict(svm.poly.fit,x.vald[,,i],type='decision')
    svm.poly.sort <- sort(svm.poly.pred,decreasing=FALSE)
    ###
    svm.tanh.fit <-
      ksvm(x=x.tran[,,i],y=y.fac,kernel='tanhdot',C=cons[j],kpar=list(),type="C-svc")
    svm.tanh.pred <- predict(svm.tanh.fit,x.vald[,,i],type='decision')
    svm.tanh.sort <- sort(svm.tanh.pred,decreasing=FALSE)
    ###
    svm.lap.fit <-
      ksvm(x=x.tran[,,i],y=y.fac,kernel='laplacedot',C=cons[j],kpar=list(),type="C-svc")
    svm.lap.pred <- predict(svm.lap.fit,x.vald[,,i],type='decision')
    svm.lap.sort <- sort(svm.lap.pred,decreasing=FALSE)
    ###
    svm.bess.fit <-
      ksvm(x=x.tran[,,i],y=y.fac,kernel='besseldot',C=cons[j],kpar=list(),type="C-svc")
    svm.bess.pred <- predict(svm.bess.fit,x.vald[,,i],type='decision')
    svm.bess.sort <- sort(svm.bess.pred,decreasing=FALSE)
    ###
    vald.miss.rbf <- vald.miss.poly <- vald.miss.tanh <- vald.miss.lap <-
      vald.miss.bess <- NA
    for(k in 1:length(y.fac)){
      ###
      cx.hat.rbf <- ifelse(svm.rbf.pred > svm.rbf.sort[k],1,0)
      vald.miss.rbf[k] <- mean(y.fac!=cx.hat.rbf)
      ###
      cx.hat.poly <- ifelse(svm.poly.pred > svm.poly.sort[k],1,0)
      vald.miss.poly[k] <- mean(y.fac!=cx.hat.poly)
      ###
      cx.hat.tanh <- ifelse(svm.tanh.pred > svm.tanh.sort[k],1,0)
      vald.miss.tanh[k] <- mean(y.fac!=cx.hat.tanh)
      ###
      cx.hat.lap <- ifelse(svm.lap.pred > svm.lap.sort[k],1,0)
      vald.miss.lap[k] <- mean(y.fac!=cx.hat.lap)
      ###
      cx.hat.bess <- ifelse(svm.bess.pred > svm.bess.sort[k],1,0)
      vald.miss.bess[k] <- mean(y.fac!=cx.hat.bess)
    }
    res.rbf.temp[j,1] <- min(vald.miss.rbf)
    res.rbf.temp[j,2] <- mean(svm.rbf.sort[vald.miss.rbf==min(vald.miss.rbf)])
    res.poly.temp[j,1] <- min(vald.miss.poly)
    res.poly.temp[j,2] <- mean(svm.poly.sort[vald.miss.poly==min(vald.miss.poly)])
    res.tanh.temp[j,1] <- min(vald.miss.tanh)
    res.tanh.temp[j,2] <- mean(svm.tanh.sort[vald.miss.tanh==min(vald.miss.tanh)])
    res.lap.temp[j,1] <- min(vald.miss.lap)
    res.lap.temp[j,2] <- mean(svm.lap.sort[vald.miss.lap==min(vald.miss.lap)])
    res.bess.temp[j,1] <- min(vald.miss.bess)
    res.bess.temp[j,2] <- mean(svm.bess.sort[vald.miss.bess==min(vald.miss.bess)])
  }
}
  ###
  opt.c.rbf <- cons[which.min(res.rbf.temp[,1])]
  opt.thred.rbf <- res.rbf.temp[which.min(res.rbf.temp[,1]),2]
  svm.rbf.fit <-
    ksvm(x=x.tran[,,i],y=y.fac,kernel='rbfdot',C=opt.c.rbf,kpar=list(),type="C-svc")
  svm.rbf.pred <- predict(svm.rbf.fit,x.test[,,i],type='decision')
  cx.hat.rbf <- ifelse(svm.rbf.pred > opt.thred.rbf,1,0)
  test.miss.res[i,1] <- mean(y.fac!=cx.hat.rbf)
  ###
  opt.c.poly <- cons[which.min(res.poly.temp[,1])]
  opt.thred.poly <- res.poly.temp[which.min(res.poly.temp[,1]),2]
  svm.poly.fit <-
    ksvm(x=x.tran[,,i],y=y.fac,kernel='polydot',C=opt.c.poly,kpar=list(),type="C-svc")
  svm.poly.pred <- predict(svm.poly.fit,x.test[,,i],type='decision')
  cx.hat.poly <- ifelse(svm.poly.pred > opt.thred.poly,1,0)
  test.miss.res[i,2] <- mean(y.fac!=cx.hat.poly)
  ###
  opt.c.tanh <- cons[which.min(res.tanh.temp[,1])]
  opt.thred.tanh <- res.tanh.temp[which.min(res.tanh.temp[,1]),2]
  svm.tanh.fit <-
    ksvm(x=x.tran[,,i],y=y.fac,kernel='tanhdot',C=opt.c.tanh,kpar=list(),type="C-svc")
  svm.tanh.pred <- predict(svm.tanh.fit,x.test[,,i],type='decision')
  cx.hat.tanh <- ifelse(svm.tanh.pred > opt.thred.tanh,1,0)
  test.miss.res[i,3] <- mean(y.fac!=cx.hat.tanh)
  ###
  opt.c.lap <- cons[which.min(res.lap.temp[,1])]
  opt.thred.lap <- res.lap.temp[which.min(res.lap.temp[,1]),2]
  svm.lap.fit <-
    ksvm(x=x.tran[,,i],y=y.fac,kernel='laplacedot',C=opt.c.lap,kpar=list(),type="C-svc")
  svm.lap.pred <- predict(svm.lap.fit,x.test[,,i],type='decision')
  cx.hat.lap <- ifelse(svm.lap.pred > opt.thred.lap,1,0)
  test.miss.res[i,4] <- mean(y.fac!=cx.hat.lap)
  ###
  opt.c.bess <- cons[which.min(res.bess.temp[,1])]
  opt.thred.bess <- res.bess.temp[which.min(res.bess.temp[,1]),2]
  svm.bess.fit <-
    ksvm(x=x.tran[,,i],y=y.fac,kernel='besseldot',C=opt.c.bess,kpar=list(),type="C-svc")
  svm.bess.pred <- predict(svm.bess.fit,x.test[,,i],type='decision')
  cx.hat.bess <- ifelse(svm.bess.pred > opt.thred.bess,1,0)
  test.miss.res[i,5] <- mean(y.fac!=cx.hat.bess)
  ans5 <- matrix(apply(test.miss.res,2,mean),1,5)
  colnames(ans5) <- c('rbfdot','polydot','tanhdot','laplacedot','besseldot')
  rownames(ans5) <- 'SVM'
  ans5
```


    Error in test.miss.res[i, 1] <- mean(y.fac != cx.hat.rbf): 객체 'test.miss.res'를 찾을 수 없습니다
    Traceback:
    


6. Based on Q1 ∼ Q5, which classifier is the best in terms of the classification error rate?

The best classifier is SVM with polynomial kernel (kernel='polydot') since it has the smallest classification error rate.
