# R: a quick introduction #

This article focuses on R-specific concepts like vectorisation, recycling, subsetting, matrix and data.frame objects, and some built in types.
The text is laconic, there is no hand-holding, so it is best to consume it slowly.


## Types ##

R stores data in a number of different object types.
Below are some objects of different types:

```r
TRUE              # logical
1L                # integer
1                 # double
1+0i              # complex
"one"             # character
```

Some of these types have values with special meaning:

```r
NaN               # double "not a number"
Inf               # double "infinity"
-Inf              # double "negative infinity"
NA                # logical "missing" value
NA_integer_       # integer "missing" value
NA_real_          # double "missing" value
NA_complex_       # complex "missing" value
NA_character_     # character "missing" value
NULL              # special variable without a type
```

In addition R also uses other, more complex types: `raw`, `list`, `environment`, `closure`.


## Operators ##

For manipulating and combining objects R has a number of standard operators:

```r
+    # addition
-    # subtraction
/    # division
*    # multiplication
^    # power
**   # alternative notation of power
!    # negation
&    # logical and
|    # logical or
==   # equals
!=   # not equals
>    # greater than
>=   # greater than or equal
<    # less than
<=   # less than or equal
&&   # scalar version of logical and
||   # scalar version of logical or
```

Types and operators can be combined in a standard infix form:

```r
TRUE |  FALSE   # TRUE
   2 +  2       # 4
   3 *  10      # 30
3+0i +  0+1i    # 3+1i
   3 == 10      # FALSE
   4 <  Inf     # TRUE
 "a" != "b"     # TRUE
```

Some operators are used with a single object:

```r
!TRUE           # FALSE
-10L            # -10
```

Some operators can combine different types, casting the result to the most general type:

```r
TRUE + 1L       # 2
  2L * 2.2      # 4.4
   2 & TRUE     # TRUE
   0 & TRUE     # FALSE (0 is treated as FALSE, any other number as TRUE)
```

Special type values are returned in special circumstances:

```r
   10 / 0       # Inf
   -2 / 0       # -Inf
    0 / 0       # NaN
-1+0i / 0       # NaN+NaNi
```

If a value is missing operator will determine whether the result can be known:

```r
1^NA            # 1
2^NA            # NA
NA^0            # 1

TRUE  | NA      # TRUE
TRUE  & NA      # NA
FALSE | NA      # NA
FALSE & NA      # FALSE
```

When multiple operators are used within one expression their order is determined by precedence.

```r
2 + 2 / 2       # result = 3, because division has higher precedence
```

Since the available symbols on the keyboard are not enough to cover all necessary operators, other, lesser used operators are defined by surrounding them with a percent symbol `%`:

```r
%/%             # integer division
%%              # residual after integer division (modulo)

27 %/% 24       # result = 1
27 %%  24       # result = 3
```


## Variables ##

Variable names must start with a dot or a letter and contain only letters, numbers, dots, and underscores.
They cannot overwrite existing special values like `NA` and `TRUE`.
Three assignment operators can be used to achieve the same thing:

```r
x <- 2
x =  2
2 -> x
```

In addition R allows to define "non syntactic" identifiers.
Standard variable names must follow defined rules but backtick quotation forces any string to be evaluated as an identifier:

```r
x   <- 1
2   <- 1   # error
`2` <- 1   # bypasses normal evaluation and treats "2" as variable name

x          # standard way of referring to variable named "x"
`x`        # refers to the same variable
2          # object 2 of type double
`2`        # refers to non-syntactic variable "2"
```


## Function calls ##

Functions are called by typing the function name and parentheses with a list of passed parameters.
Parameters can be specified by either their position or their name.

```r
sqrt(3)
sqrt(x = 3)
```

All the operators demonstrated previously are also functions and can be called in the same manner:

```r
`+`(2, 3)
`|`(x = TRUE, y = FALSE)
`!`(TRUE)
```

Assignment itself is also a function call:

```r
`<-`("newvar", 2)  # creates a variable "newvar"
`=`("newvar", 2)   # same
```

A few functions that would have been useful so far:

```r
ls()      # lists all assigned variables
typeof()  # returns the type of an object
```


## Getting help ##

Functions implemented in R have documentation pages which are searchable via another function: `help()`:

```r
help(sum)
help(`<-`)
help(help)
?sum          # shortcut for calling help()
```

Documentation pages can also exist on their own, without being attached to function names:

```r
help(Syntax)             # to read about precedence levels
help(Reserved)           # R's list of reserved symbols
help(NumericConstants)   # parser rules for treating numeric constants
help(Quotes)             # parser rules for treating quoted strings
```

In addition there is a function for searching the documentation pages:

```r
help.search("concatinate")
??concatinate  # shortcut for calling help.search()
```


## Vectors ##

Almost every object in R is vectorized - it can contain multiple elements and has an attribute called "length".

```r
length(NaN)
length(1)
length("what")  # string is not a vector, but an element
```

A vector with multiple elements is created with a "combine" function `c()`:

```r
c(-1L, 0L, 1L)
c(1+1i, -1+3i)
c(TRUE, FALSE, TRUE)
c("a", "b", "any string")
```

`c()` can be used for combining multi-element vectors too:

```r
vec1 <- c(1, 2)
vec2 <- c(2, 3)
c(vec1, vec2)
c(c(1,2), c(2,3))  # same
c(c(1,2,2,3))      # also same
c(1,2,2,3)         # same as well
```

An increasing/decreasing sequence of integers can be created using a shortcut:

```r
1:2
10:1
-100:100
```

When multiple different types are combined the resulting vector takes the most general type:

```r
c(TRUE, 1L)               # integer
c(TRUE, 1)                # double
c(1L, 1+1i)               # complex
c(TRUE, "A")              # character
c(NA_integer_, NA_real_)  # double
```

Most operators, when applied on vectors, apply the operation to each element separately:

```r
x <- c(1, 2, 3)
y <- c(0, 10, 1)

x + 1     # 2 3 4
x + y     # 1 12 4
x > 2     # FALSE FALSE TRUE
x > y     # FALSE TRUE FALSE
```

Lots of functions work with vectors too, and provide a reasonable result:

```r
x <- c(1, 2, 3, 4, 5)
y <- c(2, 1, 3, 1, 5)

mean(x)
sum(y)
log2(x)
cor(x, y)
paste(x, y)
```

Vectors can be empty (have 0 elements):

```r
logical()
integer()
double()
character()
complex()

length(complex())
length(c(numeric(), numeric(), numeric()))
```

Finally, elements within a vector can have names:

```r
x <- c(a=1, b=2, c=3, d=4)
names(x)
```

There is no requirement for the names of a vector to be unique:

```r
x <- c(a=1, b=2, b=3, d=4)
names(x)
```


## Recycling ##

R can apply binary operators on vectors of different lengths.
In this case recycling takes place - the shorter vector is extended to match the length of the longer one by recycling its elements starting from the first one:

```r
x <- c(1, 1, 1, 1)
y <- c(0, 1)

        # original       after recylcing    result
x + y   # x = 1 1 1 1    x = 1 1 1 1        1 2 1 2
        # y = 0 1        y = 0 1 0 1
```

Recycling helps writing common element-wise vector expressions without resorting to loops:

```r
# subtract 1 from each element in x
x <- c(1, 2, 3, 4)
x - 1

# check which elements of x are greater than 2
x <- c(1, 2, 3, 4)
x > 2

# turn every second element of x to 0
x <- c(1, 2, 3, 4)
y <- c(0, 1)
x * y
```

When the longer vector is not a multiple of the shorter one - recycle takes place but a warning is displayed:

```r
c(1, 2, 3) - c(0, 1)   # result: 1 1 3

# Warning message:
# In c(1, 2, 3) - c(0, 1) :
#   longer object length is not a multiple of shorter object length
```


## Subsetting ##

A vector can be treated as an array of elements, hence we might want to subset it by selecting only a fraction of those elements.
In R this is performed using the `[` operator.

```r
x <- c(10, 20, 30, 40)
x[1]
```

Elements can be selected by specifying the desired positions (numbering in R starts from 1).

```r
x <- c(10, 20, 30, 40)

x[4]
x[1:2]
x[0]       # selects "nothing" - returns an empty vector
x[c(1,1)]  # same element can be selected multiple times
x[5]       # NA (element with unknown value)
```

Elements can be selected by specifying the positions that should be dropped out:

```r
x <- c(10, 20, 30, 40)

x[-1]
x[c(-1,-3)]
x[c(-1,-1)]  # same element is "removed twice"
x[-5]        # doesn't change x in any way
```

Elements can be selected by specifying `TRUE` if the element should be returned, and `FALSE` otherwise:

```r
x <- c(10, 20, 30, 40)

x[c(TRUE, FALSE, FALSE, FALSE)]
x[c(TRUE, TRUE, FALSE, TRUE)]
x[c(FALSE, TRUE)]               # c(FALSE, TRUE) recycled - returns every second element

# one interesting example
x[x > 20]  # 1) x > 20 compares each element of x with 20 (20 is recycled)
           # 2) x > 20 creates indexing vector of TURE/FALSE values for each element in x
           # 3) the obtained vector is then used for subsetting x
           # 4) as a result only x elements that are greater than 20 are returned
```

Elements can be selected by their name.

```r
x <- c(a=10, b=20, c=30, d=40)

x["a"]
x[c("a", "b")]
x[c("e")]      # NA - element with unknown value
x[-c("a")]     # error - this syntax cannot be used for dropping elements

# when names are not unique - first matching element is returned
x <- c(a=10, b=20, b=30, d=40)

x["b"]         # 20
```

Empty selection selects all elements:

```r
x <- c(10, 20, 30, 40)

x[]
```


## Replacement ##

Elements in a vector can be replaced using the indexing syntax with an assignment.

```r
x <- c(10, 20, 30, 40)
x[1] <- 0                  # replaces first element with 0
```

Replacing by index:

```r
x <- c(10, 20, 30, 40)

x[4] <- c(51)
x[c(1,3)] <- c(12, 32)
x[0]  <- 0                 # doesn't do anything
x[5]  <- 50                # appends "z" to the 5th position of the vector
x[10] <- 100               # appends "z" to the 10th position, adds NA to 6th:9th
```

Replacing by excluding positions:

```r
x <- c(10, 20, 30, 40)

x[-c(1,2,3)] <- 41
x[-1] <- c(22,32,43)
x[-5] <- 100               # will replace every element
```

Replacing by logical indexing:

```r
x <- c(10, 20, 30, 40)

x[c(TRUE, FALSE, TRUE, FALSE)] <- c(11,31)
x[c(TRUE, TRUE, TRUE, FALSE)] <- c(11,31)  # c(11, 31) will be recycled
x[c(FALSE, TRUE)] <- c(22,42)              # c(FALSE TRUE) will be recycled
x[x < 10] <- NA
```

Replacing by name:

```r
x <- c(a=10, b=20, c=30, d=40)

x["b"] <- 21
x[c("a","d")] <- c(11,41)
x["z"] <- 2                # will append a new element named "z"

# when names are not unique - first matching element is replaced
x <- c(a=10, b=20, b=30, d=40)

x["b"] <- 21               # replaces b=20 element
```

An empty assignment is a shortcut for replacing all elements:

```r
x <- c(10, 20, 30, 40)

x   <- 0  # replaces x with 0
x[] <- 0  # replaces all elements of x with 0
```

After replacement, if needed, the vector is converted to the most general type:

```r
x <- c(1, 2, 3, 4)  # x is double
x[4] <- "e"         # x is now character
```

## List type ##

In a vector all elements have the same type.
Lists can store multiple elements of different types.

```r
list(1L, 0, "a", TRUE)  # values are not converted to character type
```

Since list can store vectors - elements in a list can have any length:

```r
l <- list(c("a","b"), c(TRUE, FALSE), 1:100)

length(l)  # length is the nuber of elements in a list: 3
```

List can store other lists within itself:

```r
list(list(1, 3+0i), list("a"))
list(list(1, 3+0i), "a")        # one list can have both lists and simple vectors
list(list(1, list("a")))        # lists can be nested more than 2 levels deep
list(a=TRUE, b=1:100)           # list elements can have names
```

A list can be empty and store empty elements

```r
l1 <- list()
l2 <- list(numeric(), list(), complex())

length(l1)  # 0
length(l2)  # 3
```


Same subsetting rules described before also apply to lists:

```r
l <- list(a=TRUE, b=c("a","b"), c=1:100)

l[1]                     # list with one element
l[c(1,2)]
l[-1]
l[c(TRUE, FALSE, TRUE)]
l[c("a","c")]
l[]
```

Special double bracket `[[` operator is used for extracting elements from a list.
But it only allows indexing by positive numbers and names:

```r
l <- list(a=TRUE, b=c("a","b"), c=1:100)

l[[1]]                   # first element of a list
l[[c(1,2)]]              # error - cannot return two objects, without a list to hold them
l[[-c(1,2)]]             # error - negative indices cannot be used for list elements
l[[c(TRUE,FALSE,TRUE)]]  # error - logical indices cannot be used
l[[c("a")]]
l[[c("a","b")]]          # error - cannot return two objects, without a list to hold them
```

Elements in nested lists can be selected by stacking double brackets:

```r
l <- list(list(TRUE), list(list(1, 2:3)))

l[[1]]               # selects first element - a list
l[[1]][[1]]          # selects second element from the first element (list)
l[[2]][[1]][[1]]     # deeper nested selection (hard to describe, sorry!)
l[[2]][[1]][[2]][2]  # select an element of a deeply nested vector
```

Elements in a list are replaced using procedures equivalent to subsetting them:

```r
l <- list(a=TRUE, b=c("a","b"), c=1:100)

l[[1]] <- FALSE        # replace the first list element with FALSE
l[1]   <- list(FALSE)  # same as above
l[1]   <- FALSE        # also same as above
l[[1]] <- list(FALSE)  # ! replaces first element with a list
l[2]   <- NULL         # removes the second element from the list
```

Same replacement procedures work on nested lists:

```r
l <- list(list(TRUE), list(list(1, 2:3)))

l[[1]][[1]]          <- FALSE
l[[2]][[1]][[1]]     <- 0
l[[2]][[1]][[2]][1]  <- -1      # replace element of a deeply nested vector
l[[2]][[2]]          <- list()  # replace a whole nested list
```

Selecting a single element from a list by name is a frequent procedure, therefore it has a shortcut operator:

```r
l <- list(a=TRUE, b=c("a","b"), c=list(c1=1, c2=2))

l$a     # same as l[["a"]]
l$c     # same as l[["c"]]
l$c$c1  # same as l[["c"]][["c1"]]
```

Same shortcut can be used for replacement:

```r
l <- list(a=TRUE, b=c("a","b"), c=list(c1=1, c2=2))

l$a     <- FALSE  # replace element "a"
l$c$c1  <- 0      # replace "c1" element of the list "c"
l$c     <- NA     # replace whole "c" list with an object NA
```


## Matrices ##

Matrices are vectors with an additional atribute of dimension.
Below are multiple equivalent ways of creating matrices:

```r
x <- c(1, 2, 3, 4)

matrix(x, nrow=2, ncol=2)
dim(x) <- c(2,2)
attr(x, "dim") <- c(2,2)
```

The attributes of a matrix can be obtained via functions:

```r
X <- matrix(1:10, nrow=2, ncol=5)

dim(X)
nrow(X)
ncol(X)
```

Since matrices are constructed from vectors - any vector type, including a list, can be turned into a matrix:

```r
matrix(c(TRUE, FALSE, TRUE, FALSE), nrow=2, ncol=2)
matrix(1:4, nrow=2, ncol=2)
matrix(c("a","b","c","d"), nrow=2, ncol=2)
matrix(list(TRUE, 2, "a", 3+0i), nrow=2, ncol=2)
```

A matrix, like a vector, can be empty:

```r
matrix(logical(), nrow=0, ncol=0)
matrix(double(), nrow=0, ncol=0)
matrix(list(), nrow=0, ncol=0)
```

Under the hood the matrix is a vector, "folded" into a matrix form by first filling up the first column, then the second column, and so on:

```r
x <- 1:20

matrix(x, ncol=4)

#      [,1] [,2] [,3] [,4]
# [1,]    1    6   11   16
# [2,]    2    7   12   17
# [3,]    3    8   13   18
# [4,]    4    9   14   19
# [5,]    5   10   15   20
```

And all operators that work on vectors treat matrices as a vectors.
Here is what happens when a vector is added to a matrix:

```r
X <- matrix(1:8, nrow=4)
v <- 1:2

X + v    # 1) X is flattened out to a vector form 1 2 3 4 5 6 7 8
         # 2) v is recycled to match length of X  1 2 1 2 1 2 1 2
         # 3) element-wise addition takes place   2 4 4 6 6 8 9 10
         # 4) X is re-folded into a matrix again, column by column:
         #      [,1] [,2]
         # [1,]    2    6
         # [2,]    4    8
         # [3,]    4    8
         # [4,]    6   10
```

This allows specifying some common operations easily:

```r
X <- matrix(1:20, nrow=4)
Y <- matrix(runif(20), nrow=4)

X + 1            # add a number to each element of a matrix
X > 2            # compare each element of a matrix with a number
X / Y            # divide elements of one matrix from another
X < Y            # compare two matrices element-wise
X - rowMeans(X)  # subtract the mean of each row of a matrix
```

Just like operators, functions that work on vectors treat matrices as vectors:

```r
X <- matrix(1:20, ncol=4)

length(X)  # 20
sum(X)     # sum of all elements
sqrt(X)    # square root of each element within the matrix
```

Many specialized functions and operators are available for working with matrices. Some of them:

```r
X <- matrix(rnorm(20), ncol=4)
Y <- matrix(rnorm(20), nrow=4)

t(X)     # transpose of a matrix
diag(X)  # obtain matrix diagonal
X %*% Y  # matrix multiplication
X %x% Y  # kronecker product of two matrices
```

Elements within a matrix can be subsetted and replaced as if they were in a vector:

```r
X <- matrix(1:20, ncol=4)

X[1]
X[c(2,3,5)]
X[-2]
X[c(TRUE,FALSE)]
X[X >= 10]

X[1] <- 0
X[c(2,3,5)] <- c(-2,-3,-5)
X[X >= 10] <- 10
```

In addition matrix allows performing same operations on rows and columns:

```r
X <- matrix(1:20, ncol=4)
X[1:2, 1:2]
```

Selecting rows and columns by an index:

```r
X <- matrix(1:20, ncol=4)

X[1:2,]      # first two rows
X[,3:4]      # 3rd and 4th column
X[1:2, 3:4]  # combination of the above
X[1,]        # selecting a single row returns a simple vector (not a matrix)
X[,2]        # same with columns
```

Removing rows and columns by an index:

```r
X <- matrix(1:20, ncol=4)

X[-1,]          # drop first row
X[,-c(2,3)]     # drop second and third columns
X[-1, -c(2,3)]  # combination of the above
X[-1, c(2,3)]   # removal of rows combined with selection of columns
X[,-c(1:3)]     # when single row/column is left - a vector is returned
```

Selecting rows and columns by logical indices:

```r
X <- matrix(1:20, ncol=4)

X[c(TRUE,FALSE,TRUE,FALSE,FALSE),]                # select 1st and 3rd rows
X[,c(TRUE,FALSE,TRUE,FALSE)]                      # select 1st and 3nd columns
X[c(TRUE,FALSE,TRUE,FALSE,FALSE), c(TRUE,FALSE)]  # combination (with recycling)
X[,c(TRUE,FALSE,FALSE,FALSE)]                     # returns a vector

# a more practical example
X[rowMeans(X) > 10,]       # all rows with mean above 10
```

Rows and columns of a matrix can also have their own names that can be used for selection:

```r
X <- matrix(1:20, ncol=4)
rownames(X) <- paste0("r", 1:5)
colnames(X) <- paste0("c", 1:4)

X[c("r1","r2),]               # rows named "r1" and "r2"
X[,c("c1","c3)]               # columns names "c1" and "c3"
X[c("r1","r2"), c("c1","c3)]  # combination of the above
X[,"c4"]                      # returns a vector
```

In addition matrix also has a special subseting format - by row-column pairs:

```r
X <- matrix(1:20, ncol=4)
i <- rbind(c(1,2),  # element in row 1 column 2
           c(2,2),  # element in row 2 column 2
           c(4,1)   # element in row 4 column 1
           )

X[i]  # selects the elements in all the specified positions
```

And the same with names:

```r
X <- matrix(1:20, ncol=4)
rownames(X) <- paste0("r", 1:5)
colnames(X) <- paste0("c", 1:4)
i <- rbind(c("r1","c2"),
           c("r2","c2"),
           c("r4","c1")
           )

X[i]  # same as before
```

Elements within the matrix can be replaced using all the subsetting examples outline above.
But to save time and space these will not be demonstrated further.


## Dataframes ##

Dataframes in R are implemented as a class on top of lists, with the restriction of each element within a list having the same length.
Such implementation allows to construct tables, where each list element is interpreted as a separate column.
As a result, unlike a matrix, `data.frame` class can contain columns of different types.

```r
l <- list(id=1:5, name=c("a","b","c","d","e"), state=c(TRUE,TRUE,FALSE,TRUE,TRUE))
as.data.frame(l)

#   id name state
# 1  1    a  TRUE
# 2  2    b  TRUE
# 3  3    c FALSE
# 4  4    d  TRUE
# 5  5    e  TRUE

# same as above:
df <- data.frame(id=1:5, name=c("a","b","c","d","e"), state=c(TRUE,TRUE,FALSE,TRUE,TRUE))
```

This also permits to have nested lists in the columns of a data frame:

```r
df <- data.frame(id=1:2, name=c("Albert","Bob"))
df$objects <- list(c("key","pin"), c("ball"))

#   id   name  objects
# 1  1 Albert key, pin
# 2  2    Bob     ball
```

Since data frames are lists - they are subsetted using list subset operator:

```r
df <- data.frame(id = 1:3, name = c("Albert","Bob","Cindy"))

df[1]         # first list element (first column)
df[1:2]       # first two list elements (first and second columns)
df[-1]        # removes first list element (first column)
df[[2]][2:3]  # second and third vector elements from the second column
df$id         # list element (column) by name
df$name[1:2]  # first two vector elements of "name" column
```

Equivalent operators can be used for replacement.
Here only a few more practical examples are provided:

```r
df <- data.frame(id = 1:3, name = c("Albert","Bob","Cindy"))

df$name[df$id=="2"]           <- "Bobby"
df$surname                    <- c("Thompson", NA, "Friedman")
df$surname[df$name=="Bobby"]  <- "Smith"
```

Since data frame is arranged as a matrix it also allows matrix subset operations, with equivalent forms used for replacement:

```r
df <- data.frame(id = 1:3, name = c("Albert","Bob","Cindy"))

df[,1]                # first column (like with a matrix, returns a simple vector)
df[1,]                # first row of a data frame (returns a data frame)
df[df$id==2, ]        # all entries for id = 2
df[df$id %in% 2:3, ]  # all entries for ids 2 and 3
```

Just like a matrix the data frame can have row names, with column names often provided by default.
And those names can be used to subset rows and columns in a matrix style:

```r
df <- data.frame(id = 1:3, name = c("Albert","Bob","Cindy"))
rownames(df) <- c("a", "b","c")

#   id   name
# a  1 Albert
# b  2    Bob
# c  3  Cindy

df[c("a","b"),]         # rows named "a" and "b"
df[,"name"]             # column named "name"
df[c("a","b"), "name"]  # combination of the two
```

Special row-column pair selection works as well:

```r
df <- data.frame(id = 1:3, name = c("Albert","Bob","Cindy"))
i  <- rbind(c(1,2),  # 1st row, 2nd column
            c(3,1)   # 3rd row, 1st column
            )

df[i]
```

Replacing the whole entry (one row) is an often needed procedure.
Since such entries are subsets of the data frame they are data frames themselves and need to be replaced with an equivalent data frame (or a list).
Hence the replacement has to use equal element names:

```r
df <- data.frame(id = 1:3, name = c("Albert","Bob","Cindy"))

df[df$id==2, ] <- list(id=2, name="Bruce")        # new entry for id = 2
df[df$id==2, ] <- data.frame(id=2, name="Bruce")  # same

#   id   name
# 1  1 Albert
# 2  2  Bruce
# 3  3  Cindy
```
