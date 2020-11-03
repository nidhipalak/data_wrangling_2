Strings and Factors
================
Nidhi Patel
10/28/2020

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(p8105.datasets)
library(rvest)
```

    ## Loading required package: xml2

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     pluck

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
knitr::opts_chunk$set(
  fig.width = 6,
  fig.height = 6,
  out.width = "90%")

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_color_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

## Strings and regex

``` r
string_vec = c("my", "name", "is", "nidhi")

str_detect(string_vec, "nidh")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
str_replace(string_vec, "nidhi", "Nidhi")
```

    ## [1] "my"    "name"  "is"    "Nidhi"

``` r
string_vec = c(
  "i think we all rule for participating",
  "i think i have been caught",
  "i think this will be quite fun",
  "it will be fun, i think"
)
str_detect(string_vec, "^i think") #the "^" means it's looking at the first part of the string
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
str_detect(string_vec, "i think$") #"$" after means look for it at the end
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
string_vec = c(
  "Y'all remember Pres. HW Bush?",
  "I saw a green bush",
  "BBQ and Bushwalking at Molonglo Gorge",
  "BUSH -- LIVE IN CONCERT"
)

str_detect(string_vec, "[Bb]ush") #in brackets to say it can either by capital or lower case b.
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
string_vec = c(
  "7th inning stretch",
  "1st half soon to begin, Texas won the toss.",
  "she is 5 feet 4 inches tall",
  "3AM = can't sleep :("
)

str_detect(string_vec, "[0-9][a-zA-Z]") #asking for a number first followed immediately by a number. 
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
#if I say "^[0-9][a-zA-Z]", it will ask for the #followed by letter at the beginning of the line. ^ is the beginning.
```

``` r
string_vec = c(
  'Its 7:11 in the evening',
  'want to go to 7-11?',
  'my flight is AA711',
  'NetBios: scanning ip 203.167.114.66'
)

str_detect(string_vec, "7.11") #the dot should match everything; and it does match the -, :, dot. only the 711 does not have a separation and is false. dot is a SPECIAL CHARACTER 
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
#if you really only want 7.11, you need to add "\\" before: "7\\.11".
#need two slashes bc slash is also a specual chatacter like the dot. 
```

``` r
string_vec = c(
  'The CI is [2, 5]',
  ':-]',
  ':-[',
  'I found the answer on page [6-7]'
)
#open bracket is also a special character. if you want to detect it you need to have the '\\' before it. 
str_detect(string_vec, "\\[")
```

    ## [1]  TRUE FALSE  TRUE  TRUE

## Factors

``` r
factor_vec = factor(c("male", "male", "female", "female"))

factor_vec
```

    ## [1] male   male   female female
    ## Levels: female male

``` r
as.numeric(factor_vec)
```

    ## [1] 2 2 1 1

what happens if i relevel…?

``` r
factor_vec = fct_relevel(factor_vec, "male")

factor_vec
```

    ## [1] male   male   female female
    ## Levels: male female

``` r
as.numeric(factor_vec)
```

    ## [1] 1 1 2 2

``` r
#changed male = 1 from previous code chunk, where male = 2
#fct_recode, fct_reorder, fct_relevel, str_count, extract, locate, remove. google forcats and stringr.
```

## NSDUH

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

tabl_marj = 
  read_html(url) %>% 
  html_nodes(css = "table") %>% 
  first() %>% 
  html_table() %>% 
  slice(-1) %>% 
  as_tibble()
```

``` r
data_marj = 
  tabl_marj %>% 
  select(-contains("P Value")) %>% 
  pivot_longer(
    -State,
    names_to = "age_year",
    values_to = "percent"
  ) %>% 
  separate(age_year, into = c("age", "year"), sep = "\\(") %>% 
  mutate(
    year = str_replace(year, "\\)", ""),
    percent = str_replace(percent, "[a-c]$", ""),
    percent = as.numeric(percent)
  ) %>% 
  filter(!(State %in% c("Total U.S.", "Northeast", "Midwest", "South", "West")))
```