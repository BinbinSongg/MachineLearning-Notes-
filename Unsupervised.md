# Clustering 
## What is clustering? 
Clustering is an unsupervised method, it does not need the label information or don't have ‘y’. It can be applied to identify the potential pattern in the data or separate the data into several groups. 

## What can this algorithm do?
It can help us group observations that are “similar” based on predefined criteria (e.g. similarity). 
(Tip: if you want to cluster the data w.r.t the columns in R, you should transpose the dataset first) 

## Hierarchical clustering (bottom-up clustering)
### Step 1: 
Determine the distance metrics 
(Single/ complete/ distance between centroids/ average linkage) 
```
# d is a dissimilarity structure as produced by dist
# method: different distance metrics 
hclust(d, method = "complete")
```
### Step 2: 
Determine the formula for distance measurement("euclidean", "maximum", "manhattan", "canberra", "binary" or "minkowski".)
```
# x: is target dataset
# method: formula for distance measurement
dist(x, method = "euclidean")
```
Tips: We should normalize the data before implementing clustering. Otherwise, some large valued units will dominate the computed dissimilarity. [Reason]( http://stats.stackexchange.com/questions/30317/reason-to-normalize-in-euclidean-distance-measures-in-hierarchical-clustering)

### Step3: 
Implement the hierarchical clustering in R

### example 
```
# set work directory
setwd("~/Desktop/codes/STAT5003/Tutorial (1)/New")
# load package 
library(e1071)
library(leukemiasEset)
# read data
data("leukemiasEset")
dataMatrix <- exprs(leukemiasEset)
# obtain and summarize phenodata
phenodata <- pData(leukemiasEset)
#-----------hierachical clustering--------------
par(mfrow = c(1,1))
# standardize the data before implemeting the clustering 
phenodata <- apply(phenodata, 2, scale)
# in the <dist> method can be maximum/ manhattan and so on. 
# in the <hclust> method can be "single"/ "average" and so on. 
hierClu <- hclust(dist(t(dataMatrix), method = "manhattan"),method = "complete")
hierPlot <- plot(hierClu, main = "Complete method",cex = 0.3)

# visualize the 5 clusters in the tree
rect.hclust(hierClu, k=5, border="red")
```

# Partitioning methods (k-means, fuzzy c-means)
## k-means 
![My Unicorn](https://github.com/BinbinSongg/MachineLearning-Notes-/blob/master/Figure/kmeansComp.jpeg) 

### Step 1
Randomly choose k points as the initial centres 

### Step 2 
Assign each object to most similar centres 

### Step 3
For each new cluster, update the cluster means 

### Step 4 
Reassign each object to new cluster 

### Iteration
Repeat Step 3 and Step 4 until each object in the most similar centres 

## How to choose the optimal number of clusters? 
1. Plot a "Cost vs number of clusters" figure, and choose the elbow as the best number of clusters (below). 
![Elbow Method](https://github.com/BinbinSongg/MachineLearning-Notes-/blob/master/Figure/ElbowMe.jpeg)

2. Consider the relationship between number of clusters and actual problem. For example, I want to do the clustering 

Tips: 
Since the initial centres might cause the local optimum, we should repeat step one for several times to avoid this problem. 
Besides, when there is no any object in one cluster, eventually the "package" will delete this cluster in R. 

### Example 
```
# dataMatrix: the target dataset 
# centers: number of centers 
# nstart: iterative number of first step
kClu <- kmeans(dataMatrix, centers = 5, nstart = 5)

# visualise the k-means result, and the x, y axes are the first and second variable, respectively. 
kPlot <- plot(dataMatrix, col = (kClu$cluster +1), main = "visualization of kmeans clustering")
```


## Fuzzy C-means clustering
Fuzzy c-means can allow one piece of data to belong to two or more clusters, and for each assignment giving a probability. 
And, we are trying to minimise following function:
![Objective function](https://github.com/BinbinSongg/MachineLearning-Notes-/blob/master/Figure/cmeansObjective.jpeg)

where m is any real number greater than 1, uij is the degree of membership of xi in the cluster j, xi is the ith of d-dimensional measured data, cj is the d-dimension center of the cluster, and ||*|| is any norm expressing the similarity between any measured data and the center.
Fuzzy partitioning is carried out through an iterative optimization of the objective function shown above, with the update of membership uij and the cluster centers cj by:
![Explanation](https://github.com/BinbinSongg/MachineLearning-Notes-/blob/master/Figure/cmeansExplain.jpeg)

This iteration will stop when ， ![Termination condition](https://github.com/BinbinSongg/MachineLearning-Notes-/blob/master/Figure/cmeansTerm.jpeg), and theta is a termination value between 0 and 1, k is the iteration step. 

The complete procedures are shown below: 
![Complete procedure](https://github.com/BinbinSongg/MachineLearning-Notes-/blob/master/Figure/cmeansComp.jpeg)

### Example 
```
# dataMatrix: target dataset
# centers: number of clusters 
# m: A number greater than 1 giving the degree of fuzzification
cClu <- cmeans(dataMatrix, centers = 6, m = 2)

# visualise the c-means result
cPlot <- plot(dataMatrix, col = (cClu$cluster +1), main = "visualization of cmeans clustering")

```

## Difference between K-means and C-means ? 
In the FCM (Fuzzy c-means) approach, each object does not belong to certain cluster. Instead, it gives probabilities for objects in each cluster. [Detail](https://home.deib.polimi.it/matteucc/Clustering/tutorial_html/cmeans.html)

Besides, when you reduce the value of m, the result will become more accurate. 

