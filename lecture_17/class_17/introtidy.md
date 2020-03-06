R Tidyverse
================

## Coronavirus

Here we analyze infection data for the 2019 novel Coronavirus COVID-19
(2019-nCoV) epidemic. The raw data is pulled from the Johns Hopkins
University Center for Systems Science and Engineering (JHU CCSE)
Coronavirus repository.

A CSV file is available here
<https://github.com/RamiKrispin/coronavirus-csv>

``` r
url <- "https://tinyurl.com/COVID-2019"
virus <- read.csv(url)

tail(virus)
```

    ##      Province.State Country.Region      Lat     Long       date cases      type
    ## 2881         Shanxi Mainland China  37.5777 112.2922 2020-03-05     2 recovered
    ## 2882        Sichuan Mainland China  30.6171 102.7103 2020-03-05    19 recovered
    ## 2883        Tianjin Mainland China  39.3054 117.3230 2020-03-05     4 recovered
    ## 2884       Victoria      Australia -37.8136 144.9631 2020-03-05     3 recovered
    ## 2885       Xinjiang Mainland China  41.1129  85.2401 2020-03-05     1 recovered
    ## 2886       Zhejiang Mainland China  29.1832 120.0934 2020-03-05    10 recovered

## We can use R Tidyverse to calculate our statistics as well\!

This has become increasingly popular in the last few years, it’s like a
different language that’s pretty compatible with other languages such as
Python.

``` r
#install.packages("tidyverse")
library(tidyverse)
```

    ## Warning: package 'tidyverse' was built under R version 3.6.3

    ## -- Attaching packages ------------------------------------------------------------------------------------------------------------------ tidyverse 1.3.0 --

    ## v ggplot2 3.2.1     v purrr   0.3.3
    ## v tibble  2.1.3     v dplyr   0.8.4
    ## v tidyr   1.0.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## Warning: package 'tidyr' was built under R version 3.6.3

    ## Warning: package 'readr' was built under R version 3.6.3

    ## Warning: package 'dplyr' was built under R version 3.6.3

    ## Warning: package 'forcats' was built under R version 3.6.3

    ## -- Conflicts --------------------------------------------------------------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

How many infected per country?

``` r
cases_by_country <- virus %>%
  group_by(Country.Region) %>%
  summarise(tot=sum(cases)) %>%
  arrange(desc(tot))

head(cases_by_country)
```

    ## # A tibble: 6 x 2
    ##   Country.Region    tot
    ##   <fct>           <int>
    ## 1 Mainland China 135675
    ## 2 South Korea      6164
    ## 3 Italy            4420
    ## 4 Iran             4359
    ## 5 Others            722
    ## 6 Germany           498
