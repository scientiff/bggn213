BGGN213: Coronavirus Analysis (Homework)
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

> Q1. How many total infected cases are there around the world?

``` r
total <- sum(virus$cases)
total
```

    ## [1] 155031

> > There are 144,233 cases worldwide. (3/4/2020) Now there are 155,031
> > cases\! (3/6/2020)

> Q2. How many deaths linked to infected cases have there been?

``` r
table(virus$type)
```

    ## 
    ## confirmed     death recovered 
    ##      1593       212      1081

``` r
inds <- virus$type == "death"

head(virus[inds,])
```

    ##     Province.State Country.Region      Lat     Long       date cases  type
    ## 30           Hubei Mainland China 30.97560 112.2707 2020-01-22    17 death
    ## 60           Hebei Mainland China 38.04280 114.5149 2020-01-23     1 death
    ## 94    Heilongjiang Mainland China 47.86200 127.7615 2020-01-24     1 death
    ## 95           Hubei Mainland China 30.97560 112.2707 2020-01-24     7 death
    ## 133          Hubei Mainland China 30.97560 112.2707 2020-01-25    16 death
    ## 178          Henan Mainland China 33.88202 113.6140 2020-01-26     1 death

``` r
deaths <- sum(virus[inds,]$cases)

deaths
```

    ## [1] 3348

> > There are 3,160 deaths linked to the infected cases. (3/4/2020) Now
> > there are 3,348. :( (3/6/2020)

> Q3. What is the overall death rate?

``` r
death_rate<- round((deaths/total)*100, 2)

death_rate
```

    ## [1] 2.16

> > The overall death rate is 2.19%. (3/4/2020) It’s dropped\! 2.16%
> > (3/6/2020)

> Q4. What is the death rate in Mainland China?

``` r
#First, calculate the total number of cases in Mainland China.

chinese_cases <- virus$Country.Region == "Mainland China"
  
chinese_total <- sum(virus[chinese_cases,]$cases)

chinese_total
```

    ## [1] 135675

``` r
#Next, specify that we're looking for deaths in "Mainland China".

chinese_inds <- virus$type == "death" & virus$Country.Region == "Mainland China"

head(virus[chinese_inds,])
```

    ##     Province.State Country.Region      Lat     Long       date cases  type
    ## 30           Hubei Mainland China 30.97560 112.2707 2020-01-22    17 death
    ## 60           Hebei Mainland China 38.04280 114.5149 2020-01-23     1 death
    ## 94    Heilongjiang Mainland China 47.86200 127.7615 2020-01-24     1 death
    ## 95           Hubei Mainland China 30.97560 112.2707 2020-01-24     7 death
    ## 133          Hubei Mainland China 30.97560 112.2707 2020-01-25    16 death
    ## 178          Henan Mainland China 33.88202 113.6140 2020-01-26     1 death

``` r
chinese_deaths <- sum(virus[chinese_inds,]$cases)

chinese_deaths
```

    ## [1] 3013

``` r
#Finally, calculate death rate by dividing deaths/total in "Mainland China".

chinese_death_rate <- round((chinese_deaths/chinese_total)*100, 2)

chinese_death_rate
```

    ## [1] 2.22

> > The death rate in Mainland China is 2.26% (3/4/2020). Now it’s 2.22%
> > (3/6/2020).

> Q5. What is the death rate in Italy, Iran, and the US?

Italy

``` r
#First, calculate the total number of cases in Italy.

italian_cases <- virus$Country.Region == "Italy"
  
italian_total <- sum(virus[italian_cases,]$cases)

italian_total
```

    ## [1] 4420

``` r
#Next, specify that we're looking for deaths in "Italy".

italian_inds <- virus$type == "death" & virus$Country.Region == "Italy"

head(virus[italian_inds,])
```

    ##      Province.State Country.Region Lat Long       date cases  type
    ## 1806                         Italy  43   12 2020-02-21     1 death
    ## 1870                         Italy  43   12 2020-02-22     1 death
    ## 1920                         Italy  43   12 2020-02-23     1 death
    ## 1986                         Italy  43   12 2020-02-24     4 death
    ## 2049                         Italy  43   12 2020-02-25     3 death
    ## 2123                         Italy  43   12 2020-02-26     2 death

``` r
italian_deaths <- sum(virus[italian_inds,]$cases)

italian_deaths
```

    ## [1] 148

``` r
#Finally, calculate death rate by dividing deaths/total in "Italy".

italian_death_rate <- round((italian_deaths/italian_total)*100, 2)

italian_death_rate
```

    ## [1] 3.35

> > The death rate in Italy is 2.88%.(3/4/2020) New death rate of 3.35%.
> > (3/6/2020)

Iran

``` r
#First, calculate the total number of cases in Iran.

iranian_cases <- virus$Country.Region == "Iran"
  
iranian_total <- sum(virus[iranian_cases,]$cases)

iranian_total
```

    ## [1] 4359

``` r
#Next, specify that we're looking for deaths in "Iran".

iranian_inds <- virus$type == "death" & virus$Country.Region == "Iran"

head(virus[iranian_inds,])
```

    ##      Province.State Country.Region Lat Long       date cases  type
    ## 1668                          Iran  32   53 2020-02-19     2 death
    ## 1805                          Iran  32   53 2020-02-21     2 death
    ## 1869                          Iran  32   53 2020-02-22     1 death
    ## 1919                          Iran  32   53 2020-02-23     3 death
    ## 1985                          Iran  32   53 2020-02-24     4 death
    ## 2048                          Iran  32   53 2020-02-25     4 death

``` r
iranian_deaths <- sum(virus[iranian_inds,]$cases)

iranian_deaths
```

    ## [1] 107

``` r
#Finally, calculate death rate by dividing deaths/total in "Iran".

iranian_death_rate <- round((iranian_deaths/iranian_total)*100, 2)

iranian_death_rate
```

    ## [1] 2.45

> > The death rate in Iran is 2.85%. (3/4/2020) New rate, 2.45%.
> > (3/6/2020)

United States (US)

``` r
#First, calculate the total number of cases in the "US".

american_cases <- virus$Country.Region == "US"
  
american_total <- sum(virus[american_cases,]$cases)

american_total
```

    ## [1] 241

``` r
#Next, specify that we're looking for deaths in "US".

american_inds <- virus$type == "death" & virus$Country.Region == "US"

head(virus[american_inds,])
```

    ##            Province.State Country.Region     Lat      Long       date cases
    ## 2365      King County, WA             US 47.6062 -122.3321 2020-02-29     1
    ## 2548      King County, WA             US 47.6062 -122.3321 2020-03-02     4
    ## 2549 Snohomish County, WA             US 48.0330 -121.8339 2020-03-02     1
    ## 2646      King County, WA             US 47.6062 -122.3321 2020-03-03     1
    ## 2746      King County, WA             US 47.6062 -122.3321 2020-03-04     3
    ## 2748    Placer County, CA             US 39.0916 -120.8039 2020-03-04     1
    ##       type
    ## 2365 death
    ## 2548 death
    ## 2549 death
    ## 2646 death
    ## 2746 death
    ## 2748 death

``` r
american_deaths <- sum(virus[american_inds,]$cases)

american_deaths
```

    ## [1] 12

``` r
#Finally, calculate death rate by dividing deaths/total in "US".

american_death_rate <- round((american_deaths/american_total)*100, 2)

american_death_rate
```

    ## [1] 4.98

> > The death rate in the US is 5.11%. (3/4/2020) Now only 4.98%\!
> > (3/6/2020)

# Don’t forget to wash your hands\!\!\!

![](clean_hands.gif)<!-- -->

Submit a link to your knited GitHub document (i.e. the .md file)
