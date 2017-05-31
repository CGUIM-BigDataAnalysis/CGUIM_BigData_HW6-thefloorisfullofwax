個股股價 券商 金價關聯性
================

呂黃嘉，林衍丞

分析議題背景
------------

背景介紹背景介紹 \#身為半個客家人，研究一下股票也是理所當然的

分析動機
--------

分析動機分析動機 \#之前有抓了股票的DATA，想要用R練習視覺化並且做出相關聯的決策

使用資料
--------

說明使用資料們

載入使用資料們

``` r
#這是R Code Chunk
library(readxl)
stk2330 <- read_excel("C:/Users/chang/Desktop/try_stock.xlsx")
XAUUSD <- read_excel("C:/Users/chang/Desktop/stock_2330.xlsx")
```

資料處理與清洗
--------------

說明處理資料的步驟

處理資料

``` r
#這是R Code Chunk
#其實這個資料我在抓的時候就已經處理過了
#這邊轉型一下DATE就好
colnames(XAUUSD)[1] <- "date"
colnames(stk2330)[2] <- "date"

XAUUSD$date = strptime(as.character(XAUUSD$date), "%d-%m-%Y")
XAUUSD$date = format(XAUUSD$date, "%Y-%m-%d")
```

探索式資料分析
--------------

圖文並茂圖文並茂

``` r
#這是R Code Chunk
View(XAUUSD)
View(stk2330)
#這個改天在解釋吧
#先秀出來 之後會有更多資料比較好解釋
```

期末專題分析規劃
----------------

期末專題要做XXOOO交叉分析 \#期末專題應該可以做出股價與其他指數的關聯性 相關係數分析等等...... \#利用R畫出 K線圖 跟其他指數做比較
