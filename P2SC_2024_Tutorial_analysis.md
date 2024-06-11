Tutorial Analyses and Results
================
Roan Buma
2024-05-22

- [Introduction](#introduction)
- [Analyses](#analyses)
  - [Exploratory analysis](#exploratory-analysis)
  - [Hypothesis testing](#hypothesis-testing)
    - [Simple linear regression](#simple-linear-regression)
    - [Multiple Linear Regression](#multiple-linear-regression)
  - [Additional Analysis](#additional-analysis)

# Introduction

Welcome to this short R tutorial that explains an exploratory analysis
(correlation matrix), and hypothesis testing (simple linear regression
and multiple linear regression).

As always, first we load the necessary packages and set up our working
directory. Second, we load in the prepared dataset we created at the end
of the data preparation. By writing the code for the analysis in a
separate R code file, we keep things tidy.

``` r
# set wd
setwd("C:/Users/roanb/Documents")
```

``` r
# load packages
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(psych)
```

    ## 
    ## Attaching package: 'psych'
    ## 
    ## The following objects are masked from 'package:ggplot2':
    ## 
    ##     %+%, alpha

``` r
# load prepared dataset
df = readRDS("prepared_dataset_test.rds")
```

# Analyses

## Exploratory analysis

For the dataset we have, we want to do a correlation analysis first on
all the variables. These are *buy intent* (scale), *alcohol free beer
perception* (scale), *gender stereotypes* (scale), and the demographic
varibales *age*, *gender*, and *occupation*.

The *three scales* have an interval measurement level. The same goes for
*age*.

*Gender* and *occupation*, in contrast, are nominal variables.

You can see this by checking with `str()`.

``` r
# select only the variables of interest
df_cor_vars = select(
  df,
  SCA_buy_intent,
  SCA_alc_fre_beer_percep,
  SCA_gend_stereo,
  age,
  gender,
  occupation)

# inspect them
str(df_cor_vars)
```

    ## tibble [315 × 6] (S3: tbl_df/tbl/data.frame)
    ##  $ SCA_buy_intent         : num [1:315] 3.69 2 2 3 3.23 ...
    ##  $ SCA_alc_fre_beer_percep: num [1:315] 1.83 1.33 3 3.5 2 ...
    ##  $ SCA_gend_stereo        : num [1:315] 2 2.83 4.67 1.83 1.83 ...
    ##  $ age                    : num [1:315] 24 20 29 20 23 26 22 24 21 58 ...
    ##  $ gender                 : Factor w/ 5 levels "male","female",..: 1 1 1 2 1 1 1 1 1 1 ...
    ##  $ occupation             : Factor w/ 5 levels "student","full-time",..: 1 1 2 1 1 1 1 1 1 2 ...

We can examine the frequencies of the different levels of *gender* and
*occupation* with `table()`

``` r
table(df$gender)
```

    ## 
    ##                      male                    female non-binary / third gender 
    ##                       160                       144                         4 
    ##         prefer not to say                     other 
    ##                         4                         3

``` r
table(df$occupation)
```

    ## 
    ##     student   full-time not working     retired   part-time 
    ##         215          76           6           2          16

To be able to do an analysis with gender and occupation, we have to
create a **dummy variable**.

For gender, we will create a dummy with *female = 1* and *male = 0*.
Other answer categories will be coded as missing values (`NA`), as their
frequencies are too low to analyse.

For occupation, let’s say we are interested mostly in differences
between students and working people. We will create a dummy with
*student = 1* and *full-time OR part-time = 1*. Respondents who answered
*not working* or *retired* will be coded as missing (`NA`).

We can create a dummy in several ways, but an easy and versatile way to
create variables with multiple matched translations (e.g., `"female"`
needs to become `1`, and `"male"` needs to become `0`), is to use
`case_when`. This function, as the name implies, creates an output *when
something is the case*. *In the case where* gender is equal to female,
the dummy needs to be `1`. *In the case where* gender is equal to male,
the dummy needs to be `0`. *In all other cases*, the dummy needs to be
`NA`,

The function looks like this when used on its own:

``` r
case_when(gender == "female" ~ 1,
          gender == "male" ~ 0,
          TRUE ~ NA)
```

Of course, we want to create a dummy called `woman`, so we need to do
this within `mutate()` again and save it to `df`:

``` r
df = mutate(
  df,
  woman = case_when(gender == "female" ~ 1,
                    gender == "male" ~ 0,
                    TRUE ~ NA))
```

We can inspect whether we correctly created a dummy, by inspecting the
frequencies again with `table()`:

``` r
table(df$gender)
```

    ## 
    ##                      male                    female non-binary / third gender 
    ##                       160                       144                         4 
    ##         prefer not to say                     other 
    ##                         4                         3

``` r
table(df$woman)
```

    ## 
    ##   0   1 
    ## 160 144

We can also create a 2-way table

``` r
table(df$gender, df$woman)
```

    ##                            
    ##                               0   1
    ##   male                      160   0
    ##   female                      0 144
    ##   non-binary / third gender   0   0
    ##   prefer not to say           0   0
    ##   other                       0   0

We can see that we correctly created a dummy.

Let’s apply the same idea to occupation.

``` r
df = mutate(
  df,
  student = case_when(occupation == "student" ~ 1,
                      occupation == "full-time" ~ 0,
                      occupation == "part-time" ~ 0,
                      TRUE ~ NA)
  )
```

``` r
table(df$occupation, df$student)
```

    ##              
    ##                 0   1
    ##   student       0 215
    ##   full-time    76   0
    ##   not working   0   0
    ##   retired       0   0
    ##   part-time    16   0

Now, we can see that all the variables we need are actually numerical,
so we can do a correlation analysis on them:

``` r
# select only the variables of interest
df_cor_vars = select(
  df,
  SCA_buy_intent,
  SCA_alc_fre_beer_percep,
  SCA_gend_stereo,
  age,
  woman, # replace gender with woman dummy
  student) # replace occupation with student dummy
```

``` r
str(df_cor_vars)
```

    ## tibble [315 × 6] (S3: tbl_df/tbl/data.frame)
    ##  $ SCA_buy_intent         : num [1:315] 3.69 2 2 3 3.23 ...
    ##  $ SCA_alc_fre_beer_percep: num [1:315] 1.83 1.33 3 3.5 2 ...
    ##  $ SCA_gend_stereo        : num [1:315] 2 2.83 4.67 1.83 1.83 ...
    ##  $ age                    : num [1:315] 24 20 29 20 23 26 22 24 21 58 ...
    ##  $ woman                  : num [1:315] 0 0 0 1 0 0 0 0 0 0 ...
    ##  $ student                : num [1:315] 1 1 0 1 1 1 1 1 1 0 ...

We can now do a correlation test on these variables. We apply
`corr.test()` from the `psych` package on the dataset we have created
that only contains the variables that should be in the correlation test.

``` r
# correlation test
psych::corr.test(df_cor_vars)
```

    ## Call:psych::corr.test(x = df_cor_vars)
    ## Correlation matrix 
    ##                         SCA_buy_intent SCA_alc_fre_beer_percep SCA_gend_stereo
    ## SCA_buy_intent                    1.00                   -0.02            0.03
    ## SCA_alc_fre_beer_percep          -0.02                    1.00           -0.11
    ## SCA_gend_stereo                   0.03                   -0.11            1.00
    ## age                               0.02                    0.21           -0.09
    ## woman                             0.04                    0.10           -0.44
    ## student                           0.03                   -0.18            0.04
    ##                           age woman student
    ## SCA_buy_intent           0.02  0.04    0.03
    ## SCA_alc_fre_beer_percep  0.21  0.10   -0.18
    ## SCA_gend_stereo         -0.09 -0.44    0.04
    ## age                      1.00 -0.01   -0.64
    ## woman                   -0.01  1.00    0.04
    ## student                 -0.64  0.04    1.00
    ## Sample Size 
    ##                         SCA_buy_intent SCA_alc_fre_beer_percep SCA_gend_stereo
    ## SCA_buy_intent                     315                     315             315
    ## SCA_alc_fre_beer_percep            315                     315             315
    ## SCA_gend_stereo                    315                     315             315
    ## age                                315                     315             315
    ## woman                              304                     304             304
    ## student                            307                     307             307
    ##                         age woman student
    ## SCA_buy_intent          315   304     307
    ## SCA_alc_fre_beer_percep 315   304     307
    ## SCA_gend_stereo         315   304     307
    ## age                     315   304     307
    ## woman                   304   304     297
    ## student                 307   297     307
    ## Probability values (Entries above the diagonal are adjusted for multiple tests.) 
    ##                         SCA_buy_intent SCA_alc_fre_beer_percep SCA_gend_stereo
    ## SCA_buy_intent                    0.00                    1.00            1.00
    ## SCA_alc_fre_beer_percep           0.67                    0.00            0.65
    ## SCA_gend_stereo                   0.62                    0.06            0.00
    ## age                               0.68                    0.00            0.13
    ## woman                             0.52                    0.07            0.00
    ## student                           0.55                    0.00            0.45
    ##                          age woman student
    ## SCA_buy_intent          1.00  1.00    1.00
    ## SCA_alc_fre_beer_percep 0.00  0.68    0.02
    ## SCA_gend_stereo         1.00  0.00    1.00
    ## age                     0.00  1.00    0.00
    ## woman                   0.88  0.00    1.00
    ## student                 0.00  0.48    0.00
    ## 
    ##  To see confidence intervals of the correlations, print with the short=FALSE option

Almost all the information to create our APA table is in this output,
except for the *N*, *Mean* and *Standard Deviation*.

We can gather this information using `mean()`, `sd()`, and
`sum(!is.na())` (this tells R to give back the *number of non-missing
values*, which we are interested in). We use these functions on each
column that we are interested in for our APA table.

``` r
# buy intent
mean(df$SCA_buy_intent, na.rm=T)
```

    ## [1] 2.906716

``` r
sd(df$SCA_buy_intent, na.rm=T)
```

    ## [1] 0.8512911

``` r
sum(!is.na(df$SCA_buy_intent))
```

    ## [1] 315

``` r
# alcohol free beer perception
mean(df$SCA_alc_fre_beer_percep, na.rm=T)
```

    ## [1] 2.410582

``` r
sd(df$SCA_alc_fre_beer_percep, na.rm=T)
```

    ## [1] 0.8092229

``` r
sum(!is.na(df$SCA_alc_fre_beer_percep))
```

    ## [1] 315

``` r
# gender stereotypes
mean(df$SCA_gend_stereo, na.rm=T)
```

    ## [1] 1.97037

``` r
sd(df$SCA_gend_stereo, na.rm=T)
```

    ## [1] 0.6685248

``` r
sum(!is.na(df$SCA_gend_stereo))
```

    ## [1] 315

``` r
# age
mean(df$age, na.rm=T)
```

    ## [1] 25.91746

``` r
sd(df$age, na.rm=T)
```

    ## [1] 10.3929

``` r
sum(!is.na(df$age))
```

    ## [1] 315

``` r
# for dummy variables, mean and sd do not make sense so we only calculate N
# woman dummy
sum(!is.na(df$woman))
```

    ## [1] 304

``` r
# student dummy
sum(!is.na(df$student))
```

    ## [1] 307

## Hypothesis testing

### Simple linear regression

Creating a **simple linear regression** in R is fairly simple, using the
function `lm()` which stands for linear model. The function requires on
the most basic level a *formula* (what is the DV and what are the IVs?)
and a *dataset*:
`lm(dependent_variable ~ independent_variable, data = dataset)`. We
usually save the resulting *model* in an object.

I will call my first model `mod_1` (model 1). Let’s do a simple linear
regression looking at the extent to which alcohol free beer perception
predicits buying intent.

``` r
mod_1 = lm(SCA_buy_intent ~ SCA_alc_fre_beer_percep, data = df)
```

Running the code above creates the model. We can then access the results
of it with `summary()`.

``` r
summary(mod_1)
```

    ## 
    ## Call:
    ## lm(formula = SCA_buy_intent ~ SCA_alc_fre_beer_percep, data = df)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.94198 -0.65496  0.06635  0.63398  1.87308 
    ## 
    ## Coefficients:
    ##                         Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)              2.96698    0.15113  19.632   <2e-16 ***
    ## SCA_alc_fre_beer_percep -0.02500    0.05944  -0.421    0.674    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.8524 on 313 degrees of freedom
    ## Multiple R-squared:  0.0005647,  Adjusted R-squared:  -0.002628 
    ## F-statistic: 0.1769 on 1 and 313 DF,  p-value: 0.6744

We can see that this first test is not significant, as the P value of
the model is `0.6744`. Also, we can see that `SCA_alc_fre_beer_percep`
has a p-value of `0.674`.

Let’s examine a second test, where we examine to what extent age
predicts alcohol free beer perception.

``` r
mod_2 = lm(SCA_alc_fre_beer_percep ~ age, data = df)
summary(mod_2)
```

    ## 
    ## Call:
    ## lm(formula = SCA_alc_fre_beer_percep ~ age, data = df)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.92044 -0.61353 -0.02522  0.55712  2.23826 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 1.983787   0.120088  16.519  < 2e-16 ***
    ## age         0.016467   0.004302   3.828 0.000156 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.7922 on 313 degrees of freedom
    ## Multiple R-squared:  0.04473,    Adjusted R-squared:  0.04168 
    ## F-statistic: 14.66 on 1 and 313 DF,  p-value: 0.0001558

This model is significant (p = `0.0001558`). We can also see that age
has a positive effect on alcohol free beer perception (Estimate = =
`0.016467`, and p = `0.000156`). You can also see that the effect is
significant by the three stars next to the p-value (`***`).

Let’s examine a third test, looking at whether being a student predicts
alcohol free beer perception:

``` r
mod_3 = lm(SCA_alc_fre_beer_percep ~ student, data = df)
summary(mod_3)
```

    ## 
    ## Call:
    ## lm(formula = SCA_alc_fre_beer_percep ~ student, data = df)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.62319 -0.63798  0.02868  0.52868  2.19535 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  2.62319    0.08204  31.976  < 2e-16 ***
    ## student     -0.31854    0.09803  -3.249  0.00129 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.7869 on 305 degrees of freedom
    ##   (8 observations deleted due to missingness)
    ## Multiple R-squared:  0.03346,    Adjusted R-squared:  0.03029 
    ## F-statistic: 10.56 on 1 and 305 DF,  p-value: 0.001286

This model is signicicant (`p-value: 0.001286`) and we see that student
has a negative effect on alcohol free beer perception (= -0.32, *p* =
0.001).

### Multiple Linear Regression

In multiple linear regression, we examine how changes in multiple
variables (independent variables) predict changes in another variable
(dependent variable).

The idea is the same as simple linear regression, except that we add
more variables to the formula. This means that the model not only looks
at the effect of independent variables A and B on dependent variable Y,
but also whether A and B have an effect when considered together in the
same model.

For example, let’s take alcohol free beer perception as dependent
variable, and examine whether age, woman, student and gender stereotypes
predict an increase/decrease in perception. We know already that age and
student predict alcohol free beer perception in a simple linear
regression, but how about in a multiple linear regression?

``` r
mod_4 = lm(SCA_alc_fre_beer_percep ~ age + woman + student + SCA_gend_stereo, 
           data = df)

summary(mod_4)
```

    ## 
    ## Call:
    ## lm(formula = SCA_alc_fre_beer_percep ~ age + woman + student + 
    ##     SCA_gend_stereo, data = df)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.96716 -0.54274 -0.02813  0.55648  2.24549 
    ## 
    ## Coefficients:
    ##                  Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)      2.100965   0.307806   6.826 5.06e-11 ***
    ## age              0.013464   0.006346   2.122   0.0347 *  
    ## woman            0.180002   0.102300   1.760   0.0795 .  
    ## student         -0.162763   0.129191  -1.260   0.2087    
    ## SCA_gend_stereo -0.007615   0.079259  -0.096   0.9235    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.7842 on 292 degrees of freedom
    ##   (18 observations deleted due to missingness)
    ## Multiple R-squared:  0.06269,    Adjusted R-squared:  0.04985 
    ## F-statistic: 4.883 on 4 and 292 DF,  p-value: 0.0007968

We can see that this model is signicant (*p* \< .001), and that only age
has a significant effect (= 0.01, *p* = 0.03). In contrast to our
previous model, student does not have a signicant effect (*p* = .21).
Can you guess why this could be the case?

## Additional Analysis

As an additional analysis, we can guess that student and age explain
some of the same variance in alcohol free beer perception, as students
are probably young.

``` r
mod_5 = lm(formula = student ~ age,
           data = df)

summary(mod_5)
```

    ## 
    ## Call:
    ## lm(formula = student ~ age, data = df)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.92826  0.07174  0.13453  0.19732  0.51581 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  1.493326   0.058374   25.58   <2e-16 ***
    ## age         -0.031393   0.002168  -14.48   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.3538 on 305 degrees of freedom
    ##   (8 observations deleted due to missingness)
    ## Multiple R-squared:  0.4073, Adjusted R-squared:  0.4054 
    ## F-statistic: 209.6 on 1 and 305 DF,  p-value: < 2.2e-16

Indeed, we see that age negatively predicts being a student (= -.03, *p*
\<.001), which could explain why these two variables do not both show
significant effects in the previous model.

As a final additional analysis, we could examine the effect that age has
only for students, or only for working people.

``` r
# only students
df_students = filter(df, student == 1)

mod_6 = lm(formula = SCA_alc_fre_beer_percep ~ age,
           data = df_students)

summary(mod_6)
```

    ## 
    ## Call:
    ## lm(formula = SCA_alc_fre_beer_percep ~ age, data = df_students)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.52355 -0.56070 -0.02578  0.52375  2.02375 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)   
    ## (Intercept)  1.29374    0.44165   2.929  0.00377 **
    ## age          0.04730    0.02052   2.305  0.02215 * 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.7547 on 213 degrees of freedom
    ## Multiple R-squared:  0.02433,    Adjusted R-squared:  0.01975 
    ## F-statistic: 5.311 on 1 and 213 DF,  p-value: 0.02215

``` r
# only working people
df_students = filter(df, student == 0)

mod_7 = lm(formula = SCA_alc_fre_beer_percep ~ age,
           data = df_students)

summary(mod_7)
```

    ## 
    ## Call:
    ## lm(formula = SCA_alc_fre_beer_percep ~ age, data = df_students)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.83770 -0.60340 -0.01004  0.55807  2.11980 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 2.266423   0.254500   8.905 5.42e-14 ***
    ## age         0.010387   0.006961   1.492    0.139    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.8362 on 90 degrees of freedom
    ## Multiple R-squared:  0.02414,    Adjusted R-squared:  0.0133 
    ## F-statistic: 2.226 on 1 and 90 DF,  p-value: 0.1392

We can see that for students, age has a positive effect on alcohol free
beer perception, while for working people, there is no significant
effect.

We can interpret from this that the effect of age on alcohol free beer
perception occurs only among students. Therefore, age has an effect only
because young students have a worse perception of alcohol free beer than
older students.

You can also see this effect visually (you do not need to know how to
make this in R):

![](P2SC_tutorial_analysis_files/figure-gfm/unnamed-chunk-29-1.png)<!-- -->
