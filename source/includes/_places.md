# Places

A **Place** is an area of interest, usually within a bounded geographic area, associated with the running of a farming operation.  Places are collected together through some form of management or ownership.  A "Farm" (that is the concept of a single geographic location) is a place.  Paddocks are also places.  

Places can be related through hierarchical associations (e.g. a farm _place_ has many paddock _places_).  These association can be considered a network or graph of connected relationships.  

The Place APIs are used to navigate the network of places.

## Place Resource

The Place resource has the following attributes:

Attribute       |  Description
---------       |  -----------
placeURI        | The formal identifier for the place.
\_type          | Always a `place`.
\_kind          | The classification of this specific place; supported kinds: `farm`, `paddock`.

## Navigating Places

The Place resource follows the [hypermedia format](#hypermedia-format).  Links are provided to enable navigation of the place network.  The `up` link (`"rel": "up"`) provides the parent place.  The `places` link (`"rel" : "places"`) provides child places

Given a user's token, the root resource (the `/place` resource) will scope access to all the places the user has access to.

Places may include links to other associated resources; such as pasture measurements, when these are applicable to the place.

## Places API

### Root Resource

`GET /place`

<aside class="success">
Remember to provide your authorisation token in the `AUTHORIZATION` header.
</aside>

```json
{
  "links": [
    {
      "rel": "self",
      "href": "https://api.mindainfo.io/place/"
    },
    {
    "rel": "up",
    "href": "http://api.mindainfo.io/"
    }
}
  ],
  "items": [
    {
      "id": "https://api.mindainfo.io/places/1",
      "title": "TestFarm1"
    }
  ],
  "_type": "Array"
}
```

The root resource provides access to all other resources for places.  The collection of `items` contains specific places to which the client has been provided access.  

## The Farm Place Resources

`GET /place/1`

```json
{
    "links": [
        {
            "rel": "self",
            "href": "https://api.mindainfo.io/place/1"
        },
        {
            "rel": "up",
            "href": "https://api.mindainfo.io/place/"
        },
        {
            "rel": "pasture-measurement",
            "href": "https://api.mindainfo.io/pasture-measurement?placeUrl=place/1"
        },
        {
            "rel": "places",
            "href": "https://api.mindainfo.io/place/1/place/"
        },
        {
            "rel": "locations",
            "href": "https://api.mindainfo.io/place/1/location/"
        },
    ],
    "name": "PlaceName",
    "identifiers": [
        {
            "kind": "urn:lic:ids:farm",
            "value": -1
        }
    ],
    "history": "Current",
    "_type": "Place",
    "_kind": "Farm"
}
```

Places can have relationships to other concepts:
+ The `pasture-measurement` query resource provides a link to _Pasture Measurement_ information about the place.  See the [pasture measurement resource definition](#pasture-measurement) for information on this resource.

## Location Resources

`GET /place/1/location`

```json
{
    "links": [
        {
            "rel": "self",
            "href": "https://api.mindainfo.io/place/1/location/"
        },
        {
            "rel": "up",
            "href": "https://api.mindainfo.io/place/1"
        },
    ],
    "items":[
        {
            "id": "https://api.mindainfo.io/place/1/location/2",
            "title": "1"
        }
    ],
    "_type": "Array",
    "_kind": "Location"
}
```

`GET /place/1/location/2`

```json
{
    "links": [
        {
            "rel": "self",
            "href": "https://api.mindainfo.io/place/1/location/2"
        },
        {
            "rel": "up",
            "href": "https://api.mindainfo.io/place/1/location/"
        },
    ],
    "x": "174.776",
    "y": "-41.286",
    "history": "Current"
}
```
