# Introduction

This work is an \*R\* approach to Data Analysis and modeling using the
\*tidyverse\* packages. First, I became familiar with the different
datasets and their visualizations of the various features and their
(inter-related) properties. I mainly used \*dplyr\* for data
manipulation and \*ggplot2\* for visualization. Since this is my
first-ever data analysis, I added some explanations on how they work.
These comments will help me in my future analysis and for readers to
follow up on my work.

With this first milestone, I:

-   Become familiar with my dataset

-   Choose four questions that I liked to answer with my data

-   Generate a reproducible and clear report using R Markdown

# Choose my favorite dataset

## Load libraries, functions, and data files

It is essential to load all the necessary packages at the top of the
script to overview what I will need. I divided the packages into three
sets: Visualization, Wrangle, and Model.

    #install.packages("devtools")
    #devtools::install_github("UBC-MDS/datateachr")
    # vis
    library('ggplot2') # visualization
    library('ggforce')  # visualization
    library('tidyverse')  # visualization

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ tibble  3.1.4     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   2.0.1     ✓ forcats 0.5.1
    ## ✓ purrr   0.3.4

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

    # wrangle
    library('dplyr') # data manipulation
    library('tidyr') # data manipulation
    library('naniar')  # data manipulation
    library('visdat') # data manipulation

    # model
    library('datateachr') 

## Datasets

Out of the seven datasets available in the **datateachr** package, I
chose four that I liked based on their description. These are my
choices:

### vancouver\_trees

The street tree dataset includes a listing of public trees on boulevards
in the City of Vancouver and provides data on tree coordinates, species
and other related characteristics (dataset from City of Vancouver’s Open
Data Portal), it currently has 146,611 rows and 20 columns.

    vancouver_trees

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

With the function `summary`, I got a data overview of the
dataset **`vancouver_trees`**, the type of variables (character,
numerical) and the basic distribution information. The
features *`longitude`*, *`date_planted`*, and *`latitude`* have missing
values. On the other hand, within character type value, the missing ones
can’t be seen with this function. Some of the character values
are *`std_street`, `genus_name`,* and *`species_name`*.

    summary(vancouver_trees)

    ##     tree_id        civic_number    std_street         genus_name       
    ##  Min.   :    12   Min.   :    0   Length:146611      Length:146611     
    ##  1st Qu.: 65464   1st Qu.: 1306   Class :character   Class :character  
    ##  Median :134903   Median : 2604   Mode  :character   Mode  :character  
    ##  Mean   :131892   Mean   : 2937                                        
    ##  3rd Qu.:194450   3rd Qu.: 4005                                        
    ##  Max.   :266203   Max.   :17888                                        
    ##                                                                        
    ##  species_name       cultivar_name      common_name          assigned        
    ##  Length:146611      Length:146611      Length:146611      Length:146611     
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##  root_barrier        plant_area        on_street_block  on_street        
    ##  Length:146611      Length:146611      Min.   :   0    Length:146611     
    ##  Class :character   Class :character   1st Qu.:1300    Class :character  
    ##  Mode  :character   Mode  :character   Median :2600    Mode  :character  
    ##                                        Mean   :2909                      
    ##                                        3rd Qu.:4000                      
    ##                                        Max.   :9900                      
    ##                                                                          
    ##  neighbourhood_name street_side_name   height_range_id     diameter     
    ##  Length:146611      Length:146611      Min.   : 0.000   Min.   :  0.00  
    ##  Class :character   Class :character   1st Qu.: 1.000   1st Qu.:  3.50  
    ##  Mode  :character   Mode  :character   Median : 2.000   Median :  9.00  
    ##                                        Mean   : 2.627   Mean   : 11.49  
    ##                                        3rd Qu.: 4.000   3rd Qu.: 16.50  
    ##                                        Max.   :10.000   Max.   :435.00  
    ##                                                                         
    ##      curb            date_planted          longitude         latitude    
    ##  Length:146611      Min.   :1989-10-27   Min.   :-123.2   Min.   :49.20  
    ##  Class :character   1st Qu.:1998-02-23   1st Qu.:-123.1   1st Qu.:49.23  
    ##  Mode  :character   Median :2004-01-28   Median :-123.1   Median :49.25  
    ##                     Mean   :2004-04-07   Mean   :-123.1   Mean   :49.25  
    ##                     3rd Qu.:2010-03-02   3rd Qu.:-123.1   3rd Qu.:49.26  
    ##                     Max.   :2019-07-03   Max.   :-123.0   Max.   :49.29  
    ##                     NA's   :76548        NA's   :22771    NA's   :22771

### flow\_sample

Acquired courtesy of The Government of Canada’s Historical Hydrometric
Database, it currently has 218 rows and 7 columns.

    flow_sample 

    ## # A tibble: 218 × 7
    ##    station_id  year extreme_type month   day  flow sym  
    ##    <chr>      <dbl> <chr>        <dbl> <dbl> <dbl> <chr>
    ##  1 05BB001     1909 maximum          7     7   314 <NA> 
    ##  2 05BB001     1910 maximum          6    12   230 <NA> 
    ##  3 05BB001     1911 maximum          6    14   264 <NA> 
    ##  4 05BB001     1912 maximum          8    25   174 <NA> 
    ##  5 05BB001     1913 maximum          6    11   232 <NA> 
    ##  6 05BB001     1914 maximum          6    18   214 <NA> 
    ##  7 05BB001     1915 maximum          6    27   236 <NA> 
    ##  8 05BB001     1916 maximum          6    20   309 <NA> 
    ##  9 05BB001     1917 maximum          6    17   174 <NA> 
    ## 10 05BB001     1918 maximum          6    15   345 <NA> 
    ## # … with 208 more rows

Regarding the overview of the dataset **`flow_sample`**, we can see
seven features. *`Station_id`, `extreme_type`* and *`sym`* are character
type and the others are numerical. There are two missing values in
*`month`*, *`day`*and *`flow`* values.

    summary(flow_sample)

    ##   station_id             year      extreme_type           month       
    ##  Length:218         Min.   :1909   Length:218         Min.   : 1.000  
    ##  Class :character   1st Qu.:1936   Class :character   1st Qu.: 3.000  
    ##  Mode  :character   Median :1963   Mode  :character   Median : 6.000  
    ##                     Mean   :1963                      Mean   : 5.046  
    ##                     3rd Qu.:1990                      3rd Qu.: 6.000  
    ##                     Max.   :2018                      Max.   :12.000  
    ##                                                       NA's   :2       
    ##       day             flow            sym           
    ##  Min.   : 1.00   Min.   :  3.62   Length:218        
    ##  1st Qu.: 8.00   1st Qu.:  6.18   Class :character  
    ##  Median :17.00   Median :114.00   Mode  :character  
    ##  Mean   :16.24   Mean   :110.13                     
    ##  3rd Qu.:25.00   3rd Qu.:204.25                     
    ##  Max.   :31.00   Max.   :466.00                     
    ##  NA's   :2       NA's   :2

### cancer\_sample

Dataset from UCI Machine Learning Repository and it currently has 569
rows and 32 columns.

    cancer_sample

    ## # A tibble: 569 × 32
    ##          ID diagnosis radius_mean texture_mean perimeter_mean area_mean
    ##       <dbl> <chr>           <dbl>        <dbl>          <dbl>     <dbl>
    ##  1   842302 M                18.0         10.4          123.      1001 
    ##  2   842517 M                20.6         17.8          133.      1326 
    ##  3 84300903 M                19.7         21.2          130       1203 
    ##  4 84348301 M                11.4         20.4           77.6      386.
    ##  5 84358402 M                20.3         14.3          135.      1297 
    ##  6   843786 M                12.4         15.7           82.6      477.
    ##  7   844359 M                18.2         20.0          120.      1040 
    ##  8 84458202 M                13.7         20.8           90.2      578.
    ##  9   844981 M                13           21.8           87.5      520.
    ## 10 84501001 M                12.5         24.0           84.0      476.
    ## # … with 559 more rows, and 26 more variables: smoothness_mean <dbl>,
    ## #   compactness_mean <dbl>, concavity_mean <dbl>, concave_points_mean <dbl>,
    ## #   symmetry_mean <dbl>, fractal_dimension_mean <dbl>, radius_se <dbl>,
    ## #   texture_se <dbl>, perimeter_se <dbl>, area_se <dbl>, smoothness_se <dbl>,
    ## #   compactness_se <dbl>, concavity_se <dbl>, concave_points_se <dbl>,
    ## #   symmetry_se <dbl>, fractal_dimension_se <dbl>, radius_worst <dbl>,
    ## #   texture_worst <dbl>, perimeter_worst <dbl>, area_worst <dbl>, …

Almost all the features are numerical except for the `diagnosis`
variable. It is essential to mention that the values of a categorical
variable can be given in numerical codes, but that doesn’t make the
variable quantitative; that is the `ID` case. There are no missing
values in this dataset.

    summary(cancer_sample)

    ##        ID             diagnosis          radius_mean      texture_mean  
    ##  Min.   :     8670   Length:569         Min.   : 6.981   Min.   : 9.71  
    ##  1st Qu.:   869218   Class :character   1st Qu.:11.700   1st Qu.:16.17  
    ##  Median :   906024   Mode  :character   Median :13.370   Median :18.84  
    ##  Mean   : 30371831                      Mean   :14.127   Mean   :19.29  
    ##  3rd Qu.:  8813129                      3rd Qu.:15.780   3rd Qu.:21.80  
    ##  Max.   :911320502                      Max.   :28.110   Max.   :39.28  
    ##  perimeter_mean     area_mean      smoothness_mean   compactness_mean 
    ##  Min.   : 43.79   Min.   : 143.5   Min.   :0.05263   Min.   :0.01938  
    ##  1st Qu.: 75.17   1st Qu.: 420.3   1st Qu.:0.08637   1st Qu.:0.06492  
    ##  Median : 86.24   Median : 551.1   Median :0.09587   Median :0.09263  
    ##  Mean   : 91.97   Mean   : 654.9   Mean   :0.09636   Mean   :0.10434  
    ##  3rd Qu.:104.10   3rd Qu.: 782.7   3rd Qu.:0.10530   3rd Qu.:0.13040  
    ##  Max.   :188.50   Max.   :2501.0   Max.   :0.16340   Max.   :0.34540  
    ##  concavity_mean    concave_points_mean symmetry_mean    fractal_dimension_mean
    ##  Min.   :0.00000   Min.   :0.00000     Min.   :0.1060   Min.   :0.04996       
    ##  1st Qu.:0.02956   1st Qu.:0.02031     1st Qu.:0.1619   1st Qu.:0.05770       
    ##  Median :0.06154   Median :0.03350     Median :0.1792   Median :0.06154       
    ##  Mean   :0.08880   Mean   :0.04892     Mean   :0.1812   Mean   :0.06280       
    ##  3rd Qu.:0.13070   3rd Qu.:0.07400     3rd Qu.:0.1957   3rd Qu.:0.06612       
    ##  Max.   :0.42680   Max.   :0.20120     Max.   :0.3040   Max.   :0.09744       
    ##    radius_se        texture_se      perimeter_se       area_se       
    ##  Min.   :0.1115   Min.   :0.3602   Min.   : 0.757   Min.   :  6.802  
    ##  1st Qu.:0.2324   1st Qu.:0.8339   1st Qu.: 1.606   1st Qu.: 17.850  
    ##  Median :0.3242   Median :1.1080   Median : 2.287   Median : 24.530  
    ##  Mean   :0.4052   Mean   :1.2169   Mean   : 2.866   Mean   : 40.337  
    ##  3rd Qu.:0.4789   3rd Qu.:1.4740   3rd Qu.: 3.357   3rd Qu.: 45.190  
    ##  Max.   :2.8730   Max.   :4.8850   Max.   :21.980   Max.   :542.200  
    ##  smoothness_se      compactness_se      concavity_se     concave_points_se 
    ##  Min.   :0.001713   Min.   :0.002252   Min.   :0.00000   Min.   :0.000000  
    ##  1st Qu.:0.005169   1st Qu.:0.013080   1st Qu.:0.01509   1st Qu.:0.007638  
    ##  Median :0.006380   Median :0.020450   Median :0.02589   Median :0.010930  
    ##  Mean   :0.007041   Mean   :0.025478   Mean   :0.03189   Mean   :0.011796  
    ##  3rd Qu.:0.008146   3rd Qu.:0.032450   3rd Qu.:0.04205   3rd Qu.:0.014710  
    ##  Max.   :0.031130   Max.   :0.135400   Max.   :0.39600   Max.   :0.052790  
    ##   symmetry_se       fractal_dimension_se  radius_worst   texture_worst  
    ##  Min.   :0.007882   Min.   :0.0008948    Min.   : 7.93   Min.   :12.02  
    ##  1st Qu.:0.015160   1st Qu.:0.0022480    1st Qu.:13.01   1st Qu.:21.08  
    ##  Median :0.018730   Median :0.0031870    Median :14.97   Median :25.41  
    ##  Mean   :0.020542   Mean   :0.0037949    Mean   :16.27   Mean   :25.68  
    ##  3rd Qu.:0.023480   3rd Qu.:0.0045580    3rd Qu.:18.79   3rd Qu.:29.72  
    ##  Max.   :0.078950   Max.   :0.0298400    Max.   :36.04   Max.   :49.54  
    ##  perimeter_worst    area_worst     smoothness_worst  compactness_worst
    ##  Min.   : 50.41   Min.   : 185.2   Min.   :0.07117   Min.   :0.02729  
    ##  1st Qu.: 84.11   1st Qu.: 515.3   1st Qu.:0.11660   1st Qu.:0.14720  
    ##  Median : 97.66   Median : 686.5   Median :0.13130   Median :0.21190  
    ##  Mean   :107.26   Mean   : 880.6   Mean   :0.13237   Mean   :0.25427  
    ##  3rd Qu.:125.40   3rd Qu.:1084.0   3rd Qu.:0.14600   3rd Qu.:0.33910  
    ##  Max.   :251.20   Max.   :4254.0   Max.   :0.22260   Max.   :1.05800  
    ##  concavity_worst  concave_points_worst symmetry_worst   fractal_dimension_worst
    ##  Min.   :0.0000   Min.   :0.00000      Min.   :0.1565   Min.   :0.05504        
    ##  1st Qu.:0.1145   1st Qu.:0.06493      1st Qu.:0.2504   1st Qu.:0.07146        
    ##  Median :0.2267   Median :0.09993      Median :0.2822   Median :0.08004        
    ##  Mean   :0.2722   Mean   :0.11461      Mean   :0.2901   Mean   :0.08395        
    ##  3rd Qu.:0.3829   3rd Qu.:0.16140      3rd Qu.:0.3179   3rd Qu.:0.09208        
    ##  Max.   :1.2520   Max.   :0.29100      Max.   :0.6638   Max.   :0.20750

### apt\_buildings

Dataset from The City of Toronto’s Open Data Portal. It currently has
3455 rows and 37 columns.

    apt_buildings

    ## # A tibble: 3,455 × 37
    ##       id air_conditioning amenities    balconies barrier_free_acc… bike_parking 
    ##    <dbl> <chr>            <chr>        <chr>     <chr>             <chr>        
    ##  1 10359 NONE             Outdoor rec… YES       YES               0 indoor par…
    ##  2 10360 NONE             Outdoor pool YES       NO                0 indoor par…
    ##  3 10361 NONE             <NA>         YES       NO                Not Available
    ##  4 10362 NONE             <NA>         YES       YES               Not Available
    ##  5 10363 NONE             <NA>         NO        NO                12 indoor pa…
    ##  6 10364 NONE             <NA>         NO        NO                Not Available
    ##  7 10365 NONE             <NA>         NO        YES               Not Available
    ##  8 10366 CENTRAL AIR      Indoor pool… YES       NO                Not Available
    ##  9 10367 NONE             <NA>         YES       YES               0 indoor par…
    ## 10 10368 NONE             Indoor recr… YES       YES               Not Available
    ## # … with 3,445 more rows, and 31 more variables: exterior_fire_escape <chr>,
    ## #   fire_alarm <chr>, garbage_chutes <chr>, heating_type <chr>, intercom <chr>,
    ## #   laundry_room <chr>, locker_or_storage_room <chr>, no_of_elevators <dbl>,
    ## #   parking_type <chr>, pets_allowed <chr>, prop_management_company_name <chr>,
    ## #   property_type <chr>, rsn <dbl>, separate_gas_meters <chr>,
    ## #   separate_hydro_meters <chr>, separate_water_meters <chr>,
    ## #   site_address <chr>, sprinkler_system <chr>, visitor_parking <chr>, …

The last overview from `apt_buildings` has 37 features, where nine are
numerical. Within the numerical, the `no_barrier_free_accessible_units`
is the feature with more missing values: 154. Nevertheless, there are no
quantitative variables.

    summary(apt_buildings)

    ##        id        air_conditioning    amenities          balconies        
    ##  Min.   :10359   Length:3455        Length:3455        Length:3455       
    ##  1st Qu.:11222   Class :character   Class :character   Class :character  
    ##  Median :12086   Mode  :character   Mode  :character   Mode  :character  
    ##  Mean   :12086                                                           
    ##  3rd Qu.:12950                                                           
    ##  Max.   :13813                                                           
    ##                                                                          
    ##  barrier_free_accessibilty_entr bike_parking       exterior_fire_escape
    ##  Length:3455                    Length:3455        Length:3455         
    ##  Class :character               Class :character   Class :character    
    ##  Mode  :character               Mode  :character   Mode  :character    
    ##                                                                        
    ##                                                                        
    ##                                                                        
    ##                                                                        
    ##   fire_alarm        garbage_chutes     heating_type         intercom        
    ##  Length:3455        Length:3455        Length:3455        Length:3455       
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##  laundry_room       locker_or_storage_room no_of_elevators  parking_type      
    ##  Length:3455        Length:3455            Min.   : 0.000   Length:3455       
    ##  Class :character   Class :character       1st Qu.: 0.000   Class :character  
    ##  Mode  :character   Mode  :character       Median : 1.000   Mode  :character  
    ##                                            Mean   : 1.208                     
    ##                                            3rd Qu.: 2.000                     
    ##                                            Max.   :32.000                     
    ##                                            NA's   :5                          
    ##  pets_allowed       prop_management_company_name property_type     
    ##  Length:3455        Length:3455                  Length:3455       
    ##  Class :character   Class :character             Class :character  
    ##  Mode  :character   Mode  :character             Mode  :character  
    ##                                                                    
    ##                                                                    
    ##                                                                    
    ##                                                                    
    ##       rsn          separate_gas_meters separate_hydro_meters
    ##  Min.   :4152554   Length:3455         Length:3455          
    ##  1st Qu.:4153682   Class :character    Class :character     
    ##  Median :4154598   Mode  :character    Mode  :character     
    ##  Mean   :4167867                                            
    ##  3rd Qu.:4155538                                            
    ##  Max.   :4716349                                            
    ##                                                             
    ##  separate_water_meters site_address       sprinkler_system   visitor_parking   
    ##  Length:3455           Length:3455        Length:3455        Length:3455       
    ##  Class :character      Class :character   Class :character   Class :character  
    ##  Mode  :character      Mode  :character   Mode  :character   Mode  :character  
    ##                                                                                
    ##                                                                                
    ##                                                                                
    ##                                                                                
    ##      ward           window_type          year_built   year_registered
    ##  Length:3455        Length:3455        Min.   :1805   Min.   :2017   
    ##  Class :character   Class :character   1st Qu.:1955   1st Qu.:2017   
    ##  Mode  :character   Mode  :character   Median :1962   Median :2017   
    ##                                        Mean   :1962   Mean   :2017   
    ##                                        3rd Qu.:1970   3rd Qu.:2017   
    ##                                        Max.   :2019   Max.   :2020   
    ##                                        NA's   :13     NA's   :89     
    ##  no_of_storeys    emergency_power    non-smoking_building  no_of_units     
    ##  Min.   : 0.000   Length:3455        Length:3455          Min.   :   0.00  
    ##  1st Qu.: 3.000   Class :character   Class :character     1st Qu.:  25.00  
    ##  Median : 5.000   Mode  :character   Mode  :character     Median :  52.00  
    ##  Mean   : 7.738                                           Mean   :  91.09  
    ##  3rd Qu.:10.000                                           3rd Qu.: 124.00  
    ##  Max.   :51.000                                           Max.   :4111.00  
    ##                                                                            
    ##  no_of_accessible_parking_spaces facilities_available cooling_room      
    ##  Min.   :  0.000                 Length:3455          Length:3455       
    ##  1st Qu.:  0.000                 Class :character     Class :character  
    ##  Median :  1.000                 Mode  :character     Mode  :character  
    ##  Mean   :  6.559                                                        
    ##  3rd Qu.:  5.000                                                        
    ##  Max.   :340.000                                                        
    ##  NA's   :123                                                            
    ##  no_barrier_free_accessible_units
    ##  Min.   :  0.000                 
    ##  1st Qu.:  0.000                 
    ##  Median :  0.000                 
    ##  Mean   :  9.408                 
    ##  3rd Qu.:  1.000                 
    ##  Max.   :474.000                 
    ##  NA's   :154

## My decision

From these four datasets, I
chose **`flow_sample`** and **`vancouver_trees`**. Based on my knowledge
of dplyr I found at least three attributes about each of these datasets
that might be helpful for the analysis.

1.  **`vancouver_trees`**: Acquired courtesy of The City of Vancouver’s
    Open Data Portal. This data set has 146,611 rows with 20 variables.
    This data set has different categorical values such as
    `species_name`, `cultivar_name`, `common_nam`e, and others used for
    the analysis. The diameter values are the only quantitative data and
    can be used to do statistical analysis. The categorical values also
    can be used to analyze their frequency. This is a large dataset that
    could be appropriate to analyze.

2.  **`flow_sample`**: Acquired courtesy of The Government of Canada’s
    Historical Hydrometric Database. The first attribute is that this
    dataset has 218 rows and 7 columns (variables); from which the
    variables `year`, `month`, `day`and `station_id` are numerical, and
    also they are classified as categorical (second attribute). Finally,
    the third attribute is the variable `flow`, their values are
    quantitative data that can be used to do a statistical analysis.

Finally, I chose the **`vancouver_trees`** dataset since it has
different types of variables. Compared with the dataset
**`flow-sample`**, the **`vancouver_trees`** has a more significant
number of rows and might be interesting to analyze.

# Exploring my dataset

For this task, I completed *4 out of the 8 exercises* to dive deeper
into my data. These tasks made sense for the dataset
**`vancouver_trees`** (using *`dplyr`*and *`ggplot`*). For each of the 4
exercises that I completed, I provide a brief explanation of why I chose
each one.

## Distribution

Many of the variables that data scientists study are *quantitative* or
*numerical*. Their values are numbers on which we can perform
arithmetic. An example of this is the diameter of the trees in the
dataset that I chose.

Using `ggplot2` and the `facet_zoom`, we can visualize the distribution
of the diameter of each tree. In the graph on the right, all the data is
shown; however, it seems to be some outliers. For this reason, I use the
facet\_zoom function that will help to zoom in on the graph. Within this
function, I can delimit the values ​​of x and y. However, the values
​​are very varied. Perhaps a good way to visualize would be to classify
the data by neighborhood, and thus, we will have a better visualization
of the distribution.

    vancouver_trees %>% 
      ggplot(aes(diameter)) +
      geom_bar() +
      facet_zoom(ylim = c(0, 100), xlim=c(0,100))

![](Minidata_files/figure-markdown_strict/unnamed-chunk-10-1.png)

## Filter

I am interested in visualizing all the trees corresponding to Genus
“ACER” in the Kitsilano neighborhood. For this reason, I created a new
table using the function `filter`.

Something fundamental to mention is that within this work, two packages
include the function filter: `dplyr::filter()`and `stats::filter()`.
Therefore, it is essential to specify that the function`filter,`used in
this section, is from the package `dplyr` and not `stats`. Moreover, we
visualize only 2,211 rows corresponding to the trees with the ACER genus
in the Kitsilano neighborhood using the function `glimpse`and the new
table `Kitsilano_acertrees`.

    Kitsilano_acertrees<-(dplyr::filter(vancouver_trees,neighbourhood_name == "KITSILANO" & genus_name == "ACER"))
    glimpse(Kitsilano_acertrees)

    ## Rows: 2,211
    ## Columns: 20
    ## $ tree_id            <dbl> 155445, 155446, 156390, 156487, 156489, 156493, 156…
    ## $ civic_number       <dbl> 2408, 2246, 2375, 2295, 2050, 2091, 2186, 2198, 229…
    ## $ std_street         <chr> "W 13TH AV", "W 15TH AV", "W 10TH AV", "W 6TH AV", …
    ## $ genus_name         <chr> "ACER", "ACER", "ACER", "ACER", "ACER", "ACER", "AC…
    ## $ species_name       <chr> "FREEMANI   X", "RUBRUM", "CAMPESTRE", "PLATANOIDES…
    ## $ cultivar_name      <chr> "SCARLET SENTINEL", "KARPICK", NA, "GLOBOSUM", NA, …
    ## $ common_name        <chr> "FREEMAN'S S.S. MAPLE", "KARPICK RED MAPLE", "HEDGE…
    ## $ assigned           <chr> "N", "N", "N", "N", "N", "N", "N", "N", "N", "N", "…
    ## $ root_barrier       <chr> "N", "N", "N", "N", "N", "N", "N", "N", "N", "N", "…
    ## $ plant_area         <chr> "N", "5", "8", "4", "4", "4", "4", "4", "4", "7", "…
    ## $ on_street_block    <dbl> 2900, 2200, 2300, 2100, 2000, 2000, 2100, 2100, 210…
    ## $ on_street          <chr> "BALSAM ST", "W 15TH AV", "W 10TH AV", "VINE ST", "…
    ## $ neighbourhood_name <chr> "KITSILANO", "KITSILANO", "KITSILANO", "KITSILANO",…
    ## $ street_side_name   <chr> "ODD", "EVEN", "ODD", "EVEN", "EVEN", "ODD", "EVEN"…
    ## $ height_range_id    <dbl> 2, 4, 2, 2, 2, 3, 2, 2, 2, 2, 2, 4, 4, 2, 2, 2, 2, …
    ## $ diameter           <dbl> 3.00, 16.00, 10.00, 5.00, 10.00, 7.00, 8.00, 11.00,…
    ## $ curb               <chr> "N", "Y", "Y", "Y", "Y", "Y", "Y", "Y", "Y", "Y", "…
    ## $ date_planted       <date> 1997-03-20, 1996-04-16, 1994-11-21, 1994-11-15, 19…
    ## $ longitude          <dbl> -123.1603, -123.1565, -123.1594, -123.1575, -123.15…
    ## $ latitude           <dbl> 49.25995, 49.25823, 49.26301, 49.26662, 49.26759, 4…

    summary(Kitsilano_acertrees)

    ##     tree_id        civic_number   std_street         genus_name       
    ##  Min.   :   574   Min.   : 745   Length:2211        Length:2211       
    ##  1st Qu.: 11016   1st Qu.:2085   Class :character   Class :character  
    ##  Median : 70209   Median :2465   Mode  :character   Mode  :character  
    ##  Mean   : 95497   Mean   :2555                                        
    ##  3rd Qu.:172782   3rd Qu.:3004                                        
    ##  Max.   :265701   Max.   :3757                                        
    ##                                                                       
    ##  species_name       cultivar_name      common_name          assigned        
    ##  Length:2211        Length:2211        Length:2211        Length:2211       
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##  root_barrier        plant_area        on_street_block  on_street        
    ##  Length:2211        Length:2211        Min.   : 700    Length:2211       
    ##  Class :character   Class :character   1st Qu.:2000    Class :character  
    ##  Mode  :character   Mode  :character   Median :2500    Mode  :character  
    ##                                        Mean   :2527                      
    ##                                        3rd Qu.:3000                      
    ##                                        Max.   :3700                      
    ##                                                                          
    ##  neighbourhood_name street_side_name   height_range_id    diameter     
    ##  Length:2211        Length:2211        Min.   :0.000   Min.   :  0.00  
    ##  Class :character   Class :character   1st Qu.:2.000   1st Qu.:  5.50  
    ##  Mode  :character   Mode  :character   Median :4.000   Median : 15.00  
    ##                                        Mean   :3.667   Mean   : 15.44  
    ##                                        3rd Qu.:5.000   3rd Qu.: 24.00  
    ##                                        Max.   :8.000   Max.   :317.00  
    ##                                                                        
    ##      curb            date_planted          longitude         latitude    
    ##  Length:2211        Min.   :1990-12-12   Min.   :-123.2   Min.   :49.26  
    ##  Class :character   1st Qu.:2001-04-01   1st Qu.:-123.2   1st Qu.:49.26  
    ##  Mode  :character   Median :2007-02-23   Median :-123.2   Median :49.26  
    ##                     Mean   :2006-04-09   Mean   :-123.2   Mean   :49.26  
    ##                     3rd Qu.:2011-02-07   3rd Qu.:-123.2   3rd Qu.:49.27  
    ##                     Max.   :2019-05-02   Max.   :-123.0   Max.   :49.28  
    ##                     NA's   :1524         NA's   :268      NA's   :268

In the following plot, we can visualize the distribution of the diameter
of all the trees that correspond to Genus “ACER” in the Kitsilano
neighborhood. As I mentioned before, since I narrow the values we have a
better visualization of the distribution.

    Kitsilano_acertrees %>% 
      ggplot(aes(diameter)) +
      geom_bar() +
      facet_zoom(ylim = c(0, 100), xlim=c(0,60))

![](Minidata_files/figure-markdown_strict/unnamed-chunk-12-1.png)

## Frequency

Considering this last dataset **Kitsilano\_acertrees**, I plot the
frequency of height range of the Acer trees in Kitsilano. The height
range is classify as follow: 0-10 for every 10 feet (e.g., 0 = 0-10 ft,
1 = 10-20 ft, 2 = 20-30 ft, and10 = 100+ ft).

I divided the values of trees planted by the curb and the not planted by
the curb. Using a boxplot, we can see more trees by the curb (Curb
=“Y”), and the frequency is lower at a higher height range in both
cases. Also, there is a red dot representing the mean for each dataset.

As I already mentioned, the classification of the height range is an
integer (from the Latin integer meaning “whole”) is colloquially
**defined as a number that can be written without a fractional
component**. Integers are *discrete*, not continuous, but I use this
variable as “continuous” in order to exemplify the usage of the mean in
this type of plot.

    ggplot(Kitsilano_acertrees, aes(x=height_range_id, y=curb)) +
      geom_boxplot(width = 0.4, fill = "white") +
      geom_jitter( 
                  width = 0.1, size = 1,alpha = 0.5)+
      stat_summary(fun=mean, geom="point", shape=20, size=5, color="red", fill="red")

![](Minidata_files/figure-markdown_strict/unnamed-chunk-13-1.png)

## Missing Values

In this fourth and last section, I Investigated how many missing values
are per variable. Missing values are ubiquitous in data and need to be
carefully explored and handled.

Using the `cbind` and `lapply`function I get the list of all the
variables, and the count of the **NA** found within those variables.
`Lapply`function helps perform operations on list objects and returns a
list object of the same length as the original set.

    cbind(
       lapply(
         lapply(vancouver_trees, is.na)
         , sum)
       )

    ##                    [,1] 
    ## tree_id            0    
    ## civic_number       0    
    ## std_street         0    
    ## genus_name         0    
    ## species_name       0    
    ## cultivar_name      67559
    ## common_name        0    
    ## assigned           0    
    ## root_barrier       0    
    ## plant_area         1486 
    ## on_street_block    0    
    ## on_street          0    
    ## neighbourhood_name 0    
    ## street_side_name   0    
    ## height_range_id    0    
    ## diameter           0    
    ## curb               0    
    ## date_planted       76548
    ## longitude          22771
    ## latitude           22771

Moreover, if we want to visualize these data in a graph, we can use the
function `gg_miss_var`. This function will help us visualize the whole
data frame at once and provide information on whether the data is
missing.

So here, the `date_planted` (76,548 values missing) and `cultivar_name`
(67,559 values missing) have the most missing data.

    gg_miss_var(vancouver_trees) + labs(y = "How many values are missing")

    ## Warning: It is deprecated to specify `guide = FALSE` to remove a guide. Please
    ## use `guide = "none"` instead.

![](Minidata_files/figure-markdown_strict/unnamed-chunk-15-1.png)

We can also identify missing key variables using `vis_miss_upset` and
their relationship among the missing values of different variables. The
default option of `gg_miss_upset` is taken from `UpSetR::upset` - which
is to use up to 5 sets and up to 40 interactions. Some examples of these
interaction are :

-   40,141 rows have missing values of `cultivar_name`and `date_planted`
    at the same time.

-   it shows 165 rows have missing values in `plant_area`, `longitude`,
    `latitude`, `cultivar_name` and `date_ planted` at the same time

<!-- -->

    gg_miss_upset(vancouver_trees)

![](Minidata_files/figure-markdown_strict/unnamed-chunk-16-1.png)

# Research questions

So far, I have chosen a dataset and gotten familiar with it through
exploring the data. Now it’s time to figure out 4 research questions
that I would like to answer with my data.

-   Within three different neighborhoods, I would like to know which is
    the distribution of the tree genus.

-   Is there any relationship between the genus and the fact that these
    trees have a root barrier? Perhaps some specific genus trees need a
    root barrier. I will choose some genus or specific trees and then
    analyze the possible correlation.

-   I will choose a tree within the list of `common_name`, and I will
    analyze the relationship between the diameter and the
    `date_planted`. The question I would like to answer is: can I find
    any correlations between these two values? I hypothesize that if the
    trees are older, the diameter would be larger.

-   Finally, I would like to see the distribution of the trees in
    specific neighborhoods (perhaps Kitsilano) regarding the plant\_area
    and their clasification B = behind sidewalk, G = in tree grate, N =
    no sidewalk, and C = cutout. Specifically, I can analyze how many
    trees are cut out and see which street block they are.
