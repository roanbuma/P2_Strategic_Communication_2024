Tutorial Data Cleaning and Analysis Plan (team)
================
Roan Buma
2024-05-22

- [Setup R](#setup-r)
  - [Working Directory](#working-directory)
  - [Libraries](#libraries)
  - [Import Data](#import-data)
- [Data Cleaning](#data-cleaning)
  - [Selecting, Renaming and Structuring Variables
    (Columns)](#selecting-renaming-and-structuring-variables-columns)
    - [STAP 1: Selecteren van variabelen die we willen
      houden.](#stap-1-selecteren-van-variabelen-die-we-willen-houden)
    - [STAP 2: Renaming van variabelen die we geselecteerd
      hebben.](#stap-2-renaming-van-variabelen-die-we-geselecteerd-hebben)
    - [STAP 3: De juiste structuur aanbrengen aan variabelen (numeriek /
      categorisch?)](#stap-3-de-juiste-structuur-aanbrengen-aan-variabelen-numeriek--categorisch)
  - [Filteren van respondenten
    (rijen)](#filteren-van-respondenten-rijen)
  - [Scale Construction](#scale-construction)
- [Saving Dataset](#saving-dataset)

# Setup R

## Working Directory

``` r
# WINDOWS
# setwd("C:/Users/je_naam/...") >> VUL DIT IN VOOR JE EIGEN COMPUTER

# MAC
# setwd("/Users/je_naam/...") >> VUL DIT IN VOOR JE EIGEN COMPUTER

# setwd() betekent "set working directory", hiermee zeg je vanuit welke map je op je computer werkt.
# In deze map moeten staan: je data (.csv bestand) en je code (.R bestand)

# voorbeeld voor mijn computer: (windows)
setwd("C:/Users/roanb/Dropbox/VU/2023-2024 - VU Werk Communication Science/2024-2 P4 Project 2 Strategic Communication\P2SC R Tutorial for Teachers")
```

## Libraries

``` r
# als je ze nog niet hebt, run je dit eenmalig in de console:
# install.packages("tidyverse")
# install.packages("psych")

# Ik laad de volgende libraries, verder hebben we niks nodig
library(tidyverse)
library(psych)
```

## Import Data

Ik importeer de data met read_csv() en sla dit op door een informatieve
naam te geven (df_bier)

``` r
df_bier = read_csv("P2SC_heineken_dataset_2023.csv")
```

Inspecteer je data met functies zoals `head()`, `colnames()` en `dim()`.
Wat zie je?

``` r
head(df_bier) # laat eerste 6 rijen van de data zien
```

    ## # A tibble: 6 × 61
    ##   StartDate    EndDate Status IPAddress Progress Duration (in seconds…¹ Finished
    ##   <chr>        <chr>   <chr>  <chr>     <chr>    <chr>                  <chr>   
    ## 1 "Start Date" "End D… "Resp… "IP Addr… "Progre… "Duration (in seconds… "Finish…
    ## 2 "{\"ImportI… "{\"Im… "{\"I… "{\"Impo… "{\"Imp… "{\"ImportId\":\"dura… "{\"Imp…
    ## 3 "2023-05-12… "2023-… "1"     <NA>     "100"    "132"                  "1"     
    ## 4 "2023-05-16… "2023-… "1"     <NA>     "100"    "15"                   "1"     
    ## 5 "2023-05-16… "2023-… "1"     <NA>     "100"    "15"                   "1"     
    ## 6 "2023-05-16… "2023-… "1"     <NA>     "100"    "12"                   "1"     
    ## # ℹ abbreviated name: ¹​`Duration (in seconds)`
    ## # ℹ 54 more variables: RecordedDate <chr>, ResponseId <chr>,
    ## #   RecipientLastName <chr>, RecipientFirstName <chr>, RecipientEmail <chr>,
    ## #   ExternalReference <chr>, LocationLatitude <chr>, LocationLongitude <chr>,
    ## #   DistributionChannel <chr>, UserLanguage <chr>, Q1.1 <chr>, Q2.1 <chr>,
    ## #   Q2.2 <chr>, Q2.3 <chr>, Q2.4 <chr>, Q2.5 <chr>, Q2.6 <chr>,
    ## #   Q2.6_5_TEXT <chr>, Q3.1_1 <chr>, Q3.1_2 <chr>, Q3.1_3 <chr>, …

``` r
colnames(df_bier) #inspecteer namen kolommen 
```

    ##  [1] "StartDate"             "EndDate"               "Status"               
    ##  [4] "IPAddress"             "Progress"              "Duration (in seconds)"
    ##  [7] "Finished"              "RecordedDate"          "ResponseId"           
    ## [10] "RecipientLastName"     "RecipientFirstName"    "RecipientEmail"       
    ## [13] "ExternalReference"     "LocationLatitude"      "LocationLongitude"    
    ## [16] "DistributionChannel"   "UserLanguage"          "Q1.1"                 
    ## [19] "Q2.1"                  "Q2.2"                  "Q2.3"                 
    ## [22] "Q2.4"                  "Q2.5"                  "Q2.6"                 
    ## [25] "Q2.6_5_TEXT"           "Q3.1_1"                "Q3.1_2"               
    ## [28] "Q3.1_3"                "Q3.1_4"                "Q3.1_5"               
    ## [31] "Q3.1_6"                "Q3.1_7"                "Q3.1_8"               
    ## [34] "Q3.1_9"                "Q3.1_10"               "Q3.1_11"              
    ## [37] "Q3.1_12"               "Q3.1_13"               "Q4.1_1"               
    ## [40] "Q4.1_2"                "Q4.1_3"                "Q4.1_4"               
    ## [43] "Q4.1_5"                "Q4.1_6"                "Q4.1_7"               
    ## [46] "Q5.1_1"                "Q5.1_2"                "Q5.1_3"               
    ## [49] "Q5.1_4"                "Q5.1_5"                "Q5.1_6"               
    ## [52] "Q6.2"                  "Q6.3"                  "Q6.4_First Click"     
    ## [55] "Q6.4_Last Click"       "Q6.4_Page Submit"      "Q6.4_Click Count"     
    ## [58] "Q7.1_1"                "Q7.1_2"                "Q7.1_3"               
    ## [61] "Q7.1_4"

``` r
dim(df_bier) # dimensies van de data (rijen x kolommen)
```

    ## [1] 651  61

Verwijder de eerste twee rijen en sla op wat eruit (door “df_bier =” toe
tevoegen, sla je de uitkomst op)

``` r
df_bier = slice(df_bier, -(1:2))
```

# Data Cleaning

## Selecting, Renaming and Structuring Variables (Columns)

### STAP 1: Selecteren van variabelen die we willen houden.

Ik specificeer eerst de naam van de dataset (df_bier in mijn geval).
Daarna welke variabelen ik wil selecteren (progress,
`Duration (in seconds)`, distributionchannel, en alle variabelen die ik
wil gebruiken). Een handige trick is het gebruik van “:”, hiermee zeg je
selecteer van variabele x t/m y met x:y.

``` r
df_bier = select(df_bier, 
                 Progress,
                 `Duration (in seconds)`,
                 DistributionChannel,
                 Q1.1:Q5.1_6 # een handige trick is het gebruik van ":", hiermee zeg je Q1.1 t/m Q5.1_6
                 )
```

Controleer wat je geselecteerd hebt.

``` r
# check names of variables
colnames(df_bier)
```

    ##  [1] "Progress"              "Duration (in seconds)" "DistributionChannel"  
    ##  [4] "Q1.1"                  "Q2.1"                  "Q2.2"                 
    ##  [7] "Q2.3"                  "Q2.4"                  "Q2.5"                 
    ## [10] "Q2.6"                  "Q2.6_5_TEXT"           "Q3.1_1"               
    ## [13] "Q3.1_2"                "Q3.1_3"                "Q3.1_4"               
    ## [16] "Q3.1_5"                "Q3.1_6"                "Q3.1_7"               
    ## [19] "Q3.1_8"                "Q3.1_9"                "Q3.1_10"              
    ## [22] "Q3.1_11"               "Q3.1_12"               "Q3.1_13"              
    ## [25] "Q4.1_1"                "Q4.1_2"                "Q4.1_3"               
    ## [28] "Q4.1_4"                "Q4.1_5"                "Q4.1_6"               
    ## [31] "Q4.1_7"                "Q5.1_1"                "Q5.1_2"               
    ## [34] "Q5.1_3"                "Q5.1_4"                "Q5.1_5"               
    ## [37] "Q5.1_6"

### STAP 2: Renaming van variabelen die we geselecteerd hebben.

We doen dit omdat de oorspronkelijke namen (Q1 etc) niet informatief
zijn.

``` r
# rename with NEW = OLD formula
df_bier = rename(
  df_bier,
  duration = `Duration (in seconds)`,
  consent = Q1.1,
  age = Q2.1,
  live_NL_min_10_years = Q2.2,
  consume_beer = Q2.3,
  know_heineken = Q2.4,
  occupation = Q2.5,
  gender = Q2.6,
  gender_other_text = Q2.6_5_TEXT,
  buy_intention_1 = Q3.1_1,
  buy_intention_2 = Q3.1_2,
  buy_intention_3 = Q3.1_3,
  buy_intention_4 = Q3.1_4,
  buy_intention_5 = Q3.1_5,
  buy_intention_6 = Q3.1_6,
  buy_intention_7 = Q3.1_7,
  buy_intention_8 = Q3.1_8,
  buy_intention_9 = Q3.1_9,
  buy_intention_10 = Q3.1_10,
  buy_intention_11 = Q3.1_11,
  buy_intention_12 = Q3.1_12,
  buy_intention_13 = Q3.1_13,
  alcohol_free_beer_perception_1 = Q4.1_1,
  alcohol_free_beer_perception_2 = Q4.1_2,
  alcohol_free_beer_perception_3 = Q4.1_3,
  alcohol_free_beer_perception_4 = Q4.1_4,
  alcohol_free_beer_perception_5 = Q4.1_5,
  alcohol_free_beer_perception_6 = Q4.1_6,
  alcohol_free_beer_perception_7 = Q4.1_7,
  gender_stereotypes_perception_1 = Q5.1_1,
  gender_stereotypes_perception_2 = Q5.1_2,
  gender_stereotypes_perception_3 = Q5.1_3,
  gender_stereotypes_perception_4 = Q5.1_4,
  gender_stereotypes_perception_5 = Q5.1_5,
  gender_stereotypes_perception_6 = Q5.1_6)
```

Controleer weer wat je gedaan hebt met colnames()

``` r
# check names
colnames(df_bier)
```

    ##  [1] "Progress"                        "duration"                       
    ##  [3] "DistributionChannel"             "consent"                        
    ##  [5] "age"                             "live_NL_min_10_years"           
    ##  [7] "consume_beer"                    "know_heineken"                  
    ##  [9] "occupation"                      "gender"                         
    ## [11] "gender_other_text"               "buy_intention_1"                
    ## [13] "buy_intention_2"                 "buy_intention_3"                
    ## [15] "buy_intention_4"                 "buy_intention_5"                
    ## [17] "buy_intention_6"                 "buy_intention_7"                
    ## [19] "buy_intention_8"                 "buy_intention_9"                
    ## [21] "buy_intention_10"                "buy_intention_11"               
    ## [23] "buy_intention_12"                "buy_intention_13"               
    ## [25] "alcohol_free_beer_perception_1"  "alcohol_free_beer_perception_2" 
    ## [27] "alcohol_free_beer_perception_3"  "alcohol_free_beer_perception_4" 
    ## [29] "alcohol_free_beer_perception_5"  "alcohol_free_beer_perception_6" 
    ## [31] "alcohol_free_beer_perception_7"  "gender_stereotypes_perception_1"
    ## [33] "gender_stereotypes_perception_2" "gender_stereotypes_perception_3"
    ## [35] "gender_stereotypes_perception_4" "gender_stereotypes_perception_5"
    ## [37] "gender_stereotypes_perception_6"

### STAP 3: De juiste structuur aanbrengen aan variabelen (numeriek / categorisch?)

We willen dat R weet of een variabele numeriek is (bijvb 1,2,3,4). Dit
heet in R `numeric` en doe je met `as.numeric()`. We willen dat R weet
of een variabele categorisch is (bijvb man/vrouw). Dit heet in r
`factor` en doe je met `factor()`. Met `mutate()` verander je variabelen
(je kunt er ook nieuwe variabelen mee maken).

TIP: download je vragenlijst uit Qualtrics as Word document, dan zie je
alle numerieke waarden en welke labels daarbij horen.

``` r
df_bier = mutate( # mutate
    df_bier, # eerst weer de dataset intypen
    # qualtrics outputs:
    Progress = as.numeric(Progress), # progress moet numeriek zijn
    duration = as.numeric(duration), # duration moet ook numeriek zijn
    DistributionChannel = factor(DistributionChannel), # distribution channel factor (preview/anonymous)
    # eigen vragen:
    consent = factor(consent, # zoek op welke labels bij welke levels horen in Qualtrics
                     levels = c(1,2),
                     labels = c("yes","no")), 
    age = as.numeric(age),
    live_NL_min_10_years = factor(live_NL_min_10_years,
                                  levels = c(1,2),
                                  labels = c("yes","no")),
    consume_beer = factor(consume_beer,
                          levels = c(1,2),
                          labels = c("yes","no")),
    know_heineken = factor(know_heineken,
                           levels = c(1,2),
                           labels = c("yes","no")),
    occupation = factor(occupation,
                        levels=c(1,2,3,4,5),
                        labels=c("student","full-time","not working","retired","part-time")),
    gender = factor(gender,
                    levels = c(1,2,3,4,5),
                    labels = c("male","female","non-binary / third gender",
                               "prefer not to say","other")),
    # buy intention
    buy_intention_1 = as.numeric(buy_intention_1),
    buy_intention_2 = as.numeric(buy_intention_2),
    buy_intention_3 = as.numeric(buy_intention_3),
    buy_intention_4 = as.numeric(buy_intention_4),
    buy_intention_5 = as.numeric(buy_intention_5),
    buy_intention_6 = as.numeric(buy_intention_6),
    buy_intention_7 = as.numeric(buy_intention_7),
    buy_intention_8 = as.numeric(buy_intention_8),
    buy_intention_9 = as.numeric(buy_intention_9),
    buy_intention_10 = as.numeric(buy_intention_10),
    buy_intention_11 = as.numeric(buy_intention_11),
    buy_intention_12 = as.numeric(buy_intention_12),
    buy_intention_13 = as.numeric(buy_intention_13),
    # alcohol free beer perception
    alcohol_free_beer_perception_1 = as.numeric(alcohol_free_beer_perception_1),
    alcohol_free_beer_perception_2 = as.numeric(alcohol_free_beer_perception_2),
    alcohol_free_beer_perception_3 = as.numeric(alcohol_free_beer_perception_3),
    alcohol_free_beer_perception_4 = as.numeric(alcohol_free_beer_perception_4),
    alcohol_free_beer_perception_5 = as.numeric(alcohol_free_beer_perception_5),
    alcohol_free_beer_perception_6 = as.numeric(alcohol_free_beer_perception_6),
    alcohol_free_beer_perception_7 = as.numeric(alcohol_free_beer_perception_7),
    # gender stereotypes
    gender_stereotypes_perception_1 = as.numeric(gender_stereotypes_perception_1),
    gender_stereotypes_perception_2 = as.numeric(gender_stereotypes_perception_2),
    gender_stereotypes_perception_3 = as.numeric(gender_stereotypes_perception_3),
    gender_stereotypes_perception_4 = as.numeric(gender_stereotypes_perception_4),
    gender_stereotypes_perception_5 = as.numeric(gender_stereotypes_perception_5),
    gender_stereotypes_perception_6 = as.numeric(gender_stereotypes_perception_6)
  )
```

Inspecteer je data. Dit kan je doen met `class()` door een specifieke
variabele te selecteren. Je kunt ook `sapply(df_bier, class)` typen, wat
`class()` toepast op alle kolommen. Met `str()` (structure) en `dim()`
(dimensions) kun je ook weer je data inspecteren.

``` r
# inspect two random columns class
class(df_bier$consent)
```

    ## [1] "factor"

``` r
class(df_bier$alcohol_free_beer_perception_3)
```

    ## [1] "numeric"

``` r
# inspect all columns class
sapply(df_bier, class)
```

    ##                        Progress                        duration 
    ##                       "numeric"                       "numeric" 
    ##             DistributionChannel                         consent 
    ##                        "factor"                        "factor" 
    ##                             age            live_NL_min_10_years 
    ##                       "numeric"                        "factor" 
    ##                    consume_beer                   know_heineken 
    ##                        "factor"                        "factor" 
    ##                      occupation                          gender 
    ##                        "factor"                        "factor" 
    ##               gender_other_text                 buy_intention_1 
    ##                     "character"                       "numeric" 
    ##                 buy_intention_2                 buy_intention_3 
    ##                       "numeric"                       "numeric" 
    ##                 buy_intention_4                 buy_intention_5 
    ##                       "numeric"                       "numeric" 
    ##                 buy_intention_6                 buy_intention_7 
    ##                       "numeric"                       "numeric" 
    ##                 buy_intention_8                 buy_intention_9 
    ##                       "numeric"                       "numeric" 
    ##                buy_intention_10                buy_intention_11 
    ##                       "numeric"                       "numeric" 
    ##                buy_intention_12                buy_intention_13 
    ##                       "numeric"                       "numeric" 
    ##  alcohol_free_beer_perception_1  alcohol_free_beer_perception_2 
    ##                       "numeric"                       "numeric" 
    ##  alcohol_free_beer_perception_3  alcohol_free_beer_perception_4 
    ##                       "numeric"                       "numeric" 
    ##  alcohol_free_beer_perception_5  alcohol_free_beer_perception_6 
    ##                       "numeric"                       "numeric" 
    ##  alcohol_free_beer_perception_7 gender_stereotypes_perception_1 
    ##                       "numeric"                       "numeric" 
    ## gender_stereotypes_perception_2 gender_stereotypes_perception_3 
    ##                       "numeric"                       "numeric" 
    ## gender_stereotypes_perception_4 gender_stereotypes_perception_5 
    ##                       "numeric"                       "numeric" 
    ## gender_stereotypes_perception_6 
    ##                       "numeric"

``` r
# inspect structure of dataframe
str(df_bier)
```

    ## tibble [649 × 37] (S3: tbl_df/tbl/data.frame)
    ##  $ Progress                       : num [1:649] 100 100 100 100 100 100 100 100 100 100 ...
    ##  $ duration                       : num [1:649] 132 15 15 12 13 348 54 75 309 417 ...
    ##  $ DistributionChannel            : Factor w/ 3 levels "anonymous","preview",..: 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ consent                        : Factor w/ 2 levels "yes","no": 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ age                            : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ live_NL_min_10_years           : Factor w/ 2 levels "yes","no": 1 NA NA NA NA NA 2 2 1 1 ...
    ##  $ consume_beer                   : Factor w/ 2 levels "yes","no": NA NA NA NA NA NA NA NA NA NA ...
    ##  $ know_heineken                  : Factor w/ 2 levels "yes","no": 1 NA NA NA NA NA NA NA 1 1 ...
    ##  $ occupation                     : Factor w/ 5 levels "student","full-time",..: 1 NA NA NA NA NA NA NA 1 1 ...
    ##  $ gender                         : Factor w/ 5 levels "male","female",..: 2 NA NA NA NA NA NA NA 1 2 ...
    ##  $ gender_other_text              : chr [1:649] NA NA NA NA ...
    ##  $ buy_intention_1                : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_2                : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_3                : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_4                : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_5                : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_6                : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_7                : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_8                : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_9                : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_10               : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_11               : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_12               : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ buy_intention_13               : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ alcohol_free_beer_perception_1 : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ alcohol_free_beer_perception_2 : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ alcohol_free_beer_perception_3 : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ alcohol_free_beer_perception_4 : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ alcohol_free_beer_perception_5 : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ alcohol_free_beer_perception_6 : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ alcohol_free_beer_perception_7 : num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ gender_stereotypes_perception_1: num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ gender_stereotypes_perception_2: num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ gender_stereotypes_perception_3: num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ gender_stereotypes_perception_4: num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ gender_stereotypes_perception_5: num [1:649] NA NA NA NA NA NA NA NA NA NA ...
    ##  $ gender_stereotypes_perception_6: num [1:649] NA NA NA NA NA NA NA NA NA NA ...

``` r
dim(df_bier)
```

    ## [1] 649  37

## Filteren van respondenten (rijen)

We willen alle preview respondenten eruit halen, alleen de “anonymous”
respondenten moeten erin blijven. We willen alleen respondenten die de
survey afgemaakt hebben (progress moet 100 zijn). We willen alleen
respondenten die ja ingevuld hebben bij consent. Verder hebben jullie
misschien ook nog andere condities waar je op filtert, afhankelijk van
je onderzoeksvraag.

``` r
# filter distribution channel
df_bier = filter(df_bier, DistributionChannel != "preview")

# filter progress
df_bier = filter(df_bier, Progress == 100)

# filter consent
df_bier = filter(df_bier, consent == "yes")

# filter on additional vars
df_bier = filter(df_bier, live_NL_min_10_years == "yes")
df_bier = filter(df_bier, consume_beer == "yes")
df_bier = filter(df_bier, know_heineken == "yes")
```

Inspecteer na filteren weer je data.

``` r
# inspect
dim(df_bier)
```

    ## [1] 315  37

``` r
str(df_bier)
```

    ## tibble [315 × 37] (S3: tbl_df/tbl/data.frame)
    ##  $ Progress                       : num [1:315] 100 100 100 100 100 100 100 100 100 100 ...
    ##  $ duration                       : num [1:315] 530 501 254 417 492 ...
    ##  $ DistributionChannel            : Factor w/ 3 levels "anonymous","preview",..: 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ consent                        : Factor w/ 2 levels "yes","no": 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ age                            : num [1:315] 24 20 29 20 23 26 22 24 21 58 ...
    ##  $ live_NL_min_10_years           : Factor w/ 2 levels "yes","no": 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ consume_beer                   : Factor w/ 2 levels "yes","no": 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ know_heineken                  : Factor w/ 2 levels "yes","no": 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ occupation                     : Factor w/ 5 levels "student","full-time",..: 1 1 2 1 1 1 1 1 1 2 ...
    ##  $ gender                         : Factor w/ 5 levels "male","female",..: 1 1 1 2 1 1 1 1 1 1 ...
    ##  $ gender_other_text              : chr [1:315] NA NA NA NA ...
    ##  $ buy_intention_1                : num [1:315] 4 2 2 3 4 3 1 1 3 3 ...
    ##  $ buy_intention_2                : num [1:315] 4 3 3 3 3 4 1 1 4 2 ...
    ##  $ buy_intention_3                : num [1:315] 2 4 2 5 2 3 5 4 3 4 ...
    ##  $ buy_intention_4                : num [1:315] 3 4 2 4 5 3 3 3 5 4 ...
    ##  $ buy_intention_5                : num [1:315] 5 4 3 4 3 4 4 4 4 2 ...
    ##  $ buy_intention_6                : num [1:315] 1 4 5 2 2 4 5 5 5 5 ...
    ##  $ buy_intention_7                : num [1:315] 1 5 5 2 2 4 5 5 4 4 ...
    ##  $ buy_intention_8                : num [1:315] 5 1 1 4 3 4 1 1 2 1 ...
    ##  $ buy_intention_9                : num [1:315] 4 1 1 3 1 1 1 1 2 1 ...
    ##  $ buy_intention_10               : num [1:315] 2 1 3 1 1 2 1 1 1 1 ...
    ##  $ buy_intention_11               : num [1:315] 4 1 1 3 4 2 1 1 3 2 ...
    ##  $ buy_intention_12               : num [1:315] 2 3 3 4 4 1 1 1 1 1 ...
    ##  $ buy_intention_13               : num [1:315] 1 1 1 1 2 1 1 1 1 1 ...
    ##  $ alcohol_free_beer_perception_1 : num [1:315] 1 1 3 3 1 1 1 1 5 5 ...
    ##  $ alcohol_free_beer_perception_2 : num [1:315] 2 1 4 4 4 3 1 1 5 4 ...
    ##  $ alcohol_free_beer_perception_3 : num [1:315] 2 4 3 2 2 3 2 5 2 1 ...
    ##  $ alcohol_free_beer_perception_4 : num [1:315] 1 1 3 2 1 1 1 1 2 1 ...
    ##  $ alcohol_free_beer_perception_5 : num [1:315] 1 1 3 4 1 1 1 3 2 1 ...
    ##  $ alcohol_free_beer_perception_6 : num [1:315] 4 4 4 2 5 1 5 5 4 1 ...
    ##  $ alcohol_free_beer_perception_7 : num [1:315] 4 2 2 2 2 3 4 1 3 4 ...
    ##  $ gender_stereotypes_perception_1: num [1:315] 2 1 5 2 1 1 1 4 3 1 ...
    ##  $ gender_stereotypes_perception_2: num [1:315] 1 2 5 1 1 3 1 4 4 1 ...
    ##  $ gender_stereotypes_perception_3: num [1:315] 1 1 5 1 1 1 1 2 1 1 ...
    ##  $ gender_stereotypes_perception_4: num [1:315] 1 4 5 1 1 4 1 3 3 1 ...
    ##  $ gender_stereotypes_perception_5: num [1:315] 2 4 4 2 2 3 1 1 4 1 ...
    ##  $ gender_stereotypes_perception_6: num [1:315] 5 5 4 4 5 5 4 5 5 5 ...

``` r
head(df_bier)
```

    ## # A tibble: 6 × 37
    ##   Progress duration DistributionChannel consent   age live_NL_min_10_years
    ##      <dbl>    <dbl> <fct>               <fct>   <dbl> <fct>               
    ## 1      100      530 anonymous           yes        24 yes                 
    ## 2      100      501 anonymous           yes        20 yes                 
    ## 3      100      254 anonymous           yes        29 yes                 
    ## 4      100      417 anonymous           yes        20 yes                 
    ## 5      100      492 anonymous           yes        23 yes                 
    ## 6      100      259 anonymous           yes        26 yes                 
    ## # ℹ 31 more variables: consume_beer <fct>, know_heineken <fct>,
    ## #   occupation <fct>, gender <fct>, gender_other_text <chr>,
    ## #   buy_intention_1 <dbl>, buy_intention_2 <dbl>, buy_intention_3 <dbl>,
    ## #   buy_intention_4 <dbl>, buy_intention_5 <dbl>, buy_intention_6 <dbl>,
    ## #   buy_intention_7 <dbl>, buy_intention_8 <dbl>, buy_intention_9 <dbl>,
    ## #   buy_intention_10 <dbl>, buy_intention_11 <dbl>, buy_intention_12 <dbl>,
    ## #   buy_intention_13 <dbl>, alcohol_free_beer_perception_1 <dbl>, …

## Scale Construction

Eerst moeten we items hercoderen. Hieronder staan enkele voorbeelden van
hercoderen en hoe dit moet. Let er bij je eigen items goed op wat er
precies naar wat hergecodeerd moet worden.

``` r
# recode buy_intention
df_bier = mutate(
  df_bier,
  buy_intention_3_rec = recode(buy_intention_3, `1`=5, `2`=4, `3`=3, `4`=2, `5`=1),
  buy_intention_6_rec = recode(buy_intention_6, `1`=5, `2`=4, `3`=3, `4`=2, `5`=1),
  buy_intention_7_rec = recode(buy_intention_7,  `1`=5, `2`=4, `3`=3, `4`=2, `5`=1),
  alcohol_free_beer_perception_3_rec = recode(alcohol_free_beer_perception_3,  `1`=5, `2`=4, `3`=3, `4`=2, `5`=1),
  alcohol_free_beer_perception_6_rec = recode(alcohol_free_beer_perception_6,  `1`=5, `2`=4, `3`=3, `4`=2, `5`=1),
  alcohol_free_beer_perception_7_rec = recode(alcohol_free_beer_perception_7,  `1`=5, `2`=4, `3`=3, `4`=2, `5`=1)
)
```

We selecteren alleen de variabelen van een bepaalde schaal en slaan dit
op in een nieuw object (dus niet de hele data!!!). Deze dataset bevat
dus alleen antwoorden voor buy_intention. Deze dataset gebruiken we om
de cronbach alpha te berekenen.

``` r
# scale 1 (recoded items)
df_buy_intention = select(
  df_bier,
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

psych::alpha(df_buy_intention)
```

    ## 
    ## Reliability analysis   
    ## Call: psych::alpha(x = df_buy_intention)
    ## 
    ##   raw_alpha std.alpha G6(smc) average_r S/N    ase mean   sd median_r
    ##       0.91      0.91    0.93      0.44  10 0.0068  2.9 0.85     0.43
    ## 
    ##     95% confidence boundaries 
    ##          lower alpha upper
    ## Feldt      0.9  0.91  0.93
    ## Duhachek   0.9  0.91  0.93
    ## 
    ##  Reliability if an item is dropped:
    ##                     raw_alpha std.alpha G6(smc) average_r  S/N alpha se var.r
    ## buy_intention_1          0.90      0.90    0.91      0.43  8.9   0.0078 0.022
    ## buy_intention_2          0.91      0.91    0.92      0.45  9.7   0.0071 0.024
    ## buy_intention_3_rec      0.91      0.91    0.92      0.46 10.0   0.0070 0.023
    ## buy_intention_4          0.91      0.91    0.92      0.46 10.4   0.0069 0.022
    ## buy_intention_5          0.92      0.91    0.93      0.47 10.7   0.0067 0.020
    ## buy_intention_6_rec      0.90      0.90    0.91      0.42  8.9   0.0080 0.020
    ## buy_intention_7_rec      0.90      0.90    0.91      0.43  9.1   0.0078 0.021
    ## buy_intention_8          0.90      0.90    0.92      0.43  9.2   0.0076 0.022
    ## buy_intention_9          0.90      0.90    0.91      0.43  9.1   0.0077 0.020
    ## buy_intention_10         0.90      0.90    0.91      0.43  9.2   0.0076 0.021
    ## buy_intention_11         0.90      0.90    0.92      0.43  9.1   0.0076 0.022
    ## buy_intention_12         0.91      0.91    0.92      0.45  9.9   0.0071 0.024
    ## buy_intention_13         0.91      0.91    0.92      0.46 10.2   0.0070 0.021
    ##                     med.r
    ## buy_intention_1      0.41
    ## buy_intention_2      0.45
    ## buy_intention_3_rec  0.44
    ## buy_intention_4      0.47
    ## buy_intention_5      0.47
    ## buy_intention_6_rec  0.41
    ## buy_intention_7_rec  0.42
    ## buy_intention_8      0.42
    ## buy_intention_9      0.42
    ## buy_intention_10     0.42
    ## buy_intention_11     0.42
    ## buy_intention_12     0.43
    ## buy_intention_13     0.45
    ## 
    ##  Item statistics 
    ##                       n raw.r std.r r.cor r.drop mean   sd
    ## buy_intention_1     315  0.82  0.82  0.81   0.78  3.4 1.14
    ## buy_intention_2     315  0.65  0.67  0.63   0.59  3.6 1.09
    ## buy_intention_3_rec 315  0.61  0.61  0.56   0.53  3.3 1.18
    ## buy_intention_4     315  0.52  0.54  0.49   0.45  3.9 0.97
    ## buy_intention_5     315  0.48  0.50  0.43   0.40  3.1 1.06
    ## buy_intention_6_rec 315  0.84  0.83  0.84   0.80  3.2 1.42
    ## buy_intention_7_rec 315  0.81  0.79  0.79   0.75  2.6 1.39
    ## buy_intention_8     315  0.77  0.76  0.75   0.72  3.2 1.29
    ## buy_intention_9     315  0.78  0.78  0.77   0.73  2.3 1.28
    ## buy_intention_10    315  0.78  0.77  0.77   0.73  2.3 1.22
    ## buy_intention_11    315  0.78  0.78  0.76   0.72  2.7 1.24
    ## buy_intention_12    315  0.65  0.64  0.59   0.57  2.6 1.36
    ## buy_intention_13    315  0.57  0.58  0.53   0.50  1.7 1.04
    ## 
    ## Non missing response frequency for each item
    ##                        1    2    3    4    5 miss
    ## buy_intention_1     0.08 0.17 0.20 0.42 0.13    0
    ## buy_intention_2     0.06 0.12 0.17 0.48 0.17    0
    ## buy_intention_3_rec 0.09 0.17 0.23 0.37 0.16    0
    ## buy_intention_4     0.02 0.07 0.21 0.42 0.28    0
    ## buy_intention_5     0.09 0.17 0.37 0.29 0.08    0
    ## buy_intention_6_rec 0.16 0.23 0.11 0.29 0.22    0
    ## buy_intention_7_rec 0.27 0.28 0.14 0.18 0.13    0
    ## buy_intention_8     0.15 0.16 0.19 0.35 0.15    0
    ## buy_intention_9     0.37 0.24 0.17 0.16 0.06    0
    ## buy_intention_10    0.37 0.23 0.22 0.14 0.05    0
    ## buy_intention_11    0.22 0.22 0.28 0.21 0.08    0
    ## buy_intention_12    0.32 0.21 0.16 0.23 0.09    0
    ## buy_intention_13    0.58 0.22 0.12 0.04 0.03    0

We kunnen uit de output van `alpha()` ook specifieke outputs vragen:

``` r
psych::alpha(df_buy_intention)$total
```

    ##  raw_alpha std.alpha   G6(smc) average_r      S/N         ase     mean
    ##  0.9141511 0.9118485 0.9254851 0.4431143 10.34411 0.006810122 2.906716
    ##         sd  median_r
    ##  0.8512911 0.4322792

``` r
psych::alpha(df_buy_intention)$alpha.drop
```

    ##                     raw_alpha std.alpha   G6(smc) average_r       S/N
    ## buy_intention_1     0.9023763 0.8991263 0.9135806 0.4262049  8.913388
    ## buy_intention_2     0.9097070 0.9066795 0.9192145 0.4474060  9.715761
    ## buy_intention_3_rec 0.9117995 0.9094073 0.9230761 0.4554964 10.038422
    ## buy_intention_4     0.9141432 0.9122022 0.9246123 0.4640420 10.389813
    ## buy_intention_5     0.9161566 0.9143591 0.9271709 0.4708217 10.676667
    ## buy_intention_6_rec 0.9003159 0.8984915 0.9112575 0.4244990  8.851396
    ## buy_intention_7_rec 0.9026463 0.9006113 0.9133290 0.4302400  9.061501
    ## buy_intention_8     0.9042163 0.9019534 0.9164550 0.4339418  9.199232
    ## buy_intention_9     0.9037088 0.9013497 0.9134214 0.4322702  9.136817
    ## buy_intention_10    0.9041179 0.9015397 0.9131642 0.4327951  9.156374
    ## buy_intention_11    0.9041405 0.9013609 0.9152948 0.4323011  9.137966
    ## buy_intention_12    0.9110327 0.9079527 0.9232224 0.4511521  9.863981
    ## buy_intention_13    0.9125357 0.9106672 0.9238346 0.4593158 10.194100
    ##                        alpha se      var.r     med.r
    ## buy_intention_1     0.007754859 0.02182033 0.4085075
    ## buy_intention_2     0.007133788 0.02449058 0.4476309
    ## buy_intention_3_rec 0.007005848 0.02269833 0.4404287
    ## buy_intention_4     0.006864165 0.02182883 0.4654920
    ## buy_intention_5     0.006704400 0.02008231 0.4716528
    ## buy_intention_6_rec 0.008026267 0.01973136 0.4085075
    ## buy_intention_7_rec 0.007802373 0.02083933 0.4157645
    ## buy_intention_8     0.007636854 0.02230228 0.4225672
    ## buy_intention_9     0.007662030 0.02006711 0.4225672
    ## buy_intention_10    0.007610313 0.02059207 0.4225672
    ## buy_intention_11    0.007606479 0.02226351 0.4157645
    ## buy_intention_12    0.007055280 0.02407488 0.4268352
    ## buy_intention_13    0.007006194 0.02112324 0.4476309

Vervolgens, NA HET INSPECTEREN VAN ALPHA, kunnen we beslissen welke
items in de schaal moeten. Voor het maken van schalen en hoe dit moet,
NEEM [DEZE
TUTORIAL](https://github.com/roanbuma/P2_Strategic_Communication_2024/blob/main/P2SC_2024_Tutorial_Recoding_and_Scale.md)
GOED DOOR.

Voorbeeld:

``` r
df_bier = df_bier %>%
  rowwise() %>%
  mutate(
    SCA_buy_intent = mean(c(
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
      buy_intention_13),
      na.rm = TRUE)) %>%
  ungroup()
```

Herhaal dit proces voor iedere schaal.

# Saving Dataset

Je kunt nu je dataset opslaan. Doe dit met `saveRDS()`:

``` r
saveRDS(df_bier, "prepared_dataset_test.rds")
```
