# Data

## Models

### Data Source Schema

> An example Data Source Schema

```json
{
    "id": "11185e1d-c1bd-4d3c-ac50-04a222b02452",
    "name": "TestCollection",
    "description": "collection of object",
    "schema": {
        "type": "array",
        "items": {
            "type": "object",
            "properties": {
                "name": {
                    "type": "string"
                },
                "id": {
                    "type": "number"
                }
            },
            "required": [
                "name",
                "id"
            ]
        }
    },
}
```

Data Source Schema is a schema describing data coming from a source so that we can understand the structure of the data.

Attribute | Type | Description
--- | --- | --- | ---
\*name | string | Name of the Data Source Schema
\*description | string | Short description of the Data Source Schema
\*schema | JSON Schema | JSON schema describing the structure of data

### Connector

> An example Connector

```json
{
	"id": "629cfe3b-6101-4b5a-a8d9-166cd0e3be82",
	"_created": 1507270476,
	"name": "echo-connector"
	"engine": {
		"method": "npm",
		"code":"https://github.com/APIBase/echo-connector.git"
	},
}
```

A Connector is a module that allows APIBase to retrieve data from 3rd party services. For example, the socrata-connector
allows APIBase to retrieve data from Socrata given a resource id, which is then available to perfrom a [Query](#query).

Below lists attributes of a Connector:

Attribute | Type | Description
--- | --- | --- | ---
\*name | string | Name of the Conncetor, must match the name in package.json in the repository
\*description | string | Short description of the Connector
\*engine | object | see below

#### Engine
The `engine` object describe how APIBase can use the connector. As of now we only support npm packages.

##### npm
Attribute | Type | Description
--- | --- | --- | ---
\*method | string | always `npm`
\*code | string | url accepted by [npm install](https://docs.npmjs.com/cli/install), listed below

Accepted `code` format:

- [\<@scope\>/]\<name\>
- [\<@scope\>/]\<name\>@\<tag\>
- [\<@scope\>/]\<name\>@\<version\>
- [\<@scope\>/]\<name\>@\<version range\>
- \<git-host\>:\<git-user\>/\<repo-name\>
- \<git repo url\>


### Data Source

> An example Data Source entity

```json
{
	"source": {
		"dataProvider": "https://data.melbourne.vic.gov.au",
		"resourceId": "vh2v-4nfs"
	},
	"description": "testing purpose",
	"name": "test2"
}
```

A Data Source is an instance of Connector with specific configuration. For
example, one can create a Data Source using the Socrata Connector and configure
it so that when accessed, the Data Source returns the data inside a specific
resource of Socrata. Below lists attribute of a DataSource:

Attribute | Type | Description
--- | --- | --- | ---
\*name | string | Name of the Conncetor, must match the name in package.json in the repository
\*description | string | Short description of the DataSource
\*source | object | configuration for the Connector. This is Connector-specific.


### Query

> An example Query entity. Returns the first element of the array.

```json
{
	"name": "get-first-query",
	"sources": ["id1"],
	"description": "get the first row",
	"expression": ".[0]"
}
```

> Another example, filtering out rows where the first column is less than or equal to 200

```json
{
	"name": "filter 200",
	"sources": ["id1", "id2"],
	"description": "filter out rows where first column <= 200",
	"expression": ".rows | map(select(.[1] | tonumber > 200))"
}
```

A Query entity describe a [jq](https://stedolan.github.io/jq/manual/) query
expression to be executed on a DataSource.  When a Query is created, it will
not be immediately executed and thus will not return the result. Instead, the
[Display Entity](#display-entity) should be used to execute the Query and view
the result.

Attribute | Type | Description
--- | --- | --- | ---
\*name | string | Name of the Conncetor, must match the name in package.json in the repository
\*sources | [ uuid ] | IDs of sources which the expression will be evaluated on. These can be ID of DataSource or Query.
\*description | string | Short description of the Query
\*expression | string | a [jq](https://stedolan.github.io/jq/manual/) query expression

### Transformer

> An example Transformer entity.

```json
{
	"description": "transform data",
	"source": "<id-source>",
	"target": "<id-target>",
	"expression": "map({text: .body})"
}
```

A Transformer is a query that is designed transform data from one DataSourceSchema to another. Below lists the attributes of a Transformer:

Attribute | Type | Description
--- | --- | --- | ---
\*source | uuid | ID of the source DataSourceSchema
\*target | uuid | ID of the target DataSourceSchema
\*description | string | Short description of the Transformer
\*expression | string | a [jq](https://stedolan.github.io/jq/manual/) query expression


## Create DataSourceSchema

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/datasourceschemas
	-H 'Content-Type: application/json'
	-d '{
		"schema": {
			"properties": {
				"query": {
					"type": "string"
				},
				"results": {
					"type": "array"
				}
			},
			"required": [
				"query"
			]
		},
		"description": "string",
		"name": "Yooz Rest API Schema",
	}'
```

> Successful requst returns the following response

```json
{
	"message": "entity created",
	"id": "<id_of_created_entity>"
}
```

A DataSourceSchema describes the schema of data coming from a source so that we can understand and explore the structure of the data.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/datasourceschemas`

### Parameters

A [DataSourceSchema](#data-source-schema) Object.

### Response

Attribute | Description
--- | ---
message | `entity created`
id | ID of the created DataSourceSchema entity

### Errors

Code | Reason | Message
--- | --- | ---
400 | User input does not match DataSourceSchema schema | schema mismatch
409 | DataSourceSchema with the specified name already exists | entity of that name already exists

## Retrieve information of a DataSourceSchema

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/56e3e7af-8268-445f-91ed-63eabb0e5314
```

> Successful request returns a [DataSourceSchema object](#data-source-schema):

```json
{
    "id": "56e3e7af-8268-445f-91ed-63eabb0e5314",
    "schema": {
        "properties": {
            "query": {
                "type": "string"
            },
            "results": {
                "type": "array"
            }
        },
        "required": [
            "query"
        ]
    },
    "description": "string",
    "name": "Yooz Rest API Schema",
    "connectors": [
        "b5f922b9-c850-45b7-86c1-0e8bc7371cae"
    ],
    "resources": []
}
```
This endpoint retrieves a DataSourceSchema.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the DataSourceSchema

### Response

An [DataSourceSchema](#data-source-schema) Object with related `connectors` and `resources`.

Attribute | Description
--------- | ----------- |
connectors | ID of Connectors available for the DataSourceSchema
resources | ID of Resources associated to the DataSourceSchema

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | DataSourceSchema of specified `id` does not exist | resource not found

## Add a Resource to a DataSourceSchema

```shell
curl -XPOST -H 'Content-Type: application/json'
http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/resources -d '{
	"resourceType": "YoutubeVideo",
	"description": "Github Universe 2017 event video",
	"data": {
		"title": "GitHub Universe 2017",
		"link": "https://www.youtube.com/watch?v=53qGVTSUoFA"
	}
}'

```

> Successful requst returns the following response

```json
{
	"message": "entity created",
	"id": "<id_of_created_entity>"
}
```

This endpoint adds a Resource entity to a DataSourceSchema.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/{id}/resources`

### Parameters

A [Resource](#resource) Object.

### Response
Attribute | Description
--- | ---
message | `entity created`
id | ID of the created Resource object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | User input does not match Resource schema | schema mismatch
400 | `data` does not match ResourceType schema | resource schema mismatch

## List resources of a DataSourceSchema
```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/resources
```

> Successful request returns a list of ID of resources:

```json
[
	"6d5dd456-3b04-4590-b51a-c5094c6a5cd7",
	"3d53d456-3b04-3ab0-b51s-d3012a133d56"
]
```

This endpoint retrieves an array of Resources that are associated with the DataSourceSchema.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/{id}/resources`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the DataSourceSchema

### Response

An array of IDs of [Resource objects](#resource) related to the DataSourceSchema.

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | DataSourceSchema of specified `id` does not exist | resource not found

## Add Connector

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/56e3e7af-8268-445f-91ed-63eabb0e5314/connectors
	-H 'Content-Type: application/json'
	-d '{
		"id": "b5f922b9-c850-45b7-86c1-0e8bc7371cae",
		"engine": {
			"method": "npm",
			"code": "https://github.com/mysilver/yooz-connector"
		},
		"name": "yooz-api-connector"
	}'
```

> Successful requst returns the following response

```json
{
	"message": "entity created",
	"id": "<id_of_created_entity>"
}
```

This endpoint adds a Connector to a DataSourceSchema

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/{id}/connectors`

### Parameters

A [Connector](#connector) Object.

### Response

Attribute | Description
--- | ---
message | `entity created`
id | ID of the created Connector entity

### Errors

Code | Reason | Message
--- | --- | ---
400 | User input does not match Connector schema | schema mismatch
409 | Connector with the specified name already exists | entity of that name already exists

## Retrieve information of a Connector

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/connectors/b5f922b9-c850-45b7-86c1-0e8bc7371cae
```

> Successful request returns a [Connector object](#connector):

```json
{
    "id": "b5f922b9-c850-45b7-86c1-0e8bc7371cae",
    "engine": {
        "method": "npm",
        "code": "https://github.com/mysilver/yooz-connector"
    },
    "name": "yooz-api-connector",
}
```

This endpoint retrieves a Connector.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/connectors/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Connector

### Response

A [Connector](#data-source-schema) Object with related `resources` and `datasources`.

Attribute | Description
--------- | ----------- |
resources | ID of Resources associated with the Connector
datasources | ID of DataSources that use this Connector

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | Connector of specified `id` does not exist | resource not found

## Add a Resource to a Connector

```shell
curl -XPOST -H 'Content-Type: application/json'
http://johnsd.cse.unsw.edu.au:3000/connectors/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/resources -d '{
	"resourceType": "YoutubeVideo",
	"description": "Github Universe 2017 event video",
	"data": {
		"title": "GitHub Universe 2017",
		"link": "https://www.youtube.com/watch?v=53qGVTSUoFA"
	}
}'

```

> Successful requst returns the following response

```json
{
	"message": "entity created",
	"id": "<id_of_created_entity>"
}
```

This endpoint adds a Resource entity to a Connector.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/connectors/{id}/resources`

### Parameters

A [Resource](#resource) Object.

### Response
Attribute | Description
--- | ---
message | `entity created`
id | ID of the created Resource object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | User input does not match Resource schema | schema mismatch
400 | `data` does not match ResourceType schema | resource schema mismatch

## List resources of a Connector
```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/connectors/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/resources
```

> Successful request returns a list of ID of resources:

```json
[
	"6d5dd456-3b04-4590-b51a-c5094c6a5cd7",
	"3d53d456-3b04-3ab0-b51s-d3012a133d56"
]
```

This endpoint retrieves an array of Resources that are associated with the Connector.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/connectors/{id}/resources`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Connector

### Response

An array of IDs of [Resource objects](#resource) related to the Connector.

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | Connector of specified `id` does not exist | resource not found

## Add DataSource

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/connectors/3c7814c3-0e0c-4cd3-85cd-f288b9e85174/datasources
	-H 'Content-Type: application/json'
	-d '{
		"name": "TestData1",
		"description": "testing data 1",
		"source": [
			{
				"name": "abc",
				"id": 1
			},
			{
				"name": "def",
				"id": 1
			}
		]
	}'
```

> Successful requst returns the following response

```json
{
	"message": "entity created",
	"id": "<id_of_created_entity>"
}
```

This endpoint adds a DataSource using specified Connector, creating an instance of Connector with configuration
specified by `source`.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/connectors/{id}/datasources`

### Parameters

A [Connector](#connector) Object.

### Response

Attribute | Description
--- | ---
message | `entity created`
id | ID of the created Connector entity

### Errors

Code | Reason | Message
--- | --- | ---
400 | User input does not match Connector schema | schema mismatch
409 | DataSource with the specified name already exists | entity of that name already exists

## Retrieve information of a DataSource

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/datasources/c9fd63eb-8882-4a42-9919-6326b85f4768
```

> Successful request returns a [Connector object](#connector):

```json
{
    "id": "c9fd63eb-8882-4a42-9919-6326b85f4768",
	"name": "TestData1",
	"description": "testing data 1",
	"source": [
		{
			"name": "abc",
			"id": 1
		},
		{
			"name": "def",
			"id": 1
		}
	]
}
```

This endpoint retrieves a Connector.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/datasources/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the DataSource

### Response

A [DataSource](#data-source-schema) Object.

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | DataSource of specified `id` does not exist | resource not found

