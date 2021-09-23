Simple document
================

## Import some data

I want to import `FAS_litters.csv`.

``` r
litters_df = read_csv("data/FAS_litters.csv")
```

Yay! I imported the dataset. Now I want better names.

``` r
litters_df = janitor::clean_names(litters_df)

names(litters_df)
```

    ## [1] "group"           "litter_number"   "gd0_weight"      "gd18_weight"    
    ## [5] "gd_of_birth"     "pups_born_alive" "pups_dead_birth" "pups_survive"

Yay! Now I have better names. Let’s look at the dataset.

``` r
litters_df
```

    ## # A tibble: 49 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2             27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  8 Con8  #3/83/3-3             NA          NA            20               9
    ##  9 Con8  #2/95/3               NA          NA            20               8
    ## 10 Con8  #3/5/2/2/95           28.5        NA            20               8
    ## # … with 39 more rows, and 2 more variables: pups_dead_birth <dbl>,
    ## #   pups_survive <dbl>

``` r
head(litters_df)
```

    ## # A tibble: 6 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## 4 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ## 5 Con7  #4/2/95/3-3         NA          NA            20               6
    ## 6 Con7  #2/2/95/3-2         NA          NA            20               6
    ## # … with 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
tail(litters_df)
```

    ## # A tibble: 6 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Low8  #79                 25.4        43.8          19               8
    ## 2 Low8  #100                20          39.2          20               8
    ## 3 Low8  #4/84               21.8        35.2          20               4
    ## 4 Low8  #108                25.6        47.5          20               8
    ## 5 Low8  #99                 23.5        39            20               6
    ## 6 Low8  #110                25.5        42.7          20               7
    ## # … with 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

One I use a lot is:

``` r
View(litters_df)
```

Here’s `skimr`:

``` r
skimr::skim(litters_df)
```

|                                                  |             |
|:-------------------------------------------------|:------------|
| Name                                             | litters\_df |
| Number of rows                                   | 49          |
| Number of columns                                | 8           |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |             |
| Column type frequency:                           |             |
| character                                        | 2           |
| numeric                                          | 6           |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |             |
| Group variables                                  | None        |

Data summary

**Variable type: character**

| skim\_variable | n\_missing | complete\_rate | min | max | empty | n\_unique | whitespace |
|:---------------|-----------:|---------------:|----:|----:|------:|----------:|-----------:|
| group          |          0 |              1 |   4 |   4 |     0 |         6 |          0 |
| litter\_number |          0 |              1 |   3 |  15 |     0 |        49 |          0 |

**Variable type: numeric**

| skim\_variable    | n\_missing | complete\_rate |  mean |   sd |   p0 |   p25 |   p50 |   p75 | p100 | hist  |
|:------------------|-----------:|---------------:|------:|-----:|-----:|------:|------:|------:|-----:|:------|
| gd0\_weight       |         15 |           0.69 | 24.38 | 3.28 | 17.0 | 22.30 | 24.10 | 26.67 | 33.4 | ▃▇▇▆▁ |
| gd18\_weight      |         17 |           0.65 | 41.52 | 4.05 | 33.4 | 38.88 | 42.25 | 43.80 | 52.7 | ▃▃▇▂▁ |
| gd\_of\_birth     |          0 |           1.00 | 19.65 | 0.48 | 19.0 | 19.00 | 20.00 | 20.00 | 20.0 | ▅▁▁▁▇ |
| pups\_born\_alive |          0 |           1.00 |  7.35 | 1.76 |  3.0 |  6.00 |  8.00 |  8.00 | 11.0 | ▁▃▂▇▁ |
| pups\_dead\_birth |          0 |           1.00 |  0.33 | 0.75 |  0.0 |  0.00 |  0.00 |  0.00 |  4.0 | ▇▂▁▁▁ |
| pups\_survive     |          0 |           1.00 |  6.41 | 2.05 |  1.0 |  5.00 |  7.00 |  8.00 |  9.0 | ▁▃▂▇▇ |

## Arguments in `read_csv`

Note that in class we reviewed the various arguments read\_csv could
take, e.g. skip, na, etc. Review these!

Let’s try specifying column types:

``` r
litters_data = read_csv(file = "Data/FAS_litters.csv",
  col_types = cols(
    Group = col_character(),
    `Litter Number` = col_character(),
    `GD0 weight` = col_double(),
    `GD18 weight` = col_double(),
    `GD of Birth` = col_integer(),
    `Pups born alive` = col_integer(),
    `Pups dead @ birth` = col_integer(),
    `Pups survive` = col_integer()
  )
)
tail(litters_data)
```

    ## # A tibble: 6 × 8
    ##   Group `Litter Number` `GD0 weight` `GD18 weight` `GD of Birth`
    ##   <chr> <chr>                  <dbl>         <dbl>         <int>
    ## 1 Low8  #79                     25.4          43.8            19
    ## 2 Low8  #100                    20            39.2            20
    ## 3 Low8  #4/84                   21.8          35.2            20
    ## 4 Low8  #108                    25.6          47.5            20
    ## 5 Low8  #99                     23.5          39              20
    ## 6 Low8  #110                    25.5          42.7            20
    ## # … with 3 more variables: Pups born alive <int>, Pups dead @ birth <int>,
    ## #   Pups survive <int>

## Reading from excel

``` r
mlb11_df = read_excel("Data/mlb11.xlsx")
mlb11_df
```

    ## # A tibble: 30 × 12
    ##    team        runs at_bats  hits homeruns bat_avg strikeouts stolen_bases  wins
    ##    <chr>      <dbl>   <dbl> <dbl>    <dbl>   <dbl>      <dbl>        <dbl> <dbl>
    ##  1 Texas Ran…   855    5659  1599      210   0.283        930          143    96
    ##  2 Boston Re…   875    5710  1600      203   0.28        1108          102    90
    ##  3 Detroit T…   787    5563  1540      169   0.277       1143           49    95
    ##  4 Kansas Ci…   730    5672  1560      129   0.275       1006          153    71
    ##  5 St. Louis…   762    5532  1513      162   0.273        978           57    90
    ##  6 New York …   718    5600  1477      108   0.264       1085          130    77
    ##  7 New York …   867    5518  1452      222   0.263       1138          147    97
    ##  8 Milwaukee…   721    5447  1422      185   0.261       1083           94    96
    ##  9 Colorado …   735    5544  1429      163   0.258       1201          118    73
    ## 10 Houston A…   615    5598  1442       95   0.258       1164          118    56
    ## # … with 20 more rows, and 3 more variables: new_onbase <dbl>, new_slug <dbl>,
    ## #   new_obs <dbl>
