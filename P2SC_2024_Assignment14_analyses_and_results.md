Analyses and Results (team)
================
Roan Buma
2024-05-22

- [Introduction](#introduction)
- [Assignment](#assignment)
  - [1. Title page](#1-title-page)
  - [2. Chapter 4: Results (500 to 750
    words)](#2-chapter-4-results-500-to-750-words)
    - [Subheading 1: Exploratory
      analysis](#subheading-1-exploratory-analysis)
    - [Subheading 2: Hypothesis
      testing](#subheading-2-hypothesis-testing)
    - [Subheading 3: Additional
      Analysis](#subheading-3-additional-analysis)
- [Writing Style](#writing-style)
- [What to Submit?](#what-to-submit)

# Introduction

Your clean dataset is ready and in your analysis plan you describe
exactly how you will perform the statistical analyses. This week you
will actually perform these analyses and report the results in the
Results chapter of your scientific report.

In the Results chapter you make the same distinction between statistical
analyses: (1) the exploratory analysis, (2) hypothesis testing and (3)
an additional analysis. For this weekly assignment, use the slides from
the workgroups and the R tutorials.

The Results chapter, like the Methodology chapter, is detailed and
highly standardized. Thoroughness, completeness and accuracy are very
important. An indication of 500 to 750 words applies to the Results,
excluding title page, tables and any appendices. Note: In the Results,
you focus only on the results of the statistical analyses - not yet on
the interpretation thereof.

Of course, the Results chapter is entirely based on the results of your
analyses in R. Report all steps that you perform in your R code.
Furthermore, indicate with comments in your code what step you are
taking (for example `# testing hypothesis 1`) You perform the analyses
as a team. Really do this together, because each of you must also be
able to do this individually! 100%.

The Results chapter is part of the second graded assignment of
**Assignment 2: Scientific report – Results, conclusion and discussion
(30% of the final grade)**. You will receive feedback on this weekly
assignment from your teacher. Adjust the Results chapter for Assignment
2 based on this feedback. **The deadline for this graded assignment is
Friday June 30, but it is highly recommended to process the feedback as
soon as possible after receiving the feedback.**

# Assignment

Write the Results chapter of your scientific report. This chapter should
contain the following elements:

## 1. Title page

- Title and subtitle of your report.
- Names and student numbers of your team members.
- Course title, assignment title and the name of your teacher
- Date
- The number of words in the report (excluding front page, table of
  contents, references and any attachments)

## 2. Chapter 4: Results (500 to 750 words)

### Subheading 1: Exploratory analysis

Create a **correlation matrix**. Present the **correlation matrix** and
name the significant correlations. It is common practice to *include all
relevant variables in the dataset in the correlation analysis*. For
Project 2, these are all the variables you included, including control
and demographic variables (unless these are nominal measures). In the
result of analysis, you immediately see whether there are relationships
between variables and thus which relationships could be interesting to
investigate further as additional analysis. Present the results of the
correlation analysis in a correlation table and name the significant
correlations in accordance with APA.

- **Psych**: see [creating a correlation
  matrix](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/6_1_correlation.md#pearson-correlation)
  with `psych::corr.test()`. Tip: you can use pipes (`%>%`) and
  `select()`

``` r
# eerst altijd: working directory, packages en data

# wd voorbeeld (hoef ik niet te runnen)
# setwd("C:/Users/roanb/Documents")

# packages
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
# dataset laden (RDS bestand, dit is wat jullie opgeslagen hebben na data prep)
df = readRDS("prepared_dataset_test.rds")
```

``` r
# in het geval van mijn dataset wil ik de variabelen:
  # Scale buy intent
  # scale alcohol free beer perception
  # scale gender stereotypes
  # age
  # gender
  # occupation

# het probleem is dat gender en occupation categorisch zijn, hier kan je geen correlatie op doen
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

``` r
# we moeten ze dus eerst hercoderen
# bij gender maak ik een dummy woman (1) vs. man (0). De rest wordt NA
# bij occupation maak ik een dummy student (1) vs. werkend (0). De rest wordt NA.
df = mutate(
  df,
  woman = case_when(gender == "female" ~ 1,
                    gender == "male" ~ 0,
                    TRUE ~ NA), # NA: other / non-binary
  student = case_when(occupation == "student" ~ 1,
                      occupation == "full-time" ~ 0,
                      occupation == "part-time" ~ 0,
                      TRUE ~ NA)# NA: not working and retired
  ) 

# kijk naar de nieuwe variabelen, deze zijn nu dummies.
# Daar kunnen we een correlatie op doen:
table(df$woman)
```

    ## 
    ##   0   1 
    ## 160 144

``` r
table(df$student)
```

    ## 
    ##   0   1 
    ##  92 215

``` r
# we selecteren eerst de variabelen die we nodig hebben met select, ik noem die nieuwe data df_cor
df_cor = select(
  df,
  SCA_buy_intent,
  SCA_alc_fre_beer_percep,
  SCA_gend_stereo,
  age,
  woman,
  student)

# correlation test
psych::corr.test(df_cor)
```

    ## Call:psych::corr.test(x = df_cor)
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

``` r
# voor een correcte APA tabel moeten we nog de MEAN, SD en N weten van iedere variabele
# mean doe je met mean()
# sd doe je met sd()
# N doe je met sum(!is.na(df$SCA_buy_intent))
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
# bij woman en student betekent de mean en sd natuurlijk niks, omdat het dummies zijn
# wel de N berekenen

sum(!is.na(df$woman))
```

    ## [1] 304

``` r
sum(!is.na(df$student))
```

    ## [1] 307

### Subheading 2: Hypothesis testing

Present the results of the **regression analysis** with which you test
the first and second hypothesis. Repeat the first hypothesis, indicate
how it was tested and present the result per hypothesis in accordance
with APA. End the paragraph by explaining what this outcome means for
the hypothesis (i.e. whether it is supported by the data or not). Repeat
this in a new paragraph for the second hypothesis.

- [Simple linear regression in R
  tutorial](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/9_1_linear_regression.md#linear-regression)
- [Multiple linear regression in R
  tutorial](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/9_1_linear_regression.md#multiple-linear-regression)

``` r
# simplere lineaire regressie voorbeeld

# in welke mate voorspelt alcohol free beer perceptie buying intent?
mod_1 = lm(SCA_buy_intent ~ SCA_alc_fre_beer_percep, data = df)
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

``` r
# dit is niet significant

# in welke mate voorspelt leeftijd perceptie van alcoholvrij bier?
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

``` r
# leeftijd heeft een positief effect op perceptie van alcoholvrij bier
```

### Subheading 3: Additional Analysis

Present the results of an additional analysis. Based on the exploratory
analysis and the hypothesis testing, choose one additional analysis that
you will perform. For example, this could be a main effect of a control
or demographic variable on the dependent variable, or a test of a
hypothesis for male versus female participants. Present the result in
accordance with APA.

``` r
# voorbeeld met woman als IV en alcohol free beer pereptie als DV
mod_3 = lm(SCA_alc_fre_beer_percep ~ woman, data = df)
summary(mod_3)
```

    ## 
    ## Call:
    ## lm(formula = SCA_alc_fre_beer_percep ~ woman, data = df)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.49306 -0.65625  0.00694  0.51042  2.17708 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  2.32292    0.06387  36.372   <2e-16 ***
    ## woman        0.17014    0.09279   1.834   0.0677 .  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.8078 on 302 degrees of freedom
    ##   (11 observations deleted due to missingness)
    ## Multiple R-squared:  0.01101,    Adjusted R-squared:  0.007734 
    ## F-statistic: 3.362 on 1 and 302 DF,  p-value: 0.06771

``` r
# geen significant verschil (hoger dan 0.05)
```

# Writing Style

The writing style of the Result chapter needs to be scientific and
correct. Follow the [APA guidelines for the
layout](https://canvas.vu.nl/courses/75803/pages/apa-style-handouts-+-instruction)
(including results, tables, figures, etc.) and avoid any form of
plagiarism. You can use [this APA
hand-out](https://canvas.vu.nl/courses/75803/files/7270974?wrap=1) to
see how you need to report numbers and symbols.

# What to Submit?

For this assignment you will submit three separate files:

- The **Results chapter** (**.docx**) - consisting of (1) the
  exploratory analysis, (2) the hypothesis test and (3) the additional
  analysis
- The **R dataset** (**.rds**) that you used for the analyses
- The **R code** (**.R**) containing all the steps you have performed in
  R
