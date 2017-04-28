# Hypermedia Format

The Hypermedia REST microformat is represented in JSON. It is based, by intent, on Atom feeds.
This microformat is used to represent the nodes and arcs in a graph - the network of data. There are several types within the microformat. Note that we follow standard HTTP terminology: A resource is some source of general information (it could correspond to an image or data within a database) which has a unique URL. A representation is in a format, such as JSON or XML, for providing data about the given resource. In general, a browser/system can request a specific type of representation for a given resource, and follows links to access data in the graph.

## Link Representation

A Linked Representation is used to represent a resource that corresponds to a single thing, such as a domain object. For example, here is a specific Customer that is accessed by a GET of https://api1.example.com/customer/123:

```
{

    "links": [
        {
            "rel": "self",
            "href": "https://api1.mindainfo.io/pasture-measurement/newPlace/20"
        },
        {
            "rel": "up",
            "href": "https://api1.mindainfo.io/pasture-measurement/newPlace/"
        },
        {
            "rel": "weighted-mean-cover-search",
            "href":
       "https://api1.mindainfo.io/pasture-measurement/newPlace/20/weightedMeanCoverSearch"
        },
        {
            "rel": "weighted-mean-growth-search",
            "href":
      "https://api1.mindainfo.io/pasture-measurement/newPlace/20/weightedMeanGrowthSearch"
        },
        {
            "rel": "places",
            "href": "https://api1.mindainfo.io/pasture-measurement/newPlace/20/newPlace/"
        }
    ],
    "@type":"Customer",
    "placeUri": "http://localhost:3003/place/7",
    "upId": 0
}
```

The first field, “links”, is a special one (and is similar to the notion of a link in HTML). It holds an array of LinkedItemRepresentations, where each has a “rel”, which is a human-readable link, and an “href”, which is a URL. Here there are four links:

+ `self` provides the URL of the given resource.
+ `up` refers to the “owner”, where that makes sense. In this case, the owner is the collection of `NewPlaces`. Another example: a `LactationEvent` may be owned by an Animal. Note that `up` is not concerned with the history of traversal of the graph, but with fixed relationships.
+ `weighted-mean-cover-search` and `weighted-mean-growth-search` each provide a way of getting metrics for a farm based on a date. This is based on a `search-form`, as discussed below.
+ `places` is a link to a collection of `sub-NewPlaces` (paddocks) that are owned by this Customer. This collection (actually called a FeedRepresentation), is a node on the network with a unique URL. In this case the text of the URL happens to include the place details, although that is not necessary.
+ `edit` and `delete` may be provided as a means of editing or deleting the resource. These is not included here as the current user has no the right to change/delete that resource.

These links name the arcs between the nodes in the graph, and are used to traverse the graph to access data (and make updates). The `self`, `up` and `edit` links are IANA standard links. There may be links to collections and to directly to other resources.

The other fields in the example above include the `@type` of the data (optional), and the simple properties of the `NewPlace`. These have simple valid values, such as strings, dates, booleans, numbers, etc, or arrays of simple values. Notice that more complex values, such as domain objects like a PastureMeasurement or associated Herds, are handled with links.


## Root resource

There is a single entry point to the whole network of data, sometimes called the root node. This is also a Linked Representation. For example:

```
{
   {

    "links": [
        {
            "rel": "self",
            "href": "https://api1.mindainfo.io/pasture-measurement/"
        },
        {
            "rel": "places",
            "href": "https://api1.mindainfo.io/pasture-measurement/newPlace/"
        }
    ],
    "@type":"Api",
    "version": "1"
}
```


This provides access to the root link relations for all the domain concepts (resources) in the domain.  Note that the root node doesn’t have an “up” link.

## Feed Representation

A FeedRepresentation is used to represent a collection of things. For example, here is a list of NewPlaces:

```
{
    "links": [
        {
            "rel": "self",
            "href": "http://localhost:3002/pasture-measurement/newPlace/"
        },
        {
            "rel": "up",
            "href": "http://localhost:3002/pasture-measurement/"
        }
    ],
    "items": [
        {
            "id": "http://localhost:3002/pasture-measurement/newPlace/1",
            "title": "http://localhost:3003/place/5"
        },
        {
            "id": "http://localhost:3002/pasture-measurement/newPlace/20",
            "title": "http://localhost:3003/place/7"
        }
    ]
   "@type":"Array",
   "@itemType":"NewPlace"
}
```

Like a LinkedRepresentation, a FeedRepresentation has a set of links. It has the usual `self` and `up` links. In general, there can be other links in a feed, but not in this case due to the restricted access and update:

+ A `search` link, which can be used to search for a subset of the elements in the collection.
+ A `create` link, which can be used to create a new element in the collection.

The “@type” of the feed is either an “Array” or a “Set”. The type of the elements of the collection is defined by the “@itemType”. These two fields are optional.

The “items” field in the example above holds an array of links to each of the elements of the collection, consisting of an `id` that holds a URL and a `title` that holds a string that could be useful to a user when selecting an element to view (although that doesn’t apply to this example). Unlike some microformats, the details of the elements themselves are not (usually) included, but may be embedded for performance reasons. The `items` array may be empty.
