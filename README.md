# COJ: Cloud Optimized (Geo)JSON

## What?
COJ is a dirivative of GeoJSON that is optimized for network storage and efficient data access over HTTP using range reads. It was inspired by the Cloud Optimized GeoTiff spec and is envisioned to be a vector storage counterpart.

The goals are to be: 
- Simple -->  HTTP
- Flexible --> just enough spec
- Democratic --> human readable and easy to implement

## Why?

As cloud technologies become the norm and not the exception we need to think differently about how we store data. Having opaque files on remote services requires you to download the entire file before being able to determine wheter or not you need it. Just as COG changed this for raster, COJ aims to do the same for vector.

The problem is pervasive:
- Sqlite based (MBTiles, GeoPackage)
- Shapefiles (often zipped)
- GeoJSON and GML require custom parsing to avoid XML or JSON encoding issues with partial reads
- Vector services (PostGIS/WFS) are great for subsetting data but require running a service

## How does it work?

The concept is simple (and should sound familiar): 

- The first 10k of the file is reserved for a JSON header, which contains:
-- metadata about the entire file
-- the byte range of an extended header (optional)
-- byte ranges of groups of vector features, each encoded as a valid GeoJSON document
- The rest of the file is composed of the content described above
