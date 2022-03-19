---
title: "Data Analysis Template"
author: "W.Y."
date: "2022/3/10"
output: 
  html_document:
    toc: true
    toc_float: true
    toc_depth: 4
    number_sections: true
---



# 数据分析思路
1. 数据读取，基本情况  
2. 变量处理：变量转换、分组、计算等  
3. 数据筛选：只要某些行或某些列等  
4. 描述性分析：看数据分布情况、看相关性  
5. 选择模型进行分析  
6. 模型检验：比如回归中是否存在共线性、异方差等  
7. 稳健性检验：数据、变量、模型  


# 数据问题排查思路
1. 数据  
  1）数据是否正确：数据来源、数据读取、数据类型、数据筛选等  
  2）是否有离群值：看分布  
  3）样本量是否足够？  
2. 变量  
  1）计算是否正确？  
  2）有没有其他的测量方式？  
  3）合并测量？  
  4）控制变量的选择  
  5）是否有遗漏变量  
  6）变量滞后？  
3. 模型  
  1）模型是否合适？线性非线性？面板？  
  2）多重共线性  
  3）异方差：取对数/加权最小二乘法  



# setup

```{r}
rm(list=ls())  # 清理工作空间
options(max.print = 1000)  # 设置打印长度
```

## package
```{r}
if (!require("pacman")) install.packages("pacman")
pacman::p_load(
  readxl
  )
```


# data
## read data
```{r}
data <- readxl::read_excel("")  # excel数据
data <- read.csv("")  # csv数据
data <- read.table("")  # txt数据

# 不建议attach(data)，数据多时容易混乱
```


## merge data
```{r}
data_merge <- cbind(data1, data2)  # 合并列（左右合并）
data_merge <- rbind(data1, data2)  # 合并行（上下合并）

data_merge <- merge(data1, data2, by.x = "ID", by.y = "ID", all.x = TRUE)  # 根据关键列匹配
```


## basic info
```{r}
head(data, 10)  # 数据预览
dim(data)  # 数据维度
str(data)  # 查看数据类型
names(data)  # 查看所有变量名
```


## NA
```{r}
# 统计各个变量的NA值数量
colSums(is.na(data))


# 删除NA值
data_complete <- data[complete.cases(data[, c("x", "y")]), ]   # 关键变量没有缺失值

```


## variable
```{r}
# 生成变量
data$v_group <- ifelse(data$v > 0, "high", "low") # 变量分组

# 变量转换
data$v_log <- log(data$v) # 取对数
data$v_log <- log(data$v + 1)  # 存在0的数据取对数
data$v_scale <- scale(data$v, center = T, scale = T) # 

# 批量转换
log <- log(data[, 2:10] + 1)  # 存在0的数据需要+1
names(log) <- paste("log_", names(log), sep="")  # 变量重命名
data_log <- cbind(data, log)  # 合并数据


# 变量计算
data$z <- data$a + data$b  # + - * /

# 变量类型转换
data$date_format <- openxlsx::convertToDate(data$date)  # 日期格式的字符串转化为日期，方便画图什么的

# 统计分类变量水平
dplyr::n_distinct(data$x)  # 有几个水平
unique(data$x)  # 每个水平是啥
```


## data aggregate

```{r}
# 分类汇总
pacman::p_load(dplyr)
data_aggregate <-
  data %>% 
  group_by(year) %>%
  summarise(count = n(), # 计数
            mean = mean(x, na.rm=TRUE),  # 均值
            median = median(x, na.rm=TRUE),  # 中位数
            )  # 按年份汇总计算

# 按行求均值
data$row_mean <- rowMeans(data[, 2:5])  # 按行计算2到5列的均值生成新变量
```



## data filter
```{r}
# 筛选行
data_new <- data[data$v > 0, ]
data_new <- data[data$v1 != "不要的数据", ]
data_new <- data[which(data$v2%in%c("只要这组1", "只要这组2")),]  # 多组
data_new <- data[!which(data$v2%in%c("不要这组1", "不要这组2")),]
data_new <- data[data$v1 == "要的数据" & !is.na(data$v2), ]  # 多条件，且&，或|


# 筛选列
data_new <- data[, c("只要这列1", "只要这列2")]
data_new <- data[, 2:10]  # 保留2到10列
data_new <- data[, c(2:4, 7:8, 10)]  # 保留2-4，7-8和第10列


# 保留三倍标准差内的数据
high <- mean(data$v) + 3*sd(data$v)
low <- mean(data$v) - 3*sd(data$v)
data_new <- data[data$v <= high & data$v >= low, ]
```


# descriptive
## distribution
```{r}
# 画图
hist(data$v)  # 看分布
boxplot(data$v)  # 看离群值

# 看分布是否均匀，主要集中在哪些区间
table(cut(data$v, breaks = 10))
```


## descriptive statistics
```{r}
summary(data$x)  # 单个描述
psych::describe(data[, 2:10])  # 批量描述，基于列索引，第2到10列
psych::describe(data[c('x', 'y', 'z')])  # 批量描述，基于列名

Hmisc::describe(data)  # 另一种更详细的描述

# 保存结果
descriptive_result <- psych::describe(data[, 2:10])
write.csv(descriptive_result, "descriptive_result.csv")
```



## descriptive statistics by group

```{r}
table(data$x)  # 分类变量分组计数

# 分组计算均值，标准差，最小值，最大值，样本量
doBy::summaryBy(data[, 2:10] ~ group_variable, 
          data = data, FUN = c(mean, sd, min, max, length))  
```


## correlation
```{r}
# 双变量相关
cor.test(data$x, data$y)  # 显著性
plot(data$x, data$y)  # 散点图
table(data$x, data$y)  # 列联表

# 多变量两两相关，画图
matrix <- as.matrix(data[,c("x", "y", "z")])
PerformanceAnalytics::chart.Correlation(matrix, histogram = TRUE)


# 多变量两两相关，显著性
correlations <- Hmisc::rcorr(as.matrix(data[, 2:5]))
correlations$r  # 相关系数
correlations$P  # 显著性
```


# plot
```{r}
# 好看的散点图+拟合线（自变量因变量都连续）

ggplot2::ggplot(data, aes(x, y)) + 
  geom_point(size = 1) + 
  geom_smooth(method = "lm", size = 1.5, color = "black", se = F, linetype = 5) + 
  labs(x = 'x', y = 'y') + 
  theme_classic() + 
  theme(text = element_text(size = 12, color = "black", family = "serif"))


# 好看的折线图（因变量连续，自变量分类）
plot1 <- ggplot2::ggplot(data, aes(x=factor(LEVEL, level = c("level1", "level2", "level3")), y=y, group=1)) + 
  geom_path(size = 1) + 
  geom_point(size = 2) + 
  labs(x = 'x', y = 'y') + 
  theme_classic() + 
  theme(text = element_text(size = 12, color = "black", family = "serif"))

# 保存图片
ggsave("image.png", plot1, width = 6, height = 5, dpi = 600)


# 其他图
# geom_histogram()
# geom_boxplot()
# geom_bar()
```


# model
## linear regression
```{r}
lm <- lm(y ~ x1 + x2 + x3, data = data)
summary(lm)
summary(step(lm))  # 逐步回归
```

## U-shaped
```{r}
# 1. 加入二次项
lm <- lm(y ~ x + I(x^2) + c1 + c2 + c3, data = data)
summary(lm)  # 二次项显著为正则为U型，显著为负则为倒U型

# 2. 计算转折点
# turning_point = -b/(2a)，其中a为二次项系数，b为一次项的系数
result <- summary(lm)
turning_point <- -(result$coefficients[2][1]/(2*(result$coefficients[3][1])))
# 如果转折点在数据范围内或可能范围内，则U型关系确实成立

# 3. 断点回归
data$x_low <- ifelse(data$x < turning_point, data$x - turning_point, 0)
data$x_high <- ifelse(data$x >= turning_point, data$x - turning_point, 0)
data$x_bigb <- ifelse(data$x >= turning_point, 1, 0)

lm <- lm(y ~ x_low + x_high + x_bigb + c1 + c2 + c3, data = data)
summary(lm)  # x_low和x_high都显著，证明U型关系成立
```


## glm
### logistic
```{r}
glm <- glm(y ~ x1 + x2 + x3, data = data, family = binomial('logit'))  
# y为二分类变量

glm <- glm(y ~ x1 + x2 + x3, data = data, family = binomial('logit'), weights = v_weights)  
# y为比例变量，weights为分母。如y为购买转化率=购买人数/浏览人数，则weights是浏览人数

summary(glm)
```

### probit
```{r}
glm <- glm(y ~ x1 + x2 + x3, data = data, family = binomial('probit'))
summary(glm)
```


### possion
```{r}
poisson <- glm(y  ~ x1 + x2 + x3, data = data, family = poisson(link = "log"))
# y为计数型变量

summary(poisson)
```



## mediation
```{r}
# change digit behind comma in mediation print
trace(mediation:::print.summary.mediate, 
      at = 11,
      tracer = quote({
        printCoefmat <- function(x, digits) {
          p <- x[, 4] #p-values seem to be stored rounded
          x[, 1:3] <- sprintf("%.6f", x[, 1:3])
          x[, 4] <- sprintf("%.2f", p)
          print(x, quote = FALSE, right = TRUE)
        } 
      }),
      print = FALSE)
```


```{r}
lm0 <- lm(mediator ~ x + c1 + c2 + c3, data = data)
# summary(lm0)

lm1 <- lm(y ~ x + mediator+ c1 + c2 + c3, data = data)
# summary(lm1)
  
set.seed(2021)
med <- mediation::mediate(lm0, lm1, sims = 5000,  treat = "x", mediator = "mediator", control.value = "control", treat.value = "treat")  # control value是x中的控制组或基准组，如果x是连续变量，则不用 
mediation:::print.summary.mediate(summary(med))
```


## moderation
### interaction
```{r}
lm <- lm(y ~ x * w + c1 + c2, data = data)
summary(lm)
```


### interaction plot
```{r}
labels <- list(X="x", W="w", Y="y", C1="c1", C2="c2")
moderator <- list(name="w", site=list("c"))  # 可以多个moderator
covar <- list(names=c("c1", "c2"), site=list("Y", "Y")) # 控制变量

interactions::interact_plot(model = lm, pred = x, modx = w, x.label = "x", y.label = "y", legend.main = "Interaction Plot")
```


## PSM
```{r}
PSM_data <- data_cele[, c('x', 'y', 'c1', 'c2', 'c3')]  # 只要做PSM的变量，自变量、因变量、控制变量
PSM.1 <- MatchIt::matchit(x ~ c1 + c2 + c3, data = PSM_data, method = "nearest", distance="logit")  # x为二分类变量

data_PSM.1 <- match.data(PSM.1)
table1 <- tableone::CreateTableOne(vars = c('y', 'c1', 'c2', 'c3'), data = data_PSM.1, strata = "x") 

table1 <- print(table1, printToggle = FALSE, noSpaces = TRUE) 
knitr::kable(table1[,1:3], align = 'c',caption = 'Table')
```


## Panel
```{r}
# 简易版
data_panel <- plm::pdata.frame(data, index=c("individual", "year"))

plm <- plm(y ~ x, data = data_panel, model = "within")
summary(plm)
```


## PCA
```{r}
data$PCA_score <- prcomp(data[, 2:5], scale = TRUE, center = TRUE, rank. = 1)[['x']]  # [['x']]不更改
```


# 问题检验
```{r}
# 共线性：vif值大于5或大于10则存在共线性问题
car::vif(lm)

# 异方差：显著则拒绝原假设，存在异方差问题
lmtest::bptest(lm)
```



# 批量分析
## 多个因变量分别跑
```{r}
# 方法一
for (i in c(2:5)){
  print(names(data[i]))
  lm <- lm(data[[i]] ~ a + b + c, data = data)
  result <- summary(lm)
  print(result)
}


# 方法二
lm <- lm(cbind(y1, y2) ~ a + b + c, data = data)
summary(lm)
```


## 多个自变量分别跑
```{r}
for (i in c(7:10)){
  print(names(data[i]))
  lm <- lm(y ~ data[[i]] + control1 + control2, data = data)
  result <- summary(lm)
  result$coefficients <- result$coefficients[1:3,]  # 可以省略控制变量的输出
  print(result)
}
```


## 多个自变量和多个因变量分别跑
```{r}
for (i in c(7:10)){
  lm <- lm(paste0("cbind(y1, y2) ~ ", names(data[i]), "+ control1 + control2"), data = data)
  result <- summary(lm)
  print(result)
}
# 自变量有4个（7到10列），因变量两个，共跑4*2=8个模型
```


