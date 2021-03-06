``` r
library(dplyr) #to use %>% (pipe operator) and mutate function
library(tidyr) #to use complete function

setwd("~/MATH 456 - Time Series/project/Traffic Volume by hour")
ptrNum <- list("R017", "R117", "S201", "S202", "S203", "S204", "S205","S825", "S827")

data <- read.csv(paste("TrafficVolumeByHour_",ptrNum[5],"_2007-01_2019-08.csv", sep=""), header = TRUE)
data <- as.data.frame(data)

#find index where direction changes from Eastbound to Westbound
idx = match('Westbound', data$TravelDirection)

dataE <- data[1:idx-1,]
dataW <- data[idx:dim(data)[1],]

#insert rows and fill with n/a's where dates are missing
dataE<- dataE %>%
  mutate(Date = as.Date(as.character(dataE$Date),format="%m/%d/%Y")) %>%
  complete(Date = seq.Date(min(Date), max(Date), by="day"))

dataW<- dataW %>%
  mutate(Date = as.Date(as.character(dataW$Date),format="%m/%d/%Y")) %>%
  complete(Date = seq.Date(min(Date), max(Date), by="day"))

#sort data according to date
dataE <- dataE[with(dataE, order(Date)), ]
dataW <- dataW[with(dataW, order(Date)), ]

# Hourly traffic flow time series 

#create vectors of hourly traffic flows in both directions
#column 6-29 represent hours 1-24
eastHourly <- vector()
for (i in 1:dim(dataE)[1]){
  eastHourly <- append(eastHourly,dataE[i,6:29])
}
eastHourly <- as.numeric(eastHourly)

westHourly <- vector()
for (i in 1:dim(dataW)[1]){
  westHourly <- append(westHourly,dataW[i,6:29])
}
westHourly <- as.numeric(westHourly)
```

``` r
par(mfrow=c(1,2))
eastHourlyTS <- ts(eastHourly,start=1)
westHourlyTS <- ts(westHourly,start=1)
tE <- 1:length(eastHourlyTS)
tW <- 1:length(westHourlyTS)
plot(tE,eastHourlyTS, type = "l", main = "E. Hourly 3/15/2007-8/31/2019")
plot(tW,westHourlyTS, type = "l", main = "W. Hourly 3/15/2007-8/31/2019")
```

![](traffic_files/figure-markdown_github/unnamed-chunk-2-1.png)

``` r
#lets take a closer look, the last 3 months
par(mfrow=c(1,2))
idx = match('2019-06-01', as.character(dataE$Date))
eastHourlyTS <- ts(eastHourly[((idx*24)-23):length(eastHourly)],start=1)
westHourlyTS <- ts(westHourly[((idx*24)-23):length(westHourly)],start=1)
tE <- 1:length(eastHourlyTS)
tW <- 1:length(westHourlyTS)
plot(tE, eastHourlyTS, type = "l", main = "E. Hourly 6/1/2019-8/31/2019")
plot(tW, westHourlyTS, type = "l", main = "W. Hourly 6/1/2019-8/31/2019")
```

![](traffic_files/figure-markdown_github/unnamed-chunk-3-1.png)

``` r
#the last month
par(mfrow=c(1,2))
idx = match('2019-08-01', as.character(dataE$Date))
eastHourlyTS <- ts(eastHourly[((idx*24)-23):length(eastHourly)],start=1)
westHourlyTS <- ts(westHourly[((idx*24)-23):length(westHourly)],start=1)
tE <- 1:length(eastHourlyTS)
tW <- 1:length(westHourlyTS)
plot(tE, eastHourlyTS, type = "l", main = "E. Hourly 8/1/2019-8/31/2019")
plot(tW, westHourlyTS, type = "l", main = "W. Hourly 8/1/2019-8/31/2019")
```

![](traffic_files/figure-markdown_github/unnamed-chunk-4-1.png)

``` r
#the last week
par(mfrow=c(1,2))
idx = match('2019-08-25', as.character(dataE$Date))
eastHourlyTS <- ts(eastHourly[((idx*24)-23):length(eastHourly)],start=1)
westHourlyTS <- ts(westHourly[((idx*24)-23):length(westHourly)],start=1)
tE <- 1:length(eastHourlyTS)
tW <- 1:length(westHourlyTS)
plot(tE, eastHourlyTS, type = "l", main = "E. Hourly 8/25/2019-8/31/2019")
plot(tW, westHourlyTS, type = "l", main = "W. Hourly 8/25/2019-8/31/2019")
```

![](traffic_files/figure-markdown_github/unnamed-chunk-5-1.png)

Total daily traffic flow time series
====================================

``` r
#find sum of traffic flow for hours 1-24 for each day
dailyTotalE <- apply(dataE[,6:29], 1, sum)
dailyTotalW <- apply(dataW[,6:29], 1, sum)

#plot of total daily traffic flow for all available data
par(mfrow=c(1,2))
eastDailyTS <- ts(dailyTotalE, start=1)
westDailyTS <- ts(dailyTotalW, start=1)
tE <- 1:length(eastDailyTS)
tW <- 1:length(westDailyTS)
plot(tE,eastDailyTS, type = "l", main = paste("E. Daily 3/15/2007-8/31/2019"))
plot(tW,westDailyTS, type = "l", main = paste("W. Daily 3/15/2007-8/31/2019"))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-6-1.png)

``` r
#last 3 years
par(mfrow=c(1,2))
idx = match('2016-09-01', as.character(dataE$Date))
eastDailyTS <- ts(dailyTotalE[idx:length(dailyTotalE)], start=1)
westDailyTS <- ts(dailyTotalW[idx:length(dailyTotalW)], start=1)
tE <- 1:length(eastDailyTS)
tW <- 1:length(westDailyTS)
plot(tE,eastDailyTS, type = "l", main = paste("E. Daily 8/31/2016-8/31/2019"))
plot(tW,westDailyTS, type = "l", main = paste("W. Daily 8/31/2016-8/31/2019"))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-7-1.png)

``` r
#last 1 year
par(mfrow=c(1,2))
idx = match('2018-09-01', as.character(dataE$Date))
eastDailyTS <- ts(dailyTotalE[idx:length(dailyTotalE)], start=1)
westDailyTS <- ts(dailyTotalW[idx:length(dailyTotalW)], start=1)
tE <- 1:length(eastDailyTS)
tW <- 1:length(westDailyTS)
plot(tE,eastDailyTS, type = "l", main = paste("E. Daily 8/31/2018-8/31/2019"))
plot(tW,westDailyTS, type = "l", main = paste("W. Daily 8/31/2018-8/31/2019"))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-8-1.png)

``` r
#last 3 months
par(mfrow=c(1,2))
idx = match('2019-06-01', as.character(dataE$Date))
eastDailyTS <- ts(dailyTotalE[idx:length(dailyTotalE)], start=1)
westDailyTS <- ts(dailyTotalW[idx:length(dailyTotalW)], start=1)
tE <- 1:length(eastDailyTS)
tW <- 1:length(westDailyTS)
plot(tE,eastDailyTS, type = "l", main = paste("E. Daily 6/1/2019-8/31/2019"))
plot(tW,westDailyTS, type = "l", main = paste("W. Daily 6/1/2019-8/31/2019"))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-9-1.png)

``` r
#last 1 month
par(mfrow=c(1,2))
idx = match('2019-08-01', as.character(dataE$Date))
eastDailyTS <- ts(dailyTotalE[idx:length(dailyTotalE)], start=1)
westDailyTS <- ts(dailyTotalW[idx:length(dailyTotalW)], start=1)
tE <- 1:length(eastDailyTS)
tW <- 1:length(westDailyTS)
plot(tE,eastDailyTS, type = "l", main = paste("E. Daily 8/1/2019-8/31/2019"))
plot(tW,westDailyTS, type = "l", main = paste("W. Daily 8/1/2019-8/31/2019"))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-10-1.png)

``` r
#last week
par(mfrow=c(1,2))
idx = match('2019-08-25', as.character(dataE$Date))
eastDailyTS <- ts(dailyTotalE[idx:length(dailyTotalE)], start=1)
westDailyTS <- ts(dailyTotalW[idx:length(dailyTotalW)], start=1)
tE <- 1:length(eastDailyTS)
tW <- 1:length(westDailyTS)
plot(tE,eastDailyTS, type = "l", main = paste("E. Daily 8/25/2019-8/31/2019"))
plot(tW,westDailyTS, type = "l", main = paste("W. Daily 8/25/2019-8/31/2019"))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-11-1.png) \# Model Fitting \#\# Data Imputation using kalman/seadec

``` r
library("imputeTS")
par(mfrow=c(1,1))
#daily total flow for last three years, use kalman to impute mising data
idx1 = match('2016-09-01', as.character(dataE$Date))
eastDailyTS <- ts(dailyTotalE[idx1:length(dailyTotalE)], start=1)
sum(is.na(eastDailyTS)) #theres only 10 missing, not too bad
```

    ## [1] 10

``` r
eastDailyTS <- na_kalman(eastDailyTS)
plot(1:length(eastDailyTS),eastDailyTS, type="l")
```

![](traffic_files/figure-markdown_github/unnamed-chunk-12-1.png)

``` r
#hourly traffic flow for last three months
#this is the time series we will focus on modeling
idx2 = match('2019-06-01', as.character(dataE$Date))
eastHourlyTS <- ts(as.numeric(eastHourly[((idx2*24)-23):length(eastHourly)]),start=1)
sum(is.na(eastHourlyTS)) #no data imputation necessary
```

    ## [1] 0

``` r
#the outlier dip in traffic corresponds july 5th
plot(1:length(eastHourlyTS),eastHourlyTS, type="l", main="S203 E. Hourly Traffic Flow for 6/1/2019-8/31/2019", xlab="Hours",ylab="Traffic Flow")
```

![](traffic_files/figure-markdown_github/unnamed-chunk-13-1.png)

1) SSA
------

``` r
library("MASS")
#split data into train/test sets
train=1:1776 #74 days
actual=1777:2208 #18 days 

eastHourlyTS_train = ts(eastHourlyTS[train])

#see if any transformation is needed
boxcox(eastHourlyTS_train~1)#no transformation is needed
```

![](traffic_files/figure-markdown_github/unnamed-chunk-14-1.png)

``` r
library("Rssa")

#lets start with the 1 month hourly traffic flow
n = length(eastHourlyTS_train)

# Decomposition stage
# If no L is specified, L is set to be half the length of the time series.
hourly3month_ssa = ssa(eastHourlyTS_train, kind="1d-ssa", L=168*2)

# Diagnostics for grouping
plot(hourly3month_ssa) # Eigenvalues
```

![](traffic_files/figure-markdown_github/unnamed-chunk-15-1.png)

``` r
plot(hourly3month_ssa, type = "vectors") # Eigenvectors
```

![](traffic_files/figure-markdown_github/unnamed-chunk-16-1.png)

``` r
plot(hourly3month_ssa, type = "paired") # Pairs of eigenvectors
```

![](traffic_files/figure-markdown_github/unnamed-chunk-17-1.png)

``` r
plot(wcor(hourly3month_ssa)) # w-correlation matrix plot
```

![](traffic_files/figure-markdown_github/unnamed-chunk-18-1.png)

``` r
# A list containing all the groups
group_hourly3month_ssa = list(c(1),c(2,3), c(4,5), c(6,7)) #Trend + Seasonalities with two different periods
num_components = length(group_hourly3month_ssa)
recon_hourly3month = reconstruct(hourly3month_ssa , groups = group_hourly3month_ssa)

# doesn't pick up on weekly seasonality, only daily
print(paste("Seasonality 1: ",parestimate(hourly3month_ssa, list(c(2,3)), method = "pairs")$periods))
```

    ## [1] "Seasonality 1:  24.003642265606"

``` r
print(paste("Seasonality 2: ",parestimate(hourly3month_ssa, list(c(4,5)), method = "pairs")$periods))
```

    ## [1] "Seasonality 2:  11.9960847979499"

``` r
print(paste("Seasonality 3: ",parestimate(hourly3month_ssa, list(c(6,7)), method = "pairs")$periods))
```

    ## [1] "Seasonality 3:  8.00009548440189"

``` r
# Reconstruction stage
par(mfrow=c(1,num_components))
main_vector = c("Trend", "Periodicity #1", "Periodicity #2", "periodicity #3")
for(i in 1:num_components){
  plot(recon_hourly3month[[i]], main=main_vector[i])
}
```

![](traffic_files/figure-markdown_github/unnamed-chunk-20-1.png)

``` r
# Reconstructed deterministic pattern
d_td3month = rep(0,n)
for(i in 1:num_components){
  d_td3month = d_td3month + recon_hourly3month[[i]]
}

#plot of original and deterministic
par(mfrow=c(1,1))
plot(cbind(eastHourlyTS_train, d_td3month), plot.type="single", col=c("black","red"),
     main="Hourly Traffic Data (6/1/2019-8/31/2019)", lwd=2, xlab="Hour", ylab="Traffic flow",ylim=c(0,10000))
abline(v=2041, col = "blue", lty = 2)

legend("topleft", legend=c("Original", "Deterministic"),
       col=c("black", "red"), lwd=2, cex=0.7, bg="lightblue")
```

![](traffic_files/figure-markdown_github/unnamed-chunk-21-1.png)

``` r
#forecasting
n_ahead = 24*18 # Number of forecasts - 18 days
for_hourly_ssa = rforecast(hourly3month_ssa, groups = group_hourly3month_ssa, len = n_ahead, only.new = FALSE)
for_hourly = rep(0,(n+n_ahead))
for(i in 1:num_components){
  for_hourly = for_hourly + for_hourly_ssa[[i]]
}

plot(cbind(eastHourlyTS, for_hourly), plot.type="single", col=c("black","red"),
     main="Hourly Traffic Data (6/1/2019-8/31/2019)", lwd=2, xlab="Hour", ylab="Traffic flow",ylim=c(0,10000))
legend("topleft", legend=c("Original", "Deterministic + Forecast"),
       col=c("black", "red"), lwd=2, cex=0.7, bg="lightblue")
abline(v=1777, col = "blue", lty = 2)
```

![](traffic_files/figure-markdown_github/unnamed-chunk-22-1.png)

``` r
#residual analysis
library("nortest")
res_det = eastHourlyTS - d_td3month

par(mfrow=c(2,2))
acf(res_det, main="SSA Residuals")
pacf(res_det)
hist(res_det)
#theres clearly a weekly seasonality
plot(res_det, type='l')
```

![](traffic_files/figure-markdown_github/unnamed-chunk-23-1.png)

``` r
#lets see what auto.arima will yield without any parameters
ar_aicc0 <-auto.arima(res_det, ic="aicc")
#it's not picking up on weekly seasonality
ar_res0=ar_aicc0$residuals
par(mfrow=c(2,2))
acf(ar_res0, lag.max=30)
pacf(ar_res0, lag.max=30)
hist(ar_res0)
plot(ar_res0, type='l', xlim=c(1500,2000))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-24-1.png)

``` r
print(ad.test(ar_res0))
```

    ## 
    ##  Anderson-Darling normality test
    ## 
    ## data:  ar_res0
    ## A = 4.5375, p-value = 2.974e-11

``` r
#try to force it to look at models with some differencing/seasonality
ar_aicc1 =auto.arima(res_det, d=1, D=1, max.p=5, max.q=5,
                   max.P=2, max.Q=2, stationary = FALSE,seasonal=TRUE, ic="aicc", allowmean=TRUE)
ar_aicc1
```

    ## Series: res_det 
    ## ARIMA(3,1,1) 
    ## 
    ## Coefficients:
    ##          ar1      ar2     ar3      ma1
    ##       1.1664  -0.5644  0.0530  -0.9462
    ## s.e.  0.0267   0.0347  0.0259   0.0118
    ## 
    ## sigma^2 estimated as 109666:  log likelihood=-12817.04
    ## AIC=25644.08   AICc=25644.12   BIC=25671.49

``` r
ar_res1=ar_aicc1$residuals
par(mfrow=c(2,2))
acf(ar_res1, lag.max=30)
pacf(ar_res1, lag.max=30)
hist(ar_res1)
plot(ar_res1, type='l')
```

![](traffic_files/figure-markdown_github/unnamed-chunk-25-1.png)

``` r
ad.test(ar_res1)
```

    ## 
    ##  Anderson-Darling normality test
    ## 
    ## data:  ar_res1
    ## A = 1.5992, p-value = 0.0004128

``` r
#still not identifying weekly seasonality, so it will be useless
plot(forecast(ar_aicc1, 168))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-26-1.png)

``` r
#lets force the weekly seasonality by setting frequency to 7*24=168
res_det = eastHourlyTS - d_td3month
res_det <- ts(res_det,frequency=7*24)

ar_aicc2 <- auto.arima(res_det, ic="aicc")
ar_aicc2
```

    ## Series: res_det 
    ## ARIMA(1,0,0)(0,1,0)[168] 
    ## 
    ## Coefficients:
    ##          ar1
    ##       0.7717
    ## s.e.  0.0158
    ## 
    ## sigma^2 estimated as 69777:  log likelihood=-11248.67
    ## AIC=22501.33   AICc=22501.34   BIC=22512.1

``` r
ar_res2=ar_aicc2$residuals
par(mfrow=c(2,2))
acf(ar_res2, lag.max=30)
pacf(ar_res2, lag.max=30)
hist(ar_res2)
plot(ar_res2, type='l')
```

![](traffic_files/figure-markdown_github/unnamed-chunk-27-1.png)

``` r
plot(forecast(ar_aicc2, 168))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-28-1.png)

``` r
#ar_aicc2 (SARIMA(1,0,0)(0,1,0)[168]) is clearly the best model choice
#lets do residual analysis on it:

### Portmanteau test
par(mfrow=c(2,2))
pvalues = double(10)
for(lag in 1:10){
  pvalues[lag] = Box.test(ar_res2, lag=lag)$p.value
}
par(mfrow=c(2,2))
plot(pvalues, main="P-Values of the Portmanteau test", xlab="Lag", ylab="P-Value",ylim=c(0,1))
abline(h=0.05, lty=2, col="blue")

### Rank test
library("randtests")
rank.test(ar_res2, alternative="two.sided")
```

    ## 
    ##  Mann-Kendall Rank Test
    ## 
    ## data:  ar_res2
    ## statistic = -0.13278, n = 1776, p-value = 0.8944
    ## alternative hypothesis: trend

``` r
### Normality
library("nortest")
ad.test(ar_res2) #Anderson-Darling test
```

    ## 
    ##  Anderson-Darling normality test
    ## 
    ## data:  ar_res2
    ## A = 60.209, p-value < 2.2e-16

``` r
shapiro.test(ar_res2) #Shapiro-Wilk test
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  ar_res2
    ## W = 0.87772, p-value < 2.2e-16

``` r
qqnorm(ar_res2) #Q-Q plot
qqline(ar_res2)
hist(ar_res2, freq=FALSE, breaks=20)
```

![](traffic_files/figure-markdown_github/unnamed-chunk-29-1.png)

``` r
### Forecasting (1 to 7 days (168 hours) ahead)
end <- length(for_hourly) # this will represent the last index of for_hourly
forecasts2=predict(ar_aicc2, n.ahead=24*18)

for_hourly[1:(end-24*18)] <- for_hourly[1:(end-24*18)]+fitted(ar_aicc2)
for_hourly[((end-24*18)+1):end]<-for_hourly[((end-24*18)+1):end]+forecasts2$pred

SSAandARMA_res <- eastHourlyTS[((end-24*18)+1):end]-for_hourly[((end-24*18)+1):end]

par(mfrow=c(1,2))
acf(SSAandARMA_res)
pacf(SSAandARMA_res)
```

![](traffic_files/figure-markdown_github/unnamed-chunk-30-1.png)

``` r
#plot combined SSA and SARIMA(1,0,0)(0,1,0)[168] with 1 week forecast
par(mfrow=c(1,1))
plot(cbind(eastHourlyTS, for_hourly), plot.type="single", col=c("black","red"),
     main="Hourly Traffic Data (6/1/2019-8/31/2019)", lwd=1, xlab="Hour", ylab="Traffic Flow")
```

![](traffic_files/figure-markdown_github/unnamed-chunk-31-1.png)

``` r
#closer look
plot(cbind(eastHourlyTS, for_hourly), plot.type="single", col=c("black","red"),
     main="Hourly Traffic Data (6/1/2019-8/31/2019)", lwd=2, xlab="Hour", ylab="Traffic Flow",xlim=c((length(for_hourly)-24*18),2208))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-32-1.png)

``` r
# #moving window RMSE
# num_window = 100
# errors_SSA_AR = matrix(NA, ncol=n_ahead, nrow=num_window)
# 
# for(w in 1:num_window){
#   eastHourly_window = eastHourly[w:(w+n-1)]
#   eastHourly_window_ssa = ssa(eastHourly_window, kind="1d-ssa")   
#   recon_eastHourly_window = reconstruct(eastHourly_window_ssa, groups = group_hourly3month_ssa)
#   
#   # Reconstructed deterministic pattern
#   d_eastHourly_window = rep(0,n)
#   for(i in 1:num_components){
#     d_eastHourly_window = d_eastHourly_window + recon_eastHourly_window[[i]]
#   }
#   
#   # Forecast of the deterministic pattern
#   for_eastHourly_ssa = rforecast(eastHourly_window_ssa, groups = group_hourly3month_ssa, len = n_ahead, only.new = FALSE)
#   for_eastHourly_window = rep(0,(n+n_ahead))
#   for(i in 1:num_components){
#     for_eastHourly_window = for_eastHourly_window + for_eastHourly_ssa[[i]]
#   }
#   
#   # Residuals from the deterministic pattern
#   res_det_window = eastHourly_window - d_eastHourly_window    
#   
#   ar_aicc_window = Arima(res_det_window, order=c(1,0,0), seasonal=list(c(0,1,0),period=168), include.mean=FALSE, method="CSS", lambda=NULL)
#  
#   ar_res_window=ar_aicc_window$residuals
#   
#   ### Forecasting (1 to 12 steps ahead)
#   forecasts_window=predict(ar_aicc_window, n.ahead=n_ahead)
#   
#   ### point forecasts (by adding the SSA and ARMA part) on the log scale
#   point_forecasts_window = c(forecasts_window$pred) + for_eastHourly_window[(n+1):(n+n_ahead)]
#   
#   ### For RMSE calculation
#   actual_window = eastHourlyTS[(w+n):(w+n-1+n_ahead)]
#   errors_SSA_AR[w,]=log_point_forecasts_window - log_actual_window
# }
##above isnt working so we'll use built in function rmse()

library(Metrics)
SSAandARMA_rmse <- rmse(eastHourlyTS[actual],for_hourly[((end-24*18)+1):end])
model1_rmse <- SSAandARMA_rmse
model1_rmse
```

    ## [1] 310.3854

2) SARIMA Model
---------------

``` r
library(forecast)
train=1:1776 #74 days
actual=1777:2208 #18 days 

eastHourlyTS <- ts(as.numeric(eastHourly[((idx2*24)-23):length(eastHourly)]),start=1)
eastHourlyTS_train = ts(eastHourlyTS[train])

#see what auto.arima yields with no parameters
sarima0 <- auto.arima(eastHourlyTS_train)

sarima_res0=sarima0$residuals
par(mfrow=c(2,2))
acf(sarima_res0, lag.max=30)
pacf(sarima_res0, lag.max=30)
hist(sarima_res0)
plot(sarima_res0, type='l', xlim=c(1500,2000))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-34-1.png)

``` r
plot(forecast(sarima0, 168))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-35-1.png)

``` r
#doesn't take weekly seasonality into effect
```

``` r
sarima1 <- auto.arima(eastHourlyTS_train, d=1, D=1, max.p=5, max.q=5,
                      max.P=2, max.Q=2, stationary = FALSE,seasonal=TRUE, ic="aicc", allowmean=TRUE)

sarima_res1=sarima1$residuals
par(mfrow=c(2,2))
acf(sarima_res1, lag.max=30)
pacf(sarima_res1, lag.max=30)
hist(sarima_res1)
plot(sarima_res1, type='l')
```

![](traffic_files/figure-markdown_github/unnamed-chunk-36-1.png)

``` r
plot(forecast(sarima1, 168))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-37-1.png)

``` r
#no better
```

``` r
#force seasonality
eastHourlyTS_train = ts(eastHourlyTS[train], frequency=24*7)

sarima2 <- auto.arima(eastHourlyTS_train)
sarima_res2=sarima2$residuals
par(mfrow=c(2,2))
acf(sarima_res2, lag.max=30)
pacf(sarima_res2, lag.max=30)
hist(sarima_res2)
plot(sarima_res2, type='l')
```

![](traffic_files/figure-markdown_github/unnamed-chunk-38-1.png)

``` r
#finally something useful
plot(forecast(sarima2, 168))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-39-1.png)

``` r
### Portmanteau test
pvalues = double(10)
for(lag in 1:10){
  pvalues[lag] = Box.test(sarima_res2, lag=lag)$p.value
}
par(mfrow=c(2,2))
plot(pvalues, main="P-Values of the Portmanteau test", xlab="Lag", ylab="P-Value",ylim=c(0,1))
abline(h=0.05, lty=2, col="blue")

### Rank test
library("randtests")
rank.test(sarima_res2, alternative="two.sided")
```

    ## 
    ##  Mann-Kendall Rank Test
    ## 
    ## data:  sarima_res2
    ## statistic = -0.46877, n = 1776, p-value = 0.6392
    ## alternative hypothesis: trend

``` r
### Normality
par(mfrow=c(1,2))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-40-1.png)

``` r
library("nortest")
ad.test(sarima_res2) #Anderson-Darling test
```

    ## 
    ##  Anderson-Darling normality test
    ## 
    ## data:  sarima_res2
    ## A = 60.151, p-value < 2.2e-16

``` r
shapiro.test(sarima_res2) #Shapiro-Wilk test
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  sarima_res2
    ## W = 0.87779, p-value < 2.2e-16

``` r
qqnorm(sarima_res2) #Q-Q plot
qqline(sarima_res2)
hist(sarima_res2, freq=FALSE, breaks=20)
```

![](traffic_files/figure-markdown_github/unnamed-chunk-40-2.png)

``` r
forecast2 <- predict(sarima2, 24*18)
SARMA_rmse <- rmse(eastHourlyTS[actual],forecast2$pred)
SARMA_rmse 
```

    ## [1] 302.0173

3) TBATS Model
--------------

``` r
library(forecast)
#daily TS
#tbatsModel <- tbats(eastDailyTS, seasonal.periods=c(7,365),use.parallel=TRUE)
#tbatsModel

#fit tbats using daily and weekly seasonalities
tbatsModel1 <- tbats(eastHourlyTS_train, seasonal.periods=c(24,7*24),use.parallel=TRUE)
plot(tbatsModel1)
```

![](traffic_files/figure-markdown_github/unnamed-chunk-41-1.png)

``` r
par(mfrow=c(1,2))
acf(tbatsModel1$errors)
pacf(tbatsModel1$errors)
```

![](traffic_files/figure-markdown_github/unnamed-chunk-42-1.png)

``` r
fc1 <- forecast(tbatsModel1, h=168)
plot(fc1, ylab="Tbats Model 1 forecast")
```

![](traffic_files/figure-markdown_github/unnamed-chunk-43-1.png)

``` r
#fit tbats using just weekly seasonality (not good fit compared to model1)
# tbatsModel2 <- tbats(eastHourlyTS_train, seasonal.periods=c(7*24),use.parallel=TRUE)
# plot(tbatsModel2)
# acf(tbatsModel2$errors)
# pacf(tbatsModel2$errors)
# fc2 <- forecast(tbatsModel2, h=168)
# plot(fc2, ylab="Tbats Model 2 forecast")
```

``` r
#fit tbats using just daily seasonality (not a good fit compared to model1)
# tbatsModel3 <- tbats(eastHourlyTS_train, seasonal.periods=c(24),use.parallel=TRUE)
# plot(tbatsModel3)
# acf(tbatsModel3$errors)
# pacf(tbatsModel3$errors)
# fc3 <- forecast(tbatsModel3, h=168)
# plot(fc3, ylab="Tbats model 3 forecast")
```

``` r
##### tbatsmodel1 is clearly the best fit since it takes into account
##### both seasonalities
tbatsModel1$lambda
```

    ## [1] 0.2922672
    ## attr(,"biasadj")
    ## [1] FALSE

``` r
tbatsModel1$damping.parameter
```

    ## NULL

``` r
tbatsModel1$alpha
```

    ## [1] 0.0268969

``` r
tbatsModel1$gamma.one.values
```

    ## [1]  0.0004013011 -0.0005660305

``` r
tbatsModel1$gamma.two.values
```

    ## [1] 0.0004018109 0.0004679240

``` r
tbatsModel1$ar.coefficients
```

    ##          [,1]       [,2]       [,3]
    ## [1,] 1.163936 -0.1335153 -0.4189779

``` r
tbatsModel1$ma.coefficients
```

    ##            [,1]       [,2]      [,3]       [,4]       [,5]
    ## [1,] -0.1425174 -0.2099639 0.1517171 0.08080171 0.05387824

``` r
# residual analysis:
### Portmanteau test
tbats_res1 <- tbatsModel1$errors
pvalues = double(10)
for(lag in 1:10){
  pvalues[lag] = Box.test(tbats_res1, lag=lag)$p.value
}
par(mfrow=c(2,2))
plot(pvalues, main="P-Values of the Portmanteau test", xlab="Lag", ylab="P-Value",ylim=c(0,1))
abline(h=0.05, lty=2, col="blue")

### Rank test
library("randtests")
rank.test(tbats_res1, alternative="two.sided")
```

    ## 
    ##  Mann-Kendall Rank Test
    ## 
    ## data:  tbats_res1
    ## statistic = -0.24424, n = 1776, p-value = 0.807
    ## alternative hypothesis: trend

``` r
### Normality
library("nortest")
ad.test(tbats_res1) #Anderson-Darling test
```

    ## 
    ##  Anderson-Darling normality test
    ## 
    ## data:  tbats_res1
    ## A = 3.2031, p-value = 5.008e-08

``` r
shapiro.test(tbats_res1) #Shapiro-Wilk test
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  tbats_res1
    ## W = 0.98283, p-value = 9.287e-14

``` r
qqnorm(tbats_res1) #Q-Q plot
qqline(tbats_res1)
hist(tbats_res1, freq=FALSE, breaks=20)

tbats1_for <- predict(tbatsModel1, h=24*18)
tbats_rmse <- rmse(eastHourlyTS[actual],tbats1_for$mean)
tbats_rmse
```

    ## [1] 465.4923

![](traffic_files/figure-markdown_github/unnamed-chunk-47-1.png)

Dynamic Harmonic Regression (DHR)
---------------------------------

``` r
## where the seasonal pattern is modelled using Fourier terms 
## with short-term time series dynamics handled by an ARMA error

##find optimal K - WARNING this take forever - many many many hours (12+)
# msts_test <- msts(eastHourlyTS_train , seasonal.periods = c(24,168))
# my_aic_df <- matrix(nrow = 8, ncol = 50)
# for(i in 1:12){ 
#   for(j in 1:30){ 
#     fn <- fourier(msts_test , K= c(i,j))
#     FourierFit <- auto.arima(msts_test , seasonal=FALSE,  xreg=fn, parallel = TRUE, stepwise = FALSE )
#     my_aic_df[i,j] <- FourierFit$aicc
#   }
# }
# arrayInd(which.min(my_aic_df), dim(my_aic_df)) #K=c(10,30)

length(eastHourlyTS_train)
```

    ## [1] 1776

``` r
length(eastHourlyTS)
```

    ## [1] 2208

``` r
msts_test <- msts(eastHourlyTS_train , seasonal.periods = c(24,168))

dhr_fit <- auto.arima(msts_test, seasonal=FALSE, xreg=fourier(msts_test , K= c(10,30)), parallel = TRUE, stepwise = FALSE )

#getting error? 
# dhr_pred <- predict(dhr_fit,xreg=fourier(msts_test , K= c(10,30)))
# dhr_pred <- predict(dhr_fit,h=24*18)
# dhr_fit$model
# length(dhr_pred$mean)
# dhr_fit

# dhr_rmse <- rmse(eastHourlyTS[actual],dhr_pred$mean)
# model4_rmse <- dhr_rmse

#above isnt working, this is not correct rmse...
# dhr_rmse <- rmse(eastHourlyTS[actual],dhr_for$mean)
# dhr_rmse

dhr_for <- forecast(dhr_fit, xreg=fourier(msts_test , K= c(10,30),h=24*18))
plot(dhr_for)
```

![](traffic_files/figure-markdown_github/unnamed-chunk-48-1.png)

Comparing all four forecasts
============================

``` r
par(mfrow=c(1,1))
plot(cbind(eastHourlyTS, for_hourly), plot.type="single", col=c("black","red"),
     main="SSA + SARIMA Forecast", lwd=2, xlab="Hour", ylab="Traffic flow",ylim=c(0,10000), xlim=c(1777,end))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-49-1.png)

``` r
plot(forecast(sarima2, 24*18), xlim=c(11.6,14.1), ylim=c(0,10000),xlab="Week",ylab="Traffic flow")
lines(ts(eastHourlyTS, frequency=168))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-50-1.png)

``` r
plot(forecast(tbatsModel1,h=24*18), ylim=c(0,10000),xlim=c(11.6,14.1),xlab="Week",ylab="Traffic flow")
lines(ts(eastHourlyTS,frequency=24*7))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-51-1.png)

``` r
plot(dhr_for, ylim=c(0,10000),xlim=c(11.6,14.1),xlab="Week",ylab="Traffic flow")
lines(ts(eastHourlyTS,frequency=24*7))
```

![](traffic_files/figure-markdown_github/unnamed-chunk-52-1.png)

``` r
SSAandARMA_rmse <- rmse(eastHourlyTS[actual],for_hourly[((end-24*18)+1):end])
SARMA_rmse<-rmse(eastHourlyTS[actual],forecast(sarima2, 24*18)$mean)
tbats_rmse<-rmse(eastHourlyTS[actual],forecast(tbatsModel1,h=24*18)$mean)
dhr_rmse <- rmse(eastHourlyTS[actual],dhr_for$mean)

rmses <-cbind(SSAandARMA_rmse,SARMA_rmse,tbats_rmse, dhr_rmse)
rmses
```

    ##      SSAandARMA_rmse SARMA_rmse tbats_rmse dhr_rmse
    ## [1,]        310.3854   302.0173   465.4923 214.2708

``` r
########################## need to finish below: ########################
############## further model diagnostics #########
### Diebold-Mariano test
### h is for the forecast horizon.
### power is the power of the loss function. For the squared loss, power=2.
# DM_pvals = c()
# for(dm in 1:(18*24)){
#   DM_pvals[dm] = dm.test(as.numeric(SSAandARMA_res[dm]), as.numeric(sarima_res2[dm]),as.numeric(dhr_pred$residuals[dm]), as.numeric(tbats1_for$residuals[dm]) ,h=dm, power=2)$p.value
# }
# length(SSAandARMA_res)
# length(sarima_res2)
# length(dhr_pred$residuals)
# length(tbats1_for$residuals)
# 
# acf(SSAandARMA_res)
# DM_pvals

#' ***
#' ## NNETAR model
#
# hourlyNN <- nnetar(eastHourlyTS)
# autoplot(forecast(hourlyNN,h=168))
#########################################################################
```

``` r
############### prediction intervals ################
```

``` r
##### shared dates (check to see if the ptrs share enough data 
##### to implement a spatio-temporal model such as STARIMA, 
##### BVAR, etc 
#######################################################################
# setwd("~/MATH 456 - Time Series/project/Traffic Volume by hour")
# library(readxl)
# library(rlang) #to use prepend function
# library(tidyr) #for date manipulation
# library(dplyr) #for mutate
# install.packages('purrr')
# 
# ptrNum <- list("R017", "R117", "S201", "S202", "S203", "S204", "S205","S825", "S827")
# #12/1/2018-2/28/2019
# 
# allptrs <- data.frame(matrix(ncol = 9, nrow = 62))
# par(mfrow=c(3,3))
# i=0
# for (ptr in ptrNum) {
#   data <- read.csv(paste("TrafficVolumeByHour_",ptr,"_2007-01_2019-08.csv", sep=""), header = TRUE)
#   data <- as.data.frame(data)
#   idx1 <- match('12/1/2018', data$Date)
#   idx2 <- match('1/31/2019', data$Date)
#   data <- data[idx1:idx2,]
#   rownames(data) <- NULL #reset index
#   
#   #imputing missing values - we'll just put avg for now
#   #we'll do it properly later
#   #1) insert n/a's where dates are missing
#   data<- data %>%
#     mutate(Date = as.Date(as.character(data$Date),format="%m/%d/%Y")) %>%
#     complete(Date = seq.Date(min(Date), max(Date), by="day"))
#   
#   total <- apply(data[,6:29], 1, sum)
#   #2) replace n/as with average
#   total[is.na(total)] <- round(mean(total, na.rm = TRUE))
#   
#   #store time series data in ith column of allptrs
#   i <- i+1
#   allptrs[,i] <- total
#   
#   total <- ts(total, start = 1)
#   t <- 1:length(total)
#   plot(t,total, type = "l", main = paste("Total traffic flow",ptr,"12/1/18-2/28/19"))
# }  
```
