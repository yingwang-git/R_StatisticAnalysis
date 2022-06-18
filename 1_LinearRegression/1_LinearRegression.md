Linear Regression
================

# Table of Contents

1.  [Data Information](#data-information)
2.  [Data Prepare](#data-prepare)
3.  [Third Example](#third-example)
4.  [Fourth Example](#fourth-examplehttpwwwfourthexamplecom)

# Data Information

数据来源于《应用商务统计分析》第一章线性回归 （王汉生.
应用商务统计分析\[M\]. 北京大学出版社, 2008.）

| 变量   | 变量名称 | 定义                      |
|--------|----------|---------------------------|
| 因变量 | ROE      | 来年的净资产收益率        |
| 自变量 | ROEt     | 当年的净资产收益率        |
|        | ATO      | 资产周转率                |
|        | PM       | 主营业务利润/主营业务收入 |
|        | LEV      | 债务资本比率              |
|        | GROWTH   | 主营业务收入增长率        |
|        | PB       | 市倍率                    |
|        | ARR      | 应收账款/主营业务收入     |
|        | INV      | 存货/资产总计             |
|        | ASSET    | 对数后的资产总计          |

# Data Prepare

## read data

``` r
data <- read.csv("../data/data_LinearRegression.csv")
```

# descriptive analysis

## data info

``` r
head(data)  # 数据预览
```

    ##   year   ROEt   ATO     PM    LEV GROWTH      PB    ARR   INV  ASSET    ROE
    ## 1 2002  0.296 0.389  0.215  4.384  0.197   8.048  0.637 0.248 20.873  0.181
    ## 2 2002  0.665 0.335  0.407  2.273 -0.084   2.154 -1.217 0.030 21.062  0.899
    ## 3 2002 -0.045 0.963  0.084 -5.339  4.155 -12.879 -0.598 0.096 21.474  1.504
    ## 4 2002 -0.783 0.437 -0.096  1.016  2.231   3.836  0.056 0.051 19.746 -0.777
    ## 5 2002  1.053 0.858 -0.154 -2.066  0.519   1.972  0.293 0.032 22.570  0.728
    ## 6 2002  0.590 0.528  0.197 -0.946 -1.441   2.405 -0.986 0.189 20.890  1.141

``` r
str(data)  # all variables
```

    ## 'data.frame':    1000 obs. of  11 variables:
    ##  $ year  : int  2002 2002 2002 2002 2002 2002 2002 2002 2002 2002 ...
    ##  $ ROEt  : num  0.296 0.665 -0.045 -0.783 1.053 ...
    ##  $ ATO   : num  0.389 0.335 0.963 0.437 0.858 0.528 0.419 0.891 -0.23 -0.176 ...
    ##  $ PM    : num  0.215 0.407 0.084 -0.096 -0.154 0.197 0.324 0.131 0.071 0.123 ...
    ##  $ LEV   : num  4.38 2.27 -5.34 1.02 -2.07 ...
    ##  $ GROWTH: num  0.197 -0.084 4.155 2.231 0.519 ...
    ##  $ PB    : num  8.05 2.15 -12.88 3.84 1.97 ...
    ##  $ ARR   : num  0.637 -1.217 -0.598 0.056 0.293 ...
    ##  $ INV   : num  0.248 0.03 0.096 0.051 0.032 0.189 -0.06 0.145 0.215 0.065 ...
    ##  $ ASSET : num  20.9 21.1 21.5 19.7 22.6 ...
    ##  $ ROE   : num  0.181 0.899 1.504 -0.777 0.728 ...

## descriptive statistics

``` r
summary(data)  # basic descriptive statistics
```

    ##       year           ROEt               ATO                PM         
    ##  Min.   :2002   Min.   :-1.39000   Min.   :-1.1910   Min.   :-0.4240  
    ##  1st Qu.:2002   1st Qu.:-0.26650   1st Qu.: 0.1187   1st Qu.: 0.0880  
    ##  Median :2002   Median : 0.06250   Median : 0.4425   Median : 0.2150  
    ##  Mean   :2002   Mean   : 0.06349   Mean   : 0.4309   Mean   : 0.2118  
    ##  3rd Qu.:2003   3rd Qu.: 0.40225   3rd Qu.: 0.7562   3rd Qu.: 0.3400  
    ##  Max.   :2003   Max.   : 1.60100   Max.   : 1.9270   Max.   : 0.7500  
    ##       LEV              GROWTH              PB               ARR         
    ##  Min.   :-7.9410   Min.   :-7.7850   Min.   :-22.952   Min.   :-2.8000  
    ##  1st Qu.:-1.1025   1st Qu.:-1.2210   1st Qu.: -3.607   1st Qu.:-0.4530  
    ##  Median : 0.9200   Median : 0.2470   Median :  2.856   Median : 0.1795  
    ##  Mean   : 0.9219   Mean   : 0.2208   Mean   :  2.880   Mean   : 0.1648  
    ##  3rd Qu.: 3.0230   3rd Qu.: 1.6692   3rd Qu.:  9.191   3rd Qu.: 0.7772  
    ##  Max.   :10.2390   Max.   : 6.1640   Max.   : 32.591   Max.   : 3.4170  
    ##       INV               ASSET            ROE          
    ##  Min.   :-0.33900   Min.   :18.46   Min.   :-1.16400  
    ##  1st Qu.: 0.01375   1st Qu.:20.51   1st Qu.: 0.05475  
    ##  Median : 0.11000   Median :21.10   Median : 0.40550  
    ##  Mean   : 0.10430   Mean   :21.09   Mean   : 0.39083  
    ##  3rd Qu.: 0.19100   3rd Qu.:21.68   3rd Qu.: 0.71125  
    ##  Max.   : 0.60900   Max.   :23.68   Max.   : 5.28500

## plot

``` r
hist(data$ROE)
```

![](E:/DataMining/Git_R_StatisticAnalysis/1_LinearRegression/1_LinearRegression_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->
