---
title: "Fly Data"
author: "Jesse Leigh Patsolic"
output: 
  html_document:
    keep_md: true
---

<!--
### ### INITIAL COMMENTS HERE ###
###
### Jesse Leigh Patsolic 
### 2018 <Jesse.L.Patsolic@alumni.wfu.edu>
### S.D.G 
#
-->



## Setup


```r
require(devtools)
devtools::install_github("youngser/mbstructure")
```


## Data Preparation 


```r
data(MBconnectome)
```

## Download data


```r
leftA <- read.table("https://raw.githubusercontent.com/neurodata/graspy/graphmodel/graspy/datasets/drosophila/left_adjacency.csv", header = FALSE, sep = " ")

rightA <- read.table("https://raw.githubusercontent.com/neurodata/graspy/graphmodel/graspy/datasets/drosophila/right_adjacency.csv", header = FALSE, sep = " ")

leftLabels <- read.csv("https://raw.githubusercontent.com/neurodata/graspy/graphmodel/graspy/datasets/drosophila/left_cell_labels.csv", header = FALSE)

rightLabels <- read.csv("https://raw.githubusercontent.com/neurodata/graspy/graphmodel/graspy/datasets/drosophila/right_cell_labels.csv", header = FALSE)
```

## Embed


```r
X <- as.matrix(rightA)
Xb <- X
Xb[which(Xb > 0)] <- 1

GR <- graph_from_adjacency_matrix((Xb))
Xr <- embed_adjacency_matrix(GR, no = 5)$X

GL <- graph_from_adjacency_matrix((t(Xb)))
Xl <- embed_adjacency_matrix(GL, no = 5)$X

XX <- cbind(Xl, Xr)

dim(XX)
```

```
## [1] 213  10
```

## Run Urerf


```r
urerf.fit <- Urerf(XX, trees = 500)
```


## Get Similarity Matrix


```r
simM <- urerf.fit$sim
write.table(file = "similarityMatrix.csv", simM, row.names = FALSE, col.names = FALSE, sep = ",")
```

##  Embed the similarity


```r
Y <- irlba(simM, nu = 4)
```


```r
pairs(Y$u)
```

![](flyData_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
pairs(Y$v)
```

![](flyData_files/figure-html/unnamed-chunk-8-2.png)<!-- -->

## Fit Mclust


```r
m.fit <-  Mclust(Y$u)
```

## Plot Mclust BIC and Classifications


```r
plot(m.fit, what = c("BIC"))
```

![](flyData_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
plot(m.fit, what = c("classification"))
```

![](flyData_files/figure-html/unnamed-chunk-10-2.png)<!-- -->

## ARI


```r
mclust::adjustedRandIndex(rightLabels[[1]], m.fit$classification)
```

```
## [1] 0.178122
```




<!--
#   Time:
##  Working status:
### Comments:
--> 

