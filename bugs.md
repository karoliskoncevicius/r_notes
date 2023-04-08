# R bugs and inconsistencies #

A list of detected bugs and inconsistencies in R.
The list only includes bugs that were detected during work.
Still active issues are on the top, solved issues are at the bottom.


## Active ##


Row selection from a matrix using rownames with `NA`s results in an error.

```
mat <- matrix(1:50, nrow=10, ncol=5)
rownames(mat) <- letters[1:10]
mat[c("a", NA, "d"),]
# <error>
```

- [Report on Bugzilla](https://bugs.r-project.org/show_bug.cgi?id=18481)


---


`any()` works with a numeric data.frame but fails on a logical data.frame.

```
any(data.frame(A=0L, B=1L))              any(data.frame(A=TRUE, B=FALSE))
# TRUE                                   # <error>
```

- [Question on StackOverflow](https://stackoverflow.com/q/60251847/1953718)
- [Issue on Henrik's WishlistForR](https://github.com/HenrikBengtsson/Wishlist-for-R/issues/112)


---


`is.nan()` doesn't work on data.frames while `is.na()` does.

```
is.na(iris)                              is.nan(iris)
# <works>                                # <error>
```

---


autocompletion for list names does not work inside square brackets.

```
iris$Sp<tab>                             iris[iris$Sp<tab>]
# works                                  # does not work
```

- [Question on StackOverflow](https://stackoverflow.com/q/30737225/1953718)
- [Issue on Henrik's WishlistForR](https://github.com/HenrikBengtsson/Wishlist-for-R/issues/129)


---


`qnorm()` drops matrix format when input is an empty `<0,0>` matrix.

```
qnorm(matrix(0.1, nrow=1, ncol=2))       qnorm(matrix(0.1, nrow=0, ncol=0))
#           [,1]      [,2]               # numeric(0)
# [1,] -1.281552 -1.281552
```


---


`rbind()` does not preserve data.frames rows, while `cbind()` does.

```
mat <- matrix(nrow=2, ncol=0)            df  <- as.data.frame(mat)

dim(mat)                                 dim(df)
# [1] 2 0                                # [1] 2 0

dim(cbind(mat, mat))                     dim(cbind(df, df))
# [1] 2 0                                # [1] 2 0

dim(rbind(mat, mat))                     dim(rbind(df, df))
# [1] 4 0                                # [1] 0 0
```

NOTE: this behaviour is documented.

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-May/077796.html)
- [Question on StackOverflow](https://stackoverflow.com/q/52233413/1953718)
- [Issue on Henrik's WishlistForR](https://github.com/HenrikBengtsson/Wishlist-for-R/issues/77)


---


`bartlett.test()` can silently return `NaN` statistic and `NA` p-value.

```
bartlett.test(rep(1,4), c("a","a","b","b"))
# <NaN statistic, NA p-value, no warning, no error>
```


---


`flinger.test()` can silently return `NaN` statistic and `NA` p-value.

```
fligner.test(c(2,3,4,5), gl(2,2))
# <NaN statistic, NA p-value, no warning, no error>
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2020-December/080334.html)


---


`wilcox.test(x, y, paired = TRUE)` shows an error about `x` when `y` has `NA`s.

```
wilcox.test(c(1,2), c(NA_integer_,NA_integer_), paired=TRUE)
# <error about not enough 'x' observations>
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-December/078774.html)


---


`var.test()` does not accept `conf.level` of either 0 or 1, while `t.test()` does.

```
t.test(rnorm(10), rnorm(10), conf.level=0)
# <works>

var.test(rnorm(10), rnorm(10), conf.level=0)
# <error>
```


---


## Solved ##


`wilcoxon.test(paired = TRUE)` has tolerance issues when detecting ties.

```
wilcox.test(c(4,3,2), c(3,2,1), paired=TRUE)
# <warning about ties>

wilcox.test(c(0.4,0.3,0.2), c(0.3,0.2,0.1), paired=TRUE)
# <no warning>
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-December/078774.html)
- [Report on Bugzilla](https://bugs.r-project.org/show_bug.cgi?id=16138)


---


`wilcox.test()` missing indication if normal approximation was used:

```
wilcox.test(rnorm(10), exact=FALSE, correct=FALSE)
wilcox.test(rnorm(10), exact=TRUE, correct=FALSE)
# different results, but same data and identical method description
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-December/078774.html)


---


`wilcox.test()` behaves inconsistently with infinite values.

```
wilcox.test(c(1,2,3,4), c(0,9,8,Inf))
# Inf is removed

wilcox.test(c(1,2,3,4), c(0,9,8,Inf), paired=TRUE)
# Inf is treated as a highest rank
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-December/078774.html)


---


`flinger.test()` can give significant result for constant values.

```
fligner.test(c(1,1,1,2,2,2), c("a","a","a","b","b","b"))
# p-value < 2.2e-16
```

- [Discussion on R-devel](https://stat.ethz.ch/pipermail/r-devel/2019-June/078038.html)

