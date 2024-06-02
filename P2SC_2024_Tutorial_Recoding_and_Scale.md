Recoding Items and Creating Scales (Indexing)
================
Roan Buma
2024-05-22

- [Introduction](#introduction)
- [A brief note on Pipes](#a-brief-note-on-pipes)
- [Recoding Items](#recoding-items)
- [Creating scales](#creating-scales)

# Introduction

In this tutorial, you will learn how to recode variables and create
scales. Recoding variables means changing specific values to different
values, which is often needed to perform statistical analyses. Creating
scales involves combining multiple variables into a single composite
measure, which can provide a more comprehensive assessment of a
particular concept or construct.

Understanding these processes is crucial in survey research because raw
survey data often contains variables that need to be adjusted or
combined to accurately reflect the underlying constructs being studied.
Recoding variables and creating scales ensures the data is appropriately
structured and ready for analysis, leading to reliable and valid
results.

# A brief note on Pipes

This tutorial makes use of
[pipes](https://style.tidyverse.org/pipes.html). In R, the pipe operator
`%>%` is used to streamline code by making it more readable and
efficient, particularly when performing a series of operations on data.
It is widely used in the Tidyverse and we will use it in this tutorial.

The pipe operator `%>%` passes the result of the expression on its
left-hand side to the first argument of the function on its right-hand
side. This allows for a clear, linear sequence of data transformations
or operations, rather than nested function calls.

For example, consider the following code:

``` r
numbers = c(1,2,3,4,5)

numbers_mean = mean(numbers)
numbers_mean
```

    ## [1] 3

``` r
numbers_mean_string = as.character(numbers_mean)
numbers_mean_string
```

    ## [1] "3"

We could nest these functions, writing it like this:

``` r
numbers = c(1,2,3,4,5)

as.character(mean(numbers))
```

    ## [1] "3"

With pipes, the same sequence of operations can be written like this:

``` r
numbers = c(1,2,3,4,5)

numbers %>% # pass on "numbers", no need to specify it in next line
  mean() %>% # pass on "mean(numbers)", no need specify it in next line
  as.character() 
```

    ## [1] "3"

Pipes can be used across the Tidyverse, making coding much more
efficient and user friendly.

# Recoding Items

When you create a scale, you probably used some reversely coded items in
the scale. These need to be recoded so that all the items point
numerically in the same direction, so we can compose a mean score of the
individual items. We will use a combination of the functions `mutate()`
and `recode()` to achieve this.

As you have learned in other tutorials, `mutate()` creates, changes or
deletes columns.

The function `recode()` can be used to recode values to other values, as
the name suggests, with the formula `old = new`. We will show some
examples of the function being used. Examine the input and the output to
understand the function.

Recode can be used to recode text to other text:

``` r
fruits = c("apple","pear","apple","pear")
fruits
```

    ## [1] "apple" "pear"  "apple" "pear"

``` r
recode(fruits, "apple" = "grape")
```

    ## [1] "grape" "pear"  "grape" "pear"

Recode can be used to recode numbers to other numbers:

``` r
numbers = c(1,2,3,4,5)
numbers
```

    ## [1] 1 2 3 4 5

``` r
recode(numbers, `1`=5, `2`=4, `3`=3, `4`=2, `5`=1)
```

    ## [1] 5 4 3 2 1

Recode can also be used to recode 99 to NA:

``` r
numbers = c(1,2,3,4,5,99)
numbers
```

    ## [1]  1  2  3  4  5 99

``` r
recode(numbers, `99` = NA_real_)
```

    ## [1]  1  2  3  4  5 NA

The `recode()` function can, in short, change values in a vector to
other values. If we want to apply this to a column, we have to combine
the function with mutate.

It is smart to keep the original variables. Therefore, we will name the
recoded variables with the same name as the original, but ending with
`_recoded` or `_rec`, so we can remember these are the recoded versions
(e.g., `item1_recoded`)

Let’s examine a dataset about beer from students that participated in
this course. We will look specifically at a scale called buy_intention
Answers to the items range from 1 to 5, as you can see from the first
item of the scale, using `range()`.

``` r
range(beer_dataset$buy_intention_3)
```

    ## [1] 1 5

Alternatively, you can also examine counts of all the possible answer
categories with `table()`

``` r
table(beer_dataset$buy_intention_3)
```

    ## 
    ##   1   2   3   4   5 
    ##  49 115  72  52  27

According to the students’ survey, three of the items need to be
recoded, namely buy_intention_3, buy_intention_6 and buy_intention_7.

Let’s examine how we can apply `recode()` to one item. We first use
mutate, giving (1) the name of the dataset to mutate, (2) the name of
the variable to create, and (3) the `recode()` function

``` r
beer_dataset = beer_dataset %>%
  mutate(buy_intention_3_rec = recode(buy_intention_3, `1`=5, `2`=4, `3`=3, `4`=2, `5`=1))
```

If we now compare buy_intention_3 to buy_intention_3_rec, we can see how
the values were recoded:

``` r
table(beer_dataset$buy_intention_3)
```

    ## 
    ##   1   2   3   4   5 
    ##  49 115  72  52  27

``` r
table(beer_dataset$buy_intention_3_rec)
```

    ## 
    ##   1   2   3   4   5 
    ##  27  52  72 115  49

To recode multiple variables, just repeat the process within the mutate
command:

``` r
beer_dataset = beer_dataset %>%
  mutate(buy_intention_3_rec = recode(buy_intention_3, `1`=5, `2`=4, `3`=3, `4`=2, `5`=1),
         buy_intention_6_rec = recode(buy_intention_6, `1`=5, `2`=4, `3`=3, `4`=2, `5`=1),
         buy_intention_7_rec = recode(buy_intention_7, `1`=5, `2`=4, `3`=3, `4`=2, `5`=1)
)
```

When you have recoded your items correctly, you can now use them to
create your scales.

# Creating scales

To create scales, we will use three functions: `mutate()`, `rowwise()`
and `mean()`. `mutate()` is used to create a new column.

`mean()` calculates the mean of multiple values. For example:

``` r
mean(c(5,10))
```

    ## [1] 7.5

``` r
mean(c(1,2,3,4,5,6,7,8,9,10))
```

    ## [1] 5.5

``` r
mean(c(2,2,3,3,4,5))
```

    ## [1] 3.166667

`rowwise()` tells tidyverse to perform calculations across rows, as this
is done across columns by default. If we apply this to a dataset, we can
also see that it is now treated rowwise:

``` r
example_data = tibble(a = c(1,2,3),
                 b = c(2,2,2),
                 c = c("a","b","c"))

# without rowwise, mean(a) is 1+2+3
example_data %>% 
  mutate(d = mean(a))
```

    ## # A tibble: 3 × 4
    ##       a     b c         d
    ##   <dbl> <dbl> <chr> <dbl>
    ## 1     1     2 a         2
    ## 2     2     2 b         2
    ## 3     3     2 c         2

``` r
# with rowwise, mean(a) is 1 OR 2 OR 3 depending on the row, as the mean is calculated rowwise
example_data %>% 
  rowwise() %>% 
  mutate(d = mean(a))
```

    ## # A tibble: 3 × 4
    ## # Rowwise: 
    ##       a     b c         d
    ##   <dbl> <dbl> <chr> <dbl>
    ## 1     1     2 a         1
    ## 2     2     2 b         2
    ## 3     3     2 c         3

It is important to revert the data back to its original state (instead
of rowwise) after performing the necessary transformations. This is done
with `ungroup()`. Notice how the label ‘rowwise’ disapears from the data
preview.

``` r
# calculate d rowwisea and save the data
example_data = example_data %>% 
  rowwise() %>% 
  mutate(d = mean(a))

# inspect, and notice it is still rowwise
example_data
```

    ## # A tibble: 3 × 4
    ## # Rowwise: 
    ##       a     b c         d
    ##   <dbl> <dbl> <chr> <dbl>
    ## 1     1     2 a         1
    ## 2     2     2 b         2
    ## 3     3     2 c         3

``` r
# revert back
example_data %>% 
  ungroup()
```

    ## # A tibble: 3 × 4
    ##       a     b c         d
    ##   <dbl> <dbl> <chr> <dbl>
    ## 1     1     2 a         1
    ## 2     2     2 b         2
    ## 3     3     2 c         3

In short, to calculate a mean across rows, a combination of `rowwise`,
`mutate` and `mean` are used, followed by `ungroup` to revert the data
to its original shape. Let’s calculate the mean of columns a and b:

``` r
example_data = example_data %>% 
  # rowwise
  rowwise() %>% 
  # create new column with mean
  mutate(d = mean(c(a,b))) %>%
  # ungroup
  ungroup()

example_data
```

    ## # A tibble: 3 × 4
    ##       a     b c         d
    ##   <dbl> <dbl> <chr> <dbl>
    ## 1     1     2 a       1.5
    ## 2     2     2 b       2  
    ## 3     3     2 c       2.5

In our beer example, the students conducted a reliability analysis
earlier, indicating that they should include items buy_intetion_1 …
buy_intention_12, using the recoded versions of items 3, 6 and 7:

``` r
df_buy_intention = beer_dataset %>%
  select(
    buy_intention_1,
    buy_intention_2,
    buy_intention_3_rec,
    buy_intention_4,
    buy_intention_5,
    buy_intention_6_rec,
    buy_intention_7_rec,
    buy_intention_8,
    buy_intention_9,
    buy_intention_10,
    buy_intention_11,
    buy_intention_12,
    buy_intention_13
)

psych::alpha(df_buy_intention)$total
```

    ##  raw_alpha std.alpha   G6(smc) average_r      S/N         ase     mean
    ##  0.9141511 0.9118485 0.9254851 0.4431143 10.34411 0.006810122 2.906716
    ##         sd  median_r
    ##  0.8512911 0.4322792

We will therefore create a composite score of the 13 items, making sure
to use the recoded versions of the recoded items. We apply `rowwise()`
to our dataset to tell R to calculate the mean across rows. We include
`na.rm = TRUE` to tell R to ignore missing values when calculating the
mean score for a scale, although ideally there should not be any missing
values, as this decreases the validity of the scale score for that
respondent.

``` r
beer_dataset = beer_dataset %>%
  # calculate rowwise
  rowwise() %>%
  # create new col with mean
  mutate(
    scale_buy_intention = mean(c(
      buy_intention_1,
      buy_intention_2,
      buy_intention_3,
      buy_intention_4,
      buy_intention_5,
      buy_intention_6,
      buy_intention_7,
      buy_intention_8,
      buy_intention_9,
      buy_intention_10,
      buy_intention_11,
      buy_intention_12,
      buy_intention_13),
      na.rm = TRUE)) %>%
  # ungroup data
  ungroup()
```

The dataset now contains a column called `scale_buy_intention`, which is
the mean of the 13 items relating to buying intention of beer.

Let’s examine this scale briefly using `summarise()`.

``` r
beer_dataset %>%
  summarise(M = mean(scale_buy_intention),
            SD = sd(scale_buy_intention),
            Min = min(scale_buy_intention),
            Max = max(scale_buy_intention))
```

    ## # A tibble: 1 × 4
    ##       M    SD   Min   Max
    ##   <dbl> <dbl> <dbl> <dbl>
    ## 1  2.89 0.458  1.92  4.38

Since we take the mean of 13 values, the values on the scale can be
decimal values, in contrast to the items that make up the scale. The
lowest value is 1.92 and the highest value is 4.38. The mean is 2.88 and
the SD is .46.

We can also plot a [histogram with
ggplot](https://r-graph-gallery.com/220-basic-ggplot2-histogram.html) to
see the distribution of values (you don’t have to understand this code
yet).

``` r
beer_dataset %>%
  ggplot() + geom_histogram(aes(x = scale_buy_intention))
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](P2SC_2024_Tutorial_Recoding_and_Scale_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->
