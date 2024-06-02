Data Cleaning and Analysis Plan (team)
================
Roan Buma
2024-05-22

- [Introduction](#introduction)
- [1. Description of the Variables](#1-description-of-the-variables)
- [2. Preparing the Dataset in R](#2-preparing-the-dataset-in-r)
  - [2.1. Setting up R](#21-setting-up-r)
  - [2.2. Data Cleaning](#22-data-cleaning)
    - [2.2.2. Selecting, Renaming and Structuring your Variables
      (Columns)](#222-selecting-renaming-and-structuring-your-variables-columns)
    - [2.2.3. Filtering your Respondents
      (Rows)](#223-filtering-your-respondents-rows)
  - [2.3. Scale Construction](#23-scale-construction)
  - [2.4. Saving your prepared
    dataset](#24-saving-your-prepared-dataset)
- [3. Describe the Statistical analyses you will run next
  week](#3-describe-the-statistical-analyses-you-will-run-next-week)
  - [3.1. The descriptive/exploratory
    analysis](#31-the-descriptiveexploratory-analysis)
  - [3.2. Hypothesis testing](#32-hypothesis-testing)
  - [3.3. The additional analysis](#33-the-additional-analysis)
- [What to submit?](#what-to-submit)

# Introduction

The data have been collected, so you are entering the next phase of your
research: analyzing your data. For a good start, it is important to
create an analysis plan and prepare the raw dataset for use. This is
what you will be working on during the first week of period 6!

Creating an analysis plan is what we call a good research practice. It
is also very useful, because it means that you work in a structured way
and with a specific plan, which also means writing your R code will be
easier. The analysis plan consists of three parts:

- **Description of the variables**
- **Preparation of the data set in R**
- **Description of the statistical analyses**

For this weekly assignment, use the slides of the session of \[INSERT
DATE\] and the R instruction tutorials provided in this document. This
weekly assignment does not contain an indication of the number of words
to be used.

# 1. Description of the Variables

- Name your **research question** and the **two hypotheses**.

- Name the **dependent variable** and its **measurement level**
  (categorical: nominal or ordinal - scale: interval or ratio). Note: we
  consider a Likert scale as an interval measurement level.

- Name the **independent variables** and the corresponding **measurement
  level** per variable.

- Name the **control and demographic variables** and the corresponding
  **measurement level** per variable.

# 2. Preparing the Dataset in R

We will make a start with this in the work group session on 5-6. Data
Preparation in R consists of three parts:

- Setting up R
- Cleaning the dataset
- Scale construction

Perform these steps together, as each one of you should also be able to
do this individually! 100%. The result is the dataset that you use for
the analyses. Save your clean dataset in one secured location that all
team members (and only team members) have access to. Also make sure you
always have a copy of the original dataset as a backup.

Before starting, you have to export your data from Qualtrics. Go to
Qualtrics to the **Data & Analysis** tab. There is a button called
**Export & Import**. Click on it and click on **Export Data**. Select
**CSV**, check **Download all fields**, and select **Use numeric
values** (this makes sure Qualtrics exports numbers, rather than the
text options you provided in your survey, such as ‘fully agree’). You
should now have your dataset as CSV file in your downloads.

## 2.1. Setting up R

Open R, create a new R script (CTRL + SHIFT + N), and save it to a
folder somewhere on your computer. Move the CSV dataset from your
downloads to the same folder as the R code. Before starting, we need to
tell R that we are working from this folder. You can either do this with
`setwd()`, telling R the location of the folder, or by clicking on
**Session** (in the menu bar) \>\> **Set Working Directory** \>\> **To
Source File Location**.

Import the R packages that you will need at the beginning of your R
file, using `library()`. For data transformation with Base R, no
packages are needed. For data transformation with Tidyverse, you need to
load the `tidyverse` package. For *reliability analysis*, you need the
`psych` package. If you haven’t installed these packages yet, run
`install.packages("tidyverse")` and `install.packages("psych")` **once**
in the console.

- **Tutorial**: [R
  Packages](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/1_1_R_basics.md#packages)

Import the dataset in R:

- **Base R**: [Reading CSV
  Data](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/1_1_R_basics.md#importing-and-saving-data)
  with `read.csv()`
- **Tidyverse**: [Reading CSV
  Data](https://github.com/ccs-amsterdam/r-course-material/blob/master/tutorials/R-tidy-5-transformation.md#reading-data-read_csv)
  with `read_csv()`

A good step is to always first inspect your data. That way, you know you
imported it correctly. Use the functions `head()` (displays the first
few rows), `colnames()` (display all the column names) and `dim()`
(displays the dimensions, the number of rows and column in the data).

Delete the first two rows of the the dataset (see why
[here](https://www.qualtrics.com/support/survey-platform/data-and-analysis-module/data/download-data/understanding-your-dataset/#Basics)):

- **Base R**: see [Selecting
  rows](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/1_1_R_basics.md#selecting-rows)
- **Tidyverse**: use the function `slice()`. The code
  `slice(your_dataset_name, -(1:2))` removes rows 1 and 2..

## 2.2. Data Cleaning

### 2.2.2. Selecting, Renaming and Structuring your Variables (Columns)

Delete most of the variables that Qualtrics has created itself (e.g.,
i.p. address, location), because they contain privacy-sensitive
information (see
[here](https://www.qualtrics.com/support/survey-platform/data-and-analysis-module/data/download-data/understanding-your-dataset/#RespondentInformation)).
Keep the variables `Progress`, `Duration`, and `DistributionChannel`, as
you will use these. TIP: You can quickly see all the column names by
using `colnames()`.

- **Base R**: see [Selecting variables of
  interest](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/4_1_Data_management_1.md#select-variables-of-interest)
- **Tidyverse**: see [Selecting
  Columns](https://github.com/ccs-amsterdam/r-course-material/blob/master/tutorials/R-tidy-5-transformation.md#selecting-certain-columns)
  with `select()`

Check the data in R and make sure that all variables have a clear name.
Ideally, you already [assigned clear variable names in
Qualtrics](https://www.qualtrics.com/support/survey-platform/survey-module/editing-questions/formatting-questions/#QuestionNumbers),
making this process quicker. You can check column names with
`colnames()`.

*TIP: you can [export a Qualtrics survey as Word
document](https://www.qualtrics.com/support/survey-platform/survey-module/survey-tools/import-and-export-surveys/#ExportingaSurveyasaWordDocument)
to see the variable names, questions and numeric values corresponding to
answer categories.*

- **Tidyverse**: see [renaming
  Columns](https://github.com/ccs-amsterdam/r-course-material/blob/master/tutorials/R-tidy-5-transformation.md#selecting-certain-columns)
  with `rename()`

Make sure **numerical variables** are treated as **numbers** and make
sure **categorical variables** are treated as **factors**, and are coded
in the correct order. This is necessary because otherwise R will treat
these as text variables, or even worse, as numbers.

- **Base R**: see [data
  types](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/1_1_R_basics.md#data-types)
  and [variable types:
  factors](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/4_2_Data_management_2.md#factor)
  – Functions: `as.numeric()`, `factor()`
- **Tidyverse**: see [adding or transforming
  variables](https://github.com/ccs-amsterdam/r-course-material/blob/master/tutorials/R-tidy-5-transformation.md#adding-or-transforming-variables-with-mutate)
  with `mutate()` and [changing data
  types](https://github.com/ccs-amsterdam/r-course-material/blob/master/tutorials/R-tidy-5-transformation.md#subsetting-with-filter)
  with `as.numeric()` and `factor()`.

You should know have a dataset where only relevant columns are left,
they have logical names, and their class (numeric or factor) has been
changed according to the type of data. There are several ways to inspect
your work.

First, you can check an individual column with the `class()` function,
which displays the class of an object. This function, applied to a
**column**, will return whether your column is numeric, factor or
character. By running `sapply(your_dataset, class)`, the `class()`
function is applied to every column in the dataset.

Second, you can check this with the `str()` function, which displays the
structure of an object. This function, applied to a **dataframe**, will
display (1) all the variable names (the columns of your dataset), (2)
all the variable classes: **num** (numeric variables), **Factor**
(categorical variables), or **chr** (character), and (3) all the levels
for Factor variables.

Inspect your data with these functions. If you notice errors, fix them
in your code.

### 2.2.3. Filtering your Respondents (Rows)

You will now filter the respondents, which are the rows in your dataset.
First, inspect how many respondents the data has. You can see this in
the Environment panel, or by using the `dim()` function.

Delete all your test data (pre-launch data), by filtering out rows where
DistributionChannel equals “preview”. Actual respondents have the label
“anonymous” here, if they filled out the survey through an anonymous
link. Remove all participants who did not complete the survey, using the
Progress and Duration variables created by Qualtrics. Also remove
respondents that did not consent to participating in the survey (you
should have a question that measures this), as well as respondents that
did not meet criteria for your sample (i.e. respondents you directed
towards end of survey).

- **Base R**: see [selecting
  subsets](https://github.com/ccs-amsterdam/r-course-material/blob/master/tutorials/R-tidy-5-transformation.md#subsetting-with-filter)
- **Tidyverse**: see
  [subsetting](https://github.com/ccs-amsterdam/r-course-material/blob/master/tutorials/R-tidy-5-transformation.md#subsetting-with-filter)
  with `filter()`

You should know have a dataset where only relevant respondents are left,
based on your filtering criteria (progress, duration, consent,
distribution channel, and maybe additional variables). Inspect your data
with several methods, such as `dim()`, `str()` and `head()`.

## 2.3. Scale Construction

You will now create scales. Check the items of the scale. Make sure that
for all items, a higher score on this item indicates a higher score on
the variable.

Reversed formulated items must be recoded. Tip: name all your recoded
variables ending with `_recoded` or `_rec`, so you remember these are
the recoded versions (e.g., `populism_item1_recoded`)

- **Base R**: [recoding
  items](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/4_1_Data_management_1.md#recode-items)
- **Tidyverse**: [recoding
  items](https://canvas.vu.nl/courses/75803/files?preview=7759527) with
  `recode()` and `mutate()`.

For all items of your scale, if a higher score on this item indicates a
higher score on the scale, conduct a reliability analysis with
`psych::alpha()`. Make sure that you use the recoded items that you
created in the previous step, in case of the original reversed phrased
items.

*TIP: If you do not do this, you will probably receive a warning in the
output that some items were negatively correlated. Running the function
with the option* `check.keys=TRUE` *recodes these items for you, but
only in the reliability analysis. This option can be useful to identify
items that need to be recoded.*

Begin reliability analysis with all items of the scale. Examine the
multiple outputs provided by the `psych::alpha()` function.

*TIP: the* `psych::alpha()` *function outputs many tables, which can be
overwhelming to look at. By typing* `psych::alpha()$total` *you only
display the total alpha, and by typing* `psych::alpha()$alpha.drop` *you
only display the alpha if items are dropped table.*

Examine which combination of items makes the scale the most reliable
(i.e. has the highest Cronbach’s alpha). Only remove items from the
scale if this really results in an improvement of the Cronbach’s alpha.

- **Psych**: see [reliability
  analysis](create%20a%20new%20data%20set%20that%20only%20contains%20the%20recoded%20items)
  with `psych::alpha()` (*since Tidyverse also has a function called
  `alpha()`, we need to tell R to use the function from the psych
  package with `psych::alpha()`*)

Once you have determined which items to include in the scale, perform
the scale construction based on the average score on the items. Here
too: make sure that you use the recoded items in case of reversed
phrased items. Give this new variable a meaningful name.

- **Base R**: see [create composite
  scores](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/4_1_Data_management_1.md#create-composite-scores)
- **Tidyverse**: see [create composite scores]() with `mutate()`,
  `rowwise()` and `mean()`. **NEED TUTORIAL**

Describe in your analysis plan all the steps you performed for the scale
construction. Mention the standardized Cronbach’s alpha (std.alpha), how
you constructed the scale (based on the mean of all items), the mean
score and the corresponding standard deviation (see slides of the
session of 9-6 for an example). The statistics you report here are also
the statistics you use to finalize your Methodology chapter.

- **Base R**: see [mean and standard
  deviation](https://github.com/maugavilla/well_hello_stats/blob/main/tutorials/5_1_descriptive_statistics.md#descriptive-statistics)
- **Tidyverse**: see [mean and standard
  deviation](https://github.com/ccs-amsterdam/r-course-material/blob/master/tutorials/R-tidy-5b-groupby.md#r-tidyverse-data-summarization-with-group_by-summarize-and-mutate)

Perform these steps of scale construction for all scale variables within
your research. Tip: name all your scale variables starting with
“scale\_”, so they are easier to find (e.g., scale_populism)

## 2.4. Saving your prepared dataset

Save your dataset with `saveRDS()`. This will enable us teachers to open
it directly in R, while keeping the data structure intact (e.g., factors
stay factors). Give the file a logical name, such as
`prepared_dataset_group_[your group number].rds`. The dataset will be
saved in the working directory.

# 3. Describe the Statistical analyses you will run next week

The statistical analyses consist of three parts:

- Exploratory analysis
- Hypothesis testing
- Additional analysis

NOTE: In this analysis plan you only describe how you will conduct these
statistical analyses. The actual execution of the analyses is planned
for next week, after you received the feedback from your teacher on the
plan.

## 3.1. The descriptive/exploratory analysis

You will run **descriptive tests** to get an overview of the
characteristics of your participants (and to use in the Method Chapter):
age, gender and other demographics that need to be reported in the first
paragraph of the method chapter. Name the **variables** you will use
here.

For the **exploratory analysis**, you create a correlation matrix with
all the variables that you measured in your research. Name the
**variables** you will include in your analyses. These are all the
variables you measured, including control and demographic variables
(unless they are nominal).

## 3.2. Hypothesis testing

For each hypothesis, describe **which statistical analysis** you will
perform exactly to test this hypothesis, what the **dependent variable**
is, and what the **independent variable** is.

Keep in mind that during the analysis you might need to take any control
variables in account based on your correlation matrix.

## 3.3. The additional analysis

After testing the hypotheses, you perform **one exploratory analysis** –
an additional analysis for in-depth analysis.

You use the control and demographic variables to do so. What have you
measured that is interesting to investigate further?

Could any of these variables be a predictor of the dependent variable,
for example? Or are outcomes different for male than for female
participants?

What you ultimately choose as an additional analysis depends on the
exploratory analysis and the hypothesis testing, but it is good to
consider your possibilities now.

# What to submit?

For this assignment, you submit three separate files:

- The **analysis plan** (**.docx**) - consisting of (1) a description of
  the variables, (2) preparation of the dataset and (3) statistical
  analyses

- The **cleaned dataset** (**.rds**) - including only the relevant
  variables, all the constructed scales, and correct formatting of
  variables (either factor or numeric)

- The **R code** (**.R**) - containing all the steps you have performed
  in R, including relevant comments
