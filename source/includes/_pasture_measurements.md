# Pasture Measurements

Pasture Measurements are metrics collected for and associated to specific [places](#places).  The following metrics are provided:

+ **Weighted Mean Cover**: A single value representing the mean pasture cover for the farm for a specific farm measurement (a walk).  The weighted mean is determined from the _qualifying paddocks_ (e.g. in rotation, with area value greater than zero) for the farm measurement and the weighting of the calculation is by the areas of the paddock ares and qualifying paddocks.   The unit of measurement is _kilograms of dry matter per hectare (kg DM/ha)_.
+ **Weighted Mean Growth**:  The difference in cover between 2 measurements; where the difference is an increase.  Growth is a single positive value in the unit of kg DM/ha.
+ **Mean Cover**:  The calculated kilograms of dry matter (kg DM/ha) cover per hectare for a specific measurement (walk) for a specific paddock.  The measurement is the mean of all individual measurements obtained for the paddock.  The unit of measurement is _kilograms of dry matter per hectare (kg DM/ha)_.
+ **Mean Growth**: The difference in _mean cover_ across 2 measurement dates.  The unit of measurement is _kilograms of dry matter per hectare (kg DM/ha)_.

A _measurement_ or a _walk_ is a means of determining the statistical significance of measurements for an effective date.


## Resource

### Growth

### Cover

Attribute       | Description
----------      | ----------
cover           | Object; a cover measurement
cover.value     | The numerical value of a cover measurement
cover.unitCode  | The units of the value; `kgDM/HAR` represents kilograms of dry matter per hectare
date            | The date of the measurement; in ISO8601 format.


## Farm-level Pasture Measurement API

```json
{

    "links": [
        {
            "rel": "self",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/pasture-measurement/place?placeUrl=TestFarm1Url"
        },
        {
            "rel": "up",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/place/"
        },
        {
            "rel": "weighted-mean-cover-search",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/pasture-measurement/1/weightedMeanCoverSearch"
        },
        {
            "rel": "weighted-mean-growth-search",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/pasture-measurement/1/weightedMeanGrowthSearch"
        }
    ],
    "_type": "PastureMeasurement",
    "_kind": "Farm"

}
```

## Paddock-level Pasture Measurement API

```json
{

    "links": [
        {
            "rel": "self",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/pasture-measurement/place?placeUrl=TestPaddock1Url"
        },
        {
            "rel": "up",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/place/1/place/"
        },
        {
            "rel": "mean-cover-search",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/pasture-measurement/2/meanCoverSearch"
        },
        {
            "rel": "mean-growth-search",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/pasture-measurement/2/meanGrowthSearch"
        },
        {
            "rel": "mean-covers",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/pasture-measurement/2/meanCover/"
        }
    ],
    "_type": "PastureMeasurement",
    "_kind": "Paddock"

}
```


```json
{

    "links": [
        {
            "rel": "self",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/pasture-measurement/2/meanCover/"
        },
        {
            "rel": "up",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/pasture-measurement/2"
        }
    ],
    "items": [
        {
            "id": "https://api1.accp.mindainfo.io/pasture-measurement-service/meanCover/1",
            "title": 1777
        },
        {
            "id": "https://api1.accp.mindainfo.io/pasture-measurement-service/meanCover/2",
            "title": 1888
        },
        {
            "id": "https://api1.accp.mindainfo.io/pasture-measurement-service/meanCover/3",
            "title": 1889
        },
        {
            "id": "https://api1.accp.mindainfo.io/pasture-measurement-service/meanCover/4",
            "title": 1890
        }
    ],
    "_type": "Array"

}
```

```json
{

    "links": [
        {
            "rel": "self",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/meanCover/1"
        },
        {
            "rel": "up",
            "href": "https://api1.accp.mindainfo.io/pasture-measurement-service/pasture-measurement/2/meanCover/"
        }
    ],
    "cover": {
        "value": 1777,
        "unitCode": "kgDM/HAR"
    },
    "date": "2000-07-05"

}
```
