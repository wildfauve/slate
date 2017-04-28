# Root

A directory of resources can be obtained from the root resource, available at the API host root

`GET /`

```json
{

    "links": [
        {
            "rel": "self",
            "href": "https://api1.accp.mindainfo.io/"
        },
        {
            "rel": "places",
            "href": "https://api1.accp.mindainfo.io/place/"
        },
        {
            "rel": "animal-groups",
            "href": "https://api1.accp.mindainfo.io/animal-timeline/animal-group"
        },
        {
            "rel": "publications",
            "href": "https://api1.accp.mindainfo.io/publication/"
        }
    ],
    "version": "1.5.0"

}
```
