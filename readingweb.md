Data Wrangling 2
================
Nidhi Patel
10/20/2020

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
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
library(httr)

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

## Scrape a table

I want the first table from [this
page](http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm)

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_html = read_html(url)
```

extract the table(s); focus on first one

``` r
drug_use_html = drug_use_html %>% 
  html_nodes(css = "table") %>% #this extracted all 15 tables 
  first() %>% #there is an nth one as well as second, third, fourth, fifth, last to select certain tables
  html_table() %>% #parses html from table or text
  slice(-1) %>% 
  as_tibble() #converts it to a tibble

#THIS ISN'T WORKING?? R says its a graph??
```

## Star Wars Movie Info

I want data from [here](https://www.imdb.com/list/ls024732224/).

``` r
url = "https://www.imdb.com/list/ls024732224/"

sw_html = read_html(url)
```

Grab elements I want

``` r
title_vec = 
  sw_html %>% 
  html_nodes(css = ".lister-item-header a") %>% 
  html_text()

gross_vec = 
  sw_html %>% 
  html_node(css = ".ghost~ .text-muted+ span") %>% 
  html_text()

sw_html
```

    ## {html_document}
    ## <html xmlns:og="http://ogp.me/ns#" xmlns:fb="http://www.facebook.com/2008/fbml">
    ## [1] <head>\n<meta http-equiv="Content-Type" content="text/html; charset=UTF-8 ...
    ## [2] <body id="styleguide-v2" class="fixed">\n            <img height="1" widt ...
