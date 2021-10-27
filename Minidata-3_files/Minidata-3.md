Mini Data-Analysis Deliverable 3
================
Maira Jimenez
2021-10-26

## Introduction

# Welcome to your last milestone in your mini data analysis project!

In Milestone 1, you explored your data and came up with research
questions. In Milestone 2, you obtained some results by making summary
tables and graphs.

In this (3rd) milestone, you’ll be sharpening some of the results you
obtained from your previous milestone by:

-   Manipulating special data types in R: factors and/or dates and
    times.
-   Fitting a model object to your data, and extract a result.
-   Reading and writing data as separate files.

\*\*NOTE\*\*: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

## Instructions

\*\*To complete this milestone\*\*, edit \[this very \`.Rmd\`
file\](<https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-3.Rmd)>
directly. Fill in the sections that are tagged with \`\<!— start your
work here—>\`.

\*\*To submit this milestone\*\*, make sure to knit this \`.Rmd\` file
to an \`.md\` file by changing the YAML output settings from \`output:
html_document\` to \`output: github_document\`. Commit and push all of
your work to your mini-analysis GitHub repository, and tag a release on
GitHub. Then, submit a link to your tagged release on canvas.

\*\*Points\*\*: This milestone is worth 40 points (compared to the usual
30 points): 30 for your analysis, and 10 for your entire mini-analysis
GitHub repository. Details follow.

\*\*Research Questions\*\*: In Milestone 2, you chose two research
questions to focus on. Wherever realistic, your work in this milestone
should relate to these research questions whenever we ask for
justification behind your work. In the case that some tasks in this
milestone don’t align well with one of your research questions, feel
free to discuss your results in the context of a different research
question.

# Setup

Begin by loading your data and the tidyverse package below:

``` r
library(datateachr) # <- contain the data I picked!
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.4     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   2.0.1     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(tsibble)
```

    ## 
    ## Attaching package: 'tsibble'

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, union

``` r
library(digest)
library(ggplot2)
library(hrbrthemes)
```

    ## NOTE: Either Arial Narrow or Roboto Condensed fonts are required to use these themes.

    ##       Please use hrbrthemes::import_roboto_condensed() to install Roboto Condensed and

    ##       if Arial Narrow is not on your system, please see https://bit.ly/arialnarrow

``` r
library(gtsummary)
library(forcats)
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following object is masked from 'package:tsibble':
    ## 
    ##     interval

    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

From Milestone 2, you chose two research questions. What were they? Put
them here.

<!-------------------------- Start your work below ---------------------------->

**Research question 1**

Within the ACER genus, I would like to know if there is any relationship
between the species and the diameter? And also if there is a
relationship with the fact that some trees have a root barrier.

Perhaps some species of trees typically have a larger diameter. Since I
only focused on STRATHCONA Neighborhood.

**Research question 2**

Considering the results from `Research question 2,` I choose three
`species_names` and I analyze the relationship between the `diameter`and
the `date_planted`. The question I would like to answer is: can I find
any correlations between these two values? I hypothesize that if the
trees are older, the diameter would be larger.

<!----------------------------------------------------------------------------->

# Exercise 1: Special Data Types (10)

For this exercise, you’ll be choosing two of the three tasks below –
both tasks that you choose are worth 5 points each.

But first, tasks 1 and 2 below ask you to modify a plot you made in a
previous milestone. The plot you choose should involve plotting across
at least three groups (whether by facetting, or using an aesthetic like
colour). Place this plot below (you’re allowed to modify the plot if
you’d like). If you don’t have such a plot, you’ll need to make one.
Place the code for your plot below.

<!-------------------------- Start your work below ---------------------------->

``` r
vancouver_trees
```

    ## # A tibble: 146,611 × 20
    ##    tree_id civic_number std_street    genus_name species_name cultivar_name  
    ##      <dbl>        <dbl> <chr>         <chr>      <chr>        <chr>          
    ##  1  149556          494 W 58TH AV     ULMUS      AMERICANA    BRANDON        
    ##  2  149563          450 W 58TH AV     ZELKOVA    SERRATA      <NA>           
    ##  3  149579         4994 WINDSOR ST    STYRAX     JAPONICA     <NA>           
    ##  4  149590          858 E 39TH AV     FRAXINUS   AMERICANA    AUTUMN APPLAUSE
    ##  5  149604         5032 WINDSOR ST    ACER       CAMPESTRE    <NA>           
    ##  6  149616          585 W 61ST AV     PYRUS      CALLERYANA   CHANTICLEER    
    ##  7  149617         4909 SHERBROOKE ST ACER       PLATANOIDES  COLUMNARE      
    ##  8  149618         4925 SHERBROOKE ST ACER       PLATANOIDES  COLUMNARE      
    ##  9  149619         4969 SHERBROOKE ST ACER       PLATANOIDES  COLUMNARE      
    ## 10  149625          720 E 39TH AV     FRAXINUS   AMERICANA    AUTUMN APPLAUSE
    ## # … with 146,601 more rows, and 14 more variables: common_name <chr>,
    ## #   assigned <chr>, root_barrier <chr>, plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   date_planted <date>, longitude <dbl>, latitude <dbl>

``` r
(STRATHCONA_trees<-vancouver_trees %>%
  filter(neighbourhood_name %in% ("STRATHCONA") & genus_name %in% ("ACER")))
```

    ## # A tibble: 536 × 20
    ##    tree_id civic_number std_street  genus_name species_name cultivar_name
    ##      <dbl>        <dbl> <chr>       <chr>      <chr>        <chr>        
    ##  1  157725          920 STATION ST  ACER       PLATANOIDES  <NA>         
    ##  2  157737          739 CAMPBELL AV ACER       CAMPESTRE    <NA>         
    ##  3  157738          814 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  4  157746          868 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  5  157762          886 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  6  157772          503 E PENDER ST ACER       RUBRUM       BOWHALL      
    ##  7  157797          603 RAYMUR AV   ACER       CAMPESTRE    <NA>         
    ##  8  157885          439 HEATLEY AV  ACER       PLATANOIDES  GLOBOSUM     
    ##  9  157898          414 CAMPBELL AV ACER       RUBRUM       BOWHALL      
    ## 10  157899          500 CAMPBELL AV ACER       RUBRUM       BOWHALL      
    ## # … with 526 more rows, and 14 more variables: common_name <chr>,
    ## #   assigned <chr>, root_barrier <chr>, plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   date_planted <date>, longitude <dbl>, latitude <dbl>

``` r
Strathcona_dates <- STRATHCONA_trees %>%
   mutate(date_planted = yearmonth(date_planted))

(Strathcona_years <- Strathcona_dates%>% 
  separate(date_planted, into = c("Year", "Month"), sep=" "))
```

    ## # A tibble: 536 × 21
    ##    tree_id civic_number std_street  genus_name species_name cultivar_name
    ##      <dbl>        <dbl> <chr>       <chr>      <chr>        <chr>        
    ##  1  157725          920 STATION ST  ACER       PLATANOIDES  <NA>         
    ##  2  157737          739 CAMPBELL AV ACER       CAMPESTRE    <NA>         
    ##  3  157738          814 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  4  157746          868 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  5  157762          886 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  6  157772          503 E PENDER ST ACER       RUBRUM       BOWHALL      
    ##  7  157797          603 RAYMUR AV   ACER       CAMPESTRE    <NA>         
    ##  8  157885          439 HEATLEY AV  ACER       PLATANOIDES  GLOBOSUM     
    ##  9  157898          414 CAMPBELL AV ACER       RUBRUM       BOWHALL      
    ## 10  157899          500 CAMPBELL AV ACER       RUBRUM       BOWHALL      
    ## # … with 526 more rows, and 15 more variables: common_name <chr>,
    ## #   assigned <chr>, root_barrier <chr>, plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   Year <chr>, Month <chr>, longitude <dbl>, latitude <dbl>

``` r
Strathcona_years_level <- Strathcona_years  %>%
  filter(species_name==c("RUBRUM", "GINNALA", "PLATANOIDES")) %>%
  mutate(diameter_level = case_when(diameter < 5.00 ~ "a) very low",
                                 diameter < 10.00 ~ "b) low",
                                 diameter < 30.00 ~ "c) moderate",
                                 diameter < 40.00 ~ "d) high",
                                 TRUE ~ "very high")) %>% 
  mutate(age_level = case_when(Year < 1996 ~ "a) more than 25 years",
                                 Year < 2006 ~ "b) between 15-25 years old",
                                 Year < 2016 ~ "c) between 5-15 years old",
                                 TRUE ~ "d) recent"))
```

    ## Warning in species_name == c("RUBRUM", "GINNALA", "PLATANOIDES"): longer object
    ## length is not a multiple of shorter object length

``` r
ggplot(Strathcona_years_level) + geom_boxplot(aes(x = diameter, y = age_level)) +
  labs(y = "How old are they", x = "Diameter") +
  ggtitle("Correlation diameter vs age") + theme_bw() + geom_jitter(aes(x = diameter, y = age_level, color=root_barrier)) + facet_grid(cols = vars(species_name))
```

![](Minidata-3_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

<!----------------------------------------------------------------------------->

Now, choose two of the following tasks.

1.  Produce a new plot that reorders a factor in your original plot,
    using the \`forcats\` package (3 points). Then, in a sentence or
    two, briefly explain why you chose this ordering (1 point here for
    demonstrating understanding of the reordering, and 1 point for
    demonstrating some justification for the reordering, which could be
    subtle or speculative.)

2.  Produce a new plot that groups some factor levels together into an
    “other” category (or something similar), using the \`forcats\`
    package (3 points). Then, in a sentence or two, briefly explain why
    you chose this grouping (1 point here for demonstrating
    understanding of the grouping, and 1 point for demonstrating some
    justification for the grouping, which could be subtle or
    speculative.)

3.  If your data has some sort of time-based column like a date (but
    something more granular than just a year):

4.  Make a new column that uses a function from the \`lubridate\` or
    \`tsibble\` package to modify your original time-based column. (3
    points)

-   Note that you might first have to \_make\_ a time-based column using
    a function like \`ymd()\`, but this doesn’t count.
-   Examples of something you might do here: extract the day of the year
    from a date, or extract the weekday, or let 24 hours elapse on your
    dates.

1.  Then, in a sentence or two, explain how your new column might be
    useful in exploring a research question. (1 point for demonstrating
    understanding of the function you used, and 1 point for your
    justification, which could be subtle or speculative).

-   For example, you could say something like “Investigating the day of
    the week might be insightful because penguins don’t work on
    weekends, and so may respond differently”.

<!-------------------------- Start your work below ---------------------------->

\*\*Task Number\*\*: 1

``` r
(Strathcona_years_level <- Strathcona_years  %>%
  filter(species_name==c("RUBRUM", "GINNALA", "PLATANOIDES")) %>%
  mutate(diameter_level = factor(case_when(diameter < 5.00 ~ "very low",
                                 diameter < 10.00 ~ "low",
                                 diameter < 30.00 ~ "moderate",
                                 diameter < 40.00 ~ "high",
                                 TRUE ~ "very high"), levels= c("very low", "low", "moderate", "high", "very high"))) %>% 
  mutate(age_level = factor(case_when(Year < 1996 ~ "more than 25 years",
                                 Year < 2006 ~ "between 15-25 years old",
                                 Year < 2016 ~ "between 5-15 years old",
                                 TRUE ~ "recent"), levels=c("more than 25 years", "between 15-25 years old","between 5-15 years old", "recent"))))
```

    ## Warning in species_name == c("RUBRUM", "GINNALA", "PLATANOIDES"): longer object
    ## length is not a multiple of shorter object length

    ## # A tibble: 100 × 23
    ##    tree_id civic_number std_street    genus_name species_name cultivar_name
    ##      <dbl>        <dbl> <chr>         <chr>      <chr>        <chr>        
    ##  1  157899          500 CAMPBELL AV   ACER       RUBRUM       BOWHALL      
    ##  2   33061          350 E 2ND AV      ACER       RUBRUM       <NA>         
    ##  3  229713          408 JACKSON AV    ACER       RUBRUM       AUTUMN FLAME 
    ##  4  182056          415 ALEXANDER ST  ACER       RUBRUM       AUTUMN FLAME 
    ##  5  182464          852 E HASTINGS ST ACER       RUBRUM       BOWHALL      
    ##  6  180664          550 E HASTINGS ST ACER       RUBRUM       BOWHALL      
    ##  7   69985          275 E CORDOVA ST  ACER       RUBRUM       <NA>         
    ##  8   13365          527 MALKIN AV     ACER       PLATANOIDES  SUPERFORM    
    ##  9  141337          738 EVANS AV      ACER       RUBRUM       BOWHALL      
    ## 10  140544          738 EVANS AV      ACER       RUBRUM       BOWHALL      
    ## # … with 90 more rows, and 17 more variables: common_name <chr>,
    ## #   assigned <chr>, root_barrier <chr>, plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   Year <chr>, Month <chr>, longitude <dbl>, latitude <dbl>,
    ## #   diameter_level <fct>, age_level <fct>

``` r
ggplot(Strathcona_years_level) + geom_boxplot(aes(x = diameter, y = age_level)) +
  labs(y = "How old are they", x = "Diameter") +
  ggtitle("Correlation diameter vs age") + theme_bw() + geom_jitter(aes(x = diameter, y = age_level, color=root_barrier)) + facet_grid(cols = vars(species_name))
```

![](Minidata-3_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

<!----------------------------------------------------------------------------->
<!-------------------------- Start your work below ---------------------------->

\*\*Task Number\*\*: 3

``` r
vancouver_trees
```

    ## # A tibble: 146,611 × 20
    ##    tree_id civic_number std_street    genus_name species_name cultivar_name  
    ##      <dbl>        <dbl> <chr>         <chr>      <chr>        <chr>          
    ##  1  149556          494 W 58TH AV     ULMUS      AMERICANA    BRANDON        
    ##  2  149563          450 W 58TH AV     ZELKOVA    SERRATA      <NA>           
    ##  3  149579         4994 WINDSOR ST    STYRAX     JAPONICA     <NA>           
    ##  4  149590          858 E 39TH AV     FRAXINUS   AMERICANA    AUTUMN APPLAUSE
    ##  5  149604         5032 WINDSOR ST    ACER       CAMPESTRE    <NA>           
    ##  6  149616          585 W 61ST AV     PYRUS      CALLERYANA   CHANTICLEER    
    ##  7  149617         4909 SHERBROOKE ST ACER       PLATANOIDES  COLUMNARE      
    ##  8  149618         4925 SHERBROOKE ST ACER       PLATANOIDES  COLUMNARE      
    ##  9  149619         4969 SHERBROOKE ST ACER       PLATANOIDES  COLUMNARE      
    ## 10  149625          720 E 39TH AV     FRAXINUS   AMERICANA    AUTUMN APPLAUSE
    ## # … with 146,601 more rows, and 14 more variables: common_name <chr>,
    ## #   assigned <chr>, root_barrier <chr>, plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   date_planted <date>, longitude <dbl>, latitude <dbl>

``` r
(STRATHCONA_trees<-vancouver_trees %>%
  filter(neighbourhood_name %in% ("STRATHCONA") & genus_name %in% ("ACER")))
```

    ## # A tibble: 536 × 20
    ##    tree_id civic_number std_street  genus_name species_name cultivar_name
    ##      <dbl>        <dbl> <chr>       <chr>      <chr>        <chr>        
    ##  1  157725          920 STATION ST  ACER       PLATANOIDES  <NA>         
    ##  2  157737          739 CAMPBELL AV ACER       CAMPESTRE    <NA>         
    ##  3  157738          814 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  4  157746          868 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  5  157762          886 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  6  157772          503 E PENDER ST ACER       RUBRUM       BOWHALL      
    ##  7  157797          603 RAYMUR AV   ACER       CAMPESTRE    <NA>         
    ##  8  157885          439 HEATLEY AV  ACER       PLATANOIDES  GLOBOSUM     
    ##  9  157898          414 CAMPBELL AV ACER       RUBRUM       BOWHALL      
    ## 10  157899          500 CAMPBELL AV ACER       RUBRUM       BOWHALL      
    ## # … with 526 more rows, and 14 more variables: common_name <chr>,
    ## #   assigned <chr>, root_barrier <chr>, plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   date_planted <date>, longitude <dbl>, latitude <dbl>

``` r
(Strathcona_dates <- STRATHCONA_trees %>%
    mutate(num_month =month(date_planted))%>%
   mutate(date_planted = yearmonth(date_planted)))
```

    ## # A tibble: 536 × 21
    ##    tree_id civic_number std_street  genus_name species_name cultivar_name
    ##      <dbl>        <dbl> <chr>       <chr>      <chr>        <chr>        
    ##  1  157725          920 STATION ST  ACER       PLATANOIDES  <NA>         
    ##  2  157737          739 CAMPBELL AV ACER       CAMPESTRE    <NA>         
    ##  3  157738          814 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  4  157746          868 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  5  157762          886 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  6  157772          503 E PENDER ST ACER       RUBRUM       BOWHALL      
    ##  7  157797          603 RAYMUR AV   ACER       CAMPESTRE    <NA>         
    ##  8  157885          439 HEATLEY AV  ACER       PLATANOIDES  GLOBOSUM     
    ##  9  157898          414 CAMPBELL AV ACER       RUBRUM       BOWHALL      
    ## 10  157899          500 CAMPBELL AV ACER       RUBRUM       BOWHALL      
    ## # … with 526 more rows, and 15 more variables: common_name <chr>,
    ## #   assigned <chr>, root_barrier <chr>, plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   date_planted <mth>, longitude <dbl>, latitude <dbl>, num_month <dbl>

``` r
(observation_month <- Strathcona_dates %>%
    group_by(num_month) %>%
    summarise(Count = n()))
```

    ## # A tibble: 12 × 2
    ##    num_month Count
    ##        <dbl> <int>
    ##  1         1    76
    ##  2         2    47
    ##  3         3    95
    ##  4         4    20
    ##  5         5     4
    ##  6         7     4
    ##  7         8     5
    ##  8         9     1
    ##  9        10     7
    ## 10        11    49
    ## 11        12    48
    ## 12        NA   180

<!----------------------------------------------------------------------------->

# Exercise 2: Modelling

## 2.0 (no points)

Pick a research question, and pick a variable of interest (we’ll call it
“Y”) that’s relevant to the research question. Indicate these.

<!-------------------------- Start your work below ---------------------------->

\*\*Research Question\*\*:

Choosing three `species_names` and I analyze the relationship between
the `diameter`and the `date_planted`. The question I would like to
answer is: can I find any correlations between these two values? I
hypothesize that if the trees are older, the diameter would be larger.

\*\*Variable of interest\*\*: Y=diameter

<!----------------------------------------------------------------------------->

## 2.1 (5 points)

Fit a model or run a hypothesis test that provides insight on this
variable with respect to the research question. Store the model object
as a variable, and print its output to screen. We’ll omit having to
justify your choice, because we don’t expect you to know about model
specifics in STAT 545.

-   \*\*Note\*\*: It’s OK if you don’t know how these models/tests work.
    Here are some examples of things you can do here, but the sky’s the
    limit.
-   You could fit a model that makes predictions on Y using another
    variable, by using the \`lm()\` function.
-   You could test whether the mean of Y equals 0 using \`t.test()\`, or
    maybe the mean across two groups are different using \`t.test()\`,
    or maybe the mean across multiple groups are different using
    \`anova()\` (you may have to pivot your data for the latter two).
-   You could use \`lm()\` to test for significance of regression.

<!-------------------------- Start your work below ---------------------------->

Considering the results from `Research question 2,` I choose three
`species_names` and I analyze the relationship between the `diameter`and
the `date_planted`. The question I would like to answer is: can I find
any correlations between these two values? I hypothesize that if the
trees are older, the diameter would be larger.

``` r
vancouver_trees
```

    ## # A tibble: 146,611 × 20
    ##    tree_id civic_number std_street    genus_name species_name cultivar_name  
    ##      <dbl>        <dbl> <chr>         <chr>      <chr>        <chr>          
    ##  1  149556          494 W 58TH AV     ULMUS      AMERICANA    BRANDON        
    ##  2  149563          450 W 58TH AV     ZELKOVA    SERRATA      <NA>           
    ##  3  149579         4994 WINDSOR ST    STYRAX     JAPONICA     <NA>           
    ##  4  149590          858 E 39TH AV     FRAXINUS   AMERICANA    AUTUMN APPLAUSE
    ##  5  149604         5032 WINDSOR ST    ACER       CAMPESTRE    <NA>           
    ##  6  149616          585 W 61ST AV     PYRUS      CALLERYANA   CHANTICLEER    
    ##  7  149617         4909 SHERBROOKE ST ACER       PLATANOIDES  COLUMNARE      
    ##  8  149618         4925 SHERBROOKE ST ACER       PLATANOIDES  COLUMNARE      
    ##  9  149619         4969 SHERBROOKE ST ACER       PLATANOIDES  COLUMNARE      
    ## 10  149625          720 E 39TH AV     FRAXINUS   AMERICANA    AUTUMN APPLAUSE
    ## # … with 146,601 more rows, and 14 more variables: common_name <chr>,
    ## #   assigned <chr>, root_barrier <chr>, plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   date_planted <date>, longitude <dbl>, latitude <dbl>

``` r
(RUBRUM_trees<-vancouver_trees %>%
  filter(neighbourhood_name %in% ("STRATHCONA") & species_name  %in% ("RUBRUM")))
```

    ## # A tibble: 194 × 20
    ##    tree_id civic_number std_street    genus_name species_name cultivar_name
    ##      <dbl>        <dbl> <chr>         <chr>      <chr>        <chr>        
    ##  1  157772          503 E PENDER ST   ACER       RUBRUM       BOWHALL      
    ##  2  157898          414 CAMPBELL AV   ACER       RUBRUM       BOWHALL      
    ##  3  157899          500 CAMPBELL AV   ACER       RUBRUM       BOWHALL      
    ##  4  163316          720 E HASTINGS ST ACER       RUBRUM       BOWHALL      
    ##  5  223300          640 E CORDOVA ST  ACER       RUBRUM       BOWHALL      
    ##  6   33048          350 E 2ND AV      ACER       RUBRUM       <NA>         
    ##  7   33061          350 E 2ND AV      ACER       RUBRUM       <NA>         
    ##  8  248032          601 E HASTINGS ST ACER       RUBRUM       BOWHALL      
    ##  9  229713          408 JACKSON AV    ACER       RUBRUM       AUTUMN FLAME 
    ## 10  229714          408 JACKSON AV    ACER       RUBRUM       AUTUMN FLAME 
    ## # … with 184 more rows, and 14 more variables: common_name <chr>,
    ## #   assigned <chr>, root_barrier <chr>, plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   date_planted <date>, longitude <dbl>, latitude <dbl>

``` r
(RUBRUM_year <- STRATHCONA_trees %>%
    mutate(year2 =year(date_planted)))
```

    ## # A tibble: 536 × 21
    ##    tree_id civic_number std_street  genus_name species_name cultivar_name
    ##      <dbl>        <dbl> <chr>       <chr>      <chr>        <chr>        
    ##  1  157725          920 STATION ST  ACER       PLATANOIDES  <NA>         
    ##  2  157737          739 CAMPBELL AV ACER       CAMPESTRE    <NA>         
    ##  3  157738          814 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  4  157746          868 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  5  157762          886 UNION ST    ACER       CAMPESTRE    <NA>         
    ##  6  157772          503 E PENDER ST ACER       RUBRUM       BOWHALL      
    ##  7  157797          603 RAYMUR AV   ACER       CAMPESTRE    <NA>         
    ##  8  157885          439 HEATLEY AV  ACER       PLATANOIDES  GLOBOSUM     
    ##  9  157898          414 CAMPBELL AV ACER       RUBRUM       BOWHALL      
    ## 10  157899          500 CAMPBELL AV ACER       RUBRUM       BOWHALL      
    ## # … with 526 more rows, and 15 more variables: common_name <chr>,
    ## #   assigned <chr>, root_barrier <chr>, plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   date_planted <date>, longitude <dbl>, latitude <dbl>, year2 <dbl>

``` r
min(RUBRUM_year$year2, na.rm = TRUE)
```

    ## [1] 1993

``` r
fitmodel <- lm(diameter~ I(year2-1993), RUBRUM_year)
fitmodel
```

    ## 
    ## Call:
    ## lm(formula = diameter ~ I(year2 - 1993), data = RUBRUM_year)
    ## 
    ## Coefficients:
    ##     (Intercept)  I(year2 - 1993)  
    ##          7.0929          -0.1847

``` r
unclass(fitmodel)
```

    ## $coefficients
    ##     (Intercept) I(year2 - 1993) 
    ##       7.0929252      -0.1847012 
    ## 
    ## $residuals
    ##           2           3           4           5           6           7 
    ##  1.77647716  3.27647716 -1.72352284 -2.97352284 -3.72352284 -2.72352284 
    ##           8           9          10          11          12          13 
    ## -2.72352284 -0.72352284  0.27647716  0.27647716 -3.72352284 -0.47352284 
    ##          14          15          16          17          20          23 
    ## -2.22352284  5.27647716 -0.72352284 -2.98471816 -1.50710878  3.10109825 
    ##          24          25          26          27          28          29 
    ##  2.10109825  4.10109825  2.10109825 -0.21420058 -0.95300527 -0.95300527 
    ##          30          31          32          33          34          35 
    ##  6.41639708 -0.76830410 -0.76830410 -0.76830410 -0.76830410 -0.76830410 
    ##          36          38          39          40          41          42 
    ## -0.76830410  0.40707481 -1.59292519  8.19998302  0.19998302  1.19998302 
    ##          43          44          45          47          48          51 
    ## -0.80001698 -2.80001698  3.19998302 -0.39890175 -0.39890175  1.10109825 
    ##          54          55          56          57          58          59 
    ## -0.39890175 -0.19180995  0.60109825 -0.87651113 -0.39890175 -0.39890175 
    ##          60          61          62          63          71          75 
    ## -1.13770644 -0.39890175 -0.39890175 -0.39890175 -0.39890175 -0.87651113 
    ##          76          77          78          79          80          81 
    ##  4.58058067  3.33058067 -0.87651113  1.33058067  2.83058067  4.33058067 
    ##          82          84          87         101         103         104 
    ## -0.76830410 -0.76830410 -1.16941933 -0.76830410 -0.13770644 -1.13770644 
    ##         111         112         113         114         115         116 
    ## -2.06121230 -0.95300527 -0.95300527 -0.76830410 -0.76830410 -0.76830410 
    ##         117         118         119         120         121         122 
    ## -0.76830410 -0.76830410 -1.69180995 -1.69180995 -1.69180995 -1.69180995 
    ##         123         124         125         126         127         128 
    ## -1.69180995 -0.69180995  2.49289122 -0.58360292  3.60109825 -0.21420058 
    ##         129         130         131         132         133         134 
    ##  2.60109825 -0.58360292 -0.58360292 -0.58360292 -0.58360292 -0.58360292 
    ##         135         136         137         144         149         150 
    ## -0.58360292 -0.39890175 -0.37651113  2.83058067 -0.66941933  9.19998302 
    ##         151         152         153         154         155         156 
    ##  9.19998302 -0.30001698 -0.30001698  2.62348887 -2.30001698  3.60109825 
    ##         157         158         159         160         161         162 
    ## -0.58360292 -0.58360292 -0.58360292 -0.58360292 -0.58360292 -0.21420058 
    ##         163         164         165         166         167         168 
    ## -0.39890175  3.35109825  2.10109825 -0.21420058 -2.06121230 -2.06121230 
    ##         169         170         171         172         173         177 
    ## -2.06121230 -2.06121230 -2.06121230 -2.06121230  5.12348887 -0.76830410 
    ##         185         186         187         188         189         190 
    ## -0.69180995 -1.69180995 -1.69180995 -1.13770644 -1.13770644 -1.13770644 
    ##         191         192         193         194         195         196 
    ## -1.13770644 -0.95300527 -0.76830410 -0.39890175 -0.39890175 -2.61531581 
    ##         197         202         203         204         205         206 
    ## -1.19180995 -0.39890175 -0.39890175 -0.39890175 -0.39890175 -0.39890175 
    ##         207         208         209         213         214         215 
    ## -0.21420058 -0.21420058 -0.21420058 -0.87651113  3.98058067  3.58058067 
    ##         216         217         218         219         229         230 
    ## -0.87651113  1.83058067  3.33058067  1.08058067  0.60109825  0.60109825 
    ##         231         232         233         234         238         239 
    ##  0.60109825 -2.59292519 -2.09292519 -1.13770644  2.83058067 -1.09292519 
    ##         241         244         245         246         247         248 
    ## -1.39890175  3.90707481  0.60109825  1.60109825  1.10109825  0.60109825 
    ##         249         250         251         252         253         254 
    ##  0.60109825  4.01528184 -1.87651113 -0.62651113 -0.87651113 -4.09292519 
    ##         258         259         260         261         263         269 
    ##  1.10109825  1.10109825 -1.13770644 -1.13770644 -3.03882167 -0.76830410 
    ##         270         271         272         273         274         275 
    ## -0.76830410 -0.76830410 -0.39890175 -0.39890175 -0.39890175 -0.14890175 
    ##         280         281         282         285         287         288 
    ## -2.06121230 -2.06121230 -2.06121230 -3.16941933 -3.53882167 -3.53882167 
    ##         289         290         291         292         293         294 
    ## -1.53882167  2.46117833  2.01528184 -3.16941933 -2.16941933  2.58058067 
    ##         295         307         308         309         310         311 
    ##  0.67759239  0.70930528 -0.39890175 -0.39890175 -0.39890175 -0.39890175 
    ##         312         313         314         315         317         322 
    ## -0.39890175  0.70930528 -4.09292519 -4.09292519  1.60109825  4.90707481 
    ##         323         324         325         327         328         329 
    ##  1.10109825  0.12348887 -0.87651113 -2.06121230 -2.06121230 -2.06121230 
    ##         330         331         332         333         334         335 
    ## -2.06121230 -2.06121230 -2.61531581  2.96117833 -0.53882167 -1.53882167 
    ##         336         339         340         342         343         344 
    ## -0.69180995 -3.16941933  2.83058067  0.51528184  1.51528184  1.51528184 
    ##         345         346         347         351         356         357 
    ## -2.98471816 -0.58360292 -0.58360292  1.60109825 -3.16941933 -3.16941933 
    ##         358         359         360         361         362         363 
    ##  4.83058067 12.33058067 -0.87651113  1.58058067  2.83058067  1.83058067 
    ##         364         366         367         368         370         371 
    ## -0.76830410  0.33990293  0.33990293  0.33990293  0.70930528  0.70930528 
    ##         372         373         374         384         385         386 
    ##  1.70930528  2.70930528  0.52460411  2.12348887 -2.06121230 -2.06121230 
    ##         387         388         390         391         392         393 
    ## -2.06121230 -2.06121230  0.42759239  2.36229356 -0.95300527 -1.13770644 
    ##         394         395         396         397         400         401 
    ## -0.95300527 -1.13770644 -1.43061464  1.56938536  1.09177598  0.02647716 
    ##         402         403         404         405         406         407 
    ##  0.77647716  4.27647716 -0.22352284 -1.72352284 -3.72352284  0.27647716 
    ##         408         409         410         411         412         413 
    ##  1.27647716  5.27647716  2.27647716  3.27647716 -0.76830410 -0.76830410 
    ##         414         415         416         417         418         419 
    ## -0.76830410 -0.76830410 -0.76830410 -0.76830410 -0.76830410 -1.69180995 
    ##         420         421         422         423         424         425 
    ## -1.69180995 -1.69180995 13.67759239 -1.50710878 -4.09292519 -2.59292519 
    ##         426         427         429         430         432         433 
    ##  0.40707481 -4.09292519 -3.09292519  4.14587950 -1.47352284  0.77647716 
    ##         434         435         436         437         438         439 
    ##  2.27647716 -3.72352284 -3.72352284  7.59177598 -3.72352284  6.77647716 
    ##         440         441         442         443         444         445 
    ## -0.72352284  0.27647716 -0.72352284 -0.72352284 -1.22352284 -0.47352284 
    ##         446         447         448         449         450         451 
    ##  3.27647716 -0.97352284  5.27647716  0.33990293  0.33990293  0.70930528 
    ##         455         456         457         458         461         462 
    ##  0.70930528  0.70930528  0.70930528  0.70930528  0.52460411  0.70930528 
    ##         463         468         470         471         474         482 
    ##  0.70930528 -1.69180995  0.15520176 -1.13770644 -0.95300527 -0.76830410 
    ##         485         486         487         488         489         490 
    ## -1.50710878 -1.50710878 -2.50710878 -2.50710878  0.23169590 -0.26830410 
    ##         491         492         493         494         495         496 
    ## -0.26830410 -0.01830410 -0.21420058 -0.58360292  1.60109825 -0.58360292 
    ##         497         498         499         500         501         502 
    ## -0.58360292 -0.58360292  0.47050059 -0.39890175 -0.21420058 -0.21420058 
    ##         504         505         507         509         510         511 
    ##  2.46117833  1.01528184 -1.03882167  1.33058067  1.33058067  1.83058067 
    ##         512         513         514         515         518         519 
    ## -1.66941933  1.01528184  0.33990293  0.70930528  0.70930528  0.70930528 
    ##         521         522         523         532         533         534 
    ##  0.52460411  0.52460411  0.70930528 -0.95300527 -1.32240761 -0.95300527 
    ##         535         536 
    ## -0.95300527 -1.13770644 
    ## 
    ## $effects
    ##     (Intercept) I(year2 - 1993)                                                 
    ##   -88.994772011   -27.040609898    -2.022769960    -3.272769960    -4.022769960 
    ##                                                                                 
    ##    -3.022769960    -3.022769960    -1.022769960    -0.022769960    -0.022769960 
    ##                                                                                 
    ##    -4.022769960    -0.772769960    -2.522769960     4.977230040    -1.022769960 
    ##                                                                                 
    ##    -3.202621268    -1.562323884     3.167899154     2.167899154     4.167899154 
    ##                                                                                 
    ##     2.167899154    -0.127063673    -0.947212365    -0.947212365     6.462861981 
    ##                                                                                 
    ##    -0.742175192    -0.742175192    -0.742175192    -0.742175192    -0.742175192 
    ##                                                                                 
    ##    -0.742175192     0.067155694    -1.932844306     8.002415905     0.002415905 
    ##                                                                                 
    ##     1.002415905    -0.997584095    -2.997584095     3.002415905    -0.332100846 
    ##                                                                                 
    ##    -0.332100846     1.167899154    -0.332100846    -0.267361057     0.667899154 
    ##                                                                                 
    ##    -0.972398230    -0.332100846    -0.332100846    -1.152249538    -0.332100846 
    ##                                                                                 
    ##    -0.332100846    -0.332100846    -0.332100846    -0.972398230     4.342341559 
    ##                                                                                 
    ##     3.092341559    -0.972398230     1.092341559     2.592341559     4.092341559 
    ##                                                                                 
    ##    -0.742175192    -0.742175192    -1.407658441    -0.742175192    -0.152249538 
    ##                                                                                 
    ##    -1.152249538    -2.177435403    -0.947212365    -0.947212365    -0.742175192 
    ##                                                                                 
    ##    -0.742175192    -0.742175192    -0.742175192    -0.742175192    -1.767361057 
    ##                                                                                 
    ##    -1.767361057    -1.767361057    -1.767361057    -1.767361057    -0.767361057 
    ##                                                                                 
    ##     2.437676116    -0.537138019     3.667899154    -0.127063673     2.667899154 
    ##                                                                                 
    ##    -0.537138019    -0.537138019    -0.537138019    -0.537138019    -0.537138019 
    ##                                                                                 
    ##    -0.537138019    -0.332100846    -0.472398230     2.592341559    -0.907658441 
    ##                                                                                 
    ##     9.002415905     9.002415905    -0.497584095    -0.497584095     2.527601770 
    ##                                                                                 
    ##    -2.497584095     3.667899154    -0.537138019    -0.537138019    -0.537138019 
    ##                                                                                 
    ##    -0.537138019    -0.537138019    -0.127063673    -0.332100846     3.417899154 
    ##                                                                                 
    ##     2.167899154    -0.127063673    -2.177435403    -2.177435403    -2.177435403 
    ##                                                                                 
    ##    -2.177435403    -2.177435403    -2.177435403     5.027601770    -0.742175192 
    ##                                                                                 
    ##    -0.767361057    -1.767361057    -1.767361057    -1.152249538    -1.152249538 
    ##                                                                                 
    ##    -1.152249538    -1.152249538    -0.947212365    -0.742175192    -0.332100846 
    ##                                                                                 
    ##    -0.332100846    -2.792546922    -1.267361057    -0.332100846    -0.332100846 
    ##                                                                                 
    ##    -0.332100846    -0.332100846    -0.332100846    -0.127063673    -0.127063673 
    ##                                                                                 
    ##    -0.127063673    -0.972398230     3.742341559     3.342341559    -0.972398230 
    ##                                                                                 
    ##     1.592341559     3.092341559     0.842341559     0.667899154     0.667899154 
    ##                                                                                 
    ##     0.667899154    -2.932844306    -2.432844306    -1.152249538     2.592341559 
    ##                                                                                 
    ##    -1.432844306    -1.332100846     3.567155694     0.667899154     1.667899154 
    ##                                                                                 
    ##     1.167899154     0.667899154     0.667899154     3.797378732    -1.972398230 
    ##                                                                                 
    ##    -0.722398230    -0.972398230    -4.432844306     1.167899154     1.167899154 
    ##                                                                                 
    ##    -1.152249538    -1.152249538    -3.317732787    -0.742175192    -0.742175192 
    ##                                                                                 
    ##    -0.742175192    -0.332100846    -0.332100846    -0.332100846    -0.082100846 
    ##                                                                                 
    ##    -2.177435403    -2.177435403    -2.177435403    -3.407658441    -3.817732787 
    ##                                                                                 
    ##    -3.817732787    -1.817732787     2.182267213     1.797378732    -3.407658441 
    ##                                                                                 
    ##    -2.407658441     2.342341559     0.642713289     0.898122192    -0.332100846 
    ##                                                                                 
    ##    -0.332100846    -0.332100846    -0.332100846    -0.332100846     0.898122192 
    ##                                                                                 
    ##    -4.432844306    -4.432844306     1.667899154     4.567155694     1.167899154 
    ##                                                                                 
    ##     0.027601770    -0.972398230    -2.177435403    -2.177435403    -2.177435403 
    ##                                                                                 
    ##    -2.177435403    -2.177435403    -2.792546922     2.682267213    -0.817732787 
    ##                                                                                 
    ##    -1.817732787    -0.767361057    -3.407658441     2.592341559     0.297378732 
    ##                                                                                 
    ##     1.297378732     1.297378732    -3.202621268    -0.537138019    -0.537138019 
    ##                                                                                 
    ##     1.667899154    -3.407658441    -3.407658441     4.592341559    12.092341559 
    ##                                                                                 
    ##    -0.972398230     1.342341559     2.592341559     1.592341559    -0.742175192 
    ##                                                                                 
    ##     0.488047846     0.488047846     0.488047846     0.898122192     0.898122192 
    ##                                                                                 
    ##     1.898122192     2.898122192     0.693085019     2.027601770    -2.177435403 
    ##                                                                                 
    ##    -2.177435403    -2.177435403    -2.177435403     0.392713289     2.347750462 
    ##                                                                                 
    ##    -0.947212365    -1.152249538    -0.947212365    -1.152249538    -1.587509749 
    ##                                                                                 
    ##     1.412490251     0.772192867    -0.272769960     0.477230040     3.977230040 
    ##                                                                                 
    ##    -0.522769960    -2.022769960    -4.022769960    -0.022769960     0.977230040 
    ##                                                                                 
    ##     4.977230040     1.977230040     2.977230040    -0.742175192    -0.742175192 
    ##                                                                                 
    ##    -0.742175192    -0.742175192    -0.742175192    -0.742175192    -0.742175192 
    ##                                                                                 
    ##    -1.767361057    -1.767361057    -1.767361057    13.642713289    -1.562323884 
    ##                                                                                 
    ##    -4.432844306    -2.932844306     0.067155694    -4.432844306    -3.432844306 
    ##                                                                                 
    ##     3.887304386    -1.772769960     0.477230040     1.977230040    -4.022769960 
    ##                                                                                 
    ##    -4.022769960     7.272192867    -4.022769960     6.477230040    -1.022769960 
    ##                                                                                 
    ##    -0.022769960    -1.022769960    -1.022769960    -1.522769960    -0.772769960 
    ##                                                                                 
    ##     2.977230040    -1.272769960     4.977230040     0.488047846     0.488047846 
    ##                                                                                 
    ##     0.898122192     0.898122192     0.898122192     0.898122192     0.898122192 
    ##                                                                                 
    ##     0.693085019     0.898122192     0.898122192    -1.767361057     0.283010673 
    ##                                                                                 
    ##    -1.152249538    -0.947212365    -0.742175192    -1.562323884    -1.562323884 
    ##                                                                                 
    ##    -2.562323884    -2.562323884     0.257824808    -0.242175192    -0.242175192 
    ##                                                                                 
    ##     0.007824808    -0.127063673    -0.537138019     1.667899154    -0.537138019 
    ##                                                                                 
    ##    -0.537138019    -0.537138019     0.577973500    -0.332100846    -0.127063673 
    ##                                                                                 
    ##    -0.127063673     2.182267213     0.797378732    -1.317732787     1.092341559 
    ##                                                                                 
    ##     1.092341559     1.592341559    -1.907658441     0.797378732     0.488047846 
    ##                                                                                 
    ##     0.898122192     0.898122192     0.898122192     0.693085019     0.693085019 
    ##                                                                                 
    ##     0.898122192    -0.947212365    -1.357286711    -0.947212365    -0.947212365 
    ##                 
    ##    -1.152249538 
    ## 
    ## $rank
    ## [1] 2
    ## 
    ## $fitted.values
    ##        2        3        4        5        6        7        8        9 
    ## 6.723523 6.723523 6.723523 6.723523 6.723523 6.723523 6.723523 6.723523 
    ##       10       11       12       13       14       15       16       17 
    ## 6.723523 6.723523 6.723523 6.723523 6.723523 6.723523 6.723523 5.984718 
    ##       20       23       24       25       26       27       28       29 
    ## 4.507109 3.398902 3.398902 3.398902 3.398902 3.214201 3.953005 3.953005 
    ##       30       31       32       33       34       35       36       38 
    ## 3.583603 3.768304 3.768304 3.768304 3.768304 3.768304 3.768304 7.092925 
    ##       39       40       41       42       43       44       45       47 
    ## 7.092925 5.800017 5.800017 5.800017 5.800017 5.800017 5.800017 3.398902 
    ##       48       51       54       55       56       57       58       59 
    ## 3.398902 3.398902 3.398902 4.691810 3.398902 4.876511 3.398902 3.398902 
    ##       60       61       62       63       71       75       76       77 
    ## 4.137706 3.398902 3.398902 3.398902 3.398902 4.876511 6.169419 6.169419 
    ##       78       79       80       81       82       84       87      101 
    ## 4.876511 6.169419 6.169419 6.169419 3.768304 3.768304 6.169419 3.768304 
    ##      103      104      111      112      113      114      115      116 
    ## 4.137706 4.137706 5.061212 3.953005 3.953005 3.768304 3.768304 3.768304 
    ##      117      118      119      120      121      122      123      124 
    ## 3.768304 3.768304 4.691810 4.691810 4.691810 4.691810 4.691810 4.691810 
    ##      125      126      127      128      129      130      131      132 
    ## 4.507109 3.583603 3.398902 3.214201 3.398902 3.583603 3.583603 3.583603 
    ##      133      134      135      136      137      144      149      150 
    ## 3.583603 3.583603 3.583603 3.398902 4.876511 6.169419 6.169419 5.800017 
    ##      151      152      153      154      155      156      157      158 
    ## 5.800017 5.800017 5.800017 4.876511 5.800017 3.398902 3.583603 3.583603 
    ##      159      160      161      162      163      164      165      166 
    ## 3.583603 3.583603 3.583603 3.214201 3.398902 3.398902 3.398902 3.214201 
    ##      167      168      169      170      171      172      173      177 
    ## 5.061212 5.061212 5.061212 5.061212 5.061212 5.061212 4.876511 3.768304 
    ##      185      186      187      188      189      190      191      192 
    ## 4.691810 4.691810 4.691810 4.137706 4.137706 4.137706 4.137706 3.953005 
    ##      193      194      195      196      197      202      203      204 
    ## 3.768304 3.398902 3.398902 5.615316 4.691810 3.398902 3.398902 3.398902 
    ##      205      206      207      208      209      213      214      215 
    ## 3.398902 3.398902 3.214201 3.214201 3.214201 4.876511 6.169419 6.169419 
    ##      216      217      218      219      229      230      231      232 
    ## 4.876511 6.169419 6.169419 6.169419 3.398902 3.398902 3.398902 7.092925 
    ##      233      234      238      239      241      244      245      246 
    ## 7.092925 4.137706 6.169419 7.092925 3.398902 7.092925 3.398902 3.398902 
    ##      247      248      249      250      251      252      253      254 
    ## 3.398902 3.398902 3.398902 5.984718 4.876511 4.876511 4.876511 7.092925 
    ##      258      259      260      261      263      269      270      271 
    ## 3.398902 3.398902 4.137706 4.137706 6.538822 3.768304 3.768304 3.768304 
    ##      272      273      274      275      280      281      282      285 
    ## 3.398902 3.398902 3.398902 3.398902 5.061212 5.061212 5.061212 6.169419 
    ##      287      288      289      290      291      292      293      294 
    ## 6.538822 6.538822 6.538822 6.538822 5.984718 6.169419 6.169419 6.169419 
    ##      295      307      308      309      310      311      312      313 
    ## 4.322408 2.290695 3.398902 3.398902 3.398902 3.398902 3.398902 2.290695 
    ##      314      315      317      322      323      324      325      327 
    ## 7.092925 7.092925 3.398902 7.092925 3.398902 4.876511 4.876511 5.061212 
    ##      328      329      330      331      332      333      334      335 
    ## 5.061212 5.061212 5.061212 5.061212 5.615316 6.538822 6.538822 6.538822 
    ##      336      339      340      342      343      344      345      346 
    ## 4.691810 6.169419 6.169419 5.984718 5.984718 5.984718 5.984718 3.583603 
    ##      347      351      356      357      358      359      360      361 
    ## 3.583603 3.398902 6.169419 6.169419 6.169419 6.169419 4.876511 6.169419 
    ##      362      363      364      366      367      368      370      371 
    ## 6.169419 6.169419 3.768304 2.660097 2.660097 2.660097 2.290695 2.290695 
    ##      372      373      374      384      385      386      387      388 
    ## 2.290695 2.290695 2.475396 4.876511 5.061212 5.061212 5.061212 5.061212 
    ##      390      391      392      393      394      395      396      397 
    ## 4.322408 4.137706 3.953005 4.137706 3.953005 4.137706 5.430615 5.430615 
    ##      400      401      402      403      404      405      406      407 
    ## 6.908224 6.723523 6.723523 6.723523 6.723523 6.723523 6.723523 6.723523 
    ##      408      409      410      411      412      413      414      415 
    ## 6.723523 6.723523 6.723523 6.723523 3.768304 3.768304 3.768304 3.768304 
    ##      416      417      418      419      420      421      422      423 
    ## 3.768304 3.768304 3.768304 4.691810 4.691810 4.691810 4.322408 4.507109 
    ##      424      425      426      427      429      430      432      433 
    ## 7.092925 7.092925 7.092925 7.092925 7.092925 6.354121 6.723523 6.723523 
    ##      434      435      436      437      438      439      440      441 
    ## 6.723523 6.723523 6.723523 6.908224 6.723523 6.723523 6.723523 6.723523 
    ##      442      443      444      445      446      447      448      449 
    ## 6.723523 6.723523 6.723523 6.723523 6.723523 6.723523 6.723523 2.660097 
    ##      450      451      455      456      457      458      461      462 
    ## 2.660097 2.290695 2.290695 2.290695 2.290695 2.290695 2.475396 2.290695 
    ##      463      468      470      471      474      482      485      486 
    ## 2.290695 4.691810 2.844798 4.137706 3.953005 3.768304 4.507109 4.507109 
    ##      487      488      489      490      491      492      493      494 
    ## 4.507109 4.507109 3.768304 3.768304 3.768304 3.768304 3.214201 3.583603 
    ##      495      496      497      498      499      500      501      502 
    ## 3.398902 3.583603 3.583603 3.583603 3.029499 3.398902 3.214201 3.214201 
    ##      504      505      507      509      510      511      512      513 
    ## 6.538822 5.984718 6.538822 6.169419 6.169419 6.169419 6.169419 5.984718 
    ##      514      515      518      519      521      522      523      532 
    ## 2.660097 2.290695 2.290695 2.290695 2.475396 2.475396 2.290695 3.953005 
    ##      533      534      535      536 
    ## 4.322408 3.953005 3.953005 4.137706 
    ## 
    ## $assign
    ## [1] 0 1
    ## 
    ## $qr
    ## $qr
    ##      (Intercept) I(year2 - 1993)
    ## 2   -18.86796226   -2.427395e+02
    ## 3     0.05299989    1.464019e+02
    ## 4     0.05299989    7.047926e-02
    ## 5     0.05299989    7.047926e-02
    ## 6     0.05299989    7.047926e-02
    ## 7     0.05299989    7.047926e-02
    ## 8     0.05299989    7.047926e-02
    ## 9     0.05299989    7.047926e-02
    ## 10    0.05299989    7.047926e-02
    ## 11    0.05299989    7.047926e-02
    ## 12    0.05299989    7.047926e-02
    ## 13    0.05299989    7.047926e-02
    ## 14    0.05299989    7.047926e-02
    ## 15    0.05299989    7.047926e-02
    ## 16    0.05299989    7.047926e-02
    ## 17    0.05299989    4.315721e-02
    ## 20    0.05299989   -1.148687e-02
    ## 23    0.05299989   -5.246993e-02
    ## 24    0.05299989   -5.246993e-02
    ## 25    0.05299989   -5.246993e-02
    ## 26    0.05299989   -5.246993e-02
    ## 27    0.05299989   -5.930045e-02
    ## 28    0.05299989   -3.197840e-02
    ## 29    0.05299989   -3.197840e-02
    ## 30    0.05299989   -4.563942e-02
    ## 31    0.05299989   -3.880891e-02
    ## 32    0.05299989   -3.880891e-02
    ## 33    0.05299989   -3.880891e-02
    ## 34    0.05299989   -3.880891e-02
    ## 35    0.05299989   -3.880891e-02
    ## 36    0.05299989   -3.880891e-02
    ## 38    0.05299989    8.414028e-02
    ## 39    0.05299989    8.414028e-02
    ## 40    0.05299989    3.632670e-02
    ## 41    0.05299989    3.632670e-02
    ## 42    0.05299989    3.632670e-02
    ## 43    0.05299989    3.632670e-02
    ## 44    0.05299989    3.632670e-02
    ## 45    0.05299989    3.632670e-02
    ## 47    0.05299989   -5.246993e-02
    ## 48    0.05299989   -5.246993e-02
    ## 51    0.05299989   -5.246993e-02
    ## 54    0.05299989   -5.246993e-02
    ## 55    0.05299989   -4.656361e-03
    ## 56    0.05299989   -5.246993e-02
    ## 57    0.05299989    2.174150e-03
    ## 58    0.05299989   -5.246993e-02
    ## 59    0.05299989   -5.246993e-02
    ## 60    0.05299989   -2.514789e-02
    ## 61    0.05299989   -5.246993e-02
    ## 62    0.05299989   -5.246993e-02
    ## 63    0.05299989   -5.246993e-02
    ## 71    0.05299989   -5.246993e-02
    ## 75    0.05299989    2.174150e-03
    ## 76    0.05299989    4.998772e-02
    ## 77    0.05299989    4.998772e-02
    ## 78    0.05299989    2.174150e-03
    ## 79    0.05299989    4.998772e-02
    ## 80    0.05299989    4.998772e-02
    ## 81    0.05299989    4.998772e-02
    ## 82    0.05299989   -3.880891e-02
    ## 84    0.05299989   -3.880891e-02
    ## 87    0.05299989    4.998772e-02
    ## 101   0.05299989   -3.880891e-02
    ## 103   0.05299989   -2.514789e-02
    ## 104   0.05299989   -2.514789e-02
    ## 111   0.05299989    9.004660e-03
    ## 112   0.05299989   -3.197840e-02
    ## 113   0.05299989   -3.197840e-02
    ## 114   0.05299989   -3.880891e-02
    ## 115   0.05299989   -3.880891e-02
    ## 116   0.05299989   -3.880891e-02
    ## 117   0.05299989   -3.880891e-02
    ## 118   0.05299989   -3.880891e-02
    ## 119   0.05299989   -4.656361e-03
    ## 120   0.05299989   -4.656361e-03
    ## 121   0.05299989   -4.656361e-03
    ## 122   0.05299989   -4.656361e-03
    ## 123   0.05299989   -4.656361e-03
    ## 124   0.05299989   -4.656361e-03
    ## 125   0.05299989   -1.148687e-02
    ## 126   0.05299989   -4.563942e-02
    ## 127   0.05299989   -5.246993e-02
    ## 128   0.05299989   -5.930045e-02
    ## 129   0.05299989   -5.246993e-02
    ## 130   0.05299989   -4.563942e-02
    ## 131   0.05299989   -4.563942e-02
    ## 132   0.05299989   -4.563942e-02
    ## 133   0.05299989   -4.563942e-02
    ## 134   0.05299989   -4.563942e-02
    ## 135   0.05299989   -4.563942e-02
    ## 136   0.05299989   -5.246993e-02
    ## 137   0.05299989    2.174150e-03
    ## 144   0.05299989    4.998772e-02
    ## 149   0.05299989    4.998772e-02
    ## 150   0.05299989    3.632670e-02
    ## 151   0.05299989    3.632670e-02
    ## 152   0.05299989    3.632670e-02
    ## 153   0.05299989    3.632670e-02
    ## 154   0.05299989    2.174150e-03
    ## 155   0.05299989    3.632670e-02
    ## 156   0.05299989   -5.246993e-02
    ## 157   0.05299989   -4.563942e-02
    ## 158   0.05299989   -4.563942e-02
    ## 159   0.05299989   -4.563942e-02
    ## 160   0.05299989   -4.563942e-02
    ## 161   0.05299989   -4.563942e-02
    ## 162   0.05299989   -5.930045e-02
    ## 163   0.05299989   -5.246993e-02
    ## 164   0.05299989   -5.246993e-02
    ## 165   0.05299989   -5.246993e-02
    ## 166   0.05299989   -5.930045e-02
    ## 167   0.05299989    9.004660e-03
    ## 168   0.05299989    9.004660e-03
    ## 169   0.05299989    9.004660e-03
    ## 170   0.05299989    9.004660e-03
    ## 171   0.05299989    9.004660e-03
    ## 172   0.05299989    9.004660e-03
    ## 173   0.05299989    2.174150e-03
    ## 177   0.05299989   -3.880891e-02
    ## 185   0.05299989   -4.656361e-03
    ## 186   0.05299989   -4.656361e-03
    ## 187   0.05299989   -4.656361e-03
    ## 188   0.05299989   -2.514789e-02
    ## 189   0.05299989   -2.514789e-02
    ## 190   0.05299989   -2.514789e-02
    ## 191   0.05299989   -2.514789e-02
    ## 192   0.05299989   -3.197840e-02
    ## 193   0.05299989   -3.880891e-02
    ## 194   0.05299989   -5.246993e-02
    ## 195   0.05299989   -5.246993e-02
    ## 196   0.05299989    2.949619e-02
    ## 197   0.05299989   -4.656361e-03
    ## 202   0.05299989   -5.246993e-02
    ## 203   0.05299989   -5.246993e-02
    ## 204   0.05299989   -5.246993e-02
    ## 205   0.05299989   -5.246993e-02
    ## 206   0.05299989   -5.246993e-02
    ## 207   0.05299989   -5.930045e-02
    ## 208   0.05299989   -5.930045e-02
    ## 209   0.05299989   -5.930045e-02
    ## 213   0.05299989    2.174150e-03
    ## 214   0.05299989    4.998772e-02
    ## 215   0.05299989    4.998772e-02
    ## 216   0.05299989    2.174150e-03
    ## 217   0.05299989    4.998772e-02
    ## 218   0.05299989    4.998772e-02
    ## 219   0.05299989    4.998772e-02
    ## 229   0.05299989   -5.246993e-02
    ## 230   0.05299989   -5.246993e-02
    ## 231   0.05299989   -5.246993e-02
    ## 232   0.05299989    8.414028e-02
    ## 233   0.05299989    8.414028e-02
    ## 234   0.05299989   -2.514789e-02
    ## 238   0.05299989    4.998772e-02
    ## 239   0.05299989    8.414028e-02
    ## 241   0.05299989   -5.246993e-02
    ## 244   0.05299989    8.414028e-02
    ## 245   0.05299989   -5.246993e-02
    ## 246   0.05299989   -5.246993e-02
    ## 247   0.05299989   -5.246993e-02
    ## 248   0.05299989   -5.246993e-02
    ## 249   0.05299989   -5.246993e-02
    ## 250   0.05299989    4.315721e-02
    ## 251   0.05299989    2.174150e-03
    ## 252   0.05299989    2.174150e-03
    ## 253   0.05299989    2.174150e-03
    ## 254   0.05299989    8.414028e-02
    ## 258   0.05299989   -5.246993e-02
    ## 259   0.05299989   -5.246993e-02
    ## 260   0.05299989   -2.514789e-02
    ## 261   0.05299989   -2.514789e-02
    ## 263   0.05299989    6.364874e-02
    ## 269   0.05299989   -3.880891e-02
    ## 270   0.05299989   -3.880891e-02
    ## 271   0.05299989   -3.880891e-02
    ## 272   0.05299989   -5.246993e-02
    ## 273   0.05299989   -5.246993e-02
    ## 274   0.05299989   -5.246993e-02
    ## 275   0.05299989   -5.246993e-02
    ## 280   0.05299989    9.004660e-03
    ## 281   0.05299989    9.004660e-03
    ## 282   0.05299989    9.004660e-03
    ## 285   0.05299989    4.998772e-02
    ## 287   0.05299989    6.364874e-02
    ## 288   0.05299989    6.364874e-02
    ## 289   0.05299989    6.364874e-02
    ## 290   0.05299989    6.364874e-02
    ## 291   0.05299989    4.315721e-02
    ## 292   0.05299989    4.998772e-02
    ## 293   0.05299989    4.998772e-02
    ## 294   0.05299989    4.998772e-02
    ## 295   0.05299989   -1.831738e-02
    ## 307   0.05299989   -9.345300e-02
    ## 308   0.05299989   -5.246993e-02
    ## 309   0.05299989   -5.246993e-02
    ## 310   0.05299989   -5.246993e-02
    ## 311   0.05299989   -5.246993e-02
    ## 312   0.05299989   -5.246993e-02
    ## 313   0.05299989   -9.345300e-02
    ## 314   0.05299989    8.414028e-02
    ## 315   0.05299989    8.414028e-02
    ## 317   0.05299989   -5.246993e-02
    ## 322   0.05299989    8.414028e-02
    ## 323   0.05299989   -5.246993e-02
    ## 324   0.05299989    2.174150e-03
    ## 325   0.05299989    2.174150e-03
    ## 327   0.05299989    9.004660e-03
    ## 328   0.05299989    9.004660e-03
    ## 329   0.05299989    9.004660e-03
    ## 330   0.05299989    9.004660e-03
    ## 331   0.05299989    9.004660e-03
    ## 332   0.05299989    2.949619e-02
    ## 333   0.05299989    6.364874e-02
    ## 334   0.05299989    6.364874e-02
    ## 335   0.05299989    6.364874e-02
    ## 336   0.05299989   -4.656361e-03
    ## 339   0.05299989    4.998772e-02
    ## 340   0.05299989    4.998772e-02
    ## 342   0.05299989    4.315721e-02
    ## 343   0.05299989    4.315721e-02
    ## 344   0.05299989    4.315721e-02
    ## 345   0.05299989    4.315721e-02
    ## 346   0.05299989   -4.563942e-02
    ## 347   0.05299989   -4.563942e-02
    ## 351   0.05299989   -5.246993e-02
    ## 356   0.05299989    4.998772e-02
    ## 357   0.05299989    4.998772e-02
    ## 358   0.05299989    4.998772e-02
    ## 359   0.05299989    4.998772e-02
    ## 360   0.05299989    2.174150e-03
    ## 361   0.05299989    4.998772e-02
    ## 362   0.05299989    4.998772e-02
    ## 363   0.05299989    4.998772e-02
    ## 364   0.05299989   -3.880891e-02
    ## 366   0.05299989   -7.979198e-02
    ## 367   0.05299989   -7.979198e-02
    ## 368   0.05299989   -7.979198e-02
    ## 370   0.05299989   -9.345300e-02
    ## 371   0.05299989   -9.345300e-02
    ## 372   0.05299989   -9.345300e-02
    ## 373   0.05299989   -9.345300e-02
    ## 374   0.05299989   -8.662249e-02
    ## 384   0.05299989    2.174150e-03
    ## 385   0.05299989    9.004660e-03
    ## 386   0.05299989    9.004660e-03
    ## 387   0.05299989    9.004660e-03
    ## 388   0.05299989    9.004660e-03
    ## 390   0.05299989   -1.831738e-02
    ## 391   0.05299989   -2.514789e-02
    ## 392   0.05299989   -3.197840e-02
    ## 393   0.05299989   -2.514789e-02
    ## 394   0.05299989   -3.197840e-02
    ## 395   0.05299989   -2.514789e-02
    ## 396   0.05299989    2.266568e-02
    ## 397   0.05299989    2.266568e-02
    ## 400   0.05299989    7.730977e-02
    ## 401   0.05299989    7.047926e-02
    ## 402   0.05299989    7.047926e-02
    ## 403   0.05299989    7.047926e-02
    ## 404   0.05299989    7.047926e-02
    ## 405   0.05299989    7.047926e-02
    ## 406   0.05299989    7.047926e-02
    ## 407   0.05299989    7.047926e-02
    ## 408   0.05299989    7.047926e-02
    ## 409   0.05299989    7.047926e-02
    ## 410   0.05299989    7.047926e-02
    ## 411   0.05299989    7.047926e-02
    ## 412   0.05299989   -3.880891e-02
    ## 413   0.05299989   -3.880891e-02
    ## 414   0.05299989   -3.880891e-02
    ## 415   0.05299989   -3.880891e-02
    ## 416   0.05299989   -3.880891e-02
    ## 417   0.05299989   -3.880891e-02
    ## 418   0.05299989   -3.880891e-02
    ## 419   0.05299989   -4.656361e-03
    ## 420   0.05299989   -4.656361e-03
    ## 421   0.05299989   -4.656361e-03
    ## 422   0.05299989   -1.831738e-02
    ## 423   0.05299989   -1.148687e-02
    ## 424   0.05299989    8.414028e-02
    ## 425   0.05299989    8.414028e-02
    ## 426   0.05299989    8.414028e-02
    ## 427   0.05299989    8.414028e-02
    ## 429   0.05299989    8.414028e-02
    ## 430   0.05299989    5.681823e-02
    ## 432   0.05299989    7.047926e-02
    ## 433   0.05299989    7.047926e-02
    ## 434   0.05299989    7.047926e-02
    ## 435   0.05299989    7.047926e-02
    ## 436   0.05299989    7.047926e-02
    ## 437   0.05299989    7.730977e-02
    ## 438   0.05299989    7.047926e-02
    ## 439   0.05299989    7.047926e-02
    ## 440   0.05299989    7.047926e-02
    ## 441   0.05299989    7.047926e-02
    ## 442   0.05299989    7.047926e-02
    ## 443   0.05299989    7.047926e-02
    ## 444   0.05299989    7.047926e-02
    ## 445   0.05299989    7.047926e-02
    ## 446   0.05299989    7.047926e-02
    ## 447   0.05299989    7.047926e-02
    ## 448   0.05299989    7.047926e-02
    ## 449   0.05299989   -7.979198e-02
    ## 450   0.05299989   -7.979198e-02
    ## 451   0.05299989   -9.345300e-02
    ## 455   0.05299989   -9.345300e-02
    ## 456   0.05299989   -9.345300e-02
    ## 457   0.05299989   -9.345300e-02
    ## 458   0.05299989   -9.345300e-02
    ## 461   0.05299989   -8.662249e-02
    ## 462   0.05299989   -9.345300e-02
    ## 463   0.05299989   -9.345300e-02
    ## 468   0.05299989   -4.656361e-03
    ## 470   0.05299989   -7.296147e-02
    ## 471   0.05299989   -2.514789e-02
    ## 474   0.05299989   -3.197840e-02
    ## 482   0.05299989   -3.880891e-02
    ## 485   0.05299989   -1.148687e-02
    ## 486   0.05299989   -1.148687e-02
    ## 487   0.05299989   -1.148687e-02
    ## 488   0.05299989   -1.148687e-02
    ## 489   0.05299989   -3.880891e-02
    ## 490   0.05299989   -3.880891e-02
    ## 491   0.05299989   -3.880891e-02
    ## 492   0.05299989   -3.880891e-02
    ## 493   0.05299989   -5.930045e-02
    ## 494   0.05299989   -4.563942e-02
    ## 495   0.05299989   -5.246993e-02
    ## 496   0.05299989   -4.563942e-02
    ## 497   0.05299989   -4.563942e-02
    ## 498   0.05299989   -4.563942e-02
    ## 499   0.05299989   -6.613096e-02
    ## 500   0.05299989   -5.246993e-02
    ## 501   0.05299989   -5.930045e-02
    ## 502   0.05299989   -5.930045e-02
    ## 504   0.05299989    6.364874e-02
    ## 505   0.05299989    4.315721e-02
    ## 507   0.05299989    6.364874e-02
    ## 509   0.05299989    4.998772e-02
    ## 510   0.05299989    4.998772e-02
    ## 511   0.05299989    4.998772e-02
    ## 512   0.05299989    4.998772e-02
    ## 513   0.05299989    4.315721e-02
    ## 514   0.05299989   -7.979198e-02
    ## 515   0.05299989   -9.345300e-02
    ## 518   0.05299989   -9.345300e-02
    ## 519   0.05299989   -9.345300e-02
    ## 521   0.05299989   -8.662249e-02
    ## 522   0.05299989   -8.662249e-02
    ## 523   0.05299989   -9.345300e-02
    ## 532   0.05299989   -3.197840e-02
    ## 533   0.05299989   -1.831738e-02
    ## 534   0.05299989   -3.197840e-02
    ## 535   0.05299989   -3.197840e-02
    ## 536   0.05299989   -2.514789e-02
    ## attr(,"assign")
    ## [1] 0 1
    ## 
    ## $qraux
    ## [1] 1.053000 1.070479
    ## 
    ## $pivot
    ## [1] 1 2
    ## 
    ## $tol
    ## [1] 1e-07
    ## 
    ## $rank
    ## [1] 2
    ## 
    ## attr(,"class")
    ## [1] "qr"
    ## 
    ## $df.residual
    ## [1] 354
    ## 
    ## $na.action
    ##   1  18  19  21  22  37  46  49  50  52  53  64  65  66  67  68  69  70  72  73 
    ##   1  18  19  21  22  37  46  49  50  52  53  64  65  66  67  68  69  70  72  73 
    ##  74  83  85  86  88  89  90  91  92  93  94  95  96  97  98  99 100 102 105 106 
    ##  74  83  85  86  88  89  90  91  92  93  94  95  96  97  98  99 100 102 105 106 
    ## 107 108 109 110 138 139 140 141 142 143 145 146 147 148 174 175 176 178 179 180 
    ## 107 108 109 110 138 139 140 141 142 143 145 146 147 148 174 175 176 178 179 180 
    ## 181 182 183 184 198 199 200 201 210 211 212 220 221 222 223 224 225 226 227 228 
    ## 181 182 183 184 198 199 200 201 210 211 212 220 221 222 223 224 225 226 227 228 
    ## 235 236 237 240 242 243 255 256 257 262 264 265 266 267 268 276 277 278 279 283 
    ## 235 236 237 240 242 243 255 256 257 262 264 265 266 267 268 276 277 278 279 283 
    ## 284 286 296 297 298 299 300 301 302 303 304 305 306 316 318 319 320 321 326 337 
    ## 284 286 296 297 298 299 300 301 302 303 304 305 306 316 318 319 320 321 326 337 
    ## 338 341 348 349 350 352 353 354 355 365 369 375 376 377 378 379 380 381 382 383 
    ## 338 341 348 349 350 352 353 354 355 365 369 375 376 377 378 379 380 381 382 383 
    ## 389 398 399 428 431 452 453 454 459 460 464 465 466 467 469 472 473 475 476 477 
    ## 389 398 399 428 431 452 453 454 459 460 464 465 466 467 469 472 473 475 476 477 
    ## 478 479 480 481 483 484 503 506 508 516 517 520 524 525 526 527 528 529 530 531 
    ## 478 479 480 481 483 484 503 506 508 516 517 520 524 525 526 527 528 529 530 531 
    ## attr(,"class")
    ## [1] "omit"
    ## 
    ## $xlevels
    ## named list()
    ## 
    ## $call
    ## lm(formula = diameter ~ I(year2 - 1993), data = RUBRUM_year)
    ## 
    ## $terms
    ## diameter ~ I(year2 - 1993)
    ## attr(,"variables")
    ## list(diameter, I(year2 - 1993))
    ## attr(,"factors")
    ##                 I(year2 - 1993)
    ## diameter                      0
    ## I(year2 - 1993)               1
    ## attr(,"term.labels")
    ## [1] "I(year2 - 1993)"
    ## attr(,"order")
    ## [1] 1
    ## attr(,"intercept")
    ## [1] 1
    ## attr(,"response")
    ## [1] 1
    ## attr(,".Environment")
    ## <environment: R_GlobalEnv>
    ## attr(,"predvars")
    ## list(diameter, I(year2 - 1993))
    ## attr(,"dataClasses")
    ##        diameter I(year2 - 1993) 
    ##       "numeric"       "numeric" 
    ## 
    ## $model
    ##     diameter I(year2 - 1993)
    ## 2       8.50               2
    ## 3      10.00               2
    ## 4       5.00               2
    ## 5       3.75               2
    ## 6       3.00               2
    ## 7       4.00               2
    ## 8       4.00               2
    ## 9       6.00               2
    ## 10      7.00               2
    ## 11      7.00               2
    ## 12      3.00               2
    ## 13      6.25               2
    ## 14      4.50               2
    ## 15     12.00               2
    ## 16      6.00               2
    ## 17      3.00               6
    ## 20      3.00              14
    ## 23      6.50              20
    ## 24      5.50              20
    ## 25      7.50              20
    ## 26      5.50              20
    ## 27      3.00              21
    ## 28      3.00              17
    ## 29      3.00              17
    ## 30     10.00              19
    ## 31      3.00              18
    ## 32      3.00              18
    ## 33      3.00              18
    ## 34      3.00              18
    ## 35      3.00              18
    ## 36      3.00              18
    ## 38      7.50               0
    ## 39      5.50               0
    ## 40     14.00               7
    ## 41      6.00               7
    ## 42      7.00               7
    ## 43      5.00               7
    ## 44      3.00               7
    ## 45      9.00               7
    ## 47      3.00              20
    ## 48      3.00              20
    ## 51      4.50              20
    ## 54      3.00              20
    ## 55      4.50              13
    ## 56      4.00              20
    ## 57      4.00              12
    ## 58      3.00              20
    ## 59      3.00              20
    ## 60      3.00              16
    ## 61      3.00              20
    ## 62      3.00              20
    ## 63      3.00              20
    ## 71      3.00              20
    ## 75      4.00              12
    ## 76     10.75               5
    ## 77      9.50               5
    ## 78      4.00              12
    ## 79      7.50               5
    ## 80      9.00               5
    ## 81     10.50               5
    ## 82      3.00              18
    ## 84      3.00              18
    ## 87      5.00               5
    ## 101     3.00              18
    ## 103     4.00              16
    ## 104     3.00              16
    ## 111     3.00              11
    ## 112     3.00              17
    ## 113     3.00              17
    ## 114     3.00              18
    ## 115     3.00              18
    ## 116     3.00              18
    ## 117     3.00              18
    ## 118     3.00              18
    ## 119     3.00              13
    ## 120     3.00              13
    ## 121     3.00              13
    ## 122     3.00              13
    ## 123     3.00              13
    ## 124     4.00              13
    ## 125     7.00              14
    ## 126     3.00              19
    ## 127     7.00              20
    ## 128     3.00              21
    ## 129     6.00              20
    ## 130     3.00              19
    ## 131     3.00              19
    ## 132     3.00              19
    ## 133     3.00              19
    ## 134     3.00              19
    ## 135     3.00              19
    ## 136     3.00              20
    ## 137     4.50              12
    ## 144     9.00               5
    ## 149     5.50               5
    ## 150    15.00               7
    ## 151    15.00               7
    ## 152     5.50               7
    ## 153     5.50               7
    ## 154     7.50              12
    ## 155     3.50               7
    ## 156     7.00              20
    ## 157     3.00              19
    ## 158     3.00              19
    ## 159     3.00              19
    ## 160     3.00              19
    ## 161     3.00              19
    ## 162     3.00              21
    ## 163     3.00              20
    ## 164     6.75              20
    ## 165     5.50              20
    ## 166     3.00              21
    ## 167     3.00              11
    ## 168     3.00              11
    ## 169     3.00              11
    ## 170     3.00              11
    ## 171     3.00              11
    ## 172     3.00              11
    ## 173    10.00              12
    ## 177     3.00              18
    ## 185     4.00              13
    ## 186     3.00              13
    ## 187     3.00              13
    ## 188     3.00              16
    ## 189     3.00              16
    ## 190     3.00              16
    ## 191     3.00              16
    ## 192     3.00              17
    ## 193     3.00              18
    ## 194     3.00              20
    ## 195     3.00              20
    ## 196     3.00               8
    ## 197     3.50              13
    ## 202     3.00              20
    ## 203     3.00              20
    ## 204     3.00              20
    ## 205     3.00              20
    ## 206     3.00              20
    ## 207     3.00              21
    ## 208     3.00              21
    ## 209     3.00              21
    ## 213     4.00              12
    ## 214    10.15               5
    ## 215     9.75               5
    ## 216     4.00              12
    ## 217     8.00               5
    ## 218     9.50               5
    ## 219     7.25               5
    ## 229     4.00              20
    ## 230     4.00              20
    ## 231     4.00              20
    ## 232     4.50               0
    ## 233     5.00               0
    ## 234     3.00              16
    ## 238     9.00               5
    ## 239     6.00               0
    ## 241     2.00              20
    ## 244    11.00               0
    ## 245     4.00              20
    ## 246     5.00              20
    ## 247     4.50              20
    ## 248     4.00              20
    ## 249     4.00              20
    ## 250    10.00               6
    ## 251     3.00              12
    ## 252     4.25              12
    ## 253     4.00              12
    ## 254     3.00               0
    ## 258     4.50              20
    ## 259     4.50              20
    ## 260     3.00              16
    ## 261     3.00              16
    ## 263     3.50               3
    ## 269     3.00              18
    ## 270     3.00              18
    ## 271     3.00              18
    ## 272     3.00              20
    ## 273     3.00              20
    ## 274     3.00              20
    ## 275     3.25              20
    ## 280     3.00              11
    ## 281     3.00              11
    ## 282     3.00              11
    ## 285     3.00               5
    ## 287     3.00               3
    ## 288     3.00               3
    ## 289     5.00               3
    ## 290     9.00               3
    ## 291     8.00               6
    ## 292     3.00               5
    ## 293     4.00               5
    ## 294     8.75               5
    ## 295     5.00              15
    ## 307     3.00              26
    ## 308     3.00              20
    ## 309     3.00              20
    ## 310     3.00              20
    ## 311     3.00              20
    ## 312     3.00              20
    ## 313     3.00              26
    ## 314     3.00               0
    ## 315     3.00               0
    ## 317     5.00              20
    ## 322    12.00               0
    ## 323     4.50              20
    ## 324     5.00              12
    ## 325     4.00              12
    ## 327     3.00              11
    ## 328     3.00              11
    ## 329     3.00              11
    ## 330     3.00              11
    ## 331     3.00              11
    ## 332     3.00               8
    ## 333     9.50               3
    ## 334     6.00               3
    ## 335     5.00               3
    ## 336     4.00              13
    ## 339     3.00               5
    ## 340     9.00               5
    ## 342     6.50               6
    ## 343     7.50               6
    ## 344     7.50               6
    ## 345     3.00               6
    ## 346     3.00              19
    ## 347     3.00              19
    ## 351     5.00              20
    ## 356     3.00               5
    ## 357     3.00               5
    ## 358    11.00               5
    ## 359    18.50               5
    ## 360     4.00              12
    ## 361     7.75               5
    ## 362     9.00               5
    ## 363     8.00               5
    ## 364     3.00              18
    ## 366     3.00              24
    ## 367     3.00              24
    ## 368     3.00              24
    ## 370     3.00              26
    ## 371     3.00              26
    ## 372     4.00              26
    ## 373     5.00              26
    ## 374     3.00              25
    ## 384     7.00              12
    ## 385     3.00              11
    ## 386     3.00              11
    ## 387     3.00              11
    ## 388     3.00              11
    ## 390     4.75              15
    ## 391     6.50              16
    ## 392     3.00              17
    ## 393     3.00              16
    ## 394     3.00              17
    ## 395     3.00              16
    ## 396     4.00               9
    ## 397     7.00               9
    ## 400     8.00               1
    ## 401     6.75               2
    ## 402     7.50               2
    ## 403    11.00               2
    ## 404     6.50               2
    ## 405     5.00               2
    ## 406     3.00               2
    ## 407     7.00               2
    ## 408     8.00               2
    ## 409    12.00               2
    ## 410     9.00               2
    ## 411    10.00               2
    ## 412     3.00              18
    ## 413     3.00              18
    ## 414     3.00              18
    ## 415     3.00              18
    ## 416     3.00              18
    ## 417     3.00              18
    ## 418     3.00              18
    ## 419     3.00              13
    ## 420     3.00              13
    ## 421     3.00              13
    ## 422    18.00              15
    ## 423     3.00              14
    ## 424     3.00               0
    ## 425     4.50               0
    ## 426     7.50               0
    ## 427     3.00               0
    ## 429     4.00               0
    ## 430    10.50               4
    ## 432     5.25               2
    ## 433     7.50               2
    ## 434     9.00               2
    ## 435     3.00               2
    ## 436     3.00               2
    ## 437    14.50               1
    ## 438     3.00               2
    ## 439    13.50               2
    ## 440     6.00               2
    ## 441     7.00               2
    ## 442     6.00               2
    ## 443     6.00               2
    ## 444     5.50               2
    ## 445     6.25               2
    ## 446    10.00               2
    ## 447     5.75               2
    ## 448    12.00               2
    ## 449     3.00              24
    ## 450     3.00              24
    ## 451     3.00              26
    ## 455     3.00              26
    ## 456     3.00              26
    ## 457     3.00              26
    ## 458     3.00              26
    ## 461     3.00              25
    ## 462     3.00              26
    ## 463     3.00              26
    ## 468     3.00              13
    ## 470     3.00              23
    ## 471     3.00              16
    ## 474     3.00              17
    ## 482     3.00              18
    ## 485     3.00              14
    ## 486     3.00              14
    ## 487     2.00              14
    ## 488     2.00              14
    ## 489     4.00              18
    ## 490     3.50              18
    ## 491     3.50              18
    ## 492     3.75              18
    ## 493     3.00              21
    ## 494     3.00              19
    ## 495     5.00              20
    ## 496     3.00              19
    ## 497     3.00              19
    ## 498     3.00              19
    ## 499     3.50              22
    ## 500     3.00              20
    ## 501     3.00              21
    ## 502     3.00              21
    ## 504     9.00               3
    ## 505     7.00               6
    ## 507     5.50               3
    ## 509     7.50               5
    ## 510     7.50               5
    ## 511     8.00               5
    ## 512     4.50               5
    ## 513     7.00               6
    ## 514     3.00              24
    ## 515     3.00              26
    ## 518     3.00              26
    ## 519     3.00              26
    ## 521     3.00              25
    ## 522     3.00              25
    ## 523     3.00              26
    ## 532     3.00              17
    ## 533     3.00              15
    ## 534     3.00              17
    ## 535     3.00              17
    ## 536     3.00              16

<!----------------------------------------------------------------------------->

## 2.2 (5 points)

Produce something relevant from your fitted model: either predictions on
Y, or a single value like a regression coefficient or a p-value.

-   Be sure to indicate in writing what you chose to produce.
-   Your code should either output a tibble (in which case you should
    indicate the column that contains the thing you’re looking for), or
    the thing you’re looking for itself.
-   Obtain your results using the \`broom\` package if possible. If your
    model is not compatible with the broom function you’re needing, then
    you can obtain your results by some other means, but first indicate
    which broom function is not compatible.

<!-------------------------- Start your work below ---------------------------->

``` r
(broom::tidy(fitmodel))
```

    ## # A tibble: 2 × 5
    ##   term            estimate std.error statistic  p.value
    ##   <chr>              <dbl>     <dbl>     <dbl>    <dbl>
    ## 1 (Intercept)        7.09     0.240       29.6 1.33e-97
    ## 2 I(year2 - 1993)   -0.185    0.0160     -11.6 1.90e-26

<!----------------------------------------------------------------------------->

# Exercise 3: Reading and writing data

Get set up for this exercise by making a folder called \`output\` in the
top level of your project folder / repository. You’ll be saving things
there.

## 3.1 (5 points)

Take a summary table that you made from Milestone 2 (Exercise 1.2), and
write it as a csv file in your \`output\` folder. Use the
\`here::here()\` function.

-   \*\*Robustness criteria\*\*: You should be able to move your Mini
    Project repository / project folder to some other location on your
    computer, or move this very Rmd file to another location within your
    project repository / folder, and your code should still work.
-   \*\*Reproducibility criteria\*\*: You should be able to delete the
    csv file, and remake it simply by knitting this Rmd file.

<!-------------------------- Start your work below ---------------------------->

``` r
Cut_trees<-vancouver_trees %>%
  filter(plant_area %in% ("C")& neighbourhood_name %in% ("KITSILANO"))

observations_cut <- Cut_trees %>%
    group_by(on_street) %>%
    summarise(Count = n())%>%
  arrange(desc(Count))

print(observations_cut)
```

    ## # A tibble: 24 × 2
    ##    on_street    Count
    ##    <chr>        <int>
    ##  1 W BROADWAY     316
    ##  2 W 4TH AV       176
    ##  3 BURRARD ST      43
    ##  4 ARBUTUS ST      32
    ##  5 YEW ST          22
    ##  6 W 1ST AV        13
    ##  7 MACDONALD ST    10
    ##  8 BALACLAVA ST     9
    ##  9 MACKENZIE ST     9
    ## 10 BAYSWATER ST     7
    ## # … with 14 more rows

``` r
write_csv(observations_cut, here::here("~/Desktop/STAT545/myrepo/output", "mytable.csv"))
```

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Write your model object from Exercise 2 to an R binary file (an RDS),
and load it again. Be sure to save the binary file in your \`output\`
folder. Use the functions \`saveRDS()\` and \`readRDS()\`.

-   The same robustness and reproducibility criteria as in 3.1 apply
    here.

<!-------------------------- Start your work below ---------------------------->

``` r
saveRDS(fitmodel, here::here("~/Desktop/STAT545/myrepo/output", "fitmodel.rds"))
```

``` r
fitmodel2 <- gzfile(here::here("~/Desktop/STAT545/myrepo/output", "fitmodel.rds"))
readRDS(fitmodel2)
```

    ## 
    ## Call:
    ## lm(formula = diameter ~ I(year2 - 1993), data = RUBRUM_year)
    ## 
    ## Coefficients:
    ##     (Intercept)  I(year2 - 1993)  
    ##          7.0929          -0.1847

<!----------------------------------------------------------------------------->

# Tidy Repository

Now that this is your last milestone, your entire project repository
should be organized. Here are the criteria we’re looking for.

## Main README (3 points)

There should be a file named \`README.md\` at the top level of your
repository. Its contents should automatically appear when you visit the
repository on GitHub.

Minimum contents of the README file:

-   In a sentence or two, explains what this repository is, so that
    future-you or someone else stumbling on your repository can be
    oriented to the repository.
-   In a sentence or two (or more??), briefly explains how to engage
    with the repository. You can assume the person reading knows the
    material from STAT 545A. Basically, if a visitor to your repository
    wants to explore your project, what should they know?

Once you get in the habit of making README files, and seeing more README
files in other projects, you’ll wonder how you ever got by without them!
They are tremendously helpful.

## File and Folder structure (3 points)

You should have at least four folders in the top level of your
repository: one for each milestone, and one output folder. If there are
any other folders, these are explained in the main README.

Each milestone document is contained in its respective folder, and
nowhere else.

Every level-1 folder (that is, the ones stored in the top level, like
“Milestone1” and “output”) has a \`README\` file, explaining in a
sentence or two what is in the folder, in plain language (it’s enough to
say something like “This folder contains the source for Milestone 1”).

## Output (2 points)

All output is recent and relevant:

-   All Rmd files have been \`knit\`ted to their output, and all data
    files saved from Exercise 3 above appear in the \`output\` folder.
-   All of these output files are up-to-date – that is, they haven’t
    fallen behind after the source (Rmd) files have been updated.
-   There should be no relic output files. For example, if you were
    knitting an Rmd to html, but then changed the output to be only a
    markdown file, then the html file is a relic and should be deleted.

Our recommendation: delete all output files, and re-knit each
milestone’s Rmd file, so that everything is up to date and relevant.

PS: there’s a way where you can run all project code using a single
command, instead of clicking “knit” three times. More on this in STAT
545B!

## Error-free code (1 point)

This Milestone 3 document knits error-free. (We’ve already graded this
aspect for Milestone 1 and 2)

## Tagged release (1 point)

You’ve tagged a release for Milestone 3. (We’ve already graded this
aspect for Milestone 1 and 2)
