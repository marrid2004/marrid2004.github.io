---
layout: single
title:  "Bigdata homework 02"
categories: R
tags : [PNU, bigdata]
author_profile : false
---


# Homework Assignment 02

Open the data set ‘Wage’ in the R package ‘ISLR’. The data information is available with ?Wage. The
variable logwage is considered as a response variable and other 8 variables including ‘year’, ‘age’, ‘maritl’,
‘race’, ‘education’, ‘jobclass’, ‘health’, and ‘health ins’ are considered as predictors. Since there are 8
predictors, a total of 28 − 1 = 255 regression models should be considered


```R
install.packages('ISLR')
library(ISLR)
data(Wage)
wage <- Wage[,-c(6,11)]
dim(wage)
y <- wage[,9]
dim(wage)
head(wage)
grid <- expand.grid(rep(list(c(0,1)),8))[-1,]

```

    
      There is a binary version available but the source version is later:
         binary source needs_compilation
    ISLR    1.2    1.4             FALSE
    
    

    installing the source package 'ISLR'
    
    


<ol class=list-inline>
	<li>3000</li>
	<li>9</li>
</ol>




<ol class=list-inline>
	<li>3000</li>
	<li>9</li>
</ol>




<table>
<thead><tr><th></th><th scope=col>year</th><th scope=col>age</th><th scope=col>maritl</th><th scope=col>race</th><th scope=col>education</th><th scope=col>jobclass</th><th scope=col>health</th><th scope=col>health_ins</th><th scope=col>logwage</th></tr></thead>
<tbody>
	<tr><th scope=row>231655</th><td>2006                                                      </td><td>18                                                        </td><td>1. Never Married                                          </td><td>1. White                                                  </td><td><span style=white-space:pre-wrap>1. &lt; HS Grad   </span></td><td>1. Industrial                                             </td><td><span style=white-space:pre-wrap>1. &lt;=Good     </span> </td><td>2. No                                                     </td><td>4.318063                                                  </td></tr>
	<tr><th scope=row>86582</th><td>2004             </td><td>24               </td><td>1. Never Married </td><td>1. White         </td><td>4. College Grad  </td><td>2. Information   </td><td>2. &gt;=Very Good</td><td>2. No            </td><td>4.255273         </td></tr>
	<tr><th scope=row>161300</th><td>2003                                                     </td><td>45                                                       </td><td><span style=white-space:pre-wrap>2. Married      </span> </td><td>1. White                                                 </td><td>3. Some College                                          </td><td>1. Industrial                                            </td><td><span style=white-space:pre-wrap>1. &lt;=Good     </span></td><td>1. Yes                                                   </td><td>4.875061                                                 </td></tr>
	<tr><th scope=row>155159</th><td>2003                                                    </td><td>43                                                      </td><td><span style=white-space:pre-wrap>2. Married      </span></td><td>3. Asian                                                </td><td>4. College Grad                                         </td><td>2. Information                                          </td><td>2. &gt;=Very Good                                       </td><td>1. Yes                                                  </td><td>5.041393                                                </td></tr>
	<tr><th scope=row>11443</th><td>2005                                                     </td><td>50                                                       </td><td><span style=white-space:pre-wrap>4. Divorced     </span> </td><td>1. White                                                 </td><td><span style=white-space:pre-wrap>2. HS Grad     </span>  </td><td>2. Information                                           </td><td><span style=white-space:pre-wrap>1. &lt;=Good     </span></td><td>1. Yes                                                   </td><td>4.318063                                                 </td></tr>
	<tr><th scope=row>376662</th><td>2008                                                    </td><td>54                                                      </td><td><span style=white-space:pre-wrap>2. Married      </span></td><td>1. White                                                </td><td>4. College Grad                                         </td><td>2. Information                                          </td><td>2. &gt;=Very Good                                       </td><td>1. Yes                                                  </td><td>4.845098                                                </td></tr>
</tbody>
</table>



1. Let us define AIC (Akaike information criterion) and BIC (Bayesian information criterion) as
AIC = n log 
1
n
Xn
i=1
(yi − ˆf(xi))2
!
+ 2d, and BIC = n log 
1
n
Xn
i=1
(yi − ˆf(xi))2
!
+ d log(n),
respectively. ˆf(·) is the estimated linear regression model and d is the number of regression coefficients in the model. For example, if 8 predictors are all included in the model, d = 17 due to dummy variables. 
Next, separate individuals into two groups based on their logwage value; group 1 is for median(logwage) and group 2 is for ≤ median(logwage). Note that the sample size n should be regarded as a group size when AIC(BIC) is computed. For each group, find the best model among 255 models in terms of AIC and BIC, and provide the numerical values of AIC and BIC of the best models.


```R
grp1 <- (y > median(y))
grp2 <- !grp1
grp1.size <- sum(grp1)
grp2.size <- sum(grp2)
grp1.aic <- grp1.bic <- grp2.aic <- grp2.bic <- 0
for(i in 1:dim(grid)[1]){
  wage.data <- wage[,c(grid[i,] %in% 1,TRUE)]
  grp1.lm <- lm(logwage~.,data=wage.data,subset=grp1)
  grp1.mse <- mean((y[grp1]- grp1.lm$fitted)^2)
  grp1.aic[i] <- grp1.size*log(grp1.mse)+2*length(coef(grp1.lm))
  grp1.bic[i] <- grp1.size*log(grp1.mse)+length(coef(grp1.lm))*log(grp1.size)
  grp2.lm <- lm(logwage~.,data=wage.data,subset=grp2)
  grp2.mse <- mean((y[grp2]- grp2.lm$fitted)^2)
  grp2.aic[i] <- grp2.size*log(grp2.mse)+2*length(coef(grp2.lm))
  grp2.bic[i] <- grp2.size*log(grp2.mse)+length(coef(grp2.lm))*log(grp2.size)
}

grp1.aic.pos <- which(grp1.aic==min(grp1.aic))
grp1.aic.min <- min(grp1.aic)
grp1.bic.pos <- which(grp1.bic==min(grp1.bic))
grp1.bic.min <- min(grp1.bic)
grp1.aic.best <- grid[grp1.aic.pos,]
grp1.bic.best <- grid[grp1.bic.pos,]

grp1.aic.ans <- cbind(grp1.aic.best,grp1.aic.min)
grp1.bic.ans <- cbind(grp1.bic.best,grp1.bic.min)

colnames(grp1.aic.ans) <- c(colnames(wage)[-9],'AIC')
colnames(grp1.bic.ans) <- c(colnames(wage)[-9],'BIC')

grp2.aic.pos <- which(grp2.aic==min(grp2.aic))
grp2.aic.min <- min(grp2.aic)
grp2.bic.pos <- which(grp2.bic==min(grp2.bic))
grp2.bic.min <- min(grp2.bic)
grp2.aic.best <- grid[grp2.aic.pos,]
grp2.bic.best <- grid[grp2.bic.pos,]
grp2.aic.ans <- cbind(grp2.aic.best,grp2.aic.min)
grp2.bic.ans <- cbind(grp2.bic.best,grp2.bic.min)
colnames(grp2.aic.ans) <- c(colnames(wage)[-9],'AIC')
colnames(grp2.bic.ans) <- c(colnames(wage)[-9],'BIC')

grp1.aic.ans
grp1.bic.ans
grp2.aic.ans
grp2.bic.ans
```


<table>
<thead><tr><th></th><th scope=col>year</th><th scope=col>age</th><th scope=col>maritl</th><th scope=col>race</th><th scope=col>education</th><th scope=col>jobclass</th><th scope=col>health</th><th scope=col>health_ins</th><th scope=col>AIC</th></tr></thead>
<tbody>
	<tr><th scope=row>215</th><td>0       </td><td>1       </td><td>1       </td><td>0       </td><td>1       </td><td>0       </td><td>1       </td><td>1       </td><td>-4664.77</td></tr>
</tbody>
</table>




<table>
<thead><tr><th></th><th scope=col>year</th><th scope=col>age</th><th scope=col>maritl</th><th scope=col>race</th><th scope=col>education</th><th scope=col>jobclass</th><th scope=col>health</th><th scope=col>health_ins</th><th scope=col>BIC</th></tr></thead>
<tbody>
	<tr><th scope=row>83</th><td>0        </td><td>1        </td><td>0        </td><td>0        </td><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>-4605.513</td></tr>
</tbody>
</table>




<table>
<thead><tr><th></th><th scope=col>year</th><th scope=col>age</th><th scope=col>maritl</th><th scope=col>race</th><th scope=col>education</th><th scope=col>jobclass</th><th scope=col>health</th><th scope=col>health_ins</th><th scope=col>AIC</th></tr></thead>
<tbody>
	<tr><th scope=row>216</th><td>1        </td><td>1        </td><td>1        </td><td>0        </td><td>1        </td><td>0        </td><td>1        </td><td>1        </td><td>-4609.387</td></tr>
</tbody>
</table>




<table>
<thead><tr><th></th><th scope=col>year</th><th scope=col>age</th><th scope=col>maritl</th><th scope=col>race</th><th scope=col>education</th><th scope=col>jobclass</th><th scope=col>health</th><th scope=col>health_ins</th><th scope=col>BIC</th></tr></thead>
<tbody>
	<tr><th scope=row>196</th><td>1       </td><td>1       </td><td>0       </td><td>0       </td><td>0       </td><td>0       </td><td>1       </td><td>1       </td><td>-4550.84</td></tr>
</tbody>
</table>



2. Perform 10-fold cross validation to find the best model in terms of prediction error (PE), which can
be defined as
PE =
vuut
1
m
Xm
i=1

yi − ˆf(xi)
2
,
where ˆf(·) is the estimated linear regression using the training set and m is the size of the test set.
Use the following R code to generate 10 folds.
RNGkind(sample.kind = "Rounding")
set.seed(111)
u1 <- sample(rep(seq(10), length=sum(Wage$logwage>median(Wage$logwage))))
u2 <- sample(rep(seq(10), length=sum(Wage$logwage<=median(Wage$logwage))))
Note that u1 is for group 1 and u2 is for group 2. For each group, two best models should be determined based on the minimum PE (minPE) and one-standard-error rule (1SE), respectively. 
Therefore, your result of group 1 can be summarized as In the table, write ‘1’ if the corresponding predictor is included in the model, otherwise ‘0’. In the one-standard-error rule, the best model has both the smallest model size (i.e., the smallest number of predictors) and the smallest PE among all models within one standard error boundary. You have to provide a table for group 2.


```R
##### question 2
##### PE one standard error rule 
RNGkind(sample.kind = "Rounding")
set.seed(111)
u1 <- sample(rep(seq(10), length=sum(Wage$logwage>median(Wage$logwage))))
u2 <- sample(rep(seq(10), length=sum(Wage$logwage<=median(Wage$logwage))))
K <- 10
grp1.pe <- grp2.pe <- matrix(NA,nrow(grid),K)

for(k in 1:K){
  grp1.tran <- which(u1!=k)
  grp1.test <- which(u1==k)
  grp2.tran <- which(u2!=k)
  grp2.test <- which(u2==k)
  for(i in 1:nrow(grid)){
    #group 1
    grp1.wage <- wage[grp1,c(grid[i,] %in% 1,TRUE)]
    grp1.lm <- lm(logwage~.,data=grp1.wage,subset=grp1.tran)
    grp1.pred <- predict(grp1.lm,grp1.wage)
    grp1.pe[i,k] <- sqrt(mean((y[grp1]-grp1.pred)[grp1.test]^2))
    #group 2
    grp2.wage <- wage[grp2,c(grid[i,] %in% 1,TRUE)]
    grp2.lm <- lm(logwage~.,data=grp2.wage,subset=grp2.tran)
    grp2.pred <- predict(grp2.lm,grp2.wage)
    grp2.pe[i,k] <- sqrt(mean((y[grp2]-grp2.pred)[grp2.test]^2))
  }
}
grp1.ave.pe <- apply(grp1.pe,1,mean)
grp1.min.pe <- min(grp1.ave.pe)
grp1.min.pos <- which(grp1.ave.pe==min(grp1.ave.pe))
grp1.min.model <- c(grid[grp1.min.pos,],grp1.min.pe)
```

    Warning message in RNGkind(sample.kind = "Rounding"):
    "non-uniform 'Rounding' sampler used"


```R
## one-standard error rule 
grp1.se.pe <- apply(grp1.pe,1,sd)
grp1.add.pe <- grp1.ave.pe + grp1.se.pe
grp1.1se.temp <- which(grp1.ave.pe < grp1.add.pe[grp1.min.pos])
grp1.1se.num <- apply(grid[grp1.1se.temp,],1,sum)
grp1.1se.pe <- grp1.ave.pe[grp1.1se.temp[which(grp1.1se.num==min(grp1.1se.num))]]
grp1.1se.model <-
  c(grid[grp1.1se.temp[which(grp1.1se.num==min(grp1.1se.num))],],grp1.1se.pe)
grp1.ans <- rbind(grp1.min.model,grp1.1se.model)
colnames(grp1.ans) <- c(colnames(wage)[-9],'PE')
rownames(grp1.ans) <- c('minPE','1SE')

grp2.ave.pe <- apply(grp2.pe,1,mean)
grp2.min.pe <- min(grp2.ave.pe)
grp2.min.pos <- which(grp2.ave.pe==min(grp2.ave.pe))
grp2.min.model <- c(grid[grp2.min.pos,],grp2.min.pe)
grp2.se.pe <- apply(grp2.pe,1,sd)
grp2.add.pe <- grp2.ave.pe + grp2.se.pe
grp2.1se.temp <- which(grp2.ave.pe < grp2.add.pe[grp2.min.pos])
grp2.1se.num <- apply(grid[grp2.1se.temp,],1,sum)

grp2.1se.pe <- grp2.ave.pe[grp2.1se.temp[which(grp2.1se.num==min(grp2.1se.num))]]
grp2.1se.sm.pe <- min(grp2.1se.pe)
grp2.1se.sm.pos <- which.min(grp2.1se.pe)
grp2.1se.pos <- which(grp2.1se.num==min(grp2.1se.num))[grp2.1se.sm.pos]
grp2.1se.model <- c(grid[grp2.1se.pos,],grp2.1se.sm.pe)
grp2.ans <- rbind(grp2.min.model,grp2.1se.model)
colnames(grp2.ans) <- c(colnames(wage)[-9],'PE')
rownames(grp2.ans) <- c('minPE','1SE')
```

Open the data set ‘NCI60’ in the R package ‘ISLR’. The data information is available with ?NCI60. The gene
expression data consists of 64 samples for 6,830 genes, where we assume that the first 50 genes are only
relevant. That is, only 50 among 6,830 genes are associated with a response outcome. Type the following R
codes to generate x and y with 300 lambda values and foldid for 5-fold cross validation.
data(NCI60)
x <- NCI60$data
RNGkind(sample.kind = "Rounding")
set.seed(123)
beta <- rep(0, ncol(x))
beta[1:50] <- runif(50, -2, 2)
y <- x %*% beta + rnorm(nrow(x))
lambda <- 10^seq(2, -2, length=300)
foldid <- sample(rep(seq(5), length=length(y)))
For the 5-fold cross validation, you must use lambda and foldid to answer the following questions.


3. Apply the elastic-net for variable selection, where the tuning parameter α starts from 0 to 1 increased by 0.05. Perform the 5-fold cross validation to find out two optimal lambda values: λˆmin for the smallest prediction error and λˆ 1se for the one-standard-error rule. For computation of prediction errors, the default value of the glmnet package (mean squared errors) shoule be used. What is the optimal value of ˆα that minimizes the prediction error? Provide the numerical values of λˆmin and λˆ 1se for the corresponding ˆα. How many variables are selected by λˆmin and λˆ 1se, respectively?


```R
######### question 3
library(glmnet)
data(NCI60)
x <- NCI60$data

RNGkind(sample.kind = "Rounding")
set.seed(123)
beta <- rep(0, ncol(x))
beta[1:50] <- runif(50, -2, 2)
y <- x %*% beta + rnorm(nrow(x))
colnames(x) <- paste0('G',1:6830)
lambda <- 10^seq(2, -2, length=300)
foldid <- sample(rep(seq(5), length=length(y)))

alpha <- seq(0,1,0.05)
cvm.q3 <- NA
lam.mat <- matrix(NA,2,length(alpha))
for(i in 1:length(alpha)){
  cv.fit.q3 <-
    cv.glmnet(x,y,type.measure='mse',alpha=alpha[i],lambda=lambda,foldid=foldid)
  
  cvm.q3[i] <- min(cv.fit.q3$cvm)
  lam.mat[1,i] <- cv.fit.q3$lambda.min
  lam.mat[2,i] <- cv.fit.q3$lambda.1se
}
alpha.pos <- which(cvm.q3==min(cvm.q3))
alpha.hat <- alpha[alpha.pos]
lambda.min.hat <- lam.mat[1,alpha.pos]
lambda.1se.hat <- lam.mat[2,alpha.pos]

cv.fit.q3 <-
  cv.glmnet(x,y,type.measure='mse',alpha=alpha.hat,lambda=lambda,foldid=foldid)
q3.min.coef.num <- sum(coef(cv.fit.q3, s = 'lambda.min')!=0)-1
q3.1se.coef.num <- sum(coef(cv.fit.q3, s = 'lambda.1se')!=0)-1

alpha.hat
lambda.min.hat
lambda.1se.hat
q3.min.coef.num
q3.1se.coef.num
```

    Loading required package: Matrix
    Loading required package: foreach
    Loaded glmnet 2.0-16
    
    Warning message in RNGkind(sample.kind = "Rounding"):
    "non-uniform 'Rounding' sampler used"


0.65



1.08005237451625



2.48112605513777



28



12


4. In the 5-fold cross validation, let us denote the number of training samples and the number of test samples by ntr and nte, respectively. Suppose that we select q variables from training samples, i.e., only q + 1 regression coefficients including the intercept paramter are not zeros. Then, we newly define the prediction error as
PEnew =
vuuut
1
nte
Xnte
i=1

yi − βˆls
0 −
Xq0
j=1
xijβˆls
j


2
,
where q0 = min(q, ntr − 1) and βˆls j is the ordinary least square estimate for the jth selected variable. For each λ value, first find q variables that have nonzero regression coefficients and then compute the ordinary least square estimate for the correponding q variables. Note that the ordinary least square esitmate cannot be computed when q ≥ ntr, so we only select the first ntr − 1 variables in this case. Your optimal λˆmin and λˆ 1se should be determined based on PEnew of the 5-fold cross validation. For lasso (α = 1), find the numerical values of λˆmin and λˆ 1se, and provide the number ofelected variables for the corresponding λˆmin and λˆ 1se, respectively.


```R
###Q4
### q0 vs q and then lm
### using lasso, pick betas and do the lm if beta!=0
K <- 5
lambda.num <- length(lambda)
pe.new <- matrix(NA,K,lambda.num)

for(k in 1:K){
  tran <- which(foldid!=k)
  test <- which(foldid==k)
  fit.q4 <- glmnet(x[tran,],y[tran],alpha=1,lambda=lambda,family='gaussian')
  for(i in 1:lambda.num){
    if(sum(fit.q4$beta[,i]!=0)!=0){
      gene.pos <- which(fit.q4$beta[,i]!=0)
      q <- length(gene.pos)
      q0 <- min(q,length(tran)-1)
      lm.fit <- lm(y~x[,gene.pos[1:q0]],subset=tran)
      lm.pred <- predict(lm.fit,data.frame(x[,gene.pos[1:q0]]))
      pe.new[k,i] <- sqrt(mean((y-lm.pred)[test]^2))
    }else{
      beta0 <- mean(y[tran])
      pe.new[k,i] <- sqrt(mean((y[test]-beta0)^2))
    }
  }
}
pe.new.ave <- apply(pe.new,2,mean)
lambda.min <- lambda[which(pe.new.ave==min(pe.new.ave))]
pe.new.sd <- apply(pe.new,2,sd)
pe.new.up <- pe.new.ave+pe.new.sd
lambda.1se <- lambda[min(which(pe.new.ave <
                                 pe.new.up[which(pe.new.ave==min(pe.new.ave))]))]
fit.min <- glmnet(x,y,alpha=1,lambda=lambda.min,family='gaussian')
num.min <- sum(fit.min$beta!=0)
fit.1se <- glmnet(x,y,alpha=1,lambda=lambda.1se,family='gaussian')
num.1se <- sum(fit.1se$beta!=0)

lambda.min
num.min
lambda.1se
num.1se
```


1.22167734899679



15



2.55874352016859



1


5. Repeat Q4 with the elastic-net, where the tuning parameter α starts from 0 to 1 increased by 0.05.
Suppose that ˆα minimizes PEnew among all α. Find the optimal value of ˆα, and the corresponding
λˆmin and λˆ 1se. Also provide the number of selected variables for λˆmin and λˆ
1se, respectively


```R
###Q5
pe.new <- array(NA,c(length(alpha),lambda.num,K))
for(k in 1:K){
  tran <- which(foldid!=k)
  test <- which(foldid==k)
  for(i in 1:length(alpha)){
    fit.q5 <- glmnet(x[tran,],y[tran],alpha=alpha[i],lambda=lambda,family='gaussian')
    for(j in 1:lambda.num){
      if(sum(fit.q5$beta[,j]!=0)!=0){
        gene.pos <- which(fit.q5$beta[,j]!=0)
        q <- length(gene.pos)
        q0 <- min(q,length(tran)-1)
        lm.fit <- lm(y~x[,gene.pos[1:q0]],subset=tran)
        lm.pred <- predict(lm.fit,data.frame(x[,gene.pos[1:q0]]))
        pe.new[i,j,k] <- sqrt(mean((y-lm.pred)[test]^2))
      }else{
        beta0 <- mean(y[tran])
        pe.new[i,j,k] <- sqrt(mean((y[test]-beta0)^2))
      }
    }
  }
}
cv.ave <- apply(pe.new,c(1,2),mean)
dim(cv.ave)
cv.alpha <- apply(cv.ave,1,min)
alpha.hat <- alpha[which(cv.alpha==min(cv.alpha))]
cv.lambda <- pe.new[which(cv.alpha==min(cv.alpha)),,]
cv.lambda.ave <- apply(cv.lambda,1,mean)
cv.lambda.se <- apply(cv.lambda,1,sd)
cv.lambda.up <- cv.lambda.ave + cv.lambda.se
lambda.min.pos <- which(cv.lambda.ave==min(cv.lambda.ave))
lambda.min <- lambda[lambda.min.pos]
lambda.1se.pos <- min(which(cv.lambda.ave <
                              cv.lambda.up[which(cv.lambda.ave==min(cv.lambda.ave))]))
lambda.1se <- lambda[lambda.1se.pos]
fit.min <- glmnet(x,y,alpha=alpha.hat,lambda=lambda.min,family='gaussian')
num.min <- sum(fit.min$beta!=0)
fit.1se <- glmnet(x,y,alpha=alpha.hat,lambda=lambda.1se,family='gaussian')
num.1se <- sum(fit.1se$beta!=0)

alpha.hat
lambda.min
lambda.1se
num.min
num.1se
```


<ol class=list-inline>
	<li>21</li>
	<li>300</li>
</ol>




0.95



1.33995526965934



2.72133876837531



15



1



```R

```
