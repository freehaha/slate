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
	"name": "get number ",
	"sources": ["id1", "id2"],
	"description": "get the first row",
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
