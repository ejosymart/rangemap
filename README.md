README
================
May 16, 2018

-   [Package description](#package-description)
-   [Installing the package](#installing-the-package)
-   [Using the package functions](#using-the-package-functions)
    -   [Setting R up](#setting-r-up)
    -   [Simple graphical exploration of your data.](#simple-graphical-exploration-of-your-data.)
    -   [Species ranges from buffered occurrences](#species-ranges-from-buffered-occurrences)
    -   [Species ranges from boundaries](#species-ranges-from-boundaries)
    -   [Species ranges from hull polygons](#species-ranges-from-hull-polygons)
    -   [Species ranges from ecological niche models](#species-ranges-from-ecological-niche-models)
    -   [Species ranges using trend surface analyses](#species-ranges-using-trend-surface-analyses)
    -   [Nice fugures of species ranges](#nice-fugures-of-species-ranges)
    -   [Species ranges in the environmental space](#species-ranges-in-the-environmental-space)

<br>

### Package description

The **rangemap** R package presents various tools to create species range maps based on occurrence data, statistics, and distinct shapefiles. Other tools of this package can be used to analyze environmental characteristics of the species ranges and to create high quality figures of these maps.

<br>

### Installing the package

**rangemap** is in a GitHub repository and can be installed and/or loaded using the following code (make sure to have Internet connection).

``` r
# Installing and loading packages
if(!require(devtools)){
    install.packages("devtools")
    library(devtools)
}

if(!require(rangemap)){
    devtools::install_github("marlonecobos/rangemap")
    library(rangemap)
}
```

<br>

### Using the package functions

#### Setting R up

``` r
# dependincies
if(!require(rgbif)){
  install.packages("rgbif")
  library(rgbif)
}

if(!require(devtools)){
install.packages("devtools")
library(devtools)
}

if(!require(kuenm)){
install_github("marlonecobos/kuenm")
library(kuenm)
}

if(!require(maps)){
install.packages("maps")
library(maps)
}

if(!require(maptools)){
 install.packages("maptools")
 library(maptools)
}

# working directory
setwd("YOUR/WORKING/DIRECTORY")
```

#### Simple graphical exploration of your data.

The *rangemap\_explore* function .

``` r
# getting the data from GBIF
species <- name_lookup(query = "Dasypus kappleri",
                       rank="species", return = "data") # information about the species

occ_count(taxonKey = species$key[14], georeferenced = TRUE) # testing if keys return records

key <- species$key[14] # using species key that return information

occ <- occ_search(taxonKey = key, return = "data") # using the taxon key

# keeping only georeferenced records
occ_g <- occ[!is.na(occ$decimalLatitude) & !is.na(occ$decimalLongitude),
             c("name", "decimalLongitude", "decimalLatitude")]

# simple figure of the species occurrence data
explore_map <- rangemap_explore(occurrences = occ_g)

dev.off() # for returning to default par settings
```

<br>

#### Species ranges from buffered occurrences

The *rangemap\_buff* function .

``` r
# getting the data from GBIF
species <- name_lookup(query = "Peltophryne empusa",
                       rank="species", return = "data") # information about the species

occ_count(taxonKey = species$key[1], georeferenced = TRUE) # testing if keys return records

key <- species$key[1] # using species key that return information

occ <- occ_search(taxonKey = key, return = "data") # using the taxon key

# keeping only georeferenced records
occ_g <- occ[!is.na(occ$decimalLatitude) & !is.na(occ$decimalLongitude),
            c("name", "decimalLongitude", "decimalLatitude")]

# buffer distance
dist <- 100000
save <- TRUE
name <- "test"

buff_range <- rangemap_buff(occurrences = occ_g, buffer_distance = dist,
                            save_shp = save, name = name)
```

<br>

#### Species ranges from boundaries

The *rangemap\_bound* function .

``` r
# getting the data from GBIF
species <- name_lookup(query = "Dasypus kappleri",
                       rank="species", return = "data") # information about the species

occ_count(taxonKey = species$key[14], georeferenced = TRUE) # testing if keys return records

key <- species$key[14] # using species key that return information

occ <- occ_search(taxonKey = key, return = "data") # using the taxon key

# keeping only georeferenced records
occ_g <- occ[!is.na(occ$decimalLatitude) & !is.na(occ$decimalLongitude),
            c("name", "decimalLongitude", "decimalLatitude")]

level <- 0
dissolve <- FALSE
save <- TRUE
name <- "test1"
countries <- c("PER", "BRA", "COL", "VEN", "ECU", "GUF", "GUY", "SUR", "BOL")

bound_range <- rangemap_bound(occurrences = occ_g, country_code = countries, boundary_level = level,
                              dissolve = dissolve, save_shp = save, name = name)
```

<br>

#### Species ranges from hull polygons

The *rangemap\_hull* function .

Convex hulls

``` r
# getting the data from GBIF
species <- name_lookup(query = "Dasypus kappleri",
rank="species", return = "data") # information about the species

occ_count(taxonKey = species$key[14], georeferenced = TRUE) # testing if keys return records

key <- species$key[14] # using species key that return information

occ <- occ_search(taxonKey = key, return = "data", limit = 2000) # using the taxon key

# keeping only georeferenced records
occ_g <- occ[!is.na(occ$decimalLatitude) & !is.na(occ$decimalLongitude),
             c("name", "decimalLongitude", "decimalLatitude")]

# unique polygon (non-disjunct distribution)
dist <- 100000
hull <- "convex" 
split <- FALSE
save <- TRUE
name <- "test2"

hull_range <- rangemap_hull(occurrences = occ_g, hull_type = hull, buffer_distance = dist,
                            split = split, save_shp = save, name = name)

# disjunct distributions
## clustering occurrences with the hierarchical method
split <- TRUE
c_method <- "hierarchical"
split_d <- 1500000
name <- "test3"

hull_range1 <- rangemap_hull(occurrences = occ_g, hull_type = hull, buffer_distance = dist,
                            split = split, cluster_method = c_method, split_distance = split_d,
                            save_shp = save, name = name)

## clustering occurrences with the k-means method
c_method <- "k-means"
n_clus <- 3
name <- "test4"

hull_range2 <- rangemap_hull(occurrences = occ_g, hull_type = hull, buffer_distance = dist,
                            split = split, cluster_method = c_method, n_k_means = n_clus,
                            save_shp = save, name = name)
```

<br>

Concave hulls .

``` r
# unique polygon (non-disjunct distribution)
dist <- 100000
hull <- "concave" 
split <- FALSE
save <- TRUE
name <- "test5"

hull_range3 <- rangemap_hull(occurrences = occ_g, hull_type = hull, buffer_distance = dist,
                            split = split, save_shp = save, name = name)

# disjunct distributions
## clustering occurrences with the hierarchical method
split <- TRUE
c_method <- "hierarchical"
split_d <- 1500000
name <- "test6"

hull_range4 <- rangemap_hull(occurrences = occ_g, hull_type = hull, buffer_distance = dist,
                            split = split, cluster_method = c_method, split_distance = split_d,
                            save_shp = save, name = name)

## clustering occurrences with the k-means method
c_method <- "k-means"
n_clus <- 3
name <- "test7"

hull_range5 <- rangemap_hull(occurrences = occ_g, hull_type = hull, buffer_distance = dist,
                            split = split, cluster_method = c_method, n_k_means = n_clus,
                            save_shp = save, name = name)
```

<br>

#### Species ranges from ecological niche models

The *rangemap\_enm* function .

``` r
# parameters
data(sp_mod)
data(sp_train)
occ_sp <- data.frame("A_americanum", sp_train)
thres <- 5
save <- TRUE
name <- "test8"

enm_range <- rangemap_enm(occurrences = occ_sp, model = sp_mod,  threshold = thres,
                          save_shp = save, name = name)
```

<br>

#### Species ranges using trend surface analyses

The *rangemap\_tsa* function .

``` r
# getting the data from GBIF
species <- name_lookup(query = "Peltophryne fustiger",
                       rank="species", return = "data") # information about the species

occ_count(taxonKey = species$key[5], georeferenced = TRUE) # testing if keys return records

key <- species$key[5] # using species key that return information

occ <- occ_search(taxonKey = key, return = "data") # using the taxon key

# keeping only georeferenced records
occ_g <- occ[!is.na(occ$decimalLatitude) & !is.na(occ$decimalLongitude),
             c("name", "decimalLongitude", "decimalLatitude")]

# region of interest
WGS84 <- CRS("+proj=longlat +datum=WGS84 +ellps=WGS84 +towgs84=0,0,0")
w_map <- map(database = "world", regions = "Cuba", fill = TRUE, plot = FALSE) # map of the world
w_po <- sapply(strsplit(w_map$names, ":"), function(x) x[1]) # preparing data to create polygon
reg <- map2SpatialPolygons(w_map, IDs = w_po, proj4string = WGS84) # map to polygon

# other data
res <- 1
thr <- 0
save <- TRUE
name <- "test"

tsa <- rangemap_tsa(occurrences = occ_g, region_of_interest = reg, threshold = thr,
                    resolution = res, save_shp = save, name = name)
```

<br>

#### Nice fugures of species ranges

The *rangemap\_fig* function .

``` r
# arguments for the species range figure
extent <- TRUE
occ <- TRUE
grid <- TRUE
sides <- "bottomleft"

# creating the species range figure
range_map <- rangemap_fig(range, add_extent = extent, add_occurrences = occ,
                          grid = grid, sides = sides)

dev.off() # for returning to default par settings
```

<br>

#### Species ranges in the environmental space

The *rangemap\_envcomp* function .

``` r
# 
```
