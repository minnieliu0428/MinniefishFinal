Let's Go Out and Play!!!(Minniefish)
================

題目: 我要出去玩！！！- 台灣觀光景點推薦
----------------------------------------

資料介紹與來源:
---------------

十種類型的觀光景點在各個月份的遊客人數資料 主要欄位包括 類型 Class、觀光遊憩區 Scenic Spots、縣市別 Location、各個月份的遊客人數以及合計 Total（年總遊客人數）

1.  中華民國交通部觀光局 - 2015年國內主要觀光遊憩據點遊客人數月別統計
2.  中華民國交通部觀光局 - 2016年國內主要觀光遊憩據點遊客人數月別統計

格式:
-----

execl(xls)檔

分析議題:
---------

藉由比較2015~2016年的熱門景點的變化做2017年旅遊景點推薦

假設:
-----

假設越多人去的地方越好玩

分析結果:
---------

2015與2016年的熱門景點幾乎都是一樣的，代表那些景點依舊很有吸引力

分析結果可能解決的問題:
-----------------------

不知道去哪玩的時候可以參考喔

組員名單與分工:
---------------

B0344128 劉旻妮 B0344133 陳宛俞

分工： 劉旻妮：資料處理與清洗、圖表製作、書面報告製作、ppt製作 陳宛俞：找資料（包括資料來源等等）、資料處理（excel）、圖表製作、ggmap製作

資料載入
--------

``` r
#2015
library(readxl)
test2015 <- read_excel("~/Desktop/大數據分析/2015.xls")
#幫欄位命名
names(test2015) <- c("Class", "Scenic Spots", "Location","Jan","Feb", "Mar", "Apr", "May", "Jun" , "Jul", "Aug", "Sep", "Oct", "Nov", "Dec", "Total")
#將第一欄刪除
test2015 <- test2015[-1,]

#2016
library(readxl)
test2016 <- read_excel("~/Desktop/大數據分析/2016.xls")
#幫欄位命名
names(test2016) <- c("Class", "Scenic Spots", "Location","Jan","Feb", "Mar", "Apr", "May", "Jun" , "Jul", "Aug", "Sep", "Oct", "Nov", "Dec", "Total")
#將第一欄刪除
test2016 <- test2016[-1,]
```

資料處理與清洗
--------------

| Class                                                  |
|:-------------------------------------------------------|
| 國家風景區                                             |
| 國家公園                                               |
| 公營遊憩區                                             |
| 縣級風景特定區(2015)直轄市級及縣(市)級風景特定區(2016) |
| 森林遊樂區                                             |
| 海水浴場                                               |
| 民營遊憩區                                             |
| 寺廟                                                   |
| 古蹟、歷史建物                                         |
| 其他                                                   |

``` r
#2015寬轉長
library(reshape2)
test2015.m <- melt(test2015, id.vars = c("Class", "Scenic Spots", "Location"), na.rm = TRUE)
#欄位轉換成數值
test2015.m$value <- as.numeric(test2015.m$value)

#2016寬轉長
library(reshape2)
test2016.m <- melt(test2016, id.vars = c("Class", "Scenic Spots", "Location"), na.rm = TRUE)
#欄位轉換成數值
test2016.m$value <- as.numeric(test2016.m$value)
```

比較2015與2016年各類型觀光景點的遊客人數
----------------------------------------

``` r
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
library(ggplot2)
library(stats)
library(base)

#比較2015年每一種類型的觀光景點的遊客人數
test2015.total <- subset(test2015.m, grepl("Total", test2015.m$variable))
test2015.t <- test2015.total %>% group_by(Class, variable) %>%
  summarise(TotalSum = sum(value, na.rm = T)) %>%
  arrange(desc(TotalSum))
d <- sum(test2015.t$TotalSum)
test2015.t <- mutate(test2015.t,per=round(TotalSum/d*100,digits = 2))
#畫圖
a <- ggplot(test2015.t, aes(Class, TotalSum)) + 
  geom_bar(stat="identity", fill = "#80aaff") + 
  xlab("觀光景點類型") + 
  ylab("遊客人數")+
  ggtitle("2015年各種類型觀光景點的遊客人數") +
  theme_minimal() +
  theme(text = element_text(family = "STHeiti"))
a + theme(axis.text.x = element_text(size = 6))
```

![](README_files/figure-markdown_github/unnamed-chunk-3-1.png)

``` r
#比較2016年每一種類型的觀光景點的遊客人數
test2016.total <- subset(test2016.m, grepl("Total", test2016.m$variable))
test2016.t <- test2016.total %>% group_by(Class, variable) %>%
  summarise(TotalSum = sum(value, na.rm = T)) %>%
  arrange(desc(TotalSum))
d <- sum(test2016.t$TotalSum)
test2016.t <- mutate(test2016.t,per=round(TotalSum/d*100,digits = 2))
#畫圖
b <- ggplot(test2016.t, aes(Class, TotalSum)) + 
  geom_bar(stat="identity", fill = "#ffcc66") + 
  xlab("觀光景點類型") +
  ylab("遊客人數")+
  ggtitle("2016年各種類型觀光景點的遊客人數") +
  theme_minimal() +
  theme(text = element_text(family = "STHeiti"))
b + theme(axis.text.x = element_text(size = 6))
```

![](README_files/figure-markdown_github/unnamed-chunk-3-2.png) \#\#1-12月份的資料分別做子集處理

``` r
library(dplyr)
library(ggplot2)
library(stats)
library(base)

##把total值去掉
test2015.all <- anti_join(test2015.m, test2015.total, by = "variable")  
test2016.all <- anti_join(test2016.m, test2016.total, by = "variable") 

##排序
test2016.all <- test2016.all[order(test2016.all$value, decreasing = T),]
test2015.all <- test2015.all[order(test2015.all$value, decreasing = T),] 

#2015-1月份資料 
test2015.all.jan <- subset(test2015.all, grepl("Jan", test2015.all$variable))
#2015-2月份資料 
test2015.all.feb <- subset(test2015.all, grepl("Feb", test2015.all$variable))
#2015-3月份資料 
test2015.all.mar <- subset(test2015.all, grepl("Mar", test2015.all$variable))
#2015-4月份資料 
test2015.all.apr <- subset(test2015.all, grepl("Apr", test2015.all$variable))
#2015-5月份資料 
test2015.all.may <- subset(test2015.all, grepl("May", test2015.all$variable))
#2015-6月份資料 
test2015.all.jun <- subset(test2015.all, grepl("Jun", test2015.all$variable))
#2015-7月份資料 
test2015.all.jul <- subset(test2015.all, grepl("Jul", test2015.all$variable))
#2015-8月份資料 
test2015.all.aug <- subset(test2015.all, grepl("Aug", test2015.all$variable))
#2015-9月份資料 
test2015.all.sep <- subset(test2015.all, grepl("Sep", test2015.all$variable))
#2015-10月份資料 
test2015.all.oct <- subset(test2015.all, grepl("Oct", test2015.all$variable))
#2015-11月份資料 
test2015.all.nov <- subset(test2015.all, grepl("Nov", test2015.all$variable))
#2015-12月份資料 
test2015.all.dec <- subset(test2015.all, grepl("Dec", test2015.all$variable))

##2016-1月份資料
test2016.all.jan <- subset(test2016.all, grepl("Jan", test2016.all$variable))
##2016-2月份資料
test2016.all.feb <- subset(test2016.all, grepl("Feb", test2016.all$variable))
##2016-3月份資料
test2016.all.mar <- subset(test2016.all, grepl("Mar", test2016.all$variable))
##2016-4月份資料
test2016.all.apr <- subset(test2016.all, grepl("Apr", test2016.all$variable))
##2016-5月份資料
test2016.all.may <- subset(test2016.all, grepl("May", test2016.all$variable))
##2016-6月份資料
test2016.all.jun <- subset(test2016.all, grepl("Jun", test2016.all$variable))
##2016-7月份資料
test2016.all.jul <- subset(test2016.all, grepl("Jul", test2016.all$variable))
##2016-8月份資料
test2016.all.aug <- subset(test2016.all, grepl("Aug", test2016.all$variable))
##2016-9月份資料
test2016.all.sep <- subset(test2016.all, grepl("Sep", test2016.all$variable))
##2016-10月份資料
test2016.all.oct <- subset(test2016.all, grepl("Oct", test2016.all$variable))
##2016-11月份資料
test2016.all.nov <- subset(test2016.all, grepl("Nov", test2016.all$variable))
##2016-12月份資料
test2016.all.dec <- subset(test2016.all, grepl("Dec", test2016.all$variable))
```

2015年1~12月份的前十大夯點
--------------------------

``` r
library(dplyr)
library(ggplot2)
library(stats)
library(base)

#2015-1月份資料 
test2015.all.jan <- subset(test2015.all, grepl("Jan", test2015.all$variable)) 
test2015.all.jan.head <- head(test2015.all.jan,10) 
test2015.all.jan.head[1:10,2]<-c("草悟道","南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","國立中正紀念堂","駁二藝術特區","國立故宮博物院","獅頭山風景區","旗津風景區","世博台灣館","佛光山") 
ggplot(test2015.all.jan.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年1月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-5-1.png)

``` r
#2015-2月份資料 
test2015.all.feb <- subset(test2015.all, grepl("Feb", test2015.all$variable)) 
test2015.all.feb.head <- head(test2015.all.feb,10) 
test2015.all.feb.head[1:10,2]<-c("北港朝天宮","佛光山","陽明公園","南鯤鯓代天府","草悟道","日月潭風景區","十分旅遊服務中心","東豐自行車綠廊及后豐鐵馬道 ","國立中正紀念堂","獅頭山風景區") 
ggplot(test2015.all.feb.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年2月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-5-2.png)

``` r
#2015-3月份資料 
test2015.all.mar <- subset(test2015.all, grepl("Mar", test2015.all$variable)) 
test2015.all.mar.head <- head(test2015.all.mar,10) 
test2015.all.mar.head[1:10,2]<-c("北港朝天宮","臺中公園","佛光山","南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","國立中正紀念堂","陽明公園 ","國父紀念館","八卦山風景區") 
ggplot(test2015.all.mar.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年3月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti"))  
```

![](README_files/figure-markdown_github/unnamed-chunk-5-3.png)

``` r
#2015-4月份資料 
test2015.all.apr <- subset(test2015.all, grepl("Apr", test2015.all$variable)) 
test2015.all.apr.head <- head(test2015.all.apr,10) 
test2015.all.apr.head[1:10,2]<-c("東豐自行車綠廊及后豐鐵馬道","大甲鎮瀾宮","草悟道","國立中正紀念堂","南鯤鯓代天府","國父紀念館","八卦山風景區","獅頭山風景區 ","旗津風景區","北港朝天宮 ") 
ggplot(test2015.all.apr.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年4月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-5-4.png)

``` r
#2015-5月份資料 
test2015.all.may <- subset(test2015.all, grepl("May", test2015.all$variable)) 
test2015.all.may.head <- head(test2015.all.may,10) 
test2015.all.may.head[1:10,2]<-c("南鯤鯓代天府","草悟道","東豐自行車綠廊及后豐鐵馬道","獅頭山風景區","國立中正紀念堂","國父紀念館","國立故宮博物院","麻豆代天府 ","旗津風景區","中臺禪寺 ") 
ggplot(test2015.all.may.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年5月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-5-5.png)

``` r
#2015-6月份資料 
test2015.all.jun <- subset(test2015.all, grepl("Jun", test2015.all$variable)) 
test2015.all.jun.head <- head(test2015.all.jun,10) 
test2015.all.jun.head[1:10,2]<-c("南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","國立中正紀念堂","國父紀念館","獅頭山風景區","旗津風景區","麻豆代天府 ","國立故宮博物院","瑞芳風景特定區 ") 
ggplot(test2015.all.jun.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年6月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-5-6.png)

``` r
#2015-7月份資料 
test2015.all.jul <- subset(test2015.all, grepl("Jul", test2015.all$variable)) 
test2015.all.jul.head <- head(test2015.all.jul,10) 
test2015.all.jul.head[1:10,2]<-c("南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","國父紀念館","國立中正紀念堂","旗津風景區","獅頭山風景區","國立故宮博物院 ","麻豆代天府","八卦山風景區 ") 
ggplot(test2015.all.jul.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年7月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-5-7.png)

``` r
#2015-8月份資料 
test2015.all.aug <- subset(test2015.all, grepl("Aug", test2015.all$variable)) 
test2015.all.aug.head <- head(test2015.all.aug,10) 
test2015.all.aug.head[1:10,2]<-c("草悟道","國立中正紀念堂","東豐自行車綠廊及后豐鐵馬道","南鯤鯓代天府","國父紀念館","獅頭山風景區","國立故宮博物院","瑞芳風景特定區 ","旗津風景區","日月潭風景區") 
ggplot(test2015.all.aug.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年8月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-5-8.png)

``` r
#2015-9月份資料 
test2015.all.sep <- subset(test2015.all, grepl("Sep", test2015.all$variable)) 
test2015.all.sep.head <- head(test2015.all.sep,10) 
test2015.all.sep.head[1:10,2]<-c("南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","國立中正紀念堂","國父紀念館","獅頭山風景區","旗津風景區 ","瑞芳風景特定區","國立故宮博物院","八卦山風景區") 
ggplot(test2015.all.sep.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年9月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-5-9.png)

``` r
#2015-10月份資料 
test2015.all.oct <- subset(test2015.all, grepl("Oct", test2015.all$variable)) 
test2015.all.oct.head <- head(test2015.all.oct,10) 
test2015.all.oct.head[1:10,2]<-c("蓮池潭","南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","國立中正紀念堂","國父紀念館","國立故宮博物院 ","獅頭山風景區","八里左岸公園","旗津風景區") 
ggplot(test2015.all.oct.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年10月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-5-10.png)

``` r
#2015-11月份資料 
test2015.all.nov <- subset(test2015.all, grepl("Nov", test2015.all$variable)) 
test2015.all.nov.head <- head(test2015.all.nov,10) 
test2015.all.nov.head[1:10,2]<-c("南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","佛光山","士林官邸公園","國立中正紀念堂","國父紀念館 ","獅頭山風景區","國立故宮博物院","瑞芳風景特定區") 
ggplot(test2015.all.nov.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年11月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-5-11.png)

``` r
#2015-12月份資料 
test2015.all.dec <- subset(test2015.all, grepl("Dec", test2015.all$variable)) 
test2015.all.dec.head <- head(test2015.all.dec,10) 
test2015.all.dec.head[1:10,2]<-c("南鯤鯓代天府","草悟道","東豐自行車綠廊及后豐鐵馬道","國父紀念館 ","國立中正紀念堂","獅頭山風景區","國立故宮博物院","旗津風景區 ","佛光山","蓮池潭") 
ggplot(test2015.all.dec.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#80aaff") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2015年12月份遊客人數最多的多前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-5-12.png) \#\#2015年1~12月份的前十大夯點

``` r
library(dplyr)
library(ggplot2)
library(stats)
library(base)

##2016-1月份資料 
test2016.all.jan <- subset(test2016.all, grepl("Jan", test2016.all$variable)) 
test2016.all.jan.head <- head(test2016.all.jan,10) 
test2016.all.jan.head[1:10,2]<-c("南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","淡水金色水岸","國立中正紀念堂","國父紀念館","獅頭山風景區","麻豆代天府","國立故宮博物院","旗津風景區")

ggplot(test2016.all.jan.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年1月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti"))  
```

![](README_files/figure-markdown_github/unnamed-chunk-6-1.png)

``` r
##2016-2月份資料 
test2016.all.feb <- subset(test2016.all, grepl("Feb", test2016.all$variable)) 
test2016.all.feb.head <- head(test2016.all.feb,10) 
test2016.all.feb.head[1:10,2]<-c("佛光山","北港朝天宮","臺中公園","東豐自行車綠廊及后豐鐵馬道","南鯤鯓代天府","駁二藝術特區","草悟道","十分旅遊服務中心","國立中正紀念堂","獅頭山風景區")

ggplot(test2016.all.feb.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年2月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-6-2.png)

``` r
##2016-3月份資料 
test2016.all.mar <- subset(test2016.all, grepl("Mar", test2016.all$variable)) 
test2016.all.mar.head <- head(test2016.all.mar,10) 
test2016.all.mar.head[1:10,2]<-c("北港朝天宮","佛光山","南鯤鯓代天府","陽明公園","草悟道","國立中正紀念堂","東豐自行車綠廊及后豐鐵馬道","淡水金色水岸","國父紀念館","日月潭風景區")

ggplot(test2016.all.mar.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年3月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-6-3.png)

``` r
##2016-4月份資料 
test2016.all.apr <- subset(test2016.all, grepl("Apr", test2016.all$variable)) 
test2016.all.apr.head <- head(test2016.all.apr,10) 
test2016.all.apr.head[1:10,2]<-c("東豐自行車綠廊及后豐鐵馬道","草悟道","南鯤鯓代天府","國立中正紀念堂","大甲鎮瀾宮","淡水金色水岸","國父紀念館","獅頭山風景區","駁二藝術特區","國立故宮博物院")

ggplot(test2016.all.apr.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年4月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-6-4.png)

``` r
##2016-5月份資料 
test2016.all.may <- subset(test2016.all, grepl("May", test2016.all$variable))
test2016.all.may.head <- head(test2016.all.may,10) 
test2016.all.may.head[1:10,2]<-c("南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","駁二藝術特區","淡水金色水岸","國立中正紀念堂","獅頭山風景區","國父紀念館","國立故宮博物院","麻豆代天府")

ggplot(test2016.all.may.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年5月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-6-5.png)

``` r
##2016-6月份資料 
test2016.all.jun <- subset(test2016.all, grepl("Jun", test2016.all$variable))
test2016.all.jun.head <- head(test2016.all.jun,10) 
test2016.all.jun.head[1:10,2]<-c("南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","淡水金色水岸","國立中正紀念堂","國父紀念館","獅頭山風景區","麻豆代天府","國立故宮博物院","旗津風景區")

ggplot(test2016.all.jun.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年6月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-6-6.png)

``` r
##2016-7月份資料 
test2016.all.jul <- subset(test2016.all, grepl("Jul", test2016.all$variable)) 
test2016.all.jul.head <- head(test2016.all.jul,11) 
test2016.all.jul.head <- test2016.all.jul.head[-7,] 
test2016.all.jul.head[1:10,2]<-c("南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","國立中正紀念堂","八卦山風景區 ","獅頭山風景區","福隆蔚藍海岸","國父紀念館","旗津風景區","駁二藝術特區")

ggplot(test2016.all.jul.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年7月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-6-7.png)

``` r
##2016-8月份資料 
test2016.all.aug <- subset(test2016.all, grepl("Aug", test2016.all$variable)) 
test2016.all.aug.head <- head(test2016.all.aug,10) 
test2016.all.aug.head[1:10,2]<-c("東豐自行車綠廊及后豐鐵馬道","草悟道","旗津風景區","獅頭山風景區","國立中正紀念堂","南鯤鯓代天府","八卦山風景區","國父紀念館","瑞芳風景特定區","駁二藝術特區")

ggplot(test2016.all.aug.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年8月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti")) 
```

![](README_files/figure-markdown_github/unnamed-chunk-6-8.png)

``` r
##2016-9月份資料 
test2016.all.sep <- subset(test2016.all, grepl("Sep", test2016.all$variable)) 
test2016.all.sep.head <- head(test2016.all.sep,10) 
test2016.all.sep.head[1:10,2]<-c("南鯤鯓代天府","東豐自行車綠廊及后豐鐵馬道","草悟道","八卦山風景區","國立中正紀念堂","國父紀念館","獅頭山風景區","旗津風景區","瑞芳風景特定區","八里左岸公園")

ggplot(test2016.all.sep.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年9月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-6-9.png)

``` r
##2016-10月份資料 
test2016.all.oct <- subset(test2016.all, grepl("Oct", test2016.all$variable)) 
test2016.all.oct.head <- head(test2016.all.oct,10) 
test2016.all.oct.head[1:10,2]<-c("蓮池潭","東豐自行車綠廊及后豐鐵馬道","南鯤鯓代天府","草悟道","獅頭山風景區","國立中正紀念堂","八卦山風景區","國父紀念館","旗津風景區","瑞芳風景特定區")

ggplot(test2016.all.oct.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年10月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-6-10.png)

``` r
##2016-11月份資料 
test2016.all.nov <- subset(test2016.all, grepl("Nov", test2016.all$variable)) 
test2016.all.nov.head <- head(test2016.all.nov,10) 
test2016.all.nov.head[1:10,2]<-c("東豐自行車綠廊及后豐鐵馬道","南鯤鯓代天府","草悟道","佛光山","獅頭山風景區","八卦山風景區","國立中正紀念堂","國父紀念館","瑞芳風景特定區","旗津風景區")

ggplot(test2016.all.nov.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年11月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-6-11.png)

``` r
##2016-12月份資料 
test2016.all.dec <- subset(test2016.all, grepl("Dec", test2016.all$variable)) 
test2016.all.dec <- subset(test2016.all, grepl("Dec", test2015.all$variable)) 
test2016.all.dec.head <- head(test2015.all.dec,10) 
test2016.all.dec.head[1:10,2]<-c("南鯤鯓代天府","草悟道","東豐自行車綠廊及后豐鐵馬道","國父紀念館","國立中正紀念堂","獅頭山風景區","國立故宮博物院","旗津風景區","佛光山","蓮池潭")

ggplot(test2016.all.dec.head, aes(reorder(`Scenic Spots`,value), value)) +  
  geom_bar(stat="identity", fill = "#ffcc66") +  
  xlab("觀光景點") + 
  ylab("遊客人數")+ 
  ggtitle("2016年12月份遊客人數最多的前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-6-12.png) \#\#2015與2016年1~12月份遊客人數漲勢最高前十名（月份分別） |2015||2016| |:---|:---:| |縣級風景特定區||| |彰化溪州費茲洛公園 Fitzroy Gardens||溪州公園 Sijhou Park| |三芝遊客中心||三芝遊憩區| |秀姑巒溪泛舟||秀姑巒溪遊客中心| |瑞太遊客服務中心||無| |澎湖水族館（關閉）||無|

``` r
library(dplyr)
library(ggplot2)
library(stats)
library(base)

##1月
test.all.jan.t <- full_join(test2015.all.jan, test2016.all.jan, by = c("Class", "Scenic Spots", "Location", "variable"))
##將2016沒有或2016才有的景點刪除
for(i in 1:ncol(test.all.jan.t)) 
{ 
  test.all.jan.t <- test.all.jan.t[!is.na(test.all.jan.t[,i]),] 
}
test.all.jan.t <- mutate(test.all.jan.t, improve = value.y-value.x)
#排序
test.all.jan.t <- head(test.all.jan.t[order(test.all.jan.t$improve, decreasing = T),],10)

test2015.all.jan.t <- test.all.jan.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015")
test2016.all.jan.t <- test.all.jan.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016")
test.all.jan <- full_join(test2015.all.jan.t, test2016.all.jan.t)
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.jan[11:20,5] <- test.all.jan[1:10,6]
test.all.jan[1:10,2]<-c("國父紀念館","蓮池潭","國立臺灣科學教育館","國立中正紀念堂","鹿港龍山寺","士林官邸公園","太魯閣國家公園遊客中心","小野柳(加路蘭)","國立科學工藝博物館","淡水漁人碼頭")
test.all.jan[11:20,2]<-c("國父紀念館","蓮池潭","國立臺灣科學教育館","國立中正紀念堂","鹿港龍山寺","士林官邸公園","太魯閣國家公園遊客中心","小野柳(加路蘭)","國立科學工藝博物館","淡水漁人碼頭")

#畫圖
ggplot(test.all.jan, aes(x=reorder(`Scenic Spots`, improve), value.x, fill = year))+
  geom_bar(stat = "identity", position = "dodge") +
  xlab("觀光景點") +
  ylab("遊客人數") +
  ggtitle("2015~2016年1月份遊客成長數前十名") +
  theme_minimal() +
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-1.png)

``` r
##2月
test.all.feb.t <- full_join(test2015.all.feb, test2016.all.feb, by = c("Class","Scenic Spots", "Location", "variable"))

test.all.feb.t <- mutate(test.all.feb.t, improve = value.y-value.x)
#排序
test.all.feb.t <- head(test.all.feb.t[order(test.all.feb.t$improve, decreasing = T),],10)

test2015.all.feb.t <- test.all.feb.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015")
test2016.all.feb.t <- test.all.feb.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016")
test.all.feb <- full_join(test2015.all.feb.t, test2016.all.feb.t)
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.feb[11:20,5] <- test.all.feb[1:10,6]
test.all.feb[1:10,2]<-c("佛光山","臺中公園","東豐自行車綠廊及后豐鐵馬道","駁二藝術特區","蓮池潭","士林官邸公園","鯉魚潭風景特定區","淡水金色水岸","國立臺灣科學教育館","溪州公園")
test.all.feb[11:20,2]<-c("佛光山","臺中公園","東豐自行車綠廊及后豐鐵馬道","駁二藝術特區","蓮池潭","士林官邸公園","鯉魚潭風景特定區","淡水金色水岸","國立臺灣科學教育館","溪州公園")

#畫圖
ggplot(test.all.feb, aes(x=reorder(`Scenic Spots`, improve), value.x, fill = year))+
  geom_bar(stat = "identity", position = "dodge") +
  xlab("觀光景點") +
  ylab("遊客人數") +
  ggtitle("2015~2016年2月份遊客成長數前十名") +
  theme_minimal() +
  theme(text = element_text(family = "STHeiti"))+coord_flip()
```

![](README_files/figure-markdown_github/unnamed-chunk-7-2.png)

``` r
##3月
test.all.mar.t <- full_join(test2015.all.mar, test2016.all.mar, by = c("Class", "Scenic Spots", "Location", "variable"))
##將2016沒有或2016才有的景點刪除
for(i in 1:ncol(test.all.mar.t)) 
{ 
  test.all.mar.t <- test.all.mar.t[!is.na(test.all.mar.t[,i]),] 
}
test.all.mar.t <- mutate(test.all.mar.t, improve = value.y-value.x)
#排序
test.all.mar.t <- head(test.all.mar.t[order(test.all.mar.t$improve, decreasing = T),],10)

test2015.all.mar.t <- test.all.mar.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015")
test2016.all.mar.t <- test.all.mar.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016")
test.all.mar <- full_join(test2015.all.mar.t, test2016.all.mar.t)
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.mar[11:20,5] <- test.all.mar[1:10,6]
test.all.mar[1:10,2]<-c("佛光山","淡水金色水岸","蓮池潭","駁二藝術特區","關子嶺溫泉區","日月潭風景區","貓鼻頭公園","陽明公園","臺北101景觀臺","野柳地質公園")
test.all.mar[11:20,2]<-c("佛光山","淡水金色水岸","蓮池潭","駁二藝術特區","關子嶺溫泉區","日月潭風景區","貓鼻頭公園","陽明公園","臺北101景觀臺","野柳地質公園")

#畫圖
ggplot(test.all.mar, aes(x=reorder(`Scenic Spots`, improve), value.x, fill = year))+
  geom_bar(stat = "identity", position = "dodge") +
  xlab("觀光景點") +
  ylab("遊客人數") +
  ggtitle("2015~2016年3月份遊客成長數前十名") +
  theme_minimal() +
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-3.png)

``` r
##4月
test.all.apr.t <- full_join(test2015.all.apr, test2016.all.apr, by = c("Class", "Scenic Spots", "Location", "variable"))
##將2016沒有或2016才有的景點刪除
for(i in 1:ncol(test.all.apr.t)) 
{ 
  test.all.apr.t <- test.all.apr.t[!is.na(test.all.apr.t[,i]),] 
}
test.all.apr.t <- mutate(test.all.apr.t, improve = value.y-value.x)
#排序
test.all.apr.t <- head(test.all.apr.t[order(test.all.apr.t$improve, decreasing = T),],10)

test2015.all.apr.t <- test.all.apr.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015")
test2016.all.apr.t <- test.all.apr.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016")
test.all.apr <- full_join(test2015.all.apr.t, test2016.all.apr.t)
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.apr[11:20,5] <- test.all.apr[1:10,6]
test.all.apr[1:10,2]<-c("東豐自行車綠廊及后豐鐵馬道","淡水金色水岸","蓮池潭","駁二藝術特區","臺中公園","池上牧野渡假村","淡水漁人碼頭","台北當代藝術館","龍潭湖","市立動物園")
test.all.apr[11:20,2]<-c("東豐自行車綠廊及后豐鐵馬道","淡水金色水岸","蓮池潭","駁二藝術特區","臺中公園","池上牧野渡假村","淡水漁人碼頭","台北當代藝術館","龍潭湖","市立動物園")
#畫圖
ggplot(test.all.apr, aes(x=reorder(`Scenic Spots`, improve), value.x, fill = year))+
  geom_bar(stat = "identity", position = "dodge") +
  xlab("觀光景點") +
  ylab("遊客人數") +
  ggtitle("2015~2016年4月份遊客成長數前十名") +
  theme_minimal() +
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-4.png)

``` r
##5月
test.all.may.t <- full_join(test2015.all.may, test2016.all.may, by = c("Class", "Scenic Spots", "Location", "variable"))
##將2016沒有或2016才有的景點刪除
for(i in 1:ncol(test.all.may.t)) 
{ 
  test.all.may.t <- test.all.may.t[!is.na(test.all.may.t[,i]),] 
}
test.all.may.t <- mutate(test.all.may.t, improve = value.y-value.x)
#排序
test.all.may.t <- head(test.all.may.t[order(test.all.may.t$improve, decreasing = T),],10)

test2015.all.may.t <- test.all.may.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015")
test2016.all.may.t <- test.all.may.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016")
test.all.may <- full_join(test2015.all.may.t, test2016.all.may.t)
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.may[11:20,5] <- test.all.may[1:10,6]
test.all.may[1:10,2]<-c("淡水金色水岸","蓮池潭","駁二藝術特區","東豐自行車綠廊及后豐鐵馬道","池上牧野渡假村","關子嶺溫泉區","龍潭湖 ","大坑登山步道","白沙灣","國立臺灣科學教育館")
test.all.may[11:20,2]<-c("淡水金色水岸","蓮池潭","駁二藝術特區","東豐自行車綠廊及后豐鐵馬道","池上牧野渡假村","關子嶺溫泉區","龍潭湖 ","大坑登山步道","白沙灣","國立臺灣科學教育館")

#畫圖
ggplot(test.all.may, aes(x=reorder(`Scenic Spots`, improve), value.x, fill = year))+
  geom_bar(stat = "identity", position = "dodge") +
  xlab("觀光景點") +
  ylab("遊客人數") +
  ggtitle("2015~2016年5月份遊客成長數前十名") +
  theme_minimal() +
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-5.png)

``` r
##6月
test.all.jun.t <- full_join(test2015.all.jun, test2016.all.jun, by = c("Class", "Scenic Spots", "Location", "variable"))
##將2016沒有或2016才有的景點刪除
for(i in 1:ncol(test.all.jun.t)) 
{ 
  test.all.jun.t <- test.all.jun.t[!is.na(test.all.jun.t[,i]),] 
}
test.all.jun.t <- mutate(test.all.jun.t, improve = value.y-value.x)
#排序
test.all.jun.t <- head(test.all.jun.t[order(test.all.jun.t$improve, decreasing = T),],10)

test2015.all.jun.t <- test.all.jun.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015")
test2016.all.jun.t <- test.all.jun.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016")
test.all.jun <- full_join(test2015.all.jun.t, test2016.all.jun.t)
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.jun[11:20,5] <- test.all.jun[1:10,6]
test.all.jun[1:10,2]<-c("淡水金色水岸","蓮池潭","駁二藝術特區","池上牧野渡假村","石門水庫風景區","國立科學工藝博物館","關子嶺溫泉區","國立臺灣科學教育館","福隆遊客服務中心","三芝遊憩區")
test.all.jun[11:20,2]<-c("淡水金色水岸","蓮池潭","駁二藝術特區","池上牧野渡假村","石門水庫風景區","國立科學工藝博物館","關子嶺溫泉區","國立臺灣科學教育館","福隆遊客服務中心","三芝遊憩區")

#畫圖
ggplot(test.all.feb, aes(x=reorder(`Scenic Spots`, improve), value.x, fill = year))+
  geom_bar(stat = "identity", position = "dodge") +
  xlab("觀光景點") +
  ylab("遊客人數") +
  ggtitle("2015~2016年6月份遊客成長數前十名") +
  theme_minimal() +
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-6.png)

``` r
#7月份資料 
test.all.jul.t <- full_join(test2015.all.jul, test2016.all.jul, by = c("Class", "Scenic Spots", "Location", "variable")) 
##將2016沒有或2016才有的景點刪除 
for(i in 1:ncol(test.all.jul.t))  
{  
  test.all.jul.t <- test.all.jul.t[!is.na(test.all.jul.t[,i]),]  
} 
test.all.jul.t <- mutate(test.all.jul.t, improve = value.y-value.x) 
#排序 
test.all.jul.t <- head(test.all.jul.t[order(test.all.jul.t$improve, decreasing = T),],11) 

test2015.all.jul.t <- test.all.jul.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015") 
test2016.all.jul.t <- test.all.jul.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016") 
test.all.jul <- full_join(test2015.all.jul.t, test2016.all.jul.t)
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.jul <- test.all.jul[c(-1,-12),]
test.all.jul[11:20,5] <- test.all.jul[1:10,6]
test.all.jul[1:10,2]<-c("福隆蔚藍海岸","駁二藝術特區","八卦山風景區","蓮池潭","池上牧野渡假村","國立科學工藝博物館","獅頭山風景區","味全埔心牧場","國立臺灣美術館","東豐自行車綠廊及后豐鐵馬道")
test.all.jul[11:20,2]<-c("福隆蔚藍海岸","駁二藝術特區","八卦山風景區","蓮池潭","池上牧野渡假村","國立科學工藝博物館","獅頭山風景區","味全埔心牧場","國立臺灣美術館","東豐自行車綠廊及后豐鐵馬道")

#畫圖 
ggplot(test.all.jul, aes(x=reorder(`Scenic Spots`,improve), value.x, fill = year))+ 
  geom_bar(stat = "identity", position = "dodge") + 
  xlab("觀光景點") + 
  ylab("遊客人數") + 
  ggtitle("2015~2016年7月份遊客成長數前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-7.png)

``` r
#8月份資料 
test.all.aug.t <- full_join(test2015.all.aug, test2016.all.aug, by = c("Class", "Scenic Spots", "Location", "variable")) 
##將2016沒有或2016才有的景點刪除 
for(i in 1:ncol(test.all.aug.t))  
{  
  test.all.aug.t <- test.all.aug.t[!is.na(test.all.aug.t[,i]),]  
} 
test.all.aug.t <- mutate(test.all.aug.t, improve = value.y-value.x) 
#排序 
test.all.aug.t <- head(test.all.aug.t[order(test.all.aug.t$improve, decreasing = T),],10) 

test2015.all.aug.t <- test.all.aug.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015") 
test2016.all.aug.t <- test.all.aug.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016") 
test.all.aug <- full_join(test2015.all.aug.t, test2016.all.aug.t) 
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.aug[11:20,5] <- test.all.aug[1:10,6]
test.all.aug[1:10,2]<-c("東豐自行車綠廊及后豐鐵馬道","旗津風景區","蓮池潭","八卦山風景區","十七公里海岸觀光帶","駁二藝術特區","獅頭山風景區","池上牧野渡假村","世運主場館","國立科學工藝博物館")
test.all.aug[11:20,2]<-c("東豐自行車綠廊及后豐鐵馬道","旗津風景區","蓮池潭","八卦山風景區","十七公里海岸觀光帶","駁二藝術特區","獅頭山風景區","池上牧野渡假村","世運主場館","國立科學工藝博物館")

#畫圖 
ggplot(test.all.aug, aes(x=reorder(`Scenic Spots`,improve), value.x, fill = year))+ 
  geom_bar(stat = "identity", position = "dodge") + 
  xlab("觀光景點") + 
  ylab("遊客人數") + 
  ggtitle("2015~2016年8月份遊客成長數前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-8.png)

``` r
#9月份資料 
test.all.sep.t <- full_join(test2015.all.sep, test2016.all.sep, by = c("Class", "Scenic Spots", "Location", "variable")) 
##將2016沒有或2016才有的景點刪除 
for(i in 1:ncol(test.all.sep.t))  
{  
  test.all.sep.t <- test.all.sep.t[!is.na(test.all.sep.t[,i]),]  
} 
test.all.sep.t <- mutate(test.all.sep.t, improve = value.y-value.x) 
#排序 
test.all.sep.t <- head(test.all.sep.t[order(test.all.sep.t$improve, decreasing = T),],10) 

test2015.all.sep.t <- test.all.sep.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015") 
test2016.all.sep.t <- test.all.sep.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016") 
test.all.sep <- full_join(test2015.all.sep.t, test2016.all.sep.t) 
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.sep[11:20,5] <- test.all.sep[1:10,6] 
test.all.sep[1:10,2]<-c("八卦山風景區","駁二藝術特區","國立科學工藝博物館","南鯤鯓代天府","梧棲觀光漁港","獅頭山風景區","關子嶺溫泉區","東豐自行車綠廊及后豐鐵馬道","烏來風景特定區","安平小鎮")
test.all.sep[11:20,2]<-c("八卦山風景區","駁二藝術特區","國立科學工藝博物館","南鯤鯓代天府","梧棲觀光漁港","獅頭山風景區","關子嶺溫泉區","東豐自行車綠廊及后豐鐵馬道","烏來風景特定區","安平小鎮")

#畫圖 
ggplot(test.all.sep, aes(x=reorder(`Scenic Spots`,improve), value.x, fill = year))+ 
  geom_bar(stat = "identity", position = "dodge") + 
  xlab("觀光景點") + 
  ylab("遊客人數") + 
  ggtitle("2015~2016年9月份遊客成長數前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-9.png)

``` r
#10月份資料 
test.all.oct.t <- full_join(test2015.all.oct, test2016.all.oct, by = c("Class", "Scenic Spots", "Location", "variable")) 
##將2016沒有或2016才有的景點刪除 
for(i in 1:ncol(test.all.oct.t))  
{  
  test.all.oct.t <- test.all.oct.t[!is.na(test.all.oct.t[,i]),]  
} 
test.all.oct.t <- mutate(test.all.oct.t, improve = value.y-value.x) 
#排序 
test.all.oct.t <- head(test.all.oct.t[order(test.all.oct.t$improve, decreasing = T),],10) 

test2015.all.oct.t <- test.all.oct.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015") 
test2016.all.oct.t <- test.all.oct.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016") 
test.all.oct <- full_join(test2015.all.oct.t, test2016.all.oct.t) 
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.oct[11:20,5] <- test.all.oct[1:10,6]
test.all.oct[1:10,2]<-c("東豐自行車綠廊及后豐鐵馬道","八卦山風景區","獅頭山風景區","關子嶺溫泉區","北港朝天宮","梧棲觀光漁港","安平小鎮","三芝遊憩區","高雄市文化中心","國立臺灣歷史博物館")
test.all.oct[11:20,2]<-c("東豐自行車綠廊及后豐鐵馬道","八卦山風景區","獅頭山風景區","關子嶺溫泉區","北港朝天宮","梧棲觀光漁港","安平小鎮","三芝遊憩區","高雄市文化中心","國立臺灣歷史博物館")

#畫圖 
ggplot(test.all.oct, aes(x=reorder(`Scenic Spots`,improve), value.x, fill = year))+ 
  geom_bar(stat = "identity", position = "dodge") + 
  xlab("觀光景點") + 
  ylab("遊客人數") + 
  ggtitle("2015~2016年10月份遊客成長數前十名") + 
  theme_minimal() + 
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-10.png)

``` r
#11月份資料 
test.all.nov.t <- full_join(test2015.all.nov, test2016.all.nov, by = c("Class", "Scenic Spots", "Location", "variable")) 
##將2016沒有或2016才有的景點刪除 
for(i in 1:ncol(test.all.nov.t))  
{  
  test.all.nov.t <- test.all.nov.t[!is.na(test.all.nov.t[,i]),]  
} 
test.all.nov.t <- mutate(test.all.nov.t, improve = value.y-value.x) 
#排序 
test.all.nov.t <- head(test.all.nov.t[order(test.all.nov.t$improve, decreasing = T),],10) 

test2015.all.nov.t <- test.all.nov.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015") 
test2016.all.nov.t <- test.all.nov.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016") 
test.all.nov <- full_join(test2015.all.nov.t, test2016.all.nov.t) 
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.nov[11:20,5] <- test.all.nov[1:10,6] 
test.all.nov[1:10,2]<-c("八卦山風景區","獅頭山風景區","國立臺灣科學教育館","大湖草莓文化館","安平小鎮","關子嶺溫泉區","溪州公園","國立臺灣歷史博物館","石門水庫風景區","高雄市文化中心")
test.all.nov[11:20,2]<-c("八卦山風景區","獅頭山風景區","國立臺灣科學教育館","大湖草莓文化館","安平小鎮","關子嶺溫泉區","溪州公園","國立臺灣歷史博物館","石門水庫風景區","高雄市文化中心")

#畫圖 
ggplot(test.all.nov, aes(x=reorder(`Scenic Spots`,improve), value.x, fill = year))+ 
  geom_bar(stat = "identity", position = "dodge") + 
  xlab("觀光景點") + 
  ylab("遊客人數") + 
  ggtitle("2015~2016年11月份遊客成長數前十名") + 
  theme_minimal() + 
  coord_flip() +
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-11.png)

``` r
#12月份資料 
test.all.dec.t <- full_join(test2015.all.dec, test2016.all.dec, by = c("Class", "Scenic Spots", "Location", "variable")) 
##將2016沒有或2016才有的景點刪除 
for(i in 1:ncol(test.all.dec.t))  
{  
  test.all.dec.t <- test.all.dec.t[!is.na(test.all.dec.t[,i]),]  
} 
test.all.dec.t <- mutate(test.all.dec.t, improve = value.y-value.x) 
#排序 
test.all.dec.t <- head(test.all.dec.t[order(test.all.dec.t$improve, decreasing = T),],10) 

test2015.all.dec.t <- test.all.dec.t %>% group_by(`Scenic Spots`, value.x)%>%mutate(year = "2015") 
test2016.all.dec.t <- test.all.dec.t %>% group_by(`Scenic Spots`, value.y)%>%mutate(year = "2016") 
test.all.dec <- full_join(test2015.all.dec.t, test2016.all.dec.t) 
```

    ## Joining, by = c("Class", "Scenic Spots", "Location", "variable", "value.x", "value.y", "improve", "year")

``` r
test.all.dec[11:20,5] <- test.all.dec[1:10,6]
test.all.dec[1:10,2]<-c("士林官邸公園","八卦山風景區","獅頭山風景區","東豐自行車綠廊及后豐鐵馬道","大湖草莓文化館","八里左岸公園","碧潭風景特定區","內灣風景區","市立動物園","佛光山")
test.all.dec[11:20,2]<-c("士林官邸公園","八卦山風景區","獅頭山風景區","東豐自行車綠廊及后豐鐵馬道","大湖草莓文化館","八里左岸公園","碧潭風景特定區","內灣風景區","市立動物園","佛光山")

#畫圖 
ggplot(test.all.dec, aes(x=reorder(`Scenic Spots`,improve), value.x, fill = year))+ 
  geom_bar(stat = "identity", position = "dodge") + 
  xlab("觀光景點") + 
  ylab("遊客人數") + 
  ggtitle("2015~2016年12月份遊客成長數前十名") + 
  theme_minimal() + 
  coord_flip()+
  theme(text = element_text(family = "STHeiti"))
```

![](README_files/figure-markdown_github/unnamed-chunk-7-12.png)

地圖
----

``` r
#2016景點 
library(ggmap)  
taiwanmap <- get_map(location  = 'Taiwan',   
                        zoom = 8,  
                        maptype = "roadmap",  
                        language = "zh-TW") 
```

    ## Map from URL : http://maps.googleapis.com/maps/api/staticmap?center=Taiwan&zoom=8&size=640x640&scale=2&maptype=roadmap&language=zh-TW&sensor=false

    ## Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=Taiwan&sensor=false

``` r
ggmap(taiwanmap)+ 
  #南鯤鯓代天府 
  geom_point(aes(x=120.143120,  
                 y=23.286023),  
             color="red",  
             size = 2)+  
  #東豐自行車綠廊及后豐鐵馬道  
  geom_point(aes(x=120.742013,  
                 y=24.272879),  
             color="red",  
             size = 2)+  
  #草悟道 
  geom_point(aes(x=120.664802,  
                 y=24.148381),  
             color="red",  
             size = 2)+  
  #國立中正紀念堂  
  geom_point(aes(x=121.518620,  
                 y=25.036211),  
             color="red",  
             size = 2) + 
  #國父紀念館 
  geom_point(aes(x=121.560352,  
                 y=25.040157),  
             color="red",  
             size = 2) + 
  #獅頭山風景區 
  geom_point(aes(x=121.006111,  
                 y=24.644357),  
             color="red",  
             size = 2) + 
  #旗津風景區 
  geom_point(aes(x=120.268469,  
                 y=22.613605),  
             color="red",  
             size = 2) 
```

![](README_files/figure-markdown_github/unnamed-chunk-8-1.png)

``` r
#高雄 
kaohsiungmap <- get_map(location  = c(120,22.4,120.6,22.9),   
                        zoom = 11,  
                        maptype = "roadmap",  
                        language = "zh-TW")  
```

    ## Warning: bounding box given to google - spatial extent only approximate.

    ## converting bounding box to center/zoom specification. (experimental)

    ## Map from URL : http://maps.googleapis.com/maps/api/staticmap?center=22.65,120.3&zoom=11&size=640x640&scale=2&maptype=roadmap&language=zh-TW&sensor=false

``` r
ggmap(kaohsiungmap)+  
  #蓮池潭  
  geom_point(aes(x=120.296701,  
                 y=22.683352),  
             color="red",  
             size = 3) +  
  #國立科學工藝博物館  
  geom_point(aes(x=120.322694,  
                 y=22.640573),  
             color="red",  
             size = 3) + 
  #佛光山 
  geom_point(aes(x=120.445914,  
                 y=22.747335),  
             color="red",  
             size = 3) + 
  #駁二藝術特區 
  geom_point(aes(x=120.281614,  
                 y=22.619972),  
             color="red",  
             size = 3) + 
  #旗津風景區 
  geom_point(aes(x=120.268469,  
                 y=22.613605),  
             color="red",  
             size = 3) + 
  #高雄市文化中心 
  geom_point(aes(x=120.317365,  
                 y=22.627668),  
             color="red",  
             size = 3)  
```

![](README_files/figure-markdown_github/unnamed-chunk-8-2.png)

``` r
#臺中 
taichungmap <- get_map(location  = c(119.8,23.93,121.50,24.46),   
                       zoom = 11,  
                       maptype = "roadmap",  
                       language = "zh-TW")  
```

    ## Warning: bounding box given to google - spatial extent only approximate.

    ## converting bounding box to center/zoom specification. (experimental)

    ## Map from URL : http://maps.googleapis.com/maps/api/staticmap?center=24.195,120.65&zoom=11&size=640x640&scale=2&maptype=roadmap&language=zh-TW&sensor=false

``` r
ggmap(taichungmap)+  
  #臺中公園  
  geom_point(aes(x=120.684464,  
                 y=24.144823),  
             color="red",  
             size = 3)+  
  #東豐自行車綠廊及后豐鐵馬道  
  geom_point(aes(x=120.742013,  
                 y=24.272879),  
             color="red",  
             size = 3) + 
  #大坑登山步道 
  geom_point(aes(x=120.733681,  
                 y=24.180191),  
             color="red",  
             size = 3) + 
  #國立臺灣美術館 
  geom_point(aes(x=120.663623,  
                 y=24.140977),  
             color="red",  
             size = 3) +  
  #梧棲觀光漁港 
  geom_point(aes(x=120.518074,  
                 y=24.292630),  
             color="red",  
             size = 3) + 
   #佛光山 
  geom_point(aes(x=120.641054,  
                 y=24.149138),  
             color="red",  
             size = 3) +  
  #草悟道 
  geom_point(aes(x=120.664802,  
                 y=24.148381),  
             color="red",  
             size = 3)
```

![](README_files/figure-markdown_github/unnamed-chunk-8-3.png)
