---
title       : Base R Graphs/Charts
subtitle    : 
author      : Vivek Patil
job         : 
framework   : bootstrap        ## {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  ## {highlight.js, prettify, highlight}
hitheme     : tomorrow      ## 
widgets     : []            ## {mathjax, quiz, bootstrap}
mode        : standalone ## {standalone, draft}
---

### Base R Version

#### Data
Let us begin by simulating our sample data of 3 factor variables and 4 numeric variables.


```r
## Simulate some data

## 3 Factor Variables
FacVar1 = as.factor(rep(c("level1", "level2"), 25))
FacVar2 = as.factor(rep(c("levelA", "levelB", "levelC"), 17)[-51])
FacVar3 = as.factor(rep(c("levelI", "levelII", "levelIII", "levelIV"), 13)[-c(51:52)])

## 4 Numeric Vars
set.seed(123)
NumVar1 = round(rnorm(n = 50, mean = 1000, sd = 50), digits = 2)  ## Normal distribution
set.seed(123)
NumVar2 = round(runif(n = 50, min = 500, max = 1500), digits = 2)  ## Uniform distribution
set.seed(123)
NumVar3 = round(rexp(n = 50, rate = 0.001))  ## Exponential distribution
NumVar4 = 2001:2050

simData = data.frame(FacVar1, FacVar2, FacVar3, NumVar1, NumVar2, NumVar3, NumVar4)
```


#### One Variable: Numeric Variable


```r
plot(simData$NumVar1, type = "o")  ## Index plot
```

![plot of chunk unnamed-chunk-2](assets/fig/unnamed-chunk-21.png) 

```r
hist(simData$NumVar1)  ## histogram
```

![plot of chunk unnamed-chunk-2](assets/fig/unnamed-chunk-22.png) 

```r
plot(density(simData$NumVar1))  ## Kernel density plot
```

![plot of chunk unnamed-chunk-2](assets/fig/unnamed-chunk-23.png) 

```r
boxplot(simData$NumVar1)  ## box plot
```

![plot of chunk unnamed-chunk-2](assets/fig/unnamed-chunk-24.png) 


#### One Variable: Factor Variable


```r
plot(simData$FacVar3)  ## bar plot
```

![plot of chunk unnamed-chunk-3](assets/fig/unnamed-chunk-31.png) 

```r

## pie chart - Not the best graph --- use with caution
counts = table(simData$FacVar3)  ## get counts
labs = paste(simData$FacVar3, counts)  ## create labels
pie(counts, labels = labs)  ## plot
```

![plot of chunk unnamed-chunk-3](assets/fig/unnamed-chunk-32.png) 


#### Two Variables: Two Numeric Variables

```r
plot(simData$NumVar1, type = "o", ylim = c(0, max(simData$NumVar1, simData$NumVar2)))  ## index plot with one variable
lines(simData$NumVar2, type = "o", lty = 2, col = "red")  ## add another variable
```

![plot of chunk unnamed-chunk-4](assets/fig/unnamed-chunk-41.png) 

```r

## Let's draw density plots :
## https://stat.ethz.ch/pipermail/r-help/2006-August/111865.html
dv1 = density(simData$NumVar1)
dv2 = density(simData$NumVar2)
plot(range(dv1$x, dv2$x), range(dv1$y, dv2$y), type = "n", xlab = "NumVar1(red) and NumVar2 (blue)", 
    ylab = "Density")
lines(dv1, col = "red")
lines(dv2, col = "blue")
```

![plot of chunk unnamed-chunk-4](assets/fig/unnamed-chunk-42.png) 

```r

## scatterplots
plot(simData$NumVar1, simData$NumVar2)
```

![plot of chunk unnamed-chunk-4](assets/fig/unnamed-chunk-43.png) 


#### Two Variables: Two Factor Variables

```r
## Mosaic plot
plot(table(simData$FacVar2, simData$FacVar3))
```

![plot of chunk unnamed-chunk-5](assets/fig/unnamed-chunk-51.png) 

```r

## barplots
bartable = table(simData$FacVar2, simData$FacVar3)  ## get the cross tab
barplot(bartable, beside = TRUE, legend = levels(unique(simData$FacVar2)))  ## plot 
```

![plot of chunk unnamed-chunk-5](assets/fig/unnamed-chunk-52.png) 

```r
barplot(bartable, legend = levels(unique(simData$FacVar2)))  ## stacked
```

![plot of chunk unnamed-chunk-5](assets/fig/unnamed-chunk-53.png) 

```r
barplot(prop.table(bartable, 2) * 100, legend = levels(unique(simData$FacVar2)))  ## stacked 100%
```

![plot of chunk unnamed-chunk-5](assets/fig/unnamed-chunk-54.png) 


#### Two Variables: One Factor and One Numeric

```r
## Box plots for the numeric var over the levels of the factor var
plot(simData$FacVar1, simData$NumVar1)
```

![plot of chunk unnamed-chunk-6](assets/fig/unnamed-chunk-61.png) 

```r

## density plot of numeric var across multiple levels of the factor var
level1 = simData[simData$FacVar1 == "level1", ]
level2 = simData[simData$FacVar1 == "level2", ]

dv3 = density(level1$NumVar1)
dv4 = density(level2$NumVar1)

plot(range(dv3$x, dv4$x), range(dv3$y, dv4$y), type = "n", xlab = "NumVar1 at Level1 (red) and NumVar1 at Level2 (blue)", 
    ylab = "Density")
lines(dv3, col = "red")
lines(dv4, col = "blue")
```

![plot of chunk unnamed-chunk-6](assets/fig/unnamed-chunk-62.png) 

```r

## Mean of one numeric var over levels of one factor var
meanagg = aggregate(simData$NumVar1, list(simData$FacVar3), mean)

dotchart(meanagg$x, labels = meanagg$Group.1)  ## Dot Chart 
```

![plot of chunk unnamed-chunk-6](assets/fig/unnamed-chunk-63.png) 

```r
barplot(meanagg$x, names.arg = meanagg$Group.1)  ## Bar plot 
```

![plot of chunk unnamed-chunk-6](assets/fig/unnamed-chunk-64.png) 

```r
## Question: Is a bar plot even appropriate when displaying a mean--- a
## point?
```


#### Three Variables: Three Factor Variables


```r
par(mfrow = c(1, 2))

bar1table = table(level1$FacVar2, level1$FacVar3)
barplot(bar1table, beside = TRUE, main = "FacVar1=level1")

bar2table = table(level2$FacVar2, level2$FacVar3)
barplot(bar2table, beside = TRUE, main = "FacVar1=level2", legend = levels(unique(level2$FacVar2)))
```

![plot of chunk unnamed-chunk-7](assets/fig/unnamed-chunk-7.png) 


#### Three Variables: One Numeric and Two Factor Variables

```r
par(mfrow = c(1, 1))
## boxplot of NumVar1 over an interaction of 6 levels of the combination of
## FacVar1 and FacVar2
boxplot(NumVar1 ~ interaction(FacVar1, FacVar2), data = simData)
```

![plot of chunk unnamed-chunk-8](assets/fig/unnamed-chunk-81.png) 

```r

## Mean of 1 Numeric over levels of two factor vars
meanaggg = aggregate(simData$NumVar1, list(simData$FacVar1, simData$FacVar2), 
    mean)
meanaggg = meanaggg[order(meanaggg$Group.1), ]
meanaggg$color[meanaggg$Group.2 == "levelA"] = "red"
meanaggg$color[meanaggg$Group.2 == "levelB"] = "blue"
meanaggg$color[meanaggg$Group.2 == "levelC"] = "darkgreen"

dotchart(meanaggg$x, labels = meanaggg$Group.2, groups = meanaggg$Group.1, color = meanaggg$color)  ## dotchart
```

![plot of chunk unnamed-chunk-8](assets/fig/unnamed-chunk-82.png) 

```r

interaction.plot(meanaggg$Group.2, meanaggg$Group.1, meanaggg$x, type = "b", 
    col = c(1:2), pch = c(18, 24))  ## interaction plot - line plots of means
```

![plot of chunk unnamed-chunk-8](assets/fig/unnamed-chunk-83.png) 

```r

## some a bar plot
par(mfrow = c(1, 2))

level1 = meanaggg[meanaggg$Group.1 == "level1", ]
level2 = meanaggg[meanaggg$Group.1 == "level2", ]

barplot(level1$x, names.arg = level1$Group.2, main = "FacVar1=level1")
barplot(level2$x, names.arg = level2$Group.2, main = "FacVar1=level2")
```

![plot of chunk unnamed-chunk-8](assets/fig/unnamed-chunk-84.png) 


#### Three Variables: Two Numeric and One Factor Variables


```r
## Scatter plot with color identifying the factor variable
par(mfrow = c(1, 1))
plot(simData$NumVar1, simData$NumVar2, col = simData$FacVar1)
legend("topright", levels(simData$FacVar1), fill = simData$FacVar1)
```

![plot of chunk unnamed-chunk-9](assets/fig/unnamed-chunk-9.png) 


#### Three Variables: Three Numeric Variables 


```r
## NumVar4 is 2001 through 2050... possibly, a time variable - use that as
## the x-axis
plot(simData$NumVar4, simData$NumVar1, type = "o", ylim = c(0, max(simData$NumVar1, 
    simData$NumVar2)))  ## join dots with lines

lines(simData$NumVar4, simData$NumVar2, type = "o", lty = 2, col = "red")  ## add another line
```

![plot of chunk unnamed-chunk-10](assets/fig/unnamed-chunk-101.png) 

```r

## Bubble plot - scatter plot of NumVar1 and NumVar2 with individual
## observations sized by NumVar3
## http://flowingdata.com/2010/11/23/how-to-make-bubble-charts/
radius <- sqrt(simData$NumVar3/pi)
symbols(simData$NumVar1, simData$NumVar2, circles = radius, inches = 0.25, fg = "white", 
    bg = "red", main = "Sized by NumVar3")
```

![plot of chunk unnamed-chunk-10](assets/fig/unnamed-chunk-102.png) 


#### Scatterplot Matrix of all Numeric Vars, colored by a Factor variable

```r
pairs(simData[, 4:7], col = simData$FacVar1)
```

![plot of chunk unnamed-chunk-11](assets/fig/unnamed-chunk-11.png) 


#### References

Besides the link from flowingdata.com referred to in the context of the bubble plot, additional websites were used as references. 
http://www.harding.edu/fmccown/r/
http://www.statmethods.net/
