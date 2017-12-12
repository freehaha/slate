# Entity

## Entity search 

```shell
# search for API with the 'Github' keyword
curl -XGET 'http://johnsd.cse.unsw.edu.au:3000/entities/search?q=Github&type=API'
```

> if you want to specify a particular field to search, use `field:` syntax

```shell
# search for APIs that have 'music' in its description
curl -XGET 'http://johnsd.cse.unsw.edu.au:3000/entities/search?q=description:music&type=API'
```

> example result

```json
[
    {
        "_type": "Method",
        "description": "Get info about songs given a list of ids",
        "id": "7e21ca78-34e2-4329-bb18-e35de4b107ea",
        "name": "profile",
        "path": "http://developer.echonest.com/api/v4/song/profile?api_key={apiKey}&format={outputFormat}&id={songId}",
        "verb": "GET"
    },
    {
        "_type": "Method",
        "description": "get user's profile",
        "id": "07acb558-7626-4bb4-946a-2ddd77c4dbb6",
        "name": "GetProfile",
        "path": "/users/{id}",
        "verb": "GET"
    },
    {
        "_type": "Method",
        "description": "<p>Get a User&rsquo;s Profile.</p>",
        "id": "2fd74bdf-7aa5-46b5-a9d3-63a270691537",
        "name": "GetUserProfile",
        "path": "/v1/users/{user_id}",
        "verb": "GET"
    },
    {
        "_type": "Method",
        "id": "e686e6c9-9d17-4834-9344-a2d641c99250",
        "name": "GetOwnProfile",
        "path": "https://www.pivotaltracker.com/services/v5/me",
        "verb": "GET"
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
curl -XGET 'http://johnsd.cse.unsw.edu.au:3000/entities/ee1db224-3331-4b8a-bc11-8839b4e5d6b4'
```

> successful request returns the entity:

```json
{
    "_type": "API",
    "type": "REST",
    "baseUrl": "https://api.github.com",
    "description": "github",
    "id": "ee1db224-3331-4b8a-bc11-8839b4e5d6b4",
    "methods": [
        "07acb558-7626-4bb4-946a-2ddd77c4dbb6",
        "9446bdf1-da5a-4096-98bc-47cfa8c15157",
        "df5f4ae2-e80c-48c1-a641-7d6b2d02d5cb"
    ],
    "name": "Github",
    "provider": "http://github.com",
    "resources": [
        "6d5dd456-3b04-4590-b51a-c5094c6a5cd7"
    ],
    "_relations": [
        {
            "direction": "in",
            "target": {
                "_created": 1404866573,
                "_id": 33,
                "_type": "CategoryFolder",
                "description": "root node",
                "id": "55cdee12-c49a-46f6-909c-98f983c79a92",
                "name": "APIs"
            },
            "type": "CONTAINS"
        },
        {
            "direction": "out",
            "target": {
                "_created": 1432536694,
                "_id": 38932,
                "_type": "Operation",
                "id": "df5f4ae2-e80c-48c1-a641-7d6b2d02d5cb",
                "method": "PUT",
                "name": "MergePR",
                "path": "/repos/{owner}/{repo}/pulls/{number}/merge"
            },
            "type": "HAS_METHOD"
        },
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

In addition to the attributes of the entity, this endpoint also returns an
`_relation` array if `brief=false`(default). `The _relation` array list
entities related to the entity, each containing the following
attribute:

Attribute | Description
--------- | ----------- |
type | The relationship of the two entities
direction | `in` or `out` indicating the direction of their relationship
target | The related entity


### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | Entity of specified `id` does not exist | resource not found

## Display Entity

```shell
curl -XGET 'http://johnsd.cse.unsw.edu.au:3000/entities/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/display'
```
> successful request returns the entity:

```json
{
    "_type": "API",
    "id": "ee1db224-3331-4b8a-bc11-8839b4e5d6b4",
    "baseUrl": "https://api.github.com",
    "description": "github",
    "methods": [
        "07acb558-7626-4bb4-946a-2ddd77c4dbb6",
        "9446bdf1-da5a-4096-98bc-47cfa8c15157",
        "df5f4ae2-e80c-48c1-a641-7d6b2d02d5cb"
    ],
    "name": "Github",
    "provider": "http://github.com",
    "resources": [
        "6d5dd456-3b04-4590-b51a-c5094c6a5cd7"
    ],
    "type": "REST"
}
```

This endpoint shows arbitrary entity and interpret/execute it if applicable.
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
404 | Entity of specified `id` does not exist | resource not found
