# COGJ Spec 

COGJ defines a header included before an unbounded list of  GeoJSON FeatureCollections. Therefore this spec only defines the header and file structure -- not the FeatureCollection or sub structures of it. For information on those please reference the official [IETF doc](https://tools.ietf.org/html/rfc7946). 


## COGJ Header
The following describes the structure and meaning of properties in the COGJ header.  The header must start at byte 0 of the COGJ file and cannot exceed beyond byte 9999 (0 indexed).  The header must be valid JSON and must adhere to the JSON schema defined [here](./header_schema.json). 

### Mandatory Fields 

The following properties are mandatory in the header: 

| Name | Optional | Type | Meaning |
|:------:|:-----------:|:------:|:--------------:|
| `size`| no | integer | the size in bytes of the entire file |
|`features`| no | integer | the number of total features contained in the entire file |
|`bbox`| no| an array of 4 numbers | the bounding box of the of the entire file in WGS84 ordered as left, bottom, right, top |
|`collections`|no| an array of `collection` objects | each object describes a FeatureCollection contained in this file |
|`name`| yes | string |a short name for this dataset |
|`description`| yes | string |a textual desciption of this dataset |
|`version`| yes | string | the version of this dataset, ideally compliant with [sematic versioning](https://semver.org/) |
|`published`| yes | string | the publication timestamp in [ISO8601 format](https://en.wikipedia.org/wiki/ISO_8601) | 
|`extended_metadata`| yes | object | an object that points to an range of bytes which allows this header to extend beyond 10k byte size | 


The `collection` object contains the following properties:

| Name | Optional | Type | Meaning |
|:------:|:-----------:|:------:|:--------------:|
| `start`| no| integer | the first byte of the collection in the file |
|`size` | no | integer | the size of the collection in bytes |
|`bbox`| no| an array of 4 numbers | the bounding box of the of the entire file in WGS84 ordered as left, bottom, right, top |
|`features`| no | integer | the number of features in this collection |
|`name`| yes | string |a short name for this dataset |
|`description`| yes | string |a textual desciption of this dataset |

The `extended_metadata` object contains the following properties: 

| Name | Optional | Type | Meaning |
|:------:|:-----------:|:------:|:--------------:|
| `start`| no| integer | the first byte of the `extended_metadata` in the file |
|`size` | no | integer | the size of the `extended_metadata` in bytes |


## Additional Properties

Both the top level `header` and the `collection` objects allow `additionalProperties` in the JSON schema definition.  This means that any arbitrary properties may be inserted to enrich the file or collection metadata.

## COGJ Extended header

When dealing with very large datasets which have been devided into large numbers of collections it may be possible for the header to overflow the 10k limit. In such cases the  `extended_metadata` object allows the publisher to specify an additional byte range containing the full metadata header. 

The content of the byte range specified by the `extended_metadata` object should be valid JSON and adhere to the same header schema. 


## Header Padding 

As  the header will almost never be exactly 10,000 bytes the header should be padded to that limit using spaces ( UTF8 `0x0020`

