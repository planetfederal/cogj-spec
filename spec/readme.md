# COGJ Spec 

COGJ differs from standard GeoJSON in that there is a header included before an unbounded list of gs:FeatureCollections. Therefore this spec only defines the header and file structure -- not the FeatureCollection or sub structures of it. For information on those please reference the official [IETF doc](https://tools.ietf.org/html/rfc7946). 


## COGJ Header
The following describes the structure and meaning of fields in the COGJ header.  The header must start at byte 0 of the COGJ file and cannot exceed beyond byte 9999 (0 indexed).  The header must be valid JSON and must adhere to the JSON schene defined [here](./header_schema.json). 

### Mandatory Fields 

The following fields are mandatory in the header: 

| Name | Optional | Type | Meaning |
|:------:|:-----------:|:------:|:--------------:|
| `size`| yes | integer | the size in bytes of the entire file |
|`features`| yes | integer | the number of total features contained in the entire file |
|`bbox`| yes| an array of 4 numbers | the bounding box of the of the entire file in WGS8 ordered as left, bottom, right, top |
|`collections`|yes| an array of `collection` objects | each object describes a FeatureCollection contained in this file |
|`name`| no | string |a short name for this dataset |
|`description`| no | string |a textual desciption of this dataset |
|`version`| no | string | the version of this dataset, ideally compliant with [sematic versioning](https://semver.org/) |
|`published`| no | string | the publication timestamp in [ISO8601 format](https://en.wikipedia.org/wiki/ISO_8601) | 


The `collection` object contains the following fields:

| Name | Optional | Type | Meaning |
|:------:|:-----------:|:------:|:--------------:|
| `start`| no| integer | the first byte of the collection in the file |
|`size` | no | integer | the size of the collection in bytes |
|`bbox`| yes| an array of 4 numbers | the bounding box of the of the entire file in WGS8 ordered as left, bottom, right, top |
|`features`| no | integer | the number of features in this collection |

#### Example







## COGJ Extended header


