---
layout: single
title:  "Bigdata homework 01"
categories: R
tags : [PNU, bigdata]
---


# Homework Assignment 01

Open the data set Boston in the R package MASS. The data information is available with ?Boston. The variable crim is considered as a response variable and the other 10 variables except chas, dis and rad are considered as predictors. Also, we remove the 55th observation and then scale all the predictors so that each predictor has a mean of 0 and a standard deviation of 
1. So, you can make the predictor x and the response
y using the following R codes
data(Boston)
y <- Boston[-55, 1]
x <- Boston[-55, -c(1,4,8,9)]
x <- as.matrix(scale(x))

In this problem, we want to find the best model that can predict the crime rate in Boston. Use the following R codes to randomly select a training set including 450 observations.
RNGkind(sample.kind = "Rounding")
set.seed(123)
tran <- sample(nrow(x), 450)
The other 55 observations are considered as a test set.

1. For each predictor, apply a polynomial regression model with the kth degree, where k = 1, 2, . . . , 15.
You should use the R function poly(..., k). Since you have a total of 10 predictors, you can have 10
polynomial regression models where each model has 15 different degrees of the polynomial. For each
model, find the optimal value of k that can minimize the prediction error (PE) of the test set. The PE
is defined as
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
where ˆf(·) is a fitted polynomial regression using the training set and m = 55. For each model
(predictor), provide the optimal value of k and the numerical value of the corresponding PE.


```R
library(MASS)
head(Boston)
summary(Boston)
str(Boston)

data(Boston)
y = Boston[-55, 1]
x = Boston[-55, -c(1,4,8,9)]
x = as.matrix(scale(x))

RNGkind(sample.kind = "Rounding")
set.seed(123)
tran = sample(nrow(x), 450)

data = cbind(y,x)
data = data.frame(data)
```


<table>
<thead><tr><th scope=col>crim</th><th scope=col>zn</th><th scope=col>indus</th><th scope=col>chas</th><th scope=col>nox</th><th scope=col>rm</th><th scope=col>age</th><th scope=col>dis</th><th scope=col>rad</th><th scope=col>tax</th><th scope=col>ptratio</th><th scope=col>black</th><th scope=col>lstat</th><th scope=col>medv</th></tr></thead>
<tbody>
	<tr><td>0.00632</td><td>18     </td><td>2.31   </td><td>0      </td><td>0.538  </td><td>6.575  </td><td>65.2   </td><td>4.0900 </td><td>1      </td><td>296    </td><td>15.3   </td><td>396.90 </td><td>4.98   </td><td>24.0   </td></tr>
	<tr><td>0.02731</td><td> 0     </td><td>7.07   </td><td>0      </td><td>0.469  </td><td>6.421  </td><td>78.9   </td><td>4.9671 </td><td>2      </td><td>242    </td><td>17.8   </td><td>396.90 </td><td>9.14   </td><td>21.6   </td></tr>
	<tr><td>0.02729</td><td> 0     </td><td>7.07   </td><td>0      </td><td>0.469  </td><td>7.185  </td><td>61.1   </td><td>4.9671 </td><td>2      </td><td>242    </td><td>17.8   </td><td>392.83 </td><td>4.03   </td><td>34.7   </td></tr>
	<tr><td>0.03237</td><td> 0     </td><td>2.18   </td><td>0      </td><td>0.458  </td><td>6.998  </td><td>45.8   </td><td>6.0622 </td><td>3      </td><td>222    </td><td>18.7   </td><td>394.63 </td><td>2.94   </td><td>33.4   </td></tr>
	<tr><td>0.06905</td><td> 0     </td><td>2.18   </td><td>0      </td><td>0.458  </td><td>7.147  </td><td>54.2   </td><td>6.0622 </td><td>3      </td><td>222    </td><td>18.7   </td><td>396.90 </td><td>5.33   </td><td>36.2   </td></tr>
	<tr><td>0.02985</td><td> 0     </td><td>2.18   </td><td>0      </td><td>0.458  </td><td>6.430  </td><td>58.7   </td><td>6.0622 </td><td>3      </td><td>222    </td><td>18.7   </td><td>394.12 </td><td>5.21   </td><td>28.7   </td></tr>
</tbody>
</table>




          crim                zn             indus            chas        
     Min.   : 0.00632   Min.   :  0.00   Min.   : 0.46   Min.   :0.00000  
     1st Qu.: 0.08204   1st Qu.:  0.00   1st Qu.: 5.19   1st Qu.:0.00000  
     Median : 0.25651   Median :  0.00   Median : 9.69   Median :0.00000  
     Mean   : 3.61352   Mean   : 11.36   Mean   :11.14   Mean   :0.06917  
     3rd Qu.: 3.67708   3rd Qu.: 12.50   3rd Qu.:18.10   3rd Qu.:0.00000  
     Max.   :88.97620   Max.   :100.00   Max.   :27.74   Max.   :1.00000  
          nox               rm             age              dis        
     Min.   :0.3850   Min.   :3.561   Min.   :  2.90   Min.   : 1.130  
     1st Qu.:0.4490   1st Qu.:5.886   1st Qu.: 45.02   1st Qu.: 2.100  
     Median :0.5380   Median :6.208   Median : 77.50   Median : 3.207  
     Mean   :0.5547   Mean   :6.285   Mean   : 68.57   Mean   : 3.795  
     3rd Qu.:0.6240   3rd Qu.:6.623   3rd Qu.: 94.08   3rd Qu.: 5.188  
     Max.   :0.8710   Max.   :8.780   Max.   :100.00   Max.   :12.127  
          rad              tax           ptratio          black       
     Min.   : 1.000   Min.   :187.0   Min.   :12.60   Min.   :  0.32  
     1st Qu.: 4.000   1st Qu.:279.0   1st Qu.:17.40   1st Qu.:375.38  
     Median : 5.000   Median :330.0   Median :19.05   Median :391.44  
     Mean   : 9.549   Mean   :408.2   Mean   :18.46   Mean   :356.67  
     3rd Qu.:24.000   3rd Qu.:666.0   3rd Qu.:20.20   3rd Qu.:396.23  
     Max.   :24.000   Max.   :711.0   Max.   :22.00   Max.   :396.90  
         lstat            medv      
     Min.   : 1.73   Min.   : 5.00  
     1st Qu.: 6.95   1st Qu.:17.02  
     Median :11.36   Median :21.20  
     Mean   :12.65   Mean   :22.53  
     3rd Qu.:16.95   3rd Qu.:25.00  
     Max.   :37.97   Max.   :50.00  


    'data.frame':	506 obs. of  14 variables:
     $ crim   : num  0.00632 0.02731 0.02729 0.03237 0.06905 ...
     $ zn     : num  18 0 0 0 0 0 12.5 12.5 12.5 12.5 ...
     $ indus  : num  2.31 7.07 7.07 2.18 2.18 2.18 7.87 7.87 7.87 7.87 ...
     $ chas   : int  0 0 0 0 0 0 0 0 0 0 ...
     $ nox    : num  0.538 0.469 0.469 0.458 0.458 0.458 0.524 0.524 0.524 0.524 ...
     $ rm     : num  6.58 6.42 7.18 7 7.15 ...
     $ age    : num  65.2 78.9 61.1 45.8 54.2 58.7 66.6 96.1 100 85.9 ...
     $ dis    : num  4.09 4.97 4.97 6.06 6.06 ...
     $ rad    : int  1 2 2 3 3 3 5 5 5 5 ...
     $ tax    : num  296 242 242 222 222 222 311 311 311 311 ...
     $ ptratio: num  15.3 17.8 17.8 18.7 18.7 18.7 15.2 15.2 15.2 15.2 ...
     $ black  : num  397 397 393 395 397 ...
     $ lstat  : num  4.98 9.14 4.03 2.94 5.33 ...
     $ medv   : num  24 21.6 34.7 33.4 36.2 28.7 22.9 27.1 16.5 18.9 ...
    

    Warning message in RNGkind(sample.kind = "Rounding"):
    "non-uniform 'Rounding' sampler used"


```R
####################### question1 ############################
## polynomial
PE1 = matrix(0,10,15)

for ( k in 1:10) {
  name = data[,k+1]
  for( i in 1:15){
    model1 = lm(y~poly(name,i),subset=tran)
    h1 = predict(model1,data.frame(x)[-tran])
    PE1[k,i] = sqrt(mean((y-h1)[-tran]^2))
  }
}

result1 = matrix(0,10,2)
for (i in 1:10){
 result1[i,1] = which.min(PE1[i,])
 result1[i,2] = PE1[i,which.min(PE1[i,])]
}

model= c('model1','model2','model3','model4','model5','model6','model7','model8','model9','model10')
colnames(result1) = c('degree1','PE1')
rownames(result1) = model
result1
```


<table>
<thead><tr><th></th><th scope=col>degree1</th><th scope=col>PE1</th></tr></thead>
<tbody>
	<tr><th scope=row>model1</th><td>11      </td><td>9.889435</td></tr>
	<tr><th scope=row>model2</th><td>15      </td><td>7.431768</td></tr>
	<tr><th scope=row>model3</th><td>15      </td><td>7.649432</td></tr>
	<tr><th scope=row>model4</th><td>10      </td><td>9.925192</td></tr>
	<tr><th scope=row>model5</th><td> 8      </td><td>8.768530</td></tr>
	<tr><th scope=row>model6</th><td>13      </td><td>7.435990</td></tr>
	<tr><th scope=row>model7</th><td>14      </td><td>7.668816</td></tr>
	<tr><th scope=row>model8</th><td> 2      </td><td>8.726587</td></tr>
	<tr><th scope=row>model9</th><td>10      </td><td>8.855429</td></tr>
	<tr><th scope=row>model10</th><td> 3      </td><td>8.147778</td></tr>
</tbody>
</table>



2. For each predictor, apply smoothing spline with the degree of freedoms k from 2 to 15, i.e., k =
2, 3, . . . , 15. You can use the R function smooth.spline(..., df= ), which has been discussed in
class. Find the optimal value of k that can minimize the prediction error (PE) of the test set. For each
model (predictor), provide the optimal value of k and the numerical value of the corresponding PE.


```R
####################### question2 ############################
## smooth spline
y_train = y[tran]
x_train = x[tran,]
y_test = y[-tran]
x_test = x[-tran,]

PE2 = matrix(0,10,14)
for (j in 1:10) {
  for (i in 2:15) {
    model2 <-smooth.spline(x[tran,j],y[tran],df=i,)
    h2 <- predict(model2,x[-tran,j])
    PE2[j,i-1] = sqrt(mean((y_test-h2$y)^2))
  }
}

PE2

result2 = matrix(0,10,2)
for (i in 1:10){
  result2[i,1] = which.min(PE2[i,])+1
  result2[i,2] = PE2[i,which.min(PE2[i,])]
}

model= c('model1','model2','model3','model4','model5','model6','model7','model8','model9','model10')
colnames(result2) = c('degree2','PE2')
rownames(result2) = model
result2
```


<table>
<tbody>
	<tr><td>10.043922</td><td>9.928101 </td><td>9.903471 </td><td>9.896256 </td><td>9.893220 </td><td>9.891692 </td><td>9.890831 </td><td>9.890311 </td><td>9.889986 </td><td>9.889782 </td><td>9.889652 </td><td>9.889570 </td><td>9.889516 </td><td>9.889480 </td></tr>
	<tr><td> 8.981238</td><td>8.729187 </td><td>8.379307 </td><td>8.144290 </td><td>7.995117 </td><td>7.889556 </td><td>7.816802 </td><td>7.768389 </td><td>7.736278 </td><td>7.713956 </td><td>7.697123 </td><td>7.683181 </td><td>7.670512 </td><td>7.658422 </td></tr>
	<tr><td> 9.121631</td><td>8.857618 </td><td>8.613213 </td><td>8.529093 </td><td>8.511544 </td><td>8.503062 </td><td>8.480177 </td><td>8.431574 </td><td>8.359961 </td><td>8.274009 </td><td>8.184797 </td><td>8.099080 </td><td>8.020917 </td><td>7.952312 </td></tr>
	<tr><td>10.005262</td><td>9.937074 </td><td>9.928371 </td><td>9.928350 </td><td>9.930034 </td><td>9.934280 </td><td>9.939100 </td><td>9.942684 </td><td>9.944998 </td><td>9.946961 </td><td>9.949486 </td><td>9.953185 </td><td>9.958410 </td><td>9.965345 </td></tr>
	<tr><td> 9.450754</td><td>9.228163 </td><td>9.137265 </td><td>9.090248 </td><td>9.052993 </td><td>9.017322 </td><td>8.983098 </td><td>8.952335 </td><td>8.927022 </td><td>8.908003 </td><td>8.895086 </td><td>8.887562 </td><td>8.884226 </td><td>8.883978 </td></tr>
	<tr><td> 7.832294</td><td>7.614913 </td><td>7.559168 </td><td>7.526054 </td><td>7.495026 </td><td>7.470708 </td><td>7.454947 </td><td>7.446155 </td><td>7.441480 </td><td>7.438937 </td><td>7.437475 </td><td>7.436585 </td><td>7.436011 </td><td>7.435618 </td></tr>
	<tr><td> 9.679160</td><td>9.415599 </td><td>9.153627 </td><td>8.838948 </td><td>8.540805 </td><td>8.309800 </td><td>8.144737 </td><td>8.024502 </td><td>7.934056 </td><td>7.863655 </td><td>7.809007 </td><td>7.766728 </td><td>7.733827 </td><td>7.708716 </td></tr>
	<tr><td> 8.726939</td><td>8.726941 </td><td>8.726942 </td><td>8.721217 </td><td>8.708857 </td><td>8.705181 </td><td>8.717082 </td><td>8.745765 </td><td>8.787880 </td><td>8.838647 </td><td>8.894034 </td><td>8.950713 </td><td>9.005867 </td><td>9.058144 </td></tr>
	<tr><td> 8.947109</td><td>8.899067 </td><td>8.893568 </td><td>8.913226 </td><td>8.929197 </td><td>8.935627 </td><td>8.939339 </td><td>8.944815 </td><td>8.952685 </td><td>8.962177 </td><td>8.972431 </td><td>8.983021 </td><td>8.993994 </td><td>9.005384 </td></tr>
	<tr><td> 9.317741</td><td>8.300601 </td><td>8.103097 </td><td>8.133811 </td><td>8.203357 </td><td>8.256055 </td><td>8.287239 </td><td>8.302555 </td><td>8.307238 </td><td>8.304698 </td><td>8.297471 </td><td>8.287559 </td><td>8.276635 </td><td>8.265808 </td></tr>
</tbody>
</table>




<table>
<thead><tr><th></th><th scope=col>degree2</th><th scope=col>PE2</th></tr></thead>
<tbody>
	<tr><th scope=row>model1</th><td>15      </td><td>9.889480</td></tr>
	<tr><th scope=row>model2</th><td>15      </td><td>7.658422</td></tr>
	<tr><th scope=row>model3</th><td>15      </td><td>7.952312</td></tr>
	<tr><th scope=row>model4</th><td> 5      </td><td>9.928350</td></tr>
	<tr><th scope=row>model5</th><td>15      </td><td>8.883978</td></tr>
	<tr><th scope=row>model6</th><td>15      </td><td>7.435618</td></tr>
	<tr><th scope=row>model7</th><td>15      </td><td>7.708716</td></tr>
	<tr><th scope=row>model8</th><td> 7      </td><td>8.705181</td></tr>
	<tr><th scope=row>model9</th><td> 4      </td><td>8.893568</td></tr>
	<tr><th scope=row>model10</th><td> 4      </td><td>8.103097</td></tr>
</tbody>
</table>



3. Next, we apply a step function to each of 10 predictors, where we break the range of x into bins and
fit a different constant in each bin. The step function basically converts a continuous variable into an
ordered categorical variable. For example, we construct k + 1 new variables for the j-th predictor Xj
such that
C0(Xj ) = I(Xj < c1), C1(Xj ) = I(c1 ≤ Xj < c2), C2(Xj ) = I(c2 ≤ Xj < c3), . . . , Ck(Xj ) = I(Xj ≥ ck),
1
where c1, c2, . . . , ck are k cutpoints and I(·) is an indicator function. Note that
C0(Xj ) + C1(Xj ) + · · · + Ck(Xj ) = 1,
since Xj must be in exactly one of the k + 1 intervals. The R function cut(x[,j], k) can generate k
intervals of the jth predictor of x. This function will give you the numerical values of k cutpoints. Consider k = 2, 3, . . . , 15 for each predictor. We then consider C1(Xj ), C2(Xj ), . . . , Ck(Xj ) as predictors
of a linear model, so
yi = β0 + β1C1(xi) + β2C2(xi) + . . . + βkCk(xi) + i
,
where β0 is the mean value of yi for Xj < c1. Therefore, you can use the R function lm(y ∼ cut(x[,j],
k), subset=tran) to fit the linear model with k categorical variables based on the training set. For
each model (predictor), provide the optimal value of k and the numerical value of the corresponding
PE.


```R
####################### question3 ############################
PE3 = matrix(0,10,14)

for(i in 1:10){
  for(j in 2:15){
    step.temp <- cut(x[,i],j)
    m.step <- lm(y~step.temp,subset=tran)
    PE3[i,j-1] <- sqrt(mean((y-predict(m.step,as.data.frame(step.temp)))[-tran]^2))
  }
}

result3 = matrix(0,10,2)
for (i in 1:10){
  result3[i,1] = which.min(PE3[i,])+1
  result3[i,2] = PE3[i,which.min(PE3[i,])]
}

colnames(result3) = c('degree2','PE2')
rownames(result3) = model
result3
result = cbind(result1,result2,result3)
```


<table>
<thead><tr><th></th><th scope=col>degree2</th><th scope=col>PE2</th></tr></thead>
<tbody>
	<tr><th scope=row>model1</th><td>12      </td><td>9.889432</td></tr>
	<tr><th scope=row>model2</th><td>10      </td><td>7.431066</td></tr>
	<tr><th scope=row>model3</th><td>14      </td><td>7.967711</td></tr>
	<tr><th scope=row>model4</th><td> 7      </td><td>9.897713</td></tr>
	<tr><th scope=row>model5</th><td>12      </td><td>9.225558</td></tr>
	<tr><th scope=row>model6</th><td>15      </td><td>7.435804</td></tr>
	<tr><th scope=row>model7</th><td>15      </td><td>7.571922</td></tr>
	<tr><th scope=row>model8</th><td> 7      </td><td>8.603050</td></tr>
	<tr><th scope=row>model9</th><td> 2      </td><td>8.801308</td></tr>
	<tr><th scope=row>model10</th><td>11      </td><td>8.142274</td></tr>
</tbody>
</table>



4. You have a total of 30 models so far (10 models from each question) and the corresponding 30 PEs.
You can pick up the best model among 30 models in terms of the smallest PE. However, this result
is based on validation set approach (450 training sets and 55 test sets). Next, repeat Q1, Q2 and
Q3 to obtain the optimal k and PE from 30 models using the 10-fold cross-validation, instead of the
validation set approach. You must use the following R code to generate 10 folds.
RNGkind(sample.kind = "Rounding")
set.seed(123)
u <- sample(rep(seq(10), length=length(y)))
For 30 models, provide the PE along with the optimal value of k using the following table,
Note that Model j means that you use only the jth predictor of X in your model.


```R
####################### question4 ############################         
RNGkind(sample.kind = "Rounding")
set.seed(123)
u <- sample(rep(seq(10), length=length(y)))


data_cross = cbind(data,u)
data_cross = data.frame(data_cross)
summary(data_cross)
```

    Warning message in RNGkind(sample.kind = "Rounding"):
    "non-uniform 'Rounding' sampler used"


           y                  zn               indus             nox         
     Min.   : 0.00632   Min.   :-0.48496   Min.   :-1.558   Min.   :-1.4677  
     1st Qu.: 0.08221   1st Qu.:-0.48496   1st Qu.:-0.869   1st Qu.:-0.9151  
     Median : 0.25915   Median :-0.48496   Median :-0.213   Median :-0.1466  
     Mean   : 3.62065   Mean   : 0.00000   Mean   : 0.000   Mean   : 0.0000  
     3rd Qu.: 3.67822   3rd Qu.: 0.05448   3rd Qu.: 1.013   3rd Qu.: 0.5959  
     Max.   :88.97620   Max.   : 3.83050   Max.   : 2.418   Max.   : 2.7287  
           rm               age               tax             ptratio       
     Min.   :-3.8749   Min.   :-2.3336   Min.   :-1.3108   Min.   :-2.7036  
     1st Qu.:-0.5695   1st Qu.:-0.8386   1st Qu.:-0.7654   1st Qu.:-0.4854  
     Median :-0.1087   Median : 0.3226   Median :-0.4631   Median : 0.2540  
     Mean   : 0.0000   Mean   : 0.0000   Mean   : 0.0000   Mean   : 0.0000  
     3rd Qu.: 0.4830   3rd Qu.: 0.9049   3rd Qu.: 1.5288   3rd Qu.: 0.8086  
     Max.   : 3.5480   Max.   : 1.1144   Max.   : 1.7956   Max.   : 1.6404  
         black             lstat              medv               u         
     Min.   :-3.8993   Min.   :-1.5276   Min.   :-1.9055   Min.   : 1.000  
     1st Qu.: 0.2051   1st Qu.:-0.8001   1st Qu.:-0.6019   1st Qu.: 3.000  
     Median : 0.3813   Median :-0.1831   Median :-0.1456   Median : 5.000  
     Mean   : 0.0000   Mean   : 0.0000   Mean   : 0.0000   Mean   : 5.475  
     3rd Qu.: 0.4336   3rd Qu.: 0.6032   3rd Qu.: 0.2673   3rd Qu.: 8.000  
     Max.   : 0.4411   Max.   : 3.5427   Max.   : 2.9832   Max.   :10.000  



```R
############################4-1############################
PE4 = matrix(0,10,6)

MSE1 = matrix(0,15,10)
MSE11 = matrix(0,15,10)


for (j in 1:10){
  tran1 = which(data_cross[,'u']!=j)
  for ( k in 1:10) {
    name = data_cross[,k+1]
    for( i in 1:15){
      model1_1 = lm(y~poly(name,i),subset=tran1)
      h1_1 = predict(model1_1,data[-tran1])
      MSE1[i,k] = mean((y-h1_1)[-tran1]^2)
    }
  }
  MSE11 = MSE11 + MSE1
}
MSE11 = MSE11/10

for ( j in 1:10){
  PE4[j,1] = which.min(MSE11[,j])  
  PE4[j,2] = MSE11[which.min(MSE11[,j]),j]
}

```


```R
############################4-2###########################
MSE2 = matrix(0,14,10)
MSE22 = matrix(0,14,10)

for (j in 1:10){
  tran1 = which(data_cross[,'u']!=j)
  for ( k in 1:10) {
    for( i in 1:14){
      model2_1 <-smooth.spline(data_cross[tran1,k+1],data_cross[tran1,1],df=i+1)    
      h2_1 <- predict(model2_1,data_cross[-tran1,k+1])
      MSE2[i,k] = mean((data_cross[-tran1,1]-h2_1$y)^2)
    }
  }
  MSE22 = MSE22 + MSE2
}
MSE22 = MSE22/10

for ( j in 1:10){
  PE4[j,3] = which.min(MSE22[,j])+1  
  PE4[j,4] = MSE22[which.min(MSE22[,j]),j]
}

###########################4-3#######################################
MSE3 = matrix(0,14,10)
MSE33 = matrix(0,14,10)


for (j in 1:10){
  tran1 = which(data_cross[,'u']!=j)
  for ( k in 1:10) {
    name = data_cross[,k+1]
    tryCatch( for( i in 1:14){
      model3_1 = lm(y ~ cut(name, i+1),data=data, subset=tran1)
      h3_1 = predict(model3_1,data[-tran1])
      MSE3[i,k] = mean((y-h3_1)[-tran1]^2)
    },error = function(e) NULL)
  }
  MSE33 = MSE33 + MSE3
} 

MSE33 = MSE33/10


for ( j in 1:10){
  PE4[j,5] = which.min(MSE33[,j])  
  PE4[j,6] = MSE33[which.min(MSE33[,j]),j]
}
             
PE4
```


<table>
<thead><tr><th></th><th scope=col>k1</th><th scope=col>PE1</th><th scope=col>k2</th><th scope=col>PE2</th><th scope=col>k3</th><th scope=col>PE3</th></tr></thead>
<tbody>
	<tr><th scope=row>model1</th><td>11      </td><td>70.20424</td><td>15      </td><td>70.20380</td><td>13      </td><td>68.22371</td></tr>
	<tr><th scope=row>model2</th><td>15      </td><td>44.78873</td><td>15      </td><td>45.78309</td><td>14      </td><td>44.78772</td></tr>
	<tr><th scope=row>model3</th><td>13      </td><td>41.51670</td><td>15      </td><td>41.12742</td><td>13      </td><td>40.87637</td></tr>
	<tr><th scope=row>model4</th><td> 5      </td><td>68.48185</td><td>12      </td><td>69.16601</td><td>11      </td><td>68.93009</td></tr>
	<tr><th scope=row>model5</th><td>14      </td><td>59.88021</td><td>15      </td><td>60.79166</td><td>10      </td><td>63.71318</td></tr>
	<tr><th scope=row>model6</th><td>13      </td><td>44.87179</td><td>15      </td><td>44.85130</td><td>14      </td><td>44.89068</td></tr>
	<tr><th scope=row>model7</th><td> 9      </td><td>51.00637</td><td>15      </td><td>48.42611</td><td>14      </td><td>33.99256</td></tr>
	<tr><th scope=row>model8</th><td> 1      </td><td>64.69264</td><td> 3      </td><td>65.42474</td><td>10      </td><td>57.57120</td></tr>
	<tr><th scope=row>model9</th><td> 1      </td><td>59.13021</td><td> 3      </td><td>59.02323</td><td> 5      </td><td>61.19589</td></tr>
	<tr><th scope=row>model10</th><td>12      </td><td>43.08892</td><td> 6      </td><td>43.86766</td><td> 6      </td><td>43.42079</td></tr>
</tbody>
</table>



5. Which model among 30 models is the best model when you use the 10-fold cross-validation? How about the validation set approach? Did they pick up the same model as the best model?


```R
####################### question5 ############################
model = c('model1','model2','model3','model4','model5','model6','model7','model8','model9','model10')
row = c('k1','PE1','k2','PE2','k3','PE3')

colnames(PE4) = row
rownames(PE4) = model
colnames(result) = row
rownames(result) = model

result
PE4

which(PE4[,c('PE1','PE2','PE3')] == min(PE4[,c('PE1','PE2','PE3')]), arr.ind = TRUE)

PE4[7,5:6]

which(result[,c('PE1','PE2','PE3')] == min(result[,c('PE1','PE2','PE3')]), arr.ind = TRUE)

result[2,5:6]
```


<table>
<thead><tr><th></th><th scope=col>k1</th><th scope=col>PE1</th><th scope=col>k2</th><th scope=col>PE2</th><th scope=col>k3</th><th scope=col>PE3</th></tr></thead>
<tbody>
	<tr><th scope=row>model1</th><td>11      </td><td>9.889435</td><td>15      </td><td>9.889480</td><td>12      </td><td>9.889432</td></tr>
	<tr><th scope=row>model2</th><td>15      </td><td>7.431768</td><td>15      </td><td>7.658422</td><td>10      </td><td>7.431066</td></tr>
	<tr><th scope=row>model3</th><td>15      </td><td>7.649432</td><td>15      </td><td>7.952312</td><td>14      </td><td>7.967711</td></tr>
	<tr><th scope=row>model4</th><td>10      </td><td>9.925192</td><td> 5      </td><td>9.928350</td><td> 7      </td><td>9.897713</td></tr>
	<tr><th scope=row>model5</th><td> 8      </td><td>8.768530</td><td>15      </td><td>8.883978</td><td>12      </td><td>9.225558</td></tr>
	<tr><th scope=row>model6</th><td>13      </td><td>7.435990</td><td>15      </td><td>7.435618</td><td>15      </td><td>7.435804</td></tr>
	<tr><th scope=row>model7</th><td>14      </td><td>7.668816</td><td>15      </td><td>7.708716</td><td>15      </td><td>7.571922</td></tr>
	<tr><th scope=row>model8</th><td> 2      </td><td>8.726587</td><td> 7      </td><td>8.705181</td><td> 7      </td><td>8.603050</td></tr>
	<tr><th scope=row>model9</th><td>10      </td><td>8.855429</td><td> 4      </td><td>8.893568</td><td> 2      </td><td>8.801308</td></tr>
	<tr><th scope=row>model10</th><td> 3      </td><td>8.147778</td><td> 4      </td><td>8.103097</td><td>11      </td><td>8.142274</td></tr>
</tbody>
</table>




<table>
<thead><tr><th></th><th scope=col>k1</th><th scope=col>PE1</th><th scope=col>k2</th><th scope=col>PE2</th><th scope=col>k3</th><th scope=col>PE3</th></tr></thead>
<tbody>
	<tr><th scope=row>model1</th><td>11      </td><td>70.20424</td><td>15      </td><td>70.20380</td><td>13      </td><td>68.22371</td></tr>
	<tr><th scope=row>model2</th><td>15      </td><td>44.78873</td><td>15      </td><td>45.78309</td><td>14      </td><td>44.78772</td></tr>
	<tr><th scope=row>model3</th><td>13      </td><td>41.51670</td><td>15      </td><td>41.12742</td><td>13      </td><td>40.87637</td></tr>
	<tr><th scope=row>model4</th><td> 5      </td><td>68.48185</td><td>12      </td><td>69.16601</td><td>11      </td><td>68.93009</td></tr>
	<tr><th scope=row>model5</th><td>14      </td><td>59.88021</td><td>15      </td><td>60.79166</td><td>10      </td><td>63.71318</td></tr>
	<tr><th scope=row>model6</th><td>13      </td><td>44.87179</td><td>15      </td><td>44.85130</td><td>14      </td><td>44.89068</td></tr>
	<tr><th scope=row>model7</th><td> 9      </td><td>51.00637</td><td>15      </td><td>48.42611</td><td>14      </td><td>33.99256</td></tr>
	<tr><th scope=row>model8</th><td> 1      </td><td>64.69264</td><td> 3      </td><td>65.42474</td><td>10      </td><td>57.57120</td></tr>
	<tr><th scope=row>model9</th><td> 1      </td><td>59.13021</td><td> 3      </td><td>59.02323</td><td> 5      </td><td>61.19589</td></tr>
	<tr><th scope=row>model10</th><td>12      </td><td>43.08892</td><td> 6      </td><td>43.86766</td><td> 6      </td><td>43.42079</td></tr>
</tbody>
</table>




<table>
<thead><tr><th></th><th scope=col>row</th><th scope=col>col</th></tr></thead>
<tbody>
	<tr><th scope=row>model7</th><td>7</td><td>3</td></tr>
</tbody>
</table>




<dl class=dl-horizontal>
	<dt>k3</dt>
		<dd>14</dd>
	<dt>PE3</dt>
		<dd>33.9925607168498</dd>
</dl>




<table>
<thead><tr><th></th><th scope=col>row</th><th scope=col>col</th></tr></thead>
<tbody>
	<tr><th scope=row>model2</th><td>2</td><td>3</td></tr>
</tbody>
</table>




<dl class=dl-horizontal>
	<dt>k3</dt>
		<dd>10</dd>
	<dt>PE3</dt>
		<dd>7.43106589344303</dd>
</dl>


