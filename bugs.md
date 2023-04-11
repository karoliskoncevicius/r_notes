# R bugs and inconsistencies #

The list only includes bugs that were detected during work.
Still active issues are on the top, solved issues are at the bottom.


## Active ##


### Selection of NA rownames from a matrix ###

R allows selecting elements either by index or by name.
Having NA values in those selection are allowed and return an element with an unknown value - NA.

```
x <- c(a=1, b=2, c=3)

x[c(1,NA,3)]                             x[c("a", NA, "c")]
# a <NA>    c                            # a <NA>    c
# 1   NA    3                            # 1   NA    3
```

But the above operations are inconsistent when working with matrices.
Having NA selections when selecting by index works fine but the same operation throws an error when selecting by names.

```
mat <- matrix(c(1:6), ncol=3)
colnames(mat) <- c("a","b","c")

mat[,c(1,NA,3)]                          mat[,c("a", NA, "c")]
#      a <NA> c                          # <error>
# [1,] 1   NA 5
# [2,] 2   NA 6
```

- [Report on Bugzilla](https://bugs.r-project.org/show_bug.cgi?id=18481)


---


### Calling is.nan() on a data.frame ##


Function `is.nan()` doesn't work on data.frames while `is.na()` does.

```
is.na(iris)                              is.nan(iris)
# <works>                                # <error>
```


---


### Autocompletion inside square brackets ###

Autocompletion for list names does not work inside of square brackets.

```
iris$Sp<tab>                             iris[iris$Sp<tab>]
# works                                  # does not work
```

- [Question on StackOverflow](https://stackoverflow.com/q/30737225/1953718)
- [Issue on Henrik's WishlistForR](https://github.com/HenrikBengtsson/Wishlist-for-R/issues/129)


---


### Inconsistent output format for distribution functions ###

Distribution functions like `pnorm()` have an inconsistent output format for an emtpy `<0,0>` dimension matrix.

```
x <- matrix(numeric(), nrow=0, ncol=0)

qbeta(x, 1, 1)                           # numeric(0)
qbinom(x, 1, 1)                          # numeric(0)
qbirthday(x, 1, 1)                       # <error>
qcauchy(x)                               # numeric(0)
qchisq(x, 1)                             # <0 x 0 matrix>
qexp(x, 1)                               # <0 x 0 matrix>
qf(x, 1, 1)                              # numeric(0)
qgamma(x, 1)                             # numeric(0)
qgeom(x, 1)                              # <0 x 0 matrix>
qhyper(x, 1, 1, 1)                       # numeric(0)
qlnorm(x)                                # numeric(0)
qlogis(x)                                # numeric(0)
qnbinom(x, 1, 1)                         # numeric(0)
qnorm(x)                                 # numeric(0)
qpois(x, 1)                              # <0 x 0 matrix>
qpois(x, 1)                              # <0 x 0 matrix>
qsignrank(x, 1)                          # <0 x 0 matrix>
qsmirnov(x, c(1,1))                      # numeric(0)
qt(x, 1)                                 # <0 x 0 matrix>
qtukey(x, 1, 1)                          # numeric(0)
qunif(x)                                 # numeric(0)
qweibull(x, 1)                           # numeric(0)
qwilcox(x, 1, 1)                         # numeric(0)
```

When the matrix is non empty all the functions (except `qbirthday()`) preserve the original matrix format.
Therefore, in my opinion, returning `<0 x 0 matrix>` is the correct behaviour.

- [Report on Bugzilla](https://bugs.r-project.org/show_bug.cgi?id=18509)


---


### Calling rbind() on a data.frame with 0 columns drops all rows ###

When called on a data.frame with 0 columns `rbind()` drops all the rows, while `cbind()` keeps them.
This behaviour is also inconsistent with matrices, where both operations preserve the number of rows.

```
mat <- matrix(nrow=2, ncol=0)            df <- as.data.frame(mat)

dim(mat)                                 dim(df)
# [1] 2 0                                # [1] 2 0

dim(cbind(mat, mat))                     dim(cbind(df, df))
# [1] 2 0                                # [1] 2 0

dim(rbind(mat, mat))                     dim(rbind(df, df))
# [1] 4 0                                # [1] 0 0
```

Note: this behaviour is documented.

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-May/077796.html)
- [Question on StackOverflow](https://stackoverflow.com/q/52233413/1953718)
- [Issue on Henrik's WishlistForR](https://github.com/HenrikBengtsson/Wishlist-for-R/issues/77)


---


### Some tests can silently return NA results ###

When provided with all constant values some statistical tests silently produce NaN statistics and NA pvalues.

```
x <- c(1,1,1,1,1,1)
g <- c("a","a","a","b","b","b")

bartlett.test(x, g)                      # <NaN statistic, NA p-value, no warning, no error>
fligner.test(x, g)                       # <NaN statistic, NA p-value, no warning, no error>
kruskal.test(x, g)                       # <NaN statistic, NA p-value, no warning, no error>
oneway.test(x ~ g)                       # <NaN statistic, NA p-value, no warning, no error>
t.test(x ~ g)                            # <error>
var.test(x ~ g)                          # <NaN statistic, NA p-value, no warning, no error>
wilcox.test(x ~ g)                       # <warning + NA p-value>
```

Another case to check - when the values are constant within groups but differ between the groups

```
x <- c(1,1,1,2,2,2)
g <- c("a","a","a","b","b","b")

bartlett.test(x, g)                      # <NaN statistic, NA p-value, no warning, no error>
fligner.test(x, g)                       # <NaN statistic, NA p-value, no warning, no error>
kruskal.test(x, g)                       # <OK>
oneway.test(x ~ g)                       # <NaN statistic, NA p-value, no warning, no error>
t.test(x ~ g)                            # <error>
var.test(x ~ g)                          # <NaN statistic, NA p-value, no warning, no error>
wilcox.test(x ~ g)                       # <OK>
```

Note: the desired behaviour, in my opinion, is for all the cases with silent NA values or errors is to produce a warning instead.

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2020-December/080334.html)


---


### Misleading error in paired Wilcoxon's test ###

When a paired `wilcox.test()` is called with missing observatiosn in `y` it shows a misleading error about observations missing in `x`.

```
wilcox.test(c(1,2), c(NA_integer_,NA_integer_), paired=TRUE)
# <error about not enough 'x' observations>
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-December/078774.html)


---


### Inconsistent confidence level range between tests ###

The acceptable input range for confidence level parameter `conf.level` is not consistent between tests.

```
x <- rnorm(10)
y <- rnorm(10)
g <- gl(2, 5)

binom.test(1, 1, 1, conf.level=1)                # <error>
cor.test(x, y, conf.level=1)                     # <OK>
fisher.test(g, g, conf.level=1)                  # <error>
t.test(x, y, conf.level=0)                       # <OK>
mantelhaen.test(g, g, g, conf.level=1)           # <OK>
var.test(x, y, conf.level=0)                     # <error>
wilcox.test(x, y, conf.int=0.95, conf.level=1)   # <error>
```


## Solved ##


---


### Tolerance issues for ties in Wilcoxon's test ###

Paired version of `wilcoxon.test(paired = TRUE)` has tolerance issues when detecting ties.

```
wilcox.test(c(4,3,2), c(3,2,1), paired=TRUE)
# <warning about ties>

wilcox.test(c(0.4,0.3,0.2), c(0.3,0.2,0.1), paired=TRUE)
# <no warning>
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-December/078774.html)
- [Report on Bugzilla](https://bugs.r-project.org/show_bug.cgi?id=16138)


---


### Missing information about Wilcoxon's test variant ###


Information showing if normal approximation was used is missing from the output of  `wilcox.test()`.

```
wilcox.test(rnorm(10), exact=FALSE, correct=FALSE)
wilcox.test(rnorm(10), exact=TRUE, correct=FALSE)
# different results, but same data and identical method description
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-December/078774.html)


---


### Wilcoxon's test has inconsistent behaviour with infinite values ###


Different versions of `wilcox.test()` treats infinite values differently.

```
wilcox.test(c(1,2,3,4), c(0,9,8,Inf))
# Inf is removed

wilcox.test(c(1,2,3,4), c(0,9,8,Inf), paired=TRUE)
# Inf is treated as a highest rank
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-December/078774.html)


---


### Fligner's test can show significance for constant values ###

In some cases `flinger.test()` can produce highly significant result whan all groups have constant values.

```
fligner.test(c(1,1,1,2,2,2), c("a","a","a","b","b","b"))
# p-value < 2.2e-16
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-June/078038.html)


---


### Calling any() on a logical data.frame ###

Function `any()` does not work on a logical data.frame but works on a numeric data.frame.

```
any(data.frame(A=0L, B=1L))              any(data.frame(A=TRUE, B=FALSE))
# TRUE                                   # <error>
```

My opinion - `any()` not working on a data.frame might be an intended behaviour.
However then it also should not be working on a numeric data.frame.
Having a logical function - `any()` work on numeric inputs but not logical inputs is not expected.

- [Question on StackOverflow](https://stackoverflow.com/q/60251847/1953718)
- [Issue on Henrik's WishlistForR](https://github.com/HenrikBengtsson/Wishlist-for-R/issues/112)

