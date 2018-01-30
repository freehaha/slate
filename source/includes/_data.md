# Data

## Definitions

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

Property | Type | Description
--- | --- | --- | ---
\*name | string | Name of the Data Source Schema
\*description | string | Short description of the Data Source Schema
\*schema | JSON Schema | JSON schema describing the structure of data

### Connector

> An example Connector

```json
{
	"id": "03c60eb7-7a09-48a5-8595-cf58d0fc6475",
	"_created": 1445482308,
	"engine": {
		"method": "npm",
		"code": "https://github.com/freehaha/socrata-connector.git"
	},
	"name": "socrata-connector"
}
```

A Connector is a module that allows APIBase to retrieve data from 3rd party services. For example, the socrata-connector
allows APIBase to retrieve data from Socrata given an artifact id, which is then available to perfrom a [Query](#query).

Below lists properties of a Connector:

Property | Type | Description
--- | --- | --- | ---
\*name | string | Name of the Conncetor, must match the name in package.json in the repository
\*description | string | Short description of the Connector
\*engine | object | See below

#### Engine
The `engine` object describe how APIBase can use the connector. As of now we only support npm packages.

##### npm
Property | Type | Description
--- | --- | --- | ---
\*method | string | Always `npm`
\*code | string | URL accepted by [npm install](https://docs.npmjs.com/cli/install), listed below

Accepted `code` format:

- \<name\>
- \<name\>@\<tag\>
- \<name\>@\<version\>
- \<name\>@\<version range\>
- \<git-host\>:\<git-user\>/\<repo-name\>
- \<git repo url\>

When a `<name>` is used, the module will be pulled from the npm registry whereas if a git url(`<git-host>` or `<git repo url>`) is used it will be used to pull the module.

### Data Source

> An example Data Source entity

```json
{
	"source": {
		"dataProvider": "https://data.melbourne.vic.gov.au",
		"resourceId": "vh2v-4nfs"
	},
	"description": "On-street parking bay sensors",
	"name": "melborne-street-parking-sensors"
}
```

A Data Source is an instance of Connector with specific configuration. For
example, one can create a Data Source using the Socrata Connector and configure
it so that when accessed, the Data Source returns the data inside a specific
artifact of Socrata. Below lists property of a DataSource:

Property | Type | Description
--- | --- | --- | ---
\*name | string | Name of the DataSource
\*description | string | Short description of the DataSource
\*source | object | Configuration for the Connector. This is Connector-specific


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

Property | Type | Description
--- | --- | --- | ---
\*name | string | Name of the Query
\*sources | [ uuid ] | IDs of sources which the expression will be evaluated on. These can be ID of DataSource or Query.
\*description | string | Short description of the Query
\*expression | string | A [jq](https://stedolan.github.io/jq/manual/) query expression


## Create DataSourceSchema

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/datasourceschemas
	-H 'Content-Type: application/json'
	-d '{
		"name": "Iris Dataset",
		"description": "iris dataset from UCI Machine Learning Repository. https://archive.ics.uci.edu/ml/datasets/Iris",
		"schema": {
			"type": "array",
			"items": {
				"type": "object",
				"properties": {
					"sepalLength": {
						"type": "number"
					},
					"sepalWidth": {
						"type": "number"
					},
					"petalLength": {
						"type": "number"
					},
					"petalWidth": {
						"type": "number"
					},
					"class": {
						"type": "string"
					}
				}
			}
		}
	}''
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

Property | Description
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
curl -XGET http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/3fafc259-5abb-4780-8807-5663d374290f
```

> Successful request returns a [DataSourceSchema object](#data-source-schema):

```json
{
    "id": "3fafc259-5abb-4780-8807-5663d374290f",
    "schema": {
        "type": "array",
        "items": {
            "type": "object",
            "properties": {
                "sepalLength": {
                    "type": "number"
                },
                "sepalWidth": {
                    "type": "number"
                },
                "petalLength": {
                    "type": "number"
                },
                "petalWidth": {
                    "type": "number"
                },
                "class": {
                    "type": "string"
                }
            }
        }
    },
    "description": "iris dataset from UCI Machine Learning Repository. https://archive.ics.uci.edu/ml/datasets/Iris",
    "name": "Iris Dataset",
	"connectors": [],
    "artifacts": []
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

An [DataSourceSchema](#data-source-schema) Object with related `connectors` and `artifacts`.

Property | Description
--------- | ----------- |
connectors | ID of Connectors available for the DataSourceSchema
artifacts | ID of Artifacts associated to the DataSourceSchema

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | DataSourceSchema of specified `id` does not exist | resource not found

## Add an Artifact to a DataSourceSchema

```shell
curl -XPOST -H 'Content-Type: application/json'
http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/3fafc259-5abb-4780-8807-5663d374290f/artifacts -d '{
	"artifactType": "URL",
	"description": "UCI Machine Learning reporitory web page for Iris Data Set",
	"data": {
		"title": "UCI Machine Learning Repository: Iris Data Set",
		"url": "https://archive.ics.uci.edu/ml/datasets/Iris"
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

This endpoint adds an Artifact to a DataSourceSchema.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/{id}/artifacts`

### Parameters

An [Artifact](#artifact) Object.

### Response
Property | Description
--- | ---
message | `entity created`
id | ID of the created Artifact object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
400 | User input does not match Artifact schema | schema mismatch
400 | `data` does not match ArtifactType schema | artifact schema mismatch

## List artifacts of a DataSourceSchema
```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/3fafc259-5abb-4780-8807-5663d374290f/artifacts
```

> Successful request returns a list of ID of artifacts:

```json
[
    "f91b3f17-8b45-4370-997e-883c66d3c418"
]
```

This endpoint retrieves an array of Artifacts that are associated with the DataSourceSchema.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/{id}/artifacts`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the DataSourceSchema

### Response

An array of IDs of [Artifact objects](#artifact) related to the DataSourceSchema.

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | DataSourceSchema of specified `id` does not exist | resource not found

## Add Connector

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/3fafc259-5abb-4780-8807-5663d374290f/connectors
	-H 'Content-Type: application/json'
	-d '{
		"name": "iris-data-connector",
		"description": "iris dataset from UCI Machine Learning Repository. https://archive.ics.uci.edu/ml/datasets/Iris",
		"engine": {
			"method": "npm",
			"code": "https://github.com/APIBase/iris-data-connector.git"
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

This endpoint adds a Connector to a DataSourceSchema

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/datasourceschemas/{id}/connectors`

### Parameters

A [Connector](#connector) Object.

### Response

Property | Description
--- | ---
message | `entity created`
id | ID of the created Connector entity

### Errors

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
400 | User input does not match Connector schema | schema mismatch
409 | Connector with the specified name already exists | entity of that name already exists

## Retrieve information of a Connector

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/connectors/b3ae7391-264c-4424-adad-fcc8836da839
```

> Successful request returns a [Connector object](#connector):

```json
{
    "id": "b3ae7391-264c-4424-adad-fcc8836da839",
    "engine": {
        "method": "npm",
        "code": "https://github.com/APIBase/iris-data-connector.git"
    },
    "name": "iris-data-connector",
    "datasources": [],
    "artifacts": []
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

A [Connector](#connector) Object with related `artifacts` and `datasources`.

Property | Description
--------- | ----------- |
artifacts | ID of Artifacts associated with the Connector
datasources | ID of DataSources that use this Connector

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | Connector of specified `id` does not exist | resource not found

## Add an Artifact to a Connector

```shell
curl -XPOST -H 'Content-Type: application/json'
http://johnsd.cse.unsw.edu.au:3000/connectors/b3ae7391-264c-4424-adad-fcc8836da839/artifacts -d '{
	"artifactType": "YoutubeVideo",
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

This endpoint adds an Artifact to a Connector.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/connectors/{id}/artifacts`

### Parameters

An [Artifact](#artifact) Object.

### Response
Property | Description
--- | ---
message | `entity created`
id | ID of the created Artifact object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
400 | User input does not match Artifact schema | schema mismatch
400 | `data` does not match ArtifactType schema | artifact schema mismatch

## List artifacts of a Connector
```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/connectors/b3ae7391-264c-4424-adad-fcc8836da839/artifacts
```

> Successful request returns a list of ID of artifacts:

```json
[
	"6d5dd456-3b04-4590-b51a-c5094c6a5cd7",
	"3d53d456-3b04-3ab0-b51s-d3012a133d56"
]
```

This endpoint retrieves an array of Artifacts that are associated with the Connector.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/connectors/{id}/artifacts`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Connector

### Response

An array of IDs of [Artifact objects](#artifact) related to the Connector.

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | Connector of specified `id` does not exist | resource not found

## Add DataSource

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/connectors/b3ae7391-264c-4424-adad-fcc8836da839/datasources
	-H 'Content-Type: application/json'
	-d '{
		"name": "UCL iris data",
		"description": "original UCL iris data",
		"source": {}
	}'
```

> In this case, the connector iris-data-connector doesn't take any parameter so the `source` parameter is left blank.
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

A [DataSource](#data-source) Object.

### Response

Property | Description
--- | ---
message | `entity created`
id | ID of the created DataSource entity

### Errors

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
400 | User input does not match DataSource schema | schema mismatch
409 | DataSource with the specified name already exists | entity of that name already exists

## Retrieve information of a DataSource

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/datasources/576927bb-7cb3-4c75-81b0-7c1e67a724a1
```

> Successful request returns a [DataSource object](#data-source):

```json
{
    "id": "576927bb-7cb3-4c75-81b0-7c1e67a724a1",
    "source": {},
    "description": "original UCL iris data",
    "name": "UCL iris data"
}
```

This endpoint retrieves a DataSource.

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
400 | provided `id` is not a valid UUID | malformed UUID
404 | DataSource of specified `id` does not exist | resource not found

## Create Query

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/queries
	-H 'Content-Type: application/json'
	-d '{
		"expression": "map(select(.sepalLength > 5))",
		"description": "get all data where sepalLength is greater than 5",
		"name": "GetsLenGt5",
		"sources": ["576927bb-7cb3-4c75-81b0-7c1e67a724a1"]
	}'
```

> Successful requst returns the following response

```json
{
	"message": "entity created",
	"id": "<id_of_created_entity>"
}
```

> More complicated query, used to prepare data for the google-chart widget.

```shell
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/queries
	-H 'Content-Type: application/json'
	-d '{
		"expression": "group_by(.class) | map(map([ .sepalLength, .sepalWidth, .class ])) | [[\"sepalLength\", .[0][0][2], .[1][0][2], .[2][0][2]]] + (.[0] | map([.[0], .[1], null, null])) + (.[1] | map([.[0], null, .[1], null])) + (.[2] | map([.[0], null, null, .[1]]))",
		"description": "query iris data to render in chart",
		"name": "VisualIrisData",
		"sources": ["576927bb-7cb3-4c75-81b0-7c1e67a724a1"]
	}'
```


A Query allows one to evaluate an `expression` on one or more DataSources or Queries. Upon being
created, the query is not immediately executed and returns only the entity created message. To
evaluate the Query and view the result, use the [Display Entity](#display-entity) endpoint.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/queries`

### Parameters

A [Query](#query) Object.

### Response

Property | Description
--- | ---
message | `entity created`
id | ID of the created Query entity

### Errors

Code | Reason | Message
--- | --- | ---
400 | User input does not match Query schema | schema mismatch
409 | Query with the specified name already exists | entity of that name already exists

## Retrieve information of a Query

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/queries/b1c73190-553e-4453-a672-394e83feb417
```

> Successful request returns a [Query object](#query):

```json
{
	"id": "b1c73190-553e-4453-a672-394e83feb417",
    "expression": "map(select(.sepalLength > 5))",
    "description": "get all data where sepalLength is greater than 5",
    "name": "GetsLenGt5",
    "sources": [
        "576927bb-7cb3-4c75-81b0-7c1e67a724a1"
    ]
}
```

This endpoint retrieves a Query. Note that this endpoint only retrieves the query expression, not the evaluated result.
To evaluate the Query and view the result, use the [Display Entity](#display-entity) endpoint.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/queries/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Query

### Response

An [Query](#query) Object.

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | Query of specified `id` does not exist | resource not found
