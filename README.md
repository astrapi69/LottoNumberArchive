
<!-- README.md is generated from README.Rmd. Please edit that file -->

# API for the lotto numbers of the german lottery (1955-2019)

[![Project Status: Active – The project has reached a stable, usable
state and is being actively
developed.](http://www.repostatus.org/badges/latest/active.svg)](http://www.repostatus.org/#active)

## Background

This repo provides the german lotto numbers from 1955 - today in one
single file. All people who are interested in data analysis or just to
“calculate” their chances to win the lottery are invited to use the
data.

Two JSON files are give: Choose the one you can work with :-)

## Data analysis examples

The data provided is a JSON file and readable by all modern software
languages. In the following two examples are shown (R and Python).

### R

The package [tidyverse](https://www.tidyverse.org) is able to analyse
the data very quickly with R.

In the next chunk, all data are read, filtered (just taking the lotto
numbers) and grouped by the values and counted the number of apperance.
We can see, that lotto number 6 is the nost frequent number.

``` r
library(tidyverse)
library(jsonlite)
library(lubridate)

data <- fromJSON("https://johannesfriedrich.github.io/LottoNumberArchive/Lottonumbers_tidy_complete.json")

lottonumbers_count <- data$data %>% 
  filter(variable == "Lottozahl") %>% 
  group_by(value) %>% 
  summarise(count = n())
```

``` r
lottonumbers_count %>% 
  arrange(desc(count)) %>% 
  top_n(5)
## Selecting by count
## # A tibble: 5 x 2
##   value count
##   <int> <int>
## 1     6   593
## 2    32   573
## 3    49   571
## 4    38   568
## 5    26   567
```

Now we want to summarise all numbers from 1-49 and their appearance.

``` r
library(ggplot2)

ggplot(lottonumbers_count, aes(value, count)) +
  geom_bar(stat = "identity") +
  labs(x = "Lottonumber", title = "Lottonumbers in Germany since 1955")
```

<img src="README_figs/README-unnamed-chunk-3-1.png" width="672" style="display: block; margin: auto;" />

Since 2001 in the german lottery a number called “Zusatzzahl” was
introduced. Every Wednesday and Saturday the number chosen. The
following graph shows the distribution of the Zusatzzahl.

``` r
superzahl <- data$data %>% 
  filter(variable == "Superzahl") %>% 
  mutate(date = dmy(date),
         Day = weekdays(date),
         year = year(date)) %>% 
  filter(year >= 2001) %>% 
  group_by(value, Day) %>% 
  summarise(count = n())
```

``` r

ggplot(superzahl, aes(value, count, fill = Day)) +
  geom_bar(stat = "identity", position = "dodge") +
  scale_x_continuous(breaks = c(0:9)) +
  labs(x = "Zusatzzahl", title = "Zusatzzahl since 2001")
```

<img src="README_figs/README-unnamed-chunk-5-1.png" width="672" style="display: block; margin: auto;" />

What were the numbers most chosen in 2019?

``` r
data$data %>% 
  filter(variable == "Lottozahl") %>% 
  mutate(date = dmy(date),
         year = year(date)) %>% 
  filter(year == 2019) %>% 
  group_by(value) %>% 
  summarise(count = n()) %>% 
   arrange(desc(count)) %>% 
  top_n(5)
## Selecting by count
## # A tibble: 7 x 2
##   value count
##   <int> <int>
## 1    42    22
## 2    29    20
## 3    36    18
## 4    11    17
## 5    19    17
## 6    31    17
## 7    47    17
```

### Python

In python the module [pandas](http://pandas.pydata.org) is very handy to
analyse data. In the following the same analysis as shown above will be
executed.

``` python
import pandas as pd

data = pd.read_json("https://johannesfriedrich.github.io/LottoNumberArchive/Lottonumbers_tidy_complete.json",orient='split')

res = data[data.variable == "Lottozahl"].groupby("value")["value"].count().sort_values(ascending = False)

print(res.head(5))
## value
## 6     593
## 32    573
## 49    571
## 38    568
## 26    567
## Name: value, dtype: int64
```
