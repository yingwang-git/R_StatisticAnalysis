# Table of Contents
1. [Example](#data-information)
2. [Example2](#example2)
3. [Third Example](#third-example)
4. [Fourth Example](#fourth-examplehttpwwwfourthexamplecom)

# Data Information

数据来源于《应用商务统计分析》第一章线性回归
（王汉生. 应用商务统计分析[M]. 北京大学出版社, 2008.）

|变量|变量名称| 定义        |
|----|----|-----------|
|因变量|ROE| 来年的净资产收益率 |
|自变量|ROEt| 当年的净资产收益率 |
| |ATO| 资产周转率     |
||PM|主营业务利润/主营业务收入|
||LEV|债务资本比率|
||GROWTH|主营业务收入增长率|
||PB|市倍率|
||ARR|应收账款/主营业务收入|
||INV|存货/资产总计|
||ASSET|对数后的资产总计|




```R
data <- read.csv("data/data_LinearRegression.csv")
```


```R
head(data)
```


<table class="dataframe">
<caption>A data.frame: 6 × 11</caption>
<thead>
	<tr><th></th><th scope=col>year</th><th scope=col>ROEt</th><th scope=col>ATO</th><th scope=col>PM</th><th scope=col>LEV</th><th scope=col>GROWTH</th><th scope=col>PB</th><th scope=col>ARR</th><th scope=col>INV</th><th scope=col>ASSET</th><th scope=col>ROE</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>1</th><td>2002</td><td> 0.296</td><td>0.389</td><td> 0.215</td><td> 4.384</td><td> 0.197</td><td>  8.048</td><td> 0.637</td><td>0.248</td><td>20.873</td><td> 0.181</td></tr>
	<tr><th scope=row>2</th><td>2002</td><td> 0.665</td><td>0.335</td><td> 0.407</td><td> 2.273</td><td>-0.084</td><td>  2.154</td><td>-1.217</td><td>0.030</td><td>21.062</td><td> 0.899</td></tr>
	<tr><th scope=row>3</th><td>2002</td><td>-0.045</td><td>0.963</td><td> 0.084</td><td>-5.339</td><td> 4.155</td><td>-12.879</td><td>-0.598</td><td>0.096</td><td>21.474</td><td> 1.504</td></tr>
	<tr><th scope=row>4</th><td>2002</td><td>-0.783</td><td>0.437</td><td>-0.096</td><td> 1.016</td><td> 2.231</td><td>  3.836</td><td> 0.056</td><td>0.051</td><td>19.746</td><td>-0.777</td></tr>
	<tr><th scope=row>5</th><td>2002</td><td> 1.053</td><td>0.858</td><td>-0.154</td><td>-2.066</td><td> 0.519</td><td>  1.972</td><td> 0.293</td><td>0.032</td><td>22.570</td><td> 0.728</td></tr>
	<tr><th scope=row>6</th><td>2002</td><td> 0.590</td><td>0.528</td><td> 0.197</td><td>-0.946</td><td>-1.441</td><td>  2.405</td><td>-0.986</td><td>0.189</td><td>20.890</td><td> 1.141</td></tr>
</tbody>
</table>




```R

```
