# COGJ: Cloud Optimized GeoJSON

## What?
COGJ is a dirivative of GeoJSON that is optimized for network storage and efficient data access over HTTP using range reads. It was inspired by the [Cloud Optimized GeoTiff](https://www.cogeo.org/) spec and is envisioned to be a vector storage counterpart.

The goals are to be: 
- Simple -->  HTTP(S)
- Flexible --> just enough spec with room to move
- Democratic --> open, human readable, and easy to implement

## Why?

 Having opaque files on remote services requires you to download the entire file before being able to make use of it. Just as COG changed this for raster, COGJ aims to do the same for vector.

The problem exists in all common vector formats:
- Sqlite based (MBTiles, GeoPackage)
- Shapefiles (multiple files, often zipped)
- GeoJSON and GML require custom parsing to avoid XML or JSON encoding issues with partial reads
- Vector services (databases or WFS) are great for subsetting data but require running a service or lambda 

## Benefits 
- **Reduced infrastructure requirements:** efficient data acces without so much as a lambda (serverless)
- **Reduced bandwidth consumption:** load just the parts you need from even the most massive files 
- **Reduce client load:** allows browsers and disadvanged clients to work with datasets that would previously overwelm them
- **Simplification:** single, self describing file
- **Universal ease:** read efficiently from an HTTP(S) connection or filesystem 

## Drawbacks (to be fair)
- **Read Optimized:** it can be authored in a text editor, but isn't easy. Realistically should be written by software.
- **YAGF:** yet another geo format

## Sweet spots
- Large, read only, public datasets (i.e. Goverments, NGOs) 
- Disadvangtaged users, slow connections or mobile devices (e.g. Disaster relief)
- Aggregating datasets into a portable web friendly package
- Batch processing/viewing very large amounts of data (i.e. ML input or output)

# How does it work?

The concept is simple: a traditional GeoJSON file is broken into *n* number of feature collections, each of which is independently a valid geojson document. The collections can be made using any sorting or ordering algorithm which makes sense for the given data (temporal, spatial, etc.).

![COGJ vs GeoJSON](/img/geojson_optimize.png)

The collections are arranged back-to-back in a single file with the first 10k of the file reserved for metadata.  The [metadata header](./spec/readme.md) contains metadata about the file as a whole as well as an array of collection metadata.  

## Demo 

This demo shows 2 files on S3 which both contain the same data [Cadastral.json](https://s3.amazonaws.com/cogeojson/Cadastral.json) and [Cadastral.json.coj](https://s3.amazonaws.com/cogeojson/Cadastral.json.coj) in different formats. Both are about 160mb and contain the same cadastral data in Harford Country, Maryland.

The demo shows a simple OpenLayers app which lets you load either file with the click of a button. Running this in Chrome with network throttled to Fast 3G setting to emphasize the point -- and because thats the reality for many.  

[![Demo Video](/img/coj.png)](https://www.youtube.com/watch?v=YMM2sGZHgoA)

### How hard is this to implement? 

Reading the header: 
```javascript
fetch('https://s3.amazonaws.com/cogeojson/Cadastral.json.coj',{headers: {"Range":"bytes=0-10239"}})
        .then(response=>{return response.json();})
```
Reading collections of features: 
```javascript 
  fetch('https://s3.amazonaws.com/cogeojson/Cadastral.json.coj',{headers: {"Range":"bytes="+start+"-"+end}})
                .then(response => {return response.json()}).then(function(json){ 
                //pass geojson object to your mapping library
                ...
                }
```

### What about the overhead?

While there is certainly some overhead related to the metadata and additional feature collection boilerplate, the impact is negligable. While the COGJ version of this test data does have a few extra curly braces, the actual file size is smaller because extra whitespace was removed. What this means is that the overhead of whitespace is larger than efficiently subdeviding the file. For our test data, the COGJ is about 10mb smaller.

