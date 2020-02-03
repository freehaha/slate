# Widget

## Definitions

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

Widgets are reusable pieces of visualization code that takes some parameters
and represent them with graphical elements. In APIBase, widgets are simply
[Google Polymer Elements](https://www.polymer-project.org/). Widgets can be
standalone or associated to a [DataSourceSchema](#data-source-schema) in which
case when we receive data that is known to conform to a DataSourceSchema, we
can visualize the data with a call to the [Widget Render](#widget-render) API.

A Widget entity has the following properties:

Property | Type | Description
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
curl -XPOST http://apibase.au.ngrok.io/widgets
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

`POST http://apibase.au.ngrok.io/widgets`

### Parameters
A [Widget](#widget) Object.

### Response
Property | Description
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
curl -XGET http://apibase.au.ngrok.io/widgets/f3b8bd9a-b524-4b5e-b9e2-55b4366e58ba
```

> Successful request returns a [Widget object](#widget):

``` json
{
	"id": "f3b8bd9a-b524-4b5e-b9e2-55b4366e58ba",
	"tags": [],
	"sourceCode": "git://github.com/GoogleWebComponents/google-chart",
	"description": "google chart widget",
	"name": "google-chart",
	"parameters": {
		"type": {
			"enum": [
			"area",
			"bar",
			"bubble",
			"candlestick",
			"column",
			"combo",
			"geo",
			"histogram",
			"line",
			"pie",
			"scatter",
			"stepped-area",
			"treemap"
		]
		},
		"data": {
			"type": "array"
		}
	},
	"artifacts": []
}
```

This endpoint retrieves a Widget.

### HTTP Request

`GET http://apibase.au.ngrok.io/widgets/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Widget

### Response

A [Widget](#widget) Object with related `artifacts`.

Property | Description
--------- | ----------- |
artifacts | ID of Artifacts of the Widget

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | Widget of specified `id` does not exist | resource not found

## Render Widget

This endpoint is an iframe API to render a Widget. To use the endpoint, one must use the endpoint URL in the iframe in order to render the widget.

### HTTP Request

`GET http://apibase.au.ngrok.io/widgets/{id}/render?{parameter}={value}`

### Query Parameters

> Example using gist-element Widget to render a Gist with id `3cac2180f2c0ef43d6c7`

```html
<iframe style="width: 500px; height: 350px; border: none;"
	src="https://apibase.freehaha.org/widgets/f3b8bd9a-b524-4b5e-b9e2-55b4366e58ba/render?type=scatter&options={%22hAxis%22:%20{%22title%22:%20%22sepal%20width%22},%20%22vAxis%22:%20{%22title%22:%20%22sepal%20length%22}}&data=query://7e221af2-df53-4124-aad7-8b81b4da8c56"></iframe>
```

The query parameters trailing the URL will be used as parameters to the Widget. Values can also be a query, with the format `query://<id>`. When specified this way, the query will be executed and
the result used as the value for the widget. For example, in the example on the right, `data=query://7e221af2-df53-4124-aad7-8b81b4da8c56`, will use the result of the query `7e221af2-df53-4124-aad7-8b81b4da8c56`
as `data` parameter for google-chart.

Below is an example rendering the iris-data-set using the google-chart Widget.  The actual parameters depend on individual Widget.

<iframe style="margin-left: 30px; width: 500px; height: 350px; border: 1px solid; overflow: none;" src="https://apibase.freehaha.org/widgets/f3b8bd9a-b524-4b5e-b9e2-55b4366e58ba/render?type=scatter&options={%22hAxis%22:%20{%22title%22:%20%22sepal%20width%22},%20%22vAxis%22:%20{%22title%22:%20%22sepal%20length%22}}&data=query://7e221af2-df53-4124-aad7-8b81b4da8c56"></iframe>

## Add an Artifact to a Widget

```shell
curl -XPOST -H 'Content-Type: application/json'
http://apibase.au.ngrok.io/widgets/9a038994-16c2-4d2a-bb79-e6c8261d0cd4/artifacts -d '{
	"artifactType": "YoutubeVideo",
	"description": "Basic tutorial for Github in Mac and Windows",
	"data": {
		"title": "Github Tutorial For Beginners - Github Basics for Mac or Windows & Source Control Basics",
		"link" : "https://youtu.be/0fKg7e37bQE"
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

This endpoint adds an Artifact to a Widget.

### HTTP Request

`POST http://apibase.au.ngrok.io/widgets/{id}/artifacts`

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

## List artifacts of a Widget
```shell
curl -XGET http://apibase.au.ngrok.io/widgets/9a038994-16c2-4d2a-bb79-e6c8261d0cd4/artifacts
```

> Successful request returns a list of ID of artifacts:

```json
[
	"6d5dd456-3b04-4590-b51a-c5094c6a5cd7",
	"3d53d456-3b04-3ab0-b51s-d3012a133d56"
]
```

This endpoint retrieves an array of Artifacts that are associated with the Widget.

### HTTP Request

`GET http://apibase.au.ngrok.io/widgets/{id}/artifacts`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Widget

### Response

An array of [Artifact objects](#artifact) related to the Widget.


### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | Widget of specified `id` does not exist | resource not found
