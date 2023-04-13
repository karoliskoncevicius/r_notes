# Matrix Tricks #

Useful commands for working with matrices.


### Creating a matrix ###

Multiple ways to quickly create helper matrix objects.

```
matrix(0, 3, 3)                         mat.or.vec(3,3)
     [,1] [,2] [,3]                          [,1] [,2] [,3]
[1,]    0    0    0                     [1,]    0    0    0
[2,]    0    0    0                     [2,]    0    0    0
[3,]    0    0    0                     [3,]    0    0    0


.row(c(3,3))                            .col(c(3,3))
     [,1] [,2] [,3]                          [,1] [,2] [,3]
[1,]    1    1    1                     [1,]    1    2    3
[2,]    2    2    2                     [2,]    1    2    3
[3,]    3    3    3                     [3,]    1    2    3



rbind(1:3, 3:1, 1:3)                    cbind(1:3, 3:1, 1:3)
     [,1] [,2] [,3]                          [,1] [,2] [,3]
[1,]    1    2    3                     [1,]    1    3    1
[2,]    3    2    1                     [2,]    2    2    2
[3,]    1    2    3                     [3,]    3    1    3


diag(3)                                 outer(1:3, 1:3)
     [,1] [,2] [,3]                          [,1] [,2] [,3]
[1,]    1    0    0                     [1,]    1    2    3
[2,]    0    1    0                     [2,]    2    4    6
[3,]    0    0    1                     [3,]    3    6    9
```


---


### Matrix of objects ###

Matrix can contain various classes.
Below is a matrix of data.frames.

```
mat <- matrix(list(iris, mtcars, USArrests, chickwts), ncol=2)
     [,1]          [,2]
[1,] data.frame,5  data.frame,4
[2,] data.frame,11 data.frame,2
```

Element selection in such matrices works based on list behaviour.

```
mat[[2,2]]                              mat[[2,2]]
<returns a list>                        <returns the data.frame>
```


---


### Subtracting a vector from each row/column ###

Subtract column/row means from each column/row.

```
X - rowMeans(X)[row(X)]                 X - colMeans(X)[col(X)]
```

Since matrices are just vectors ordered column-by-column row-wise subtraction can be simplified.
For example, the example below will subtract all means from the first column, then repeat this for all the means in the second column, etc.
As a result every row will have its corresponding mean subtracted.

```
X - rowMeans(X)
```

These methods are general and works with any operations, not just subtraction.

```
(X - rowMeans(X)) / matrixStats::rowSds(X)                   # scale each row
(X - colMeans(X)[col(X)]) / matrixStats::colSds(X)[col(X)]   # scale each column
```


---


### Order matrix elements by row/column ###

Order each row/column of a matrix separately.

```
matrix(X[order(row(X), X)], nrow=nrow(X), byrow=TRUE)  # by row
matrix(X[order(col(X), X)], nrow=nrow(X))              # by column
```

Missing values are placed last.
`na.last` argument in the `order()` function can be used to control this behaviour.

```
matrix(X[order(row(X), X, na.last=FALSE)], nrow=nrow(X), byrow=TRUE)
matrix(X[order(col(X), X, na.last=FALSE)], nrow=nrow(X))
```

This method is a lot faster than using `apply()`.


