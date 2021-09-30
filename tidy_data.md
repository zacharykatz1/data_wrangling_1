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

## Binding rows

Import the LOTR movie words stuff

``` r
# Load LOTR data
fellowship_df = 
  read_excel("Data/LotR_Words.xlsx",
             range = "B3:D6"
  ) %>% 
  mutate(movie = "fellowship_rings")
fellowship_df
```

    ## # A tibble: 3 × 4
    ##   Race   Female  Male movie           
    ##   <chr>   <dbl> <dbl> <chr>           
    ## 1 Elf      1229   971 fellowship_rings
    ## 2 Hobbit     14  3644 fellowship_rings
    ## 3 Man         0  1995 fellowship_rings

``` r
two_towers_df = 
  read_excel("Data/LotR_Words.xlsx",
             range = "F3:H6"
  ) %>% 
  mutate(movie = "two_towers")
two_towers_df
```

    ## # A tibble: 3 × 4
    ##   Race   Female  Male movie     
    ##   <chr>   <dbl> <dbl> <chr>     
    ## 1 Elf       331   513 two_towers
    ## 2 Hobbit      0  2463 two_towers
    ## 3 Man       401  3589 two_towers

``` r
return_king_df = 
  read_excel("Data/LotR_Words.xlsx",
             range = "J3:L6"
  ) %>% 
  mutate(movie = "return_kind")
return_king_df
```

    ## # A tibble: 3 × 4
    ##   Race   Female  Male movie      
    ##   <chr>   <dbl> <dbl> <chr>      
    ## 1 Elf       183   510 return_kind
    ## 2 Hobbit      2  2673 return_kind
    ## 3 Man       268  2459 return_kind

``` r
# Bind rows into one df, then pivot longer
lotr_df = 
  # Use bind_rows over rbind; it is better
  bind_rows(fellowship_df, two_towers_df, return_king_df) %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    female:male,
    names_to = "sex",
    values_to = "words"
  ) %>% 
  # Put movie as first variable
  relocate(movie)
lotr_df
```

    ## # A tibble: 18 × 4
    ##    movie            race   sex    words
    ##    <chr>            <chr>  <chr>  <dbl>
    ##  1 fellowship_rings Elf    female  1229
    ##  2 fellowship_rings Elf    male     971
    ##  3 fellowship_rings Hobbit female    14
    ##  4 fellowship_rings Hobbit male    3644
    ##  5 fellowship_rings Man    female     0
    ##  6 fellowship_rings Man    male    1995
    ##  7 two_towers       Elf    female   331
    ##  8 two_towers       Elf    male     513
    ##  9 two_towers       Hobbit female     0
    ## 10 two_towers       Hobbit male    2463
    ## 11 two_towers       Man    female   401
    ## 12 two_towers       Man    male    3589
    ## 13 return_kind      Elf    female   183
    ## 14 return_kind      Elf    male     510
    ## 15 return_kind      Hobbit female     2
    ## 16 return_kind      Hobbit male    2673
    ## 17 return_kind      Man    female   268
    ## 18 return_kind      Man    male    2459

## Joins

Look at FAS data

``` r
# Read in litters data and start tidying
litters_df =
  read_csv("Data/FAS_litters.csv") %>% 
  janitor::clean_names() %>% 
  # Split on 3rd position; right now, Con7, eg, is two variables smushed together
  separate(group, into = c("dose", "day_of_treatment"), 3) %>% 
  # Move litter_number first since it'll be key to another data set
  relocate(litter_number) %>% 
  mutate(dose = str_to_lower(dose))
```

    ## Rows: 49 Columns: 8

    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...

    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
litters_df
```

    ## # A tibble: 49 × 9
    ##    litter_number   dose  day_of_treatment gd0_weight gd18_weight gd_of_birth
    ##    <chr>           <chr> <chr>                 <dbl>       <dbl>       <dbl>
    ##  1 #85             con   7                      19.7        34.7          20
    ##  2 #1/2/95/2       con   7                      27          42            19
    ##  3 #5/5/3/83/3-3   con   7                      26          41.4          19
    ##  4 #5/4/2/95/2     con   7                      28.5        44.1          19
    ##  5 #4/2/95/3-3     con   7                      NA          NA            20
    ##  6 #2/2/95/3-2     con   7                      NA          NA            20
    ##  7 #1/5/3/83/3-3/2 con   7                      NA          NA            20
    ##  8 #3/83/3-3       con   8                      NA          NA            20
    ##  9 #2/95/3         con   8                      NA          NA            20
    ## 10 #3/5/2/2/95     con   8                      28.5        NA            20
    ## # … with 39 more rows, and 3 more variables: pups_born_alive <dbl>,
    ## #   pups_dead_birth <dbl>, pups_survive <dbl>

``` r
# Read in pups data
pups_df = 
  read_csv("Data/FAS_pups.csv") %>% 
  janitor::clean_names() %>% 
  # Replace 1 and 0 with male/female in sex variable
  # Use backticks around 1 and 2 to recognize them as variables, not numbers
  mutate(sex = recode(sex, `1` = "male", `2` = "female"))
```

    ## Rows: 313 Columns: 6

    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk

    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pups_df
```

    ## # A tibble: 313 × 6
    ##    litter_number sex   pd_ears pd_eyes pd_pivot pd_walk
    ##    <chr>         <chr>   <dbl>   <dbl>    <dbl>   <dbl>
    ##  1 #85           male        4      13        7      11
    ##  2 #85           male        4      13        7      12
    ##  3 #1/2/95/2     male        5      13        7       9
    ##  4 #1/2/95/2     male        5      13        8      10
    ##  5 #5/5/3/83/3-3 male        5      13        8      10
    ##  6 #5/5/3/83/3-3 male        5      14        6       9
    ##  7 #5/4/2/95/2   male       NA      14        5       9
    ##  8 #4/2/95/3-3   male        4      13        6       8
    ##  9 #4/2/95/3-3   male        4      13        7       9
    ## 10 #2/2/95/3-2   male        4      NA        8      10
    ## # … with 303 more rows

Now let’s join these up. We want to eventually figure out: is there
something about dose that tells me something about postnatal day on
which something happens?

``` r
fas_df = 
  left_join(pups_df, litters_df, by = "litter_number") %>% 
  relocate(litter_number, dose, day_of_treatment)
fas_df
```

    ## # A tibble: 313 × 14
    ##    litter_number dose  day_of_treatment sex   pd_ears pd_eyes pd_pivot pd_walk
    ##    <chr>         <chr> <chr>            <chr>   <dbl>   <dbl>    <dbl>   <dbl>
    ##  1 #85           con   7                male        4      13        7      11
    ##  2 #85           con   7                male        4      13        7      12
    ##  3 #1/2/95/2     con   7                male        5      13        7       9
    ##  4 #1/2/95/2     con   7                male        5      13        8      10
    ##  5 #5/5/3/83/3-3 con   7                male        5      13        8      10
    ##  6 #5/5/3/83/3-3 con   7                male        5      14        6       9
    ##  7 #5/4/2/95/2   con   7                male       NA      14        5       9
    ##  8 #4/2/95/3-3   con   7                male        4      13        6       8
    ##  9 #4/2/95/3-3   con   7                male        4      13        7       9
    ## 10 #2/2/95/3-2   con   7                male        4      NA        8      10
    ## # … with 303 more rows, and 6 more variables: gd0_weight <dbl>,
    ## #   gd18_weight <dbl>, gd_of_birth <dbl>, pups_born_alive <dbl>,
    ## #   pups_dead_birth <dbl>, pups_survive <dbl>

Note for later: never use gather, just use pivot\_longer. Also: never
use spread, just use pivot\_wider.
