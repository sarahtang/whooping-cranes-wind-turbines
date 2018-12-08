Wind Turbine and Bird Analysis
================
Sarah Tang

Set Up
------

Introduction
------------

This module aims to examine and better understand how wind turbine development impacts bird populations, specifically for the whooping crane (Grus americana), one of the most endangered birds in North America. As land use needed for wind energy increases, it is crucial to understand the relationship between wind turbines, both while operating and installing, and animals' responses and wildlife habitat.

Whooping crane corridor - <https://www.sciencebase.gov/catalog/item/5a314a72e4b08e6a89d707e0> Whooping crane - <https://www.arcgis.com/home/item.html?id=7d27571bc3454a1c91edf665c67b9c3e>

Loading in files
----------------

``` r
wind_turbines <- st_read("uswtdbSHP/uswtdb_v1_2_20181001.shp", quiet=TRUE)
whooping_crane_corridors <- st_read("WHCR_corridors/WHCR_corridors.shp", quiet=TRUE)

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

Mapping Wind Turbines and Whooping Crane Migratory Paths
========================================================

Next, I plot the maps of the location of wind turbines and whooping crane patterns using `ggplot` and the `geom_sf` command.

``` r
#ggplot(wind_turbines) + geom_sf()
tm_shape(wind_turbines) + tm_dots()
```

![](analysis_files/figure-markdown_github/unnamed-chunk-3-1.png)

``` r
ggplot(whooping_crane_corridors) + geom_sf() + ggtitle("Whooping Crane Migration Corridor")
```

![](analysis_files/figure-markdown_github/unnamed-chunk-4-1.png)

I then overlay the two maps onto a map of the United States.

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
  tm_polygons("state_name", legend.show = FALSE) +
  tm_shape(whooping_crane_corridors) +
  tm_polygons() +
  tm_shape(wind_turbines) +
  tm_dots() +
  tm_grid(projection = "longlat", labels.size = .5, n.x = 5, n.y = 5) +
  tmap_options(max.categories = 52)
```

![](analysis_files/figure-markdown_github/unnamed-chunk-5-1.png)

### Are there overlaps in the location of wind turbine sites and whooping crane migratory patterns?

*Yes! By overlaying the three different shape files, we can see that where there is high wind in the United States (in the midwest), is where wind turbines are primarily located.*

Other bird populations?
=======================

``` r
genus <- "Grus"
species <- "americana"
api_call <- paste0("http://api.iucnredlist.org/index/species/", genus, "-", species, ".json")

resp <- GET(api_call)
resp
```

    ## Response [http://api.iucnredlist.org/index/species/Grus-americana.json]
    ##   Date: 2018-12-08 02:41
    ##   Status: 200
    ##   Content-Type: application/json; charset=utf-8
    ##   Size: 1.02 kB

``` r
status <- httr::status_code(resp)

out <- content(resp, as = "text")
df <- jsonlite::fromJSON(out)
rationale <- df$rationale
df
```

    ##   scientific_name primary  kingdom   phylum class      order  family genus
    ## 1  Grus americana    TRUE ANIMALIA CHORDATA  AVES GRUIFORMES GRUIDAE  Grus
    ##     species        authority infra_name infra_rank infra_authority
    ## 1 americana (Linnaeus, 1758)         NA         NA              NA
    ##   stock_name species_id main_common_name modified_year category criteria
    ## 1         NA   22692156   Whooping Crane          2012       EN        D
    ##   assessmentid trend_id biome_marine biome_freshwater biome_terrestrial
    ## 1     38458501        1         TRUE             TRUE              TRUE
    ##   taxonomicnotes
    ## 1             NA
    ##                                                                                                                                                                                                                                                                                                                                                                                                         rationale
    ## 1 This species is listed as Endangered because it has an extremely small population. However, the conservation status of the species is improving, with not only increases in the natural wild population but also establishment of two reintroduced flocks that may become self-sustaining. If the number of mature individuals continues to increase, this species may merit downlisting to Vulnerable. <p></p>
    ##                 assessor                evaluator
    ## 1 BirdLife International Butchart, S. & Symes, A.

TODO:
=====

Custom R functions Interaction with an API? - maybe just get the birds? what birds are potentially endangered/extinct? Use lintr to clean code (checks adherence to a given style, syntax errors and possible semantic issues) Making layout and presentation into secondary output (e.g. .pdf, website) - should enhance presentaiton Use of spatial vector data (sf package) and visualization of spatial data

TODO
====

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
