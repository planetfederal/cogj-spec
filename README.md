# COJ: Cloud Optimized (Geo)JSON

## What?
COJ is a dirivative of GeoJSON that is optimized for network storage and efficient data access over HTTP using range reads. It was inspired by the [Cloud Optimized GeoTiff](https://www.cogeo.org/) spec and is envisioned to be a vector storage counterpart.

The goals are to be: 
- Simple -->  HTTP(S)
- Flexible --> just enough spec with room to move
- Democratic --> open, human readable, and easy to implement

## Why?

As cloud technologies become the norm, browers rule the desktop and disks are rarely local -- we need to think differently about how we store data. Having opaque files on remote services requires you to download the entire file before being able to make use of it. Just as COG changed this for raster, COJ aims to do the same for vector.

The problem exists in all common vector formats:
- Sqlite based (MBTiles, GeoPackage)
- Shapefiles (multiple files, often zipped)
- GeoJSON and GML require custom parsing to avoid XML or JSON encoding issues with partial reads
- Vector services (databases or WFS) are great for subsetting data but require running a service or lambda 

## How does it work?

The concept is simple: a traditional GeoJSON file is broken into *n* number of feature collections, each of which is independently a valid geojson document. The collections can be made using any sorting or ordering algorithm which makes sense for the given data (temporal, spatial, etc.).

The collections are arranged back-to-back in a single file with the first 10k of the file reserved for metadata.  The [metadata header] (/spec/header.json) contains metadata about the file as a whole as well as an array of collection metadata.  

## Demo 

2 files on S3 which both contain the same data [Cadastral.json](https://s3.amazonaws.com/tomsorflow/coj/Cadastral.json) and [Cadastral.json.coj](https://s3.amazonaws.com/tomsorflow/coj/Cadastral.json.coj) both about 160mb containing the cadastral data in Harford Country, Maryland.

I have a simple OpenLayers app which lets me load either file with the click of a button. Running this in Chrome with network throttled to Fast 3G setting to emphasize the point -- and because thats the reality for many.  

[![Demo Video]](https://www.youtube.com/watch?v=YMM2sGZHgoA "Demo Video")





### What about the overhead?

 To be fair I have to admit the COJ version is slightly smaller because of the lack of pretty printing the JSON, but only ~5% smaller. 
