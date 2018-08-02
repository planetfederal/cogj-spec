# COJ: Cloud Optimized (Geo)JSON

## What?
COJ is a dirivative of GeoJSON that is optimized for network storage and efficient data access over HTTP using range reads. It was inspired by the Cloud Optimized GeoTiff spec as is envisioned to be the vector storage counterpart.

## Why?

As cloud technologies become the norm and not the exception we need to think differently about how we store data. Having opaque files on remote services requires you to download the entire file before being able to determine wheter or not you need it. 

The common vector formats all suffer from this isssue: 
- Sqlite based (MBTiles, GeoPackage)
- Shapefiles (often zipped), GeoJSON

## How?
