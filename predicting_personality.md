Posture Project
================
Giovani Gutierrez
2023-02-15

- <a href="#getting-started" id="toc-getting-started">Getting Started</a>
  - <a href="#setup" id="toc-setup">Setup</a>
  - <a href="#import-data" id="toc-import-data">Import Data</a>
  - <a href="#tidy-data--missing-values"
    id="toc-tidy-data--missing-values">Tidy Data &amp; Missing Values</a>
- <a href="#exploratory-data-analysis-eda"
  id="toc-exploratory-data-analysis-eda">Exploratory Data Analysis
  (EDA)</a>
  - <a href="#correlation-between-features"
    id="toc-correlation-between-features">Correlation Between Features</a>
  - <a href="#visual-eda" id="toc-visual-eda">Visual EDA</a>

# Getting Started

## Setup

``` r
library(tidyverse)
library(tidymodels)
library(readxl)
library(janitor)
library(visdat)
library(naniar)
library(corrplot)
tidymodels_prefer()

set.seed(123)  # set seed
```

## Import Data

``` r
data1 <- read_xls("C:/Users/giova/Desktop/PSTAT 131/predicting_personality/Data/Posture_Data.xls") %>%
    clean_names()  # read in raw data from .xls file

data1 %>%
    write.csv(file = "C:/Users/giova/Desktop/PSTAT 131/predicting_personality/Data/raw_data.csv")  # write raw data to .csv file

head(data1)  # preview data
```

<div class="kable-table">

| number_1 | age | height | weight | sex    | activity_level | pain_1 | pain_2 | pain_3 | pain_4 | mbti | e   | i   | s   | n   | t   | f   | j   | p   | posture | number_21 | l1  | l2_d               | l3  | l4_d | l5  | l6_d | l7  | l8_d | l9  | l10_d              | l11 | l12_i | l13 | l14_i               | l15 | l16_i | l17 | l18_i | l19 | l20_i | l21 | l22_i | l23 | l24_i               | l25_i |  l26 | l27 | l28 | l29 | l30                |  l31 |   l32 | l33                 | l34 | l35 |    l36 |   l37 | l38 | l39 | l40 | l41 | l42 | l43 | l44 | l45 |
|:---------|----:|-------:|-------:|:-------|:---------------|:-------|:-------|:-------|:-------|:-----|:----|:----|:----|:----|:----|:----|:----|:----|:--------|:----------|:----|:-------------------|:----|-----:|:----|-----:|:----|-----:|:----|:-------------------|:----|------:|:----|:--------------------|:----|------:|:----|------:|:----|------:|:----|------:|:----|:--------------------|------:|-----:|----:|----:|:----|:-------------------|-----:|------:|:--------------------|----:|:----|-------:|------:|:----|:----|:----|:----|:----|:----|:----|:----|
| 1        |  53 |     62 |    125 | Female | Low            | 0      | 0      | 0      | 0      | ESFJ | 18  | 3   | 17  | 9   | 9   | 13  | 18  | 4   | A       | 1         | A   | 4.7000000000000002 | A   |  3.3 | A   |  5.3 | P   |  2.6 | A   | 12.0               | A   |   1.3 | A   | 0.90000000000000002 | A   |   0.0 | A   |   0.5 | P   |   0.5 | L   |   0.5 | A   | 0.59999999999999998 |   0.8 | 10.1 | 0.4 | 0.4 | P   | 56.100000000000001 | 12.6 |  72.3 | 0.29999999999999999 | 2.1 | P   | 364.10 |  81.8 | NA  | NA  | NA  | NA  | NA  | NA  | NA  | NA  |
| 3        |  30 |     69 |    200 | Male   | High           | 0      | 0      | 0      | 0      | ESTJ | 15  | 6   | 16  | 10  | 15  | 9   | 12  | 10  | A       | 3         | A   | 27.300000000000001 | P   |  5.2 | P   |  0.3 | A   |  3.8 | A   | 12                 | A   |   2.7 | A   | 0.0                 | A   |   1.4 | A   |   0.9 | A   |   1.0 | NA  |   0.0 | A   | 0.69999999999999996 |   0.7 | 16.2 | 2.7 | 4.9 | P   | 194.80000000000001 | 43.8 | 115.6 | 0.10000000000000001 | 1.2 | A   | 537.60 | 120.8 | X   | NA  | NA  | NA  | NA  | NA  | NA  | NA  |
| 5        |  45 |     63 |    199 | Female | Moderate       | 4      | 5      | 2      | 2      | ENFJ | 14  | 7   | 20  | 6   | 9   | 15  | 16  | 6   | A       | 5         | A   | 2.5                | A   |  2.0 | A   |  2.6 | A   |  1.4 | A   | 20.100000000000001 | A   |   1.0 | A   | 0.90000000000000002 | A   |   0.3 | A   |   1.0 | A   |   0.3 | NA  |   0.0 | A   | 0.80000000000000004 |   0.8 | 16.1 | 0.2 | 0.3 | P   | 79.900000000000006 | 18.0 | 115.0 | 0                   | 0.4 | A   | 520.10 | 116.9 | NA  | NA  | NA  | NA  | NA  | X   | X   | X   |
| 15       |  30 |     69 |    190 | Male   | Moderate       | 0      | 0      | 4      | 7      | ESTP | 20  | 1   | 22  | 4   | 13  | 11  | 7   | 15  | A       | 16        | A   | 8.5                | A   |  4.0 | A   |  2.0 | P   |  1.5 | A   | 15.300000000000001 | A   |   1.8 | A   | 1.1000000000000001  | P   |   0.0 | A   |   0.1 | P   |   0.3 | L   |   0.1 | A   | 0.5                 |   0.5 | 15.4 | 0.7 | 1.1 | P   | 96.090000000000003 | 21.8 | 109.8 | 0.59999999999999998 | 7.4 | P   | 637.10 | 143.2 | NA  | NA  | NA  | NA  | NA  | NA  | NA  | NA  |
| 17       |  59 |     66 |    138 | Female | Low            | 6      | 3      | 6      | 6      | ESTJ | 12  | 9   | 18  | 8   | 14  | 10  | 20  | 2   | A       | 19        | A   | 0.5                | P   |  1.1 | A   |  0.3 | A   |  4.0 | A   | 12.1               | A   |   0.4 | A   | 0.40000000000000002 | A   |   0.7 | A   |   1.0 | A   |   0.9 | L   |   0.5 | A   | 0.59999999999999998 |   0.8 | 11.2 | 0.0 | 0.1 | P   | 51.100000000000001 | 11.5 |  79.8 | 0.29999999999999999 | 2.9 | A   | 412.00 |  92.6 | NA  | NA  | NA  | NA  | NA  | NA  | NA  | NA  |
| 27       |  36 |     68 |    165 | Male   | High           | 0      | 2      | 0      | 0      | ESTP | 16  | 5   | 20  | 6   | 15  | 9   | 7   | 15  | A       | 29        | A   | 14                 | P   |  2.6 | A   |  1.6 | A   |  1.0 | A   | 14.199999999999999 | A   |   1.2 | P   | 0.10000000000000001 | A   |   0.6 | A   |   0.6 | A   |   0.2 | L   |   0.2 | A   | 0.40000000000000002 |   0.4 | 13.4 | 1.3 | 1.9 | P   | 107.59999999999999 | 24.2 |  95.4 | 0.20000000000000001 | 2.3 | A   | 469.05 | 105.5 | X   | NA  | NA  | NA  | NA  | NA  | NA  | NA  |

</div>

## Tidy Data & Missing Values

``` r
data1 <- data1 %>%
    replace_with_na_all(condition = ~.x == "X")  # replace 'X' character values with NA values

data1 %>%
    vis_miss()  # visualize missing values
```

<img src="predicting_personality_files/figure-gfm/missing values-1.png" style="display: block; margin: auto auto auto 0;" />

``` r
data1 <- data1 %>%
    select(age:posture & -mbti) %>%
    drop_na()  # drop missing and irrelevant variables

data1 <- data1 %>%
    mutate(sex = factor(sex)) %>%
    mutate(activity_level = ordered(activity_level, levels = c("Low", "Moderate",
        "High"))) %>%
    mutate_at(c("pain_1", "pain_2", "pain_3", "pain_4"), as.numeric) %>%
    mutate(e_i = factor(ifelse(e > i, "extrovert", "introvert"))) %>%
    mutate_at(c("s", "n", "t", "f", "j", "p"), as.numeric) %>%
    mutate(posture = factor(posture)) %>%
    select(-e & -i)  # coerce variables into appropriate type

data1 %>%
    vis_dat()  # visualize variable types
```

<img src="predicting_personality_files/figure-gfm/missing values-2.png" style="display: block; margin: auto auto auto 0;" />

``` r
data1 %>%
    write.csv(file = "C:/Users/giova/Desktop/PSTAT 131/predicting_personality/Data/clean_data.csv")  # write clean data to .csv file

head(data1)  # preview cleaned data
```

<div class="kable-table">

| age | height | weight | sex    | activity_level | pain_1 | pain_2 | pain_3 | pain_4 |   s |   n |   t |   f |   j |   p | posture | e_i       |
|----:|-------:|-------:|:-------|:---------------|-------:|-------:|-------:|-------:|----:|----:|----:|----:|----:|----:|:--------|:----------|
|  53 |     62 |    125 | Female | Low            |      0 |      0 |      0 |      0 |  17 |   9 |   9 |  13 |  18 |   4 | A       | introvert |
|  30 |     69 |    200 | Male   | High           |      0 |      0 |      0 |      0 |  16 |  10 |  15 |   9 |  12 |  10 | A       | introvert |
|  45 |     63 |    199 | Female | Moderate       |      4 |      5 |      2 |      2 |  20 |   6 |   9 |  15 |  16 |   6 | A       | introvert |
|  30 |     69 |    190 | Male   | Moderate       |      0 |      0 |      4 |      7 |  22 |   4 |  13 |  11 |   7 |  15 | A       | extrovert |
|  59 |     66 |    138 | Female | Low            |      6 |      3 |      6 |      6 |  18 |   8 |  14 |  10 |  20 |   2 | A       | introvert |
|  36 |     68 |    165 | Male   | High           |      0 |      2 |      0 |      0 |  20 |   6 |  15 |   9 |   7 |  15 | A       | introvert |

</div>

*Describe variables here!*

# Exploratory Data Analysis (EDA)

## Correlation Between Features

``` r
data1 %>%
    select_if(is.numeric) %>%
    cor() %>%
    corrplot(method = "circle", type = "lower")
```

<img src="predicting_personality_files/figure-gfm/correlation plot-1.png" style="display: block; margin: auto auto auto 0;" />

## Visual EDA

``` r
ggplot(data = data1, aes(fill = e_i, x = e_i)) + geom_bar()
```

<img src="predicting_personality_files/figure-gfm/visual eda-1.png" style="display: block; margin: auto auto auto 0;" />

``` r
ggplot(data = data1, aes(fill = sex, x = e_i)) + geom_bar(position = "fill")
```

<img src="predicting_personality_files/figure-gfm/visual eda-2.png" style="display: block; margin: auto auto auto 0;" />

``` r
ggplot(data = data1, aes(fill = posture, x = e_i)) + geom_bar(position = "fill")
```

<img src="predicting_personality_files/figure-gfm/visual eda-3.png" style="display: block; margin: auto auto auto 0;" />
