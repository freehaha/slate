---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - python
  - javascript

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - entities
  - data
  - recipe
  - widget
  - resourcetypes

search: true
---
# Introduction

APIBase is a database for APIs. Similar to Wikipedia, APIBase stores knowledge for
to understanding topics related to APIs including indiviual endpoints and their parameters.
To facilitate understand of Data and data-analysis APIs, APIBase also allow
users to define data schemas and provide related methods to help the
understanding the nature of different datasets. Finally, with Recipes users can
make composition of APIs and share the knowledge of how different APIs
can be used together and how to apply them onto different sources of data.

APIBase itself is provided as a service accessed by the APIBase API and is sectioned as below:

- API Manipulation: methods to get/add/list entities related to APIs
- Entity: general operations on entities in APIBase
- Data: methods to define/query data from different sources
- Recipe: methods to create/run recipes
- Widget: methods to add/render widgets

# API Manipulation

## Models

### API

```json
{
  "name": "Github",
  "type": "REST",
  "version": "1.0",
  "provider": "Github Inc.",
  "description": "github apis",
  "baseUrl": "https://api.github.com"
}
```
API, Application Program Interface, is a set of routines and protocols for
building software applications. The API is the building block for software
applications and specifies how software components should interact. An API
object in APIBase has the following structure:

Attribute | Type | Example | Description
--- | --- | --- | ---
\*name | string | "Github" | Name of the API
type | string | "REST" | type of the API, always `REST`
version | string | "1.0" | version string
\*provider | string | "Github Inc." | a short sentence representing the provider
tags | ["strings"] | ["development", "git"] | list of tags that identify the API
\*description | string | "github APIs" | description about the API
\*baseUrl |string | "https://api.github.com" | base URL of the API

<aside class="notice">
Attributes marked with * are required
</aside>

### Method

```json
{
  "name": "GetGist”,
  "method": "POST",
  "path": "/gist/{gistId}",
  "description": "get a gist"
}
```

A Method describes an endpoint of an API. For example, the [Get a single
gist](https://developer.github.com/v3/gists/#get-a-single-gist) of Github API
is a Method in the Github API. Methods contains the following attributes:

Attribute | Type | Example | Description
--- | --- | --- | ---
\*name | string | "GetGist" | Name of the Method
\*method | `POST`, `GET`, `PUT` or `DELETE` | "GET" | HTTP method used
\*path | string | "gist/{gistId}" | Path for the endpoint, relative to the `baseURL` of the API, use brackets {} if there for placeholder segments
\*description | string | "get a gist" | Description for the Method

### Parameter

```json
{
  "name": "gistId",
  "location": "path",
  "type": "string",
  "required": true
}
```

Parameters describes what are the inputs required/available for the Method. A Method can have multiple Parameters
and in different `locations`. `locked` attribute indicates whether the parameter is intended to be changed by user or not.
If it is set, APIBase will always use the `default` value for that parameter regardless of what users input.


Attribute | Type | Example | Description
--- | --- | --- | ---
\*name | string |  "gistId" | Name of the parameter
\*location | `path`, `query`, `body`, `header` or `form` | "path" | Location where the parameter should be
\*type | `string`, `number`, `integer`, `boolean`, `array` or `file` | "string" | Type of the parameter
\*required | boolean | true | Whether the parameter is required or not
default | same as `type` | "default gist ID" | Default value if the parameter is missing or is `locked`
locked | boolean | false | Indicate whether the parameter is `locked` or not

#### Location

The `location` attribute indicate how the Parameter should be sent when invoking the endpoint.

Location | Example
--- | --- | --- | ---
path | http://host/foo/{parameter}/bar
query | http://host/foo?{parameter}=value
header | In HTTP request header. For example, Agent or Content-Type. A list of headers can be found [here](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)
body | In HTTP body, serialized as JSON
form | In HTTP body, serialized as standard x-www-form-urlencoded format


### Resource

```json
{
	"resourceType": "YoutubeVideo",
	"description": "video tutorial of using Github"
	"data": {
		"title": "Github Tutorial For Beginners - Github Basics for Mac or Windows & Source Control Basics",
		"link" : "https://youtu.be/0fKg7e37bQE"
	}
}
```

A Resource encapsulate information related to API, Method and other entities to
explain and enrich the entities.  Resources could be of various types. For
example, there are `YoutubeVideo` resources that represent videos hosted in
Youtube, `InvokeExample` resources that show example parameters for invoking a
Method. Resources can be added to various entities to provide additional
information about the entities.

A Resource has the following attributes:

Attribute | Type | Description
--- | --- | ---
\*description | string | Description of the resource
\*data | any | The actual data of the resource
\*resourceType | ResourceType | The type of Resource which schema `data` will be validated against. See a list of available `resourceType` and their schema [here](#resourcetypes)

## Add API

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/apis
	-H 'Content-Type: application/json'
	-d '{
	  "name": "Github",
	  "type": "REST",
	  "version": "1.0",
	  "provider": "Github Inc.",
	  "description": "github apis",
	  "baseUrl": "https://api.github.com"
	}'
```

```python
```

```javascript
```

> Successful requst returns the following response

```json
{
	"message": "entity created",
	"id": "<id_of_created_entity>"
}
```

This endpoint adds an API entity to APIBase.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/apis`

### Parameters
An [API](#api) Object.

### Response
Attribute | Description
--- | ---
message | `entity created`
id | ID of the created API object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | User input does not match API schema | schema mismatch
409 | API with the specified name already exists | entity of that name already exists

## Retrieve information of an API
```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/apis/ee1db224-3331-4b8a-bc11-8839b4e5d6b4
```

> Successful request returns an API object:

```json
{
    "id": "ee1db224-3331-4b8a-bc11-8839b4e5d6b4",
    "type": "REST"
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
}
```
This endpoint retrieves an API.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/apis/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the API

### Response

An [API](#api) Object with related `methods` and `resources`.

Attribute | Description
--------- | ----------- |
methods | ID of Methods of the API
resources | ID of Resources of the API

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | API of specified `id` does not exist | resource not found

## Add a Method to an API

```shell
#
curl -XPOST -H 'Content-Type: application/json' \
http://johnsd.cse.unsw.edu.au:3000/apis/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/methods -d '{
  "name": "GetGist”,
  "method": "POST",
  "path": "/gist/{gistId}",
  "description": "get a gist"
}'
```

> Successful requst returns the following response

```json
{
	"message": "entity created",
	"id": "<id_of_created_entity>"
}
```


This endpoint adds a Method entity to an API.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/apis/{id}/methods`

### Parameters

A [Method](#method) object.

### Response
Attribute | Description
--- | ---
message | `entity created`
id | ID of the created Method object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | User input does not match Method schema | schema mismatch

## Retrieve information of a Method

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/methods/99d64f8e-eace-417d-8cea-511621aaf57c
```
> Successful request returns an [Method object](#method):

```json
{
    "id": "99d64f8e-eace-417d-8cea-511621aaf57c",
    "description": "Get a gist",
    "method": "GET",
    "name": "GetGist",
    "parameters": [
        "8ca7b0c7-bda0-4667-9f74-f67897cef67f"
    ],
    "path": "/gists/{gistId}",
    "resources": [
        "55394b28-b162-4355-b65b-6574b1956c77"
    ],
}

```

This endpoint retrieves a Method.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/methods/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Method

### Response

An [Method](#method) Object with related `parameters` and `resources`.

Attribute | Description
--------- | ----------- |
parameters | ID of Parameters of the API
resources | ID of Resources of the API

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | Method of specified `id` does not exist | resource not found



## Add a Parameter to a Method

```shell
curl -XPOST -H 'Content-Type: application/json'
http://johnsd.cse.unsw.edu.au:3000/methods/99d64f8e-eace-417d-8cea-511621aaf57c/parameters -d '{
  "name": "gistId",
  "location": "path",
  "type": "string",
  "required": true
}'

```

> Successful requst returns the following response

```json
{
	"message": "entity created",
	"id": "<id_of_created_entity>"
}
```

Parameters describe the parameters of a Method, their types, whether they are optional and the place
where the parameters should be placed (in url as path/query, in the header, or in the request body).
Locked attribute indicates whether the parameter is intended to be changed by users or not. If it is
set, APIBase will always use the default value for the parameter regardless of what users input.

This endpoint adds a Parameter entity to a Method.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/methods/{id}/parameters`

### Parameters
A [Parameter](#parameter) Object.


### Response
Attribute | Description
--- | ---
message | `entity created`
id | ID of the created Parameter object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | User input does not match Parameter schema | schema mismatch

## Retrieve information of a Parameter

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/parameters/ee1db224-3331-4b8a-bc11-8839b4e5d6b4
```

> Successful request returns an [Parameter object](#parameter):

```json
{
	"id": "d74c24e6-9451-41f6-93be-afdfc20a7f4c",
	"default": "english",
	"location": "form",
	"name": "language",
	"type": "string",
	"required": false
}
```
This endpoint retrieves an Parameter.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/parameters/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Parameter

### Response

A [Parameter](#parameter) Object.

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | Parameter of specified `id` does not exist | resource not found

## Add a Resource to an API

```shell
curl -XPOST -H 'Content-Type: application/json'
http://johnsd.cse.unsw.edu.au:3000/apis/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/resources -d '{
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

This endpoint adds a Resource to an API.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/apis/{id}/resources`

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

## List resources of an API
```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/apis/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/resources
```

> Successful request returns a list of ID of resources:

```json
[
	"6d5dd456-3b04-4590-b51a-c5094c6a5cd7",
	"3d53d456-3b04-3ab0-b51s-d3012a133d56"
]
```

This endpoint retrieves an array of Resources that are associated with the API.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/apis/{id}/resources`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the API

### Response

An array of [Resource objects](#resource) related to the API.


### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | API of specified `id` does not exist | resource not found

## Add a Resource to a Method

```shell
curl -XPOST -H 'Content-Type: application/json'
http://johnsd.cse.unsw.edu.au:3000/apis/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/resources -d '{
	"resourceType": "YoutubeVideo",
	"description": "Basic tutorial for Github in Mac and Windows"
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

This endpoint adds a Resource to a Method.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/methods/{id}/resources`

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

## List resources of a Method
```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/methods/ee1db224-3331-4b8a-bc11-8839b4e5d6b4/resources
```

> Successful request returns a list of ID of resources:

```json
[
	"6d5dd456-3b04-4590-b51a-c5094c6a5cd7",
	"3d53d456-3b04-3ab0-b51s-d3012a133d56"
]
```

This endpoint retrieves an array of Resources that are associated with the Method.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/methods/{id}/resources`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Method

### Response

An array of [Resource objects](#resource) related to the Method.


### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
404 | Method of specified `id` does not exist | resource not found
