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

This endpoint performs a keyword search in APIBase. It returns only the first 30 results. To get
more results, use the `page` query parameter.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/entities/search`

### Query parameters
attribute | type | example | description
--- | --- | --- | ---
\*q | string | "Github" | Query string
type | `API`, `Method`, `Recipe`, `DataSourceSchema`, `DataSource` | "API" | type of entity you want to search, leave empty to search for all types
page | integer | 1 | page

## Retrieving arbitrary entity

```shell
curl -XGET 'http://johnsd.cse.unsw.edu.au:3000/entities/ee1db224-3331-4b8a-bc11-8839b4e5d6b4'
```

This endpoint lets you get an entity regardless of its type. For example can use
this endpoint to retrieve an API, Method or DataSourceSchema...  Related
entities will also be shown. To change this behavior, use `brief=false` in the
query.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/entities/{id}`

### Parameters

attribute | type | description
--- | --- | --- | ---
\*id | uuid | id of the entity in question
brief | bool | true to show only the entity in question, otherwise all related entities will be shown

## Display entity

```shell
curl -XGET 'http://johnsd.cse.unsw.edu.au:3000/entities/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/display'
```

This endpoint shows arbitrary entity and interpret/execute it if applicable.
Similar to the [Retrieving arbitrary entity](#retrieving-arbitrary-entity) endpoint, this
endpoint can also show an arbitrary entity but if the entity represents an
executable object, for example a Query, then the entity will be executed/run and
show the result instead.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/entities/{id}/display`

### Parameters

attribute | type | description
--- | --- | --- | ---
\*id | uuid | id of the entity in question
