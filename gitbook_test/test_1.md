# Test\_1

## Data Pre-processing <a id="data"></a>

Data assembling or pre-processing, an operation required for running accurate analyses, is arguably one of the most time-consuming tasks of any data science project. This section documents the path required to convert the _raw_ data into data usable for high resolution mapping.

### Data sources <a id="sources"></a>

The 2017 household survey _Encuesta de Hogares de Propósitos Múltiples_ \(EHPM\), collected by the national statistical office of El Salvador \(DIGESTYC\), has been utilized to compute the three main indicators below:

* Average income, 
* Poverty, 
* Literacy. 

The contents of the 2017 survey have been stored and distributed under the file name `ehpm-2017.csv`. Moreover, it is also available in the DIGESTYC website [here](test_1.md). This publicly available data set is geo-referenced at the _canton level_. The DIGESTYC also provides the _segmento_ identifiers to allow for an analysis at the lowest administrative level. These identifiers are stored in the file named `Identificador de segmento.xlsx`.

A suite of geographic information system \(GIS\) layers have been used as potential predictors of the three indicators above. There details are categorized and listed in the table below. \`\`\`{r list-GIS-sources, tidy=FALSE, echo=F} table\_df=data.frame\("Names"=c\("Population density", "Precipitation", "Lights at night", "Intergrated Food Security Phase Classsification", "Livelihood Zones", "Altitude", "Soil degradation", "Buildings", "Points of interest points", "Roads network", "Vegetation Greenness \(NDVI\)", "Schools", "Health centres", "Hospitals", "Temperature", "Slope", "Distance to OSM major roads", "Distance to OSM major roads intersections", "Distance to OSM major waterway", "Built settlement", "Land cover"\), "Type"=c\("raster", "raster", "raster", "vector", "vector", "raster", "raster", "vector", "vector", "vector", "raster", "vector", "csv", "csv", "raster", "raster", "raster", "raster", "raster", "raster", "raster"\), "Source"=c\("Gridded Population of the World version 4, accessed [here](http://sedac.ciesin.columbia.edu/data/collection/gpw-v4)", "Climate Hazards Group InfraRed Precipitation with Station data \(CHIRPS\), accessed [here](http://chg.geog.ucsb.edu/data/chirps/index.html) and see [here](ftp://ftp.chg.ucsb.edu/pub/org/chg/products/CHIRPS-2.0/global_annual/netcdf/) for a FTP download", "NOAA, accessed [here](https://www.ngdc.noaa.gov/eog/viirs/download_dnb_composites.html#NTL_2015)", "FEWS Net, accessed [here](http://fews.net/fews-data/333)", "FEWS Net, accessed [here](http://fews.net/central-america-and-caribbean/el-salvador/livelihood-zone-map/september-2010)", "SRTMGL1 v003 NASA Shuttle Radar Topography Mission Global 1 arc second \(SRTMv003\), accessed via the [Global data explorer](https://gdex.cr.usgs.gov/gdex/), from NASA, details on SRTMv003 [here](https://lpdaac.usgs.gov/products/srtmgl1v003/)", "Trend.Earth, accessed via the QGIS plugin, user guide [here](http://trends.earth/docs/en)", "OpenStreetMap, accessed via the [Humanitarian Data Exchange platform](https://data.humdata.org) on the 02.02.2019. See [here](https://wiki.openstreetmap.org/wiki/Map_Features#Amenity)", "OpenStreetMap, accessed via the [Humanitarian Data Exchange platform](https://data.humdata.org) on the 02.02.2019. See [here](https://wiki.openstreetmap.org/wiki/Map_Features#Amenity)", "OpenStreetMap, accessed via the [Humanitarian Data Exchange platform](https://data.humdata.org) on the 02.02.2019. See [here](https://wiki.openstreetmap.org/wiki/Map_Features#Amenity)", "eMODIS AQUA Normalized Difference Vegetation Index, The U.S. Geological Survey \(USGS\) Earth Resources Observation and Science \(EROS\) Center, accessed [here](https://earlywarning.usgs.gov/fews/datadownloads/East%20Africa/eMODIS%20NDVI%20C6)", "Inter-American Development Bank **?**", "Inter-American Development Bank **?**", "Inter-American Development Bank **?**", "WorldClim Version2 \[@fick2017worldclim\], accessed [here](http://worldclim.org/version2)", "WorldPop Archive global gridded spatial datasets, accessed [here](https://doi.org/10.7910/DVN/VKAYE8) \[@VKAYE8\_2016\]", "WorldPop and CIESIN, accessed [here](https://www.worldpop.org/project/categories?id=14) \[@WP2018cov\]", "WorldPop and CIESIN, accessed [here](https://www.worldpop.org/project/categories?id=14) \[@WP2018cov\]", "WorldPop and CIESIN, accessed [here](https://www.worldpop.org/project/categories?id=14) \[@WP2018cov\]", "WorldPop and CIESIN, accessed [here](https://www.worldpop.org/project/categories?id=15) \[@WP2018cov\]", "European Space Agency, accessed [here](http://maps.elie.ucl.ac.be/CCI/viewer/download.php) \[@defourny2017cci\]" \) \) knitr::kable\( table\_df, caption = 'List of GIS covariates', booktabs = TRUE \)

```text
## Loading the survey
We start the pre-processing stage by loading the data sets and shape files below:

* **ehpm**: As stated earlier, this file contains survey data for the year 2017
* **segmento_hh_id**: A file that matches survey household identifier with the corresponding segmento to allow for household-level mapping
* **segmento shapefile**: A spatial map of all *segmentos* 


Before loading the files above, let us first generate an entry recording the path to the folder where the data are stored. This simplifies subsequent data-call operations by providing the software with an easily readable and replicable directory path.  

```{r echo=FALSE, results="hide", include=FALSE}
# load packages
library(rgdal)
library(dplyr)
library(leaflet)
library(readxl) # VS: necessary for opening xlx below
```

\`\`\`{r results="hide", message=FALSE, cache=TRUE}

## change this with the path to your directory

## dir\_data="C:/Users/Xavier Vollenweider/Documents/Flowminder/IDB/ES\_poverty\_mapping/phase\_II/data/"

dir\_data = "/Users/vishva/Dropbox/IDB/data/"

## dir\_data = "/Users/Vishva/Dropbox/IDB/data/"

ehpm=read.csv\(paste0\(dir\_data, "tables/ehpm-2017.csv"\)\) segmento\_hh\_id=readxl::read\_xlsx\(paste0\(dir\_data, "tables/Identificador de segmento.xlsx"\)\)

