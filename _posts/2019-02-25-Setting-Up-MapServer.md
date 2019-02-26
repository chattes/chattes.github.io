---
layout: post
title: Setting Up an Offline Map Server 
---

#### OpenMapTiles

Download maps from [openmaptiles](https://openmaptiles.com/).
We setup the mapserver using Docker as given in the Docs.

#### POI Data

We need to setup a POI server where we can query for the neighbourhood
attractions and get a response.

We will load all the POI data as GeoJSON data into MongoDb. MongoDB has built
geojson queries which we will be using.

###### Download Data
We will download all the openstreet map data from 
[Geofabrik](http://download.geofabrik.de/)

####### Extracting Data
We need to extract the POI data from the File downloaded.
We need to use a tool called 
[Osmosis](https://wiki.openstreetmap.org/wiki/Osmosis) for this.

The command to extract the data looks like so

```
osmosis --read-pbf europe-latest.osm.pbf \
        --tf accept-nodes \
        aerialway=station \
        aeroway=aerodrome,helipad,heliport \
        amenity=* building=school,university craft=* emergency=* \
        highway=bus_stop,rest_area,services \
        historic=* leisure=* office=* \
        public_transport=stop_position,stop_area railway=station \
        shop=* tourism=* \
        --tf reject-ways --tf reject-relations \
        --write-xml Europe.nodes.osm
```
We can remove some nodes if we dont want them.
The downloaded files contain all data from India but there are some data we dont
need. We will scrup the data using 
[osmconvert](https://wiki.openstreetmap.org/wiki/Osmconvert#Linux)

In MAC we can run 

```wget -O - http://m.m.i24.cc/osmconvert.c | cc -x c - -lz -O3 -o osmconvert```
Then move the file to ```mv osmconvert /usr/local/bin```

Now we can run the following to scrub data

```osmconvert India.nodes.osm — drop-ways — drop-author — drop-relations —
drop-versions India.poi.osm```


###### Converting to GeoJSON data

For converting the .osm file to GEOJSON we use a tool [ogr2ogr](https://www.gdal.org/ogr2ogr.html)
This can be installed using
``` brew install gdal ```

We are now ready with the GEOJSON data. We will upload this to mongoDB using the
npm library

[geojson-to-mongo](https://www.npmjs.com/package/geojson-to-mongo)

The npm script reads the GeoJSON file we created and uploads it to mongodb.

Once the Data Is Loaded into MongoDB we can query using GeoJSON queries.

Example:

```
  var query = {
    $and: [
      {
        geometry: {
          $near: {
            $geometry: {
              type: "Point",
              coordinates: [options.lat, options.lng]
            },
            $maxDistance: 2000,
            $minDistance: 0
          }
        }
      },
      {
        "properties.other_tags": { $regex: "historic" }
      }
    ]
  };
```

##### Extracting details of POI

The POI data extracted has a wikidata id and wikipedia link within them.
We will use that to enrich the POI with Wikipedia Extracts and Images

We can query the [Wikipedia Rest API](https://en.wikipedia.org/api/rest_v1/#!)


