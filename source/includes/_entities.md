# Entity

## Entity search 

```shell
# search for API with the 'Github' keyword
curl -XGET 'http://johnsd.cse.unsw.edu.au:3000/entities/search?q=Github&type=API'
```

> if you want to specify a particular field to search, use `field:` syntax

```shell
# search for APIs that have 'music' in their description
curl -XGET 'http://johnsd.cse.unsw.edu.au:3000/entities/search?q=description:music&type=API'
```

> example result for APIs that have 'music' in their description

```json
[
    {
        "_type": "API",
        "description": "http://www.programmableweb.com/api/sonos-music",
        "id": "6b7f74f9-38f8-4b1e-a8b8-8d7bcb26cc8b",
        "name": "Sonos Music",
        "provider": "Sonos Music",
        "type": "REST"
    },
    {
        "_type": "API",
        "baseUrl": "https://api.spotify.com",
        "description": "<p>Spotify&nbsp;Web API lets your applications fetch data from the Spotify music catalog and manage user&rsquo;s playlists and saved music.</p>\n<p>&nbsp;</p>",
        "id": "170333ff-afa7-4dc8-92de-9b8cebe72a34",
        "name": "Spotify",
        "provider": "Spotify AB",
        "type": "REST"
    },
    {
        "_type": "API",
        "description": "4shared is an online storage and file sharing service that was founded in 2005. Users can upload, store and share all types of files, including music, video, photos and documents.\nThe 4shared API...",
        "id": "71ca0c6a-cd3a-4f31-b6a8-97ad95f431c4",
        "name": "4Shared",
        "provider": "4Shared",
        "type": "REST"
    }
]

```

This endpoint performs a keyword search in APIBase. It returns only the first 30 results. To get
more results, use the `page` query parameter.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/entities/search`

### Query Parameters
Parameter | Type | Example | Description
--- | --- | --- | ---
\*q | string | "Github" | Query string
type | `API`, `Method`, `Recipe`, `DataSourceSchema`, `DataSource` | "API" | Type of entity you want to search; leave empty to search for all types
page | integer | 1 | Page number, starting from 1

### Response

An array of entities that match the search criteria. Maximum 30 entities.

### Errors

N/A


## Retrieving arbitrary entity

```shell
curl -XGET 'http://johnsd.cse.unsw.edu.au:3000/entities/b1c73190-553e-4453-a672-394e83feb417'
```

> successful request returns the entity:

```json
{
    "id": "b1c73190-553e-4453-a672-394e83feb417",
    "expression": "map(select(.sepalLength > 5))",
    "description": "get all data where sepalLength is greater than 5",
    "name": "GetsLenGt5",
    "sources": [
        "576927bb-7cb3-4c75-81b0-7c1e67a724a1"
    ],
    "_relations": [
        {
            "type": "USE_SOURCE",
            "direction": "out",
            "target": {
                "id": "576927bb-7cb3-4c75-81b0-7c1e67a724a1",
                "_created": 1516863793,
                "source": "{}",
                "description": "original UCL iris data",
                "name": "UCL iris data",
                "_id": 103679,
                "_type": "DataSource"
            },
            "_mapping": {}
        }
    ]
}
```

This endpoint lets you get an entity regardless of its type. For example can use
this endpoint to retrieve an API, Method or DataSourceSchema...  Related
entities will also be retrieved. To change this behavior, use the `brief=true` query parameter.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/entities/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
\*id | uuid | ID of the entity in question
brief | bool | `true` to retrieve only the entity in question, otherwise all related entities will be retrieved

### Response

In addition to the properties of the entity, this endpoint also returns an
`_relation` array if `brief=false`(default). `The _relation` array list
entities related to the entity, each containing the following
property:

Property | Description
--------- | ----------- |
type | The relationship of the two entities
direction | `in` or `out` indicating the direction of their relationship
target | The related entity


### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | Entity of specified `id` does not exist | resource not found

## Display Entity

```shell
curl -XGET 'http://johnsd.cse.unsw.edu.au:3000/entities/b1c73190-553e-4453-a672-394e83feb417/display'
```
> successful request returns the entity:

```json
[
    {
        "sepalLength": 5.1,
        "sepalWidth": 3.5,
        "petalLength": 1.4,
        "petalWidth": 0.2,
        "class": "Iris-setosa"
    },
    {
        "sepalLength": 5.4,
        "sepalWidth": 3.9,
        "petalLength": 1.7,
        "petalWidth": 0.4,
        "class": "Iris-setosa"
    },
    {
        "sepalLength": 5.4,
        "sepalWidth": 3.7,
        "petalLength": 1.5,
        "petalWidth": 0.2,
        "class": "Iris-setosa"
    },
	...
]
```

This endpoint shows arbitrary entity and interprets/executes it if applicable.
Similar to the [Retrieving arbitrary entity](#retrieving-arbitrary-entity) endpoint, this
endpoint can also retrieve an arbitrary entity but if the entity represents an
executable object, for example a [Query](#query), then the entity will be executed/run and
return the result instead.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/entities/{id}/display`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
\*id | uuid | ID of the entity in question

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | Entity of specified `id` does not exist | resource not found
