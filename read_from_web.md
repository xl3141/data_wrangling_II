data\_wrangling\_II
================
Xinyuan Liu
10/19/2021

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --

    ## v ggplot2 3.3.5     v purrr   0.3.4
    ## v tibble  3.1.4     v dplyr   1.0.7
    ## v tidyr   1.1.3     v stringr 1.4.0
    ## v readr   2.0.1     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr)
```

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"
drug_use_html = read_html(url) ## read url link
 
drug_use_df =
  drug_use_html %>% 
    html_table() %>% ## give me the table in the html page
    first() %>% ## take the first table
    slice(-1) ## remove the first row 
```

## Star Wars

get some star wars data

``` r
sw_url = "https://www.imdb.com/list/ls070150896/"

sw_html = 
  read_html(sw_url)

title_vec = 
  sw_html %>%
  html_elements(".lister-item-header a") %>%
  html_text() ## convert html to text

## CSS selector: select the items that you want and then click on the items that you don't want to the the elements 

gross_rev_vec = 
  sw_html %>%
  html_elements(".text-muted .ghost~ .text-muted+ span") %>%
  html_text()


sw_df = 
  tibble(
    title = title_vec,
    rev = gross_rev_vec
    )
```

## napolean dynamite

``` r
dynamite_url = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber=1"

dynamite_html =
  read_html(dynamite_url)

dynamite_review_titles =
  dynamite_html %>% 
  html_elements(".a-text-bold span") %>% 
  html_text()

dynamite_stars = 
  dynamite_html %>% 
  html_elements("#cm_cr-review_list .review-rating") %>% 
  html_text()

dynamite_df = 
  tibble(
    titles = dynamite_review_titles,
    stars = dynamite_stars
  )
```

## try some API

``` r
water_df = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") %>% ##API command: get something from url
  content() ##parse it into a data frame that is useful
```

    ## Rows: 42 Columns: 4

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

Let’s see what JSON looks like

``` r
water_df = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json") %>% 
  content("text") %>% 
  jsonlite::fromJSON() %>% 
  as_tibble()
```

BRFSS data via API

``` r
brfss_df = 
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv", query = list("$limit" = 5000)) %>% ## change the query limit into 5000   
  content()
```

    ## Rows: 5000 Columns: 23

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr (16): locationabbr, locationdesc, class, topic, question, response, data...
    ## dbl  (6): year, sample_size, data_value, confidence_limit_low, confidence_li...
    ## lgl  (1): locationid

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

## Pokemon

``` r
poke_data = 
  GET("https://pokeapi.co/api/v2/pokemon/1") %>% 
  content()

poke_data[["height"]]
```

    ## [1] 7

``` r
poke_data[["name"]]
```

    ## [1] "bulbasaur"

``` r
poke_data[["abilities"]]
```

    ## [[1]]
    ## [[1]]$ability
    ## [[1]]$ability$name
    ## [1] "overgrow"
    ## 
    ## [[1]]$ability$url
    ## [1] "https://pokeapi.co/api/v2/ability/65/"
    ## 
    ## 
    ## [[1]]$is_hidden
    ## [1] FALSE
    ## 
    ## [[1]]$slot
    ## [1] 1
    ## 
    ## 
    ## [[2]]
    ## [[2]]$ability
    ## [[2]]$ability$name
    ## [1] "chlorophyll"
    ## 
    ## [[2]]$ability$url
    ## [1] "https://pokeapi.co/api/v2/ability/34/"
    ## 
    ## 
    ## [[2]]$is_hidden
    ## [1] TRUE
    ## 
    ## [[2]]$slot
    ## [1] 3
