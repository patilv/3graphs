---
title       : rCharts
subtitle    : 
author      : Vivek Patil
job         : 
framework   : bootstrap        ### {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  ### {highlight.js, prettify, highlight}
hitheme     : tomorrow      ### 
widgets     : []            ### {mathjax, quiz, bootstrap}
ext_widgets: {rCharts: [libraries/nvd3, libraries/polycharts, libraries/dimple, libraries/uvcharts, libraries/highcharts]}
mode        : standalone ### {standalone, draft}
---

### rCharts Version 

#### Data

Let us begin by simulating our sample data of 3 factor variables and 4 numeric variables. 


```r
### Simulate some data

### 3 Factor Variables
FacVar1 = as.factor(rep(c("level1", "level2"), 25))
FacVar2 = as.factor(rep(c("levelA", "levelB", "levelC"), 17)[-51])
FacVar3 = as.factor(rep(c("levelI", "levelII", "levelIII", "levelIV"), 13)[-c(51:52)])

### 4 Numeric Vars
set.seed(123)
NumVar1 = round(rnorm(n = 50, mean = 1000, sd = 50), digits = 2)  ### Normal distribution
set.seed(123)
NumVar2 = round(runif(n = 50, min = 500, max = 1500), digits = 2)  ### Uniform distribution
set.seed(123)
NumVar3 = round(rexp(n = 50, rate = 0.001))  ### Exponential distribution
NumVar4 = 2001:2050

simData = data.frame(FacVar1, FacVar2, FacVar3, NumVar1, NumVar2, NumVar3, NumVar4)
```


#### Initialize the libraries used for this page

```r
library(rCharts)
library(reshape2)
```


#### One Variable: Numeric Variable

##### Index Plot using HighChart

```r
simData$index = 1:nrow(simData)
h1 = hPlot(x = "index", y = "NumVar1", data = simData, type = "line")
# h1$publish('h1',host='gist') h1$save('h1.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359614" width=950 height=450> </iframe>

##### Histogram using PolyChart

```r
rp1 = rPlot(x = "bin(NumVar1,10)", y = "count(NumVar1)", data = simData, type = "bar")
# rp1$publish('rp1',host='gist') rp1$save('rp1.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359654" width=975 height=450> </iframe>

##### Density plot using NVD3

```r
dense = density(simData$NumVar1)
dense = data.frame(dense$x, dense$y)
n1 = nPlot(x = "dense.x", y = "dense.y", data = dense, type = "lineChart")
# n1$save('n1.html',cdn=TRUE) n1$publish('n1',host='gist')
```

<iframe src="http://www.pagist.info/7359687" width=975 height=450> </iframe>

##### Boxplot using HighChart

```r
### dummy variable created
simData$tmpFac = "tmp"
bwstats = setNames(as.data.frame(boxplot(NumVar1 ~ tmpFac, data = simData, plot = F)$stats), 
    nm = NULL)
h2 = Highcharts$new()
h2$set(series = list(list(name = "NumVar1 Distribution", data = bwstats)))
h2$xAxis(categories = levels(simData$tmpFac), title = list(text = "Dummy Fac Var"))
h2$yAxis(title = list(text = "NumVar1"))
h2$chart(type = "boxplot")
# h2$publish('h2',host='gist') h2$save('h2.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359858" width=975 height=450> </iframe>

#### One Variable: Factor Variable

##### Bar plot using NVD3

```r
bpf3 = data.frame(table(simData$FacVar3))
n2 = nPlot(x = "Var1", y = "Freq", data = bpf3, type = "discreteBarChart")
# n2$publish('n2',host='gist') n2$save('n2.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359874" width=975 height=450> </iframe>

##### Pie Chart using NVD3

```r
n3 = nPlot(x = "Var1", y = "Freq", data = bpf3, type = "pieChart")
# n3$publish('n3',host='gist') n3$save('n3.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359893" width=975 height=450> </iframe>

#### Two Variables: Two Numeric Variables

##### Index plots- line plots with observation number x-axis - Using HighCharts

```r
simsub = simData[, c(4:5, 8)]
simsubmelt = melt(simsub, id = c("index"))

h2a = hPlot(x = "index", y = "value", group = "variable", data = simsubmelt, 
    type = "line")
# h2a$publish('h2a',host='gist') h2a$save('h2a.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359915" width=975 height=450> </iframe>

##### Density plots for NumVar1 & NumVar2 using NVD3

```r
dense$Var = "NumVar1"
names(dense) = c("x", "y", "Var")
dense2 = density(simData$NumVar2)
dense2 = data.frame(dense2$x, dense2$y)
dense2$Var = "NumVar2"
names(dense2) = c("x", "y", "Var")
densen1n2 = rbind(dense, dense2)
n4 = nPlot(x = "x", y = "y", group = "Var", data = densen1n2, type = "lineChart")
# n4$publish('n4',host='gist') n4$save('n4.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359928" width=975 height=450> </iframe>

##### Scatter Chart using NVD3 - NumVar1 (x-Axis) versus NumVar2 (y-Axis)

```r
n5 = nPlot(NumVar2 ~ NumVar1, data = simData, type = "scatterChart")
# n5$publish('n5',host='gist') n5$save('n5.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359939" width=975 height=450> </iframe>

#### Two Variables: Two Factor Variables

##### Mosaic plot/horizontal marimekko using Dimple

```r
table = as.data.frame(table(simData$FacVar2, simData$FacVar3))  ### Crosstab

d1 = dPlot(Var2 ~ Freq, groups = "Var1", data = table, type = "bar")
d1$yAxis(type = "addAxis", measure = "Freq", showPercent = TRUE)
d1$xAxis(type = "addPctAxis")
d1$legend(x = 200, y = 10, width = 400, height = 20, horizontalAlign = "right")
# d1$publish('d1',host='gist') d1$save('d1.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359962" width=975 height=450> </iframe>

##### Bar chart using UV Chart

```r
bartabledat = as.data.frame(table(simData$FacVar2, simData$FacVar3))
u1 = uPlot("Var2", "Freq", data = bartabledat, group = "Var1", type = "Bar")
# u1$publish('u1',host='gist') u1$save('u1.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359980" width=975 height=600> </iframe>

##### Stacked Bar chart using UV Chart

```r
u2 = uPlot("Var2", "Freq", data = bartabledat, group = "Var1", type = "StackedBar")
u2$config(graph = list(palette = "Lint"))
# u2$publish('u2',host='gist') u2$save('u2.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7359991" width=975 height=600> </iframe>

##### 100% Stacked Bar chart using UV Chart

```r
u3 = uPlot("Var2", "Freq", data = bartabledat, group = "Var1", type = "PercentBar")
u3$config(graph = list(palette = "Soft"))
# u3$publish('u3',host='gist') u3$save('u3.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360001" width=975 height=600> </iframe>

#### Two Variables: One Factor and One Numeric

##### Box plots of numeric var over the levels of the factor var using HighCharts

```r
bwstats = setNames(as.data.frame(boxplot(NumVar1 ~ FacVar1, data = simData, 
    plot = F)$stats), nm = NULL)
h3 = Highcharts$new()
h3$set(series = list(list(name = "NumVar1 Distribution", data = bwstats)))
h3$xAxis(categories = levels(simData$FacVar1), title = list(text = "FacVar1"))
h3$yAxis(title = list(text = "NumVar1"))
h3$chart(type = "boxplot")
# h3$publish('h3',host='gist') h3$save('h3.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360014" width=975 height=450> </iframe>

##### Density plot numeric var across multiple levels of the factor var - NVD3

```r
level1 = simData[simData$FacVar1 == "level1", ]
level2 = simData[simData$FacVar1 == "level2", ]

densen1 = density(level1$NumVar1)
densen1 = data.frame(densen1$x, densen1$y)
densen1$FacVar = "level1"
names(densen1) = c("x", "y", "FacVar")

densen2 = density(level2$NumVar1)
densen2 = data.frame(densen2$x, densen2$y)
densen2$FacVar = "level2"
names(densen2) = c("x", "y", "FacVar")

densen1and2 = rbind(densen1, densen2)
n6 = nPlot(x = "x", y = "y", group = "FacVar", data = densen1and2, type = "lineChart")
# n6$publish('n6',host='gist') n6$save('n6.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360029" width=975 height=450> </iframe>

##### Mean of one numeric var over levels of one factor var - Scatter Chart- HighCharts

```r
meanagg = aggregate(simData$NumVar1, list(simData$FacVar3), mean)

h4 = hPlot(x = "Group.1", y = "x", data = meanagg, type = "scatter")
# h4$publish('h4',host='gist') h4$save('h4.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360039" width=975 height=450> </iframe>

##### Mean of one numeric var over levels of one factor var - Bar Chart - HighCharts

```r
h4a = hPlot(x = "Group.1", y = "x", data = meanagg, type = "bar")
# h4a$publish('h4a',host='gist') h4a$save('h4a.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360048" width=975 height=450> </iframe>

#### Three Variables: Three Factor Variables

##### Bar Chart - PolyCharts

```r
Threebartable = as.data.frame(table(simData$FacVar1, simData$FacVar2, simData$FacVar3))

rp2 <- rPlot(Freq ~ Var2, color = "Var1", data = Threebartable, type = "bar")
rp2$facet(var = "Var3", type = "wrap", rows = 2)
# rp2$publish('rp2',host='gist') rp2$save('rp2.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360052" width=975 height=450> </iframe>

#### Three Variables: One Numeric and Two Factor Variables

##### Boxplot of NumVar1 over an interaction of 6 levels of the combination of FacVar1 and FacVar2 - PolyCharts

```r
bwstats = setNames(as.data.frame(boxplot(NumVar1 ~ (FacVar1 * FacVar2), data = simData, 
    plot = F)$stats), nm = NULL)
h5 = Highcharts$new()
h5$set(series = list(list(name = "NumVar1 Distribution", data = bwstats)))
h5$xAxis(categories = unique(interaction(simData$FacVar1, simData$FacVar2)), 
    title = list(text = "Interaction of FacVar1 and FacVar2"))
h5$yAxis(title = list(text = "NumVar1"))
h5$chart(type = "boxplot")
# h5$publish('h5',host='gist') h5$save('h5.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360060" width=975 height=450> </iframe>

##### Dot chart Equivalent - Mean of 1 Numeric variable over levels of two factor vars - PolyCharts

```r
meanaggg = aggregate(simData$NumVar1, list(simData$FacVar1, simData$FacVar2), 
    mean)
names(meanaggg) = c("Group1", "Group2", "Mean")
rp3 <- rPlot(Group2 ~ Mean | Group1, color = "Group2", data = meanaggg, type = "point")
rp3$guides(x = list(min = 970, max = 1050))
# rp3$publish('rp3',host='gist') rp3$save('rp3.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360073" width=975 height=450> </iframe>

##### Interaction Plot - Line Chart equivalent - HighCharts

```r
h6 = hPlot(x = "Group2", y = "Mean", groups = "Group1", data = meanaggg, type = "line")
# h6$publish('h6',host='gist') h6$save('h6.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360082" width=975 height=450> </iframe>

##### Bar chart - Mean of 1 Numeric variable over levels of two factor vars - HighCharts

```r
h7 = hPlot(x = "Group2", y = "Mean", groups = "Group1", data = meanaggg, type = "bar")
# h7$publish('h7',host='gist') h7$save('h7.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360096" width=975 height=450> </iframe>

#### Three Variables: Two Numeric and One Factor Variables

##### Scatter Plot with color/shape identifying the factor variable - HighCharts

```r
h8 = hPlot(x = "NumVar1", y = "NumVar2", groups = c("FacVar1"), data = simData, 
    type = "scatter")
# h8$publish('h8',host='gist') h8$save('h8.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360118" width=975 height=450> </iframe>

#### Three Variables: Three Numeric Variables

##### Line Chart - one of three vars is along the x-axis - NVD3

```r
### NumVar4 is 2001 through 2050... giving the feeling of it being a time
### variable
simtmpp = simData[, c(4, 5, 7)]
simtmppmelt = melt(simtmpp, id = c("NumVar4"))

n7 = nPlot(value ~ NumVar4, group = "variable", data = simtmppmelt, type = "lineWithFocusChart")
# n7$publish('n7',host='gist') n7$save('n7.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360130" width=975 height=450> </iframe>

##### Stacked and 100% Stacked Area Chart - one of three vars is along the x-axis - NVD3

```r
n8 = nPlot(value ~ NumVar4, group = "variable", data = simtmppmelt, type = "stackedAreaChart")
# n8$publish('n8',host='gist') n8$save('n8.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360152" width=975 height=450> </iframe>

##### Bubble plot - scatter plot of NumVar1 and NumVar2 with individual observations sized by NumVar3 - HighCharts

```r
h9 = hPlot(x = "NumVar1", y = "NumVar2", size = "NumVar3", data = simData, type = "bubble")
# h9$publish('h9',host='gist') h9$save('h9.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360177" width=975 height=450> </iframe>

#### Scatterplot Matrix of all Numeric Vars, colored by a Factor variable

Do not know if this is possible using rCharts... but what is possible is the ability to see the scatterplots of any two sets of numeric vars colored by any factor var - Used NVD3

```r
simoriginal = simData[, c(1:7)]
n9 = nPlot(NumVar2 ~ NumVar1, data = simoriginal, group = "FacVar1", type = "scatterChart")
n9$addControls("x", value = "NumVar1", values = names(simoriginal[, 4:7]))
n9$addControls("y", value = "NumVar2", values = names(simoriginal[, 4:7]))
n9$addControls("group", value = "FacVar1", values = names(simoriginal[, 1:3]))
# n9$publish('n9',host='gist') n9$save('n9.html',cdn=TRUE)
```

<iframe src="http://www.pagist.info/7360194" width=1200 height=800> </iframe>

#### References

The rCharts page and gallery at: http://rcharts.io/ and the github page at: https://github.com/ramnathv/rCharts 
