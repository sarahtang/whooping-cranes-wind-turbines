analysis
================
Sarah Tang

Set Up
======

``` r
library(tidyverse)
library(ggplot2)
library(sf)
library(tmap)
library(USAboundaries)
library(fasterize)
```

Introduction
------------

This module aims to examine and better understand how wind turbine development impacts bird populations, specifically for the whooping crane (Grus americana), one of the most endangered birds in North America. As land use needed for wind energy increases, it is crucial to understand the relationship between wind turbines, both while operating and installing, and animals' responses and wildlife habitat.

Whooping crane corridor - <https://www.sciencebase.gov/catalog/item/5a314a72e4b08e6a89d707e0> Whooping crane - <https://www.arcgis.com/home/item.html?id=7d27571bc3454a1c91edf665c67b9c3e>

Loading in files
----------------

``` r
wind_turbines <- st_read("uswtdbSHP/uswtdb_v1_2_20181001.shp", quiet=TRUE)
whooping_crane_corridors <- st_read("WHCR_corridors/WHCR_corridors.shp", quiet=TRUE)
whooping_crane <- st_read("WhoopingCrane/WhoopingCraneHabitat.shp", quiet=TRUE)

wind_turbines
```

    ## Simple feature collection with 58185 features and 0 fields
    ## geometry type:  POINT
    ## dimension:      XY
    ## bbox:           xmin: -171.7131 ymin: 13.38938 xmax: 144.7227 ymax: 66.8399
    ## epsg (SRID):    4269
    ## proj4string:    +proj=longlat +datum=NAD83 +no_defs
    ## First 10 features:
    ##                      geometry
    ## 1  POINT (-102.0486 33.60797)
    ## 2  POINT (-102.0495 33.60793)
    ## 3  POINT (-102.0486 33.60919)
    ## 4  POINT (-98.26075 35.39525)
    ## 5     POINT (-98.2576 35.367)
    ## 6  POINT (-98.75173 43.70377)
    ## 7  POINT (-98.24736 35.39624)
    ## 8  POINT (-98.26535 35.37489)
    ## 9  POINT (-98.25227 35.39637)
    ## 10 POINT (-98.22907 35.35834)

``` r
whooping_crane_corridors
```

    ## Simple feature collection with 9 features and 3 fields
    ## geometry type:  POLYGON
    ## dimension:      XY
    ## bbox:           xmin: -1197376 ymin: -1322000 xmax: 32657.37 ymax: 2278000
    ## epsg (SRID):    NA
    ## proj4string:    +proj=aea +lat_1=20 +lat_2=60 +lat_0=40 +lon_0=-96 +x_0=0 +y_0=0 +datum=NAD83 +units=m +no_defs
    ##   Id Corridor CL                       geometry
    ## 1  0       95 NA POLYGON ((16808.16 -1322000...
    ## 2  0       75 NA POLYGON ((-31086.76 -132200...
    ## 3  0       50 NA POLYGON ((-44661.35 -132200...
    ## 4  0       95  E POLYGON ((32657.37 -1322000...
    ## 5  0       95  W POLYGON ((-170092 -1322000,...
    ## 6  0       75  E POLYGON ((1147.896 -1322000...
    ## 7  0       75  W POLYGON ((-123305.4 -132200...
    ## 8  0       50  E POLYGON ((-38234.65 -132200...
    ## 9  0       50  W POLYGON ((-101020.2 -132200...

``` r
whooping_crane
```

    ## Simple feature collection with 15331 features and 26 fields
    ## geometry type:  MULTIPOLYGON
    ## dimension:      XY
    ## bbox:           xmin: 239723.8 ymin: 4274293 xmax: 757123.8 ymax: 4543201
    ## epsg (SRID):    26913
    ## proj4string:    +proj=utm +zone=13 +datum=NAD83 +units=m +no_defs
    ## First 10 features:
    ##    FID_Stream FID_STRE_1   LENGTH STWAT_ID             NAME CO_FIPS
    ## 1        4018       3837 3419.834        1 East Shell Creek      69
    ## 2        4034       3853 4058.223        1             <NA>      69
    ## 3       17179      17017 4224.354        1             <NA>     125
    ## 4       17589      17429 4621.074        1       Crow Creek     123
    ## 5       18802      18641  198.527        1       Fish Creek      69
    ## 6       18803      18642  135.107        1             <NA>      69
    ## 7       18804      18643  170.080        1   Spotwood Creek      69
    ## 8       18806      18645  493.665        1             <NA>      69
    ## 9       18810      18649  555.067        1             <NA>      69
    ## 10      18812      18651  581.521        1       Fish Creek      69
    ##    Shape_Leng FID_CODivi DIV        BASIN Perimeter        Area    Acres
    ## 1   3419.8341          0   1 South Platte   1445412 72759746777 17979325
    ## 2   4058.2227          0   1 South Platte   1445412 72759746777 17979325
    ## 3   4224.3535          0   1 South Platte   1445412 72759746777 17979325
    ## 4   4621.0744          0   1 South Platte   1445412 72759746777 17979325
    ## 5    198.5272          0   1 South Platte   1445412 72759746777 17979325
    ## 6    135.1070          0   1 South Platte   1445412 72759746777 17979325
    ## 7    170.0801          0   1 South Platte   1445412 72759746777 17979325
    ## 8    493.6646          0   1 South Platte   1445412 72759746777 17979325
    ## 9    555.0668          0   1 South Platte   1445412 72759746777 17979325
    ## 10   581.5209          0   1 South Platte   1445412 72759746777 17979325
    ##    FID_Statew COUNTY CO_FIPS_1 POP_2000 HOUSEHO_00 SHAPE_Le_1 SHAPE_Area
    ## 1          -1   <NA>         0        0          0          0          0
    ## 2          -1   <NA>         0        0          0          0          0
    ## 3          -1   <NA>         0        0          0          0          0
    ## 4          -1   <NA>         0        0          0          0          0
    ## 5          -1   <NA>         0        0          0          0          0
    ## 6          -1   <NA>         0        0          0          0          0
    ## 7          -1   <NA>         0        0          0          0          0
    ## 8          -1   <NA>         0        0          0          0          0
    ## 9          -1   <NA>         0        0          0          0          0
    ## 10         -1   <NA>         0        0          0          0          0
    ##    BUFF_DIST ORIG_FID OBJECTID GNIS_ID GNIS_Name L_Miles
    ## 1         50        0        0    <NA>      <NA>       0
    ## 2         50        1        0    <NA>      <NA>       0
    ## 3         50        2        0    <NA>      <NA>       0
    ## 4         50        3        0    <NA>      <NA>       0
    ## 5         50        4        0    <NA>      <NA>       0
    ## 6         50        5        0    <NA>      <NA>       0
    ## 7         50        6        0    <NA>      <NA>       0
    ## 8         50        7        0    <NA>      <NA>       0
    ## 9         50        8        0    <NA>      <NA>       0
    ## 10        50        9        0    <NA>      <NA>       0
    ##                          geometry
    ## 1  MULTIPOLYGON (((427692.6 45...
    ## 2  MULTIPOLYGON (((497293 4538...
    ## 3  MULTIPOLYGON (((752161.2 44...
    ## 4  MULTIPOLYGON (((554221.5 45...
    ## 5  MULTIPOLYGON (((462454.6 45...
    ## 6  MULTIPOLYGON (((486626.7 45...
    ## 7  MULTIPOLYGON (((503211.9 45...
    ## 8  MULTIPOLYGON (((448002.7 45...
    ## 9  MULTIPOLYGON (((450098.4 45...
    ## 10 MULTIPOLYGON (((460302.6 45...

Mapping Wind Turbines
=====================

Next, I plot the maps of the location of wind turbines using `ggplot` and the `geom_sf` command.

``` r
ggplot(wind_turbines) + geom_sf()
```

![](analysis_files/figure-markdown_github/unnamed-chunk-3-1.png)

``` r
tm_shape(wind_turbines) + tm_dots()
```

![](analysis_files/figure-markdown_github/unnamed-chunk-3-2.png)

``` r
land <- us_states()
land
```

    ## Simple feature collection with 52 features and 12 fields
    ## geometry type:  MULTIPOLYGON
    ## dimension:      XY
    ## bbox:           xmin: -179.1743 ymin: 17.91377 xmax: 179.7739 ymax: 71.35256
    ## epsg (SRID):    4326
    ## proj4string:    +proj=longlat +datum=WGS84 +no_defs
    ## First 10 features:
    ##    statefp  statens    affgeoid geoid stusps                 name lsad
    ## 1       23 01779787 0400000US23    23     ME                Maine   00
    ## 2       15 01779782 0400000US15    15     HI               Hawaii   00
    ## 3       04 01779777 0400000US04    04     AZ              Arizona   00
    ## 4       05 00068085 0400000US05    05     AR             Arkansas   00
    ## 5       10 01779781 0400000US10    10     DE             Delaware   00
    ## 6       13 01705317 0400000US13    13     GA              Georgia   00
    ## 7       27 00662849 0400000US27    27     MN            Minnesota   00
    ## 8       02 01785533 0400000US02    02     AK               Alaska   00
    ## 9       06 01779778 0400000US06    06     CA           California   00
    ## 10      11 01702382 0400000US11    11     DC District of Columbia   00
    ##           aland       awater           state_name state_abbr
    ## 1  7.988522e+10  11748755195                Maine         ME
    ## 2  1.663410e+10  11777698394               Hawaii         HI
    ## 3  2.941986e+11   1027346486              Arizona         AZ
    ## 4  1.347715e+11   2960191698             Arkansas         AR
    ## 5  5.047195e+09   1398720828             Delaware         DE
    ## 6  1.491698e+11   4741100880              Georgia         GA
    ## 7  2.062323e+11  18929176411            Minnesota         MN
    ## 8  1.477946e+12 245390495931               Alaska         AK
    ## 9  4.035011e+11  20466718403           California         CA
    ## 10 1.583650e+08     18633403 District of Columbia         DC
    ##    jurisdiction_type                       geometry
    ## 1              state MULTIPOLYGON (((-68.92401 4...
    ## 2              state MULTIPOLYGON (((-156.0497 1...
    ## 3              state MULTIPOLYGON (((-114.7997 3...
    ## 4              state MULTIPOLYGON (((-94.61792 3...
    ## 5              state MULTIPOLYGON (((-75.77379 3...
    ## 6              state MULTIPOLYGON (((-85.60516 3...
    ## 7              state MULTIPOLYGON (((-97.22904 4...
    ## 8              state MULTIPOLYGON (((179.4813 51...
    ## 9              state MULTIPOLYGON (((-118.594 33...
    ## 10          district MULTIPOLYGON (((-77.11976 3...

``` r
tmap_mode(mode = "plot") #change mode to plot
```

    ## tmap mode set to plotting

``` r
tm_shape(land) +
  tm_polygons("state_name", lengend.show = FALSE)
```

    ## Warning: Number of levels of the variable "state_name" is 52, which is
    ## larger than max.categories (which is 30), so levels are combined. Set
    ## tmap_options(max.categories = 52) in the layer function to show all levels.

![](analysis_files/figure-markdown_github/unnamed-chunk-4-1.png)

Mapping Whooping Crane Migratory Paths
======================================

``` r
ggplot(whooping_crane_corridors) + geom_sf() + ggtitle("Whooping Crane Migration Corridor")
```

![](analysis_files/figure-markdown_github/unnamed-chunk-5-1.png)

Are there overlaps in the location of wind turbine sites and whooping crane migratory patterns?
-----------------------------------------------------------------------------------------------

TODO:
=====

Custom R functions Interaction with an API? - maybe just get the birds? what birds are potentially endangered/extinct? Use lintr to clean code (checks adherence to a given style, syntax errors and possible semantic issues) Making layout and presentation into secondary output (e.g. .pdf, website) - should enhance presentaiton Use of spatial vector data (sf package) and visualization of spatial data

TODO
====

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.