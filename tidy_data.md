Tidy Data
================

## Pivot longer

``` r
# Import data
pulse_df = 
  read_sas("Data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names()

pulse_df
```

    ## # A tibble: 1,087 × 7
    ##       id   age sex    bdi_score_bl bdi_score_01m bdi_score_06m bdi_score_12m
    ##    <dbl> <dbl> <chr>         <dbl>         <dbl>         <dbl>         <dbl>
    ##  1 10003  48.0 male              7             1             2             0
    ##  2 10015  72.5 male              6            NA            NA            NA
    ##  3 10022  58.5 male             14             3             8            NA
    ##  4 10026  72.7 male             20             6            18            16
    ##  5 10035  60.4 male              4             0             1             2
    ##  6 10050  84.7 male              2            10            12             8
    ##  7 10078  31.3 male              4             0            NA            NA
    ##  8 10088  56.9 male              5            NA             0             2
    ##  9 10091  76.0 male              0             3             4             0
    ## 10 10092  74.2 female           10             2            11             6
    ## # … with 1,077 more rows

``` r
# Pivot longer
pulse_tidy = 
  pulse_df %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    # Get rid of bdi_score_ bit before the value for visit variable
    names_prefix = "bdi_score_",
    values_to = "bdi_score"
  ) %>% 
  mutate(
    # In this variable, look for every instance where variable == bl, and everywhere that condition is true, replace with 00m
    visit = replace(visit, visit == "bl", "00m"),
    # Often easier to think of categoricals as factors, rather than character variables
    visit = factor(visit)
  )

pulse_tidy
```

    ## # A tibble: 4,348 × 5
    ##       id   age sex   visit bdi_score
    ##    <dbl> <dbl> <chr> <fct>     <dbl>
    ##  1 10003  48.0 male  00m           7
    ##  2 10003  48.0 male  01m           1
    ##  3 10003  48.0 male  06m           2
    ##  4 10003  48.0 male  12m           0
    ##  5 10015  72.5 male  00m           6
    ##  6 10015  72.5 male  01m          NA
    ##  7 10015  72.5 male  06m          NA
    ##  8 10015  72.5 male  12m          NA
    ##  9 10022  58.5 male  00m          14
    ## 10 10022  58.5 male  01m           3
    ## # … with 4,338 more rows

## Pivot wider

Make up a results data table

``` r
analysis_df = 
  tibble(
    group = c("treatment", "treatment", "control", "control"),
    time = c("a", "b", "a", "b"),
    group_mean = c(4, 8, 3, 6)
  )
analysis_df
```

    ## # A tibble: 4 × 3
    ##   group     time  group_mean
    ##   <chr>     <chr>      <dbl>
    ## 1 treatment a              4
    ## 2 treatment b              8
    ## 3 control   a              3
    ## 4 control   b              6

``` r
## Pivot wider

analysis_df %>% 
  pivot_wider(
    names_from = "time",
    values_from = "group_mean"
  ) %>% 
  # Use format more human readable once knit
  knitr::kable()
```

| group     |   a |   b |
|:----------|----:|----:|
| treatment |   4 |   8 |
| control   |   3 |   6 |
