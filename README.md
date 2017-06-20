在股市上 黃金交叉&死亡交叉 是否真的值得參考
================

組員
====

呂黃嘉(畫圖，回測)，林衍丞(匯入資料，清洗資料，PPT)

題目
----

在股市上”黃金交叉&死亡交叉”是否真的值得參考 對 ”台股較大較穩定的1735支股票” 進行回測看看是否有獲利

分析議題背景
------------

背景介紹背景介紹 \#身為半個客家人，研究一下股票也是理所當然的

分析動機
--------

分析動機分析動機 \#在張錦特老師LAB工讀的時候有抓了股票的DATA，想要用R練習視覺化並且做出相關聯的決策

使用資料
--------

說明使用資料們 從以下兩個網站爬來的 證券櫃檯買賣中心http://www.tpex.org.tw/web/stock/aftertrading/daily\_trading\_info/st43.php?l=zh-tw (上櫃) 臺灣證券交易所http://www.twse.com.tw/zh/page/trading/exchange/STOCK\_DAY.html (上市)

載入使用資料們

``` r
#這是R Code Chunk
library(readr)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(quantmod)
```

    ## Loading required package: xts

    ## Loading required package: zoo

    ## 
    ## Attaching package: 'zoo'

    ## The following objects are masked from 'package:base':
    ## 
    ##     as.Date, as.Date.numeric

    ## 
    ## Attaching package: 'xts'

    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     first, last

    ## Loading required package: TTR

    ## Version 0.4-0 included new data defaults. See ?getSymbols.

``` r
#import data
#格式是CSV
OHLC <- read_csv("C:/Users/chang/Desktop/TEMP/OHLC.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   stkno = col_character(),
    ##   Date = col_date(format = ""),
    ##   volume = col_integer(),
    ##   price = col_integer(),
    ##   open = col_double(),
    ##   high = col_double(),
    ##   low = col_double(),
    ##   close = col_double(),
    ##   change = col_character(),
    ##   deal = col_integer()
    ## )

    ## Warning in rbind(names(probs), probs_f): number of columns of result is not
    ## a multiple of vector length (arg 1)

    ## Warning: 30410 parsing failures.
    ## row # A tibble: 5 x 5 col     row   col   expected     actual                                   file expected   <int> <chr>      <chr>      <chr>                                  <chr> actual 1  1175 price an integer 3360768684 'C:/Users/chang/Desktop/TEMP/OHLC.csv' file 2  1176 price an integer 2223301193 'C:/Users/chang/Desktop/TEMP/OHLC.csv' row 3  1437 price an integer 2345288219 'C:/Users/chang/Desktop/TEMP/OHLC.csv' col 4  1453 price an integer 2180733648 'C:/Users/chang/Desktop/TEMP/OHLC.csv' expected 5  1898 price an integer 2616283272 'C:/Users/chang/Desktop/TEMP/OHLC.csv'
    ## ... ................. ... .......................................................................... ........ .......................................................................... ...... .......................................................................... .... .......................................................................... ... .......................................................................... ... .......................................................................... ........ ..........................................................................
    ## See problems(...) for more details.

``` r
three_legals <- read_csv("C:/Users/chang/Desktop/TEMP/三大法人.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   stkno = col_character(),
    ##   Date = col_date(format = ""),
    ##   F_I = col_character(),
    ##   I_T = col_character(),
    ##   Dealer = col_character()
    ## )

資料處理與清洗
--------------

說明處理資料的步驟

處理資料

``` r
#這是R Code Chunk

#clean
OHLC = OHLC[,c(-4,-10)]
#join
joined_frame = inner_join(OHLC,three_legals,by=c("stkno","Date"))
#遺漏值處理 NA to 0
joined_frame[is.na(joined_frame)] <- 0
joined_frame$Date = as.Date(joined_frame$Date, format= "%Y-%d-%m")
```

探索式資料分析
--------------

先取單股0050進行分析之後會秀出反例單股1102
==========================================

最後對全部資料進行回測計算有沒有獲利
====================================

計算黃金&死亡交叉
=================

取單股 (0050)
=============

stkno\_0050 = OHLC\[grep("0050", OHLC$stkno),\] \#為建立 XTS物件做的處理 stkno\_0050 = stkno\_0050\[,c(-1,-8)\] colnames(stkno\_0050) &lt;- c("Date","Volume","Open","High","Low","Close") stkno\_0050 = stkno\_0050\[,c("Date","Open","High","Low","Close","Volume")\] stkno\_0050 = stkno\_0050\[complete.cases(stkno\_0050),\] \#建立XTS物件 stk0050 = xts(stkno\_0050\[, -1\], order.by=as.POSIXct(stkno\_0050$Date))

1 K線圖，量，MA10，MA20 simple sample
=====================================

chartSeries(stk0050,subset="last 6 months",theme="white", up.col='red', dn.col='green') \#addSMA(10,col="blue") \#addSMA(20,col="red") MA10&lt;-runMean(stk0050\[,4\],n=10) MA20&lt;-runMean(stk0050\[,4\],n=20) addTA(MA10,on=1,col="blue") addTA(MA20,on=1,col="red")

2.0 去除量的圖 加上回測資產圖 中程(MA20 vs MA60)
================================================

chartSeries(stk0050\['2004-06-30/2017-06-16'\],theme="white", up.col='red', dn.col='green',TA=NULL) MA20&lt;-runMean(stk0050\[,4\],n=20) MA60&lt;-runMean(stk0050\[,4\],n=60) addTA(MA20,on=1,col="blue") addTA(MA60,on=1,col="red") position&lt;-Lag(ifelse(MA20&gt;=MA60, 1,0)) return&lt;-ROC(Cl(stk0050))\*position return &lt;- return\['2004-06-30/2017-06-16'\] eq &lt;- exp(cumsum(return)) addTA(eq)

2.1 去除量的圖 加上回測資產圖 中程 ZOOM IN 資產下降的部分 (2012-10~2013-12)
===========================================================================

zoomChart('2012-10::2013-12') addTA(MA20-MA60)

2.2 去除量的圖 加上回測資產圖 近程(MA5 vs MA20)
===============================================

chartSeries(stk0050\['2004-06-30/2013-12-31'\],theme="white", up.col='red', dn.col='green',TA=NULL) MA5&lt;-runMean(stk0050\[,4\],n=5) MA20&lt;-runMean(stk0050\[,4\],n=20) addTA(MA5,on=1,col="blue") addTA(MA20,on=1,col="red") position&lt;-Lag(ifelse(MA5&gt;=MA20, 1,0)) return&lt;-ROC(Cl(stk0050))\*position return &lt;- return\['2004-06-30/2013-12-31'\] eq &lt;- exp(cumsum(return)) addTA(eq) zoomChart('2012-10::2013-12') addTA(MA5-MA20)

3.0 反例 反例，資產下降
=======================

取單股 (1102)
=============

stkno\_1102 = OHLC\[grep("1102", OHLC$stkno),\] \#為建立 XTS物件做的處理 stkno\_1102 = stkno\_1102\[,c(-1,-8)\] colnames(stkno\_1102) &lt;- c("Date","Volume","Open","High","Low","Close") stkno\_1102 = stkno\_1102\[,c("Date","Open","High","Low","Close","Volume")\] stkno\_1102 = stkno\_1102\[complete.cases(stkno\_1102),\] \#建立XTS物件 stk1102 = xts(stkno\_1102\[, -1\], order.by=as.POSIXct(stkno\_1102$Date))

1102 圖表
=========

chartSeries(stk1102\['2004-06-30/2017-06-16'\],theme="white", up.col='red', dn.col='green',TA=NULL) MA20&lt;-runMean(stk1102\[,4\],n=20) MA60&lt;-runMean(stk1102\[,4\],n=60) addTA(MA20,on=1,col="blue") addTA(MA60,on=1,col="red") position&lt;-Lag(ifelse(MA20&gt;=MA60, 1,0)) return&lt;-ROC(Cl(stk1102))\*position return &lt;- return\['2004-06-30/2017-06-16'\] eq &lt;- exp(cumsum(return)) addTA(eq)

4.0 對1735ㄓ支股票回測
======================

get all stkno
=============

stkno\_all = unique(joined\_frame$stkno)

geteq\_all &lt;- function(x){ \#計算黃金&死亡交叉 \#取單股 (x) stkno\_ = OHLC\[grep(x, OHLC$stkno),\] \#為建立 XTS物件做的處理 stkno\_ = stkno\_\[,c(-1,-8)\] colnames(stkno\_) &lt;- c("Date","Volume","Open","High","Low","Close") stkno\_ = stkno\_\[,c("Date","Open","High","Low","Close","Volume")\] stkno\_ = stkno\_\[complete.cases(stkno\_),\] \#建立XTS物件 stk = xts(stkno\_\[, -1\], order.by=as.POSIXct(stkno\_$Date)) fastMA&lt;-runMean(stk\[,4\],n=20) slowMA&lt;-runMean(stk\[,4\],n=60) position&lt;-Lag(ifelse(fastMA&gt;=slowMA, 1,0)) return&lt;-ROC(Cl(stk))\*position coredata(return)\[!is.finite(return)\] &lt;- NA return = return\[complete.cases(return),\] eq &lt;- exp(cumsum(return)) last\_eq = eq\[length(eq)\] df\_last\_eq = data.frame(last\_eq, row.names=NULL) last\_value = df\_last\_eq\[1,\] return(last\_value) }

geteq\_3y &lt;- function(x){ \#計算黃金&死亡交叉 \#取單股 (x) stkno\_ = OHLC\[grep(x, OHLC$stkno),\] \#為建立 XTS物件做的處理 stkno\_ = stkno\_\[,c(-1,-8)\] colnames(stkno\_) &lt;- c("Date","Volume","Open","High","Low","Close") stkno\_ = stkno\_\[,c("Date","Open","High","Low","Close","Volume")\] stkno\_ = stkno\_\[complete.cases(stkno\_),\] \#建立XTS物件 stk = xts(stkno\_\[, -1\], order.by=as.POSIXct(stkno\_$Date)) fastMA&lt;-runMean(stk\[,4\],n=20) slowMA&lt;-runMean(stk\[,4\],n=60) position&lt;-Lag(ifelse(fastMA&gt;=slowMA, 1,0)) return&lt;-ROC(Cl(stk))\*position coredata(return)\[!is.finite(return)\] &lt;- NA return = return\[complete.cases(return),\] return = last(return, "3 years")\#三年 eq &lt;- exp(cumsum(return)) last\_eq = eq\[length(eq)\] df\_last\_eq = data.frame(last\_eq, row.names=NULL) last\_value = df\_last\_eq\[1,\] return(last\_value) }

取近所有資料進行回測
====================

indexx &lt;- 0 list &lt;- list() list\_stkno &lt;- list() P\_count\_all = 0 N\_count\_all = 0

for (i in stkno\_all) { tryCatch({ eq = geteq\_all(i) indexx &lt;- indexx + 1 list\_stkno\[\[indexx\]\] = i list\[\[indexx\]\] &lt;- eq

    if (eq  > 1) {
      P_count_all = P_count_all + 1
    } else {
      N_count_all = N_count_all + 1
    } 
    print(eq)

}, error=function(e){print(e)}) }

print(P\_count\_all) print(N\_count\_all) P\_count\_all / (N\_count\_all + P\_count\_all)

DF = data.frame(stkno=character(), eq=double(), stringsAsFactors=FALSE)

DF = do.call(rbind, Map(data.frame, stkno=list\_stkno, eq=list)) mean\_eq\_all = mean(DF$eq) \#總資產回報 mean(DF$eq) \#最大個股成長 max(DF$eq)

取近三年資料進行回測
====================

indexx &lt;- 0 list &lt;- list() list\_stkno &lt;- list() P\_count\_3years = 0 N\_count\_3years = 0

for (i in stkno\_all) { tryCatch({ eq = geteq\_3y(i) indexx &lt;- indexx + 1 list\_stkno\[\[indexx\]\] = i list\[\[indexx\]\] &lt;- eq

    if (eq  > 1) {
      P_count_3years = P_count_3years + 1
    } else {
      N_count_3years = N_count_3years + 1
    } 
    print(eq)

}, error=function(e){print(e)}) }

print(P\_count\_3years) print(N\_count\_3years) P\_count\_3years /( P\_count\_3years + N\_count\_3years) DF\_3y = data.frame(stkno=character(), eq=double(), stringsAsFactors=FALSE)

DF\_3y = do.call(rbind, Map(data.frame, stkno=list\_stkno, eq=list)) mean\_eq\_3years = mean(DF\_3y$eq) \#總資產回報 mean(DF\_3y$eq) \#最大個股成長 max(DF\_3y$eq)
