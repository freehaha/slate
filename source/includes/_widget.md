# Widget

## Models

### Widget

> A Widget entity

``` json
{
    "id": "9a038994-16c2-4d2a-bb79-e6c8261d0cd4",
    "name": "gist-element",
    "framework": "polymer",
    "sourceCode": "https://github.com/freehaha/gist-element.git",
    "description": "display a single-file gist",
    "parameters": {
        "gistId": {
            "type": "string",
            "default": "7e8bec3fd8cd3789ddd681a38068e655"
        }
    },
    "tags": [
        "gist"
    ]
}
```

Widgets are reusable pieces of visualization code that takes some parameters and represent them with
graphical elements. In APIBase, widgets are simply Google Polymer Elements. Widgets can be standalone or associated to a
[DataSourceSchema](#data-source-schema) in which case when we receive data that is known to conform to a DataSourceSchema,
we can visualize the data with a call to the [Widget Render](#widget-render) API.

A Widget entity has the following attributes:

Attribute | Type | Description
--- | --- | --- | ---
\*name | string | Name of the Widget
\*framework | string | `polymer` is the only supported value here at the moment
\*sourceCode | string | Location of the github repository
\*description | string | Short description for the widget
\*parameters | object | An object describing the parameters that the widget takes. Each key represents a parameter and the value describe the type and default value of the parameter.
tags | [ string ] | An array of string to tag the Widget. This is only used for indexing

## Add Widget

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/widgets
	-H 'Content-Type: application/json'
	-d '{
		"name": "gist-element",
		"framework": "polymer",
		"sourceCode": "https://github.com/freehaha/gist-element.git",
		"description": "display a single-file gist",
		"parameters": {
			"gistId": {
				"type": "string",
				"default": "7e8bec3fd8cd3789ddd681a38068e655"
			}
		},
		"tags": [
			"gist"
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

This endpoint adds a Widget entity to APIBase.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/widgets`

### Parameters
A [Widget](#widget) Object.

### Response
Attribute | Description
--- | ---
message | `entity created`
id | ID of the created Widget object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | User input does not match Widget schema | schema mismatch
409 | Widget with the specified name already exists | entity of that name already exists

## Retrieve information of a Widget
```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/widgets/9a038994-16c2-4d2a-bb79-e6c8261d0cd4
```

> Successful request returns a [Widget object](#widget):

``` json
{
    "id": "9a038994-16c2-4d2a-bb79-e6c8261d0cd4",
    "name": "gist-element",
    "framework": "polymer",
    "sourceCode": "https://github.com/freehaha/gist-element.git",
    "description": "display a single-file gist",
    "parameters": {
        "gistId": {
            "type": "string",
            "default": "7e8bec3fd8cd3789ddd681a38068e655"
        }
    },
	"resources": [],
    "tags": [
        "gist"
    ]
}
```

This endpoint retrieves a Widget.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/widgets/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Widget

### Response

A [Widget](#widget) Object with related `resources`.

Attribute | Description
--------- | ----------- |
resources | ID of Resources of the Widget

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | Widget of specified `id` does not exist | resource not found

