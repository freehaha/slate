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

# API

## Definitions

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
Application Program Interface(API), is a set of routines and protocols for
building software applications. The API is the building block for software
applications and specifies how software components should interact. An API
object in APIBase has the following structure:

Property | Type | Example | Description
--- | --- | --- | ---
\*name | string | "Github" | Name of the API
type | string | "REST" | Type of the API, always `REST`
version | string | "1.0" | Version string
\*provider | string | "Github Inc." | A short sentence representing the provider
tags | ["strings"] | ["development", "git"] | List of tags that identify the API
\*description | string | "github APIs" | Description about the API
\*baseUrl |string | "https://api.github.com" | Base URL of the API

<aside class="notice">
Properties marked with * are required
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
is a Method in the Github API. Methods contains the following properties:

Property | Type | Example | Description
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
and in different `locations`. `locked` property indicates whether the parameter is intended to be changed by user or not.
If it is set, APIBase will always use the `default` value for that parameter regardless of what users input.


Property | Type | Example | Description
--- | --- | --- | ---
\*name | string |  "gistId" | Name of the parameter
\*location | `path`, `query`, `body`, `header` or `form` | "path" | Location where the parameter should be
\*type | `string`, `number`, `integer`, `boolean`, `array` or `file` | "string" | Type of the parameter
\*required | boolean | true | Whether the parameter is required or not
default | same as `type` | "default gist ID" | Default value if the parameter is missing or is `locked`. When omitted, defaults to `null`
locked | boolean | false | Indicate whether the parameter is `locked` or not. When omitted, defaults to `false`

#### Location

The `location` property indicates how the Parameter should be sent when invoking the endpoint.

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

A Resource encapsulates information related to APIs, Methods and other entities to
explain and enrich the entities.  Resources could be of various types. For
example, there are `YoutubeVideo` resources that represent videos hosted in
Youtube and `InvokeExample` resources that show example parameters for invoking a
Method. Resources can be added to various entities to provide additional
information about the entities.

A Resource has the following properties:

Property | Type | Description
--- | --- | ---
\*description | string | Description of the resource
\*data | any | The actual data of the resource
\*resourceType | ResourceType | The type of Resource which schema `data` will be validated against. See a list of available `resourceType` and their schema [here](#resourcetypes)

### Resource Types

Below is a list of resource types available when creating a Resource:

### YoutubeVideo

```json
{
	"title": "Github Tutorial For Beginners - Github Basics for Mac or Windows & Source Control Basics",
	"link" : "https://youtu.be/0fKg7e37bQE"
}
```

References to Youtube videos.

Property | Type | Description
--- | --- | ---
\*title | string | Title of the video
\*link | string | URL to the Youtube video

### InvokeExample

```json
{
	"title": "Get user profile from Github",
	"method": "07acb558-7626-4bb4-946a-2ddd77c4dbb6",
	"parameters": {
		"user": "freehaha"
	}
}
```

Example for invoking a Method.

Property | Type | Description
--- | --- | ---
\*title | string | Title of the invoke example
\*method | string | ID of the Method
\*parameters | object | Example parameters

### URL

```json
{
	"title": "Using YQL and Open Data Tables",
	"url": "https://developer.yahoo.com/yql/guide/yql-users-guide.html"
}
```

External URL links.

Property | Type | Description
--- | --- | ---
\*title | string | Title of the URL
\*url | string | URL of the webpage

## Add an API

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
Property | Description
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

Property | Description
--------- | ----------- |
methods | ID of Methods of the API
resources | ID of Resources of the API

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
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
Property | Description
--- | ---
message | `entity created`
id | ID of the created Method object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
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

Property | Description
--------- | ----------- |
parameters | ID of Parameters of the API
resources | ID of Resources of the API

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
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
Locked property indicates whether the parameter is intended to be changed by users or not. If it is
set, APIBase will always use the default value for the parameter regardless of what users input.

This endpoint adds a Parameter entity to a Method.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/methods/{id}/parameters`

### Parameters
A [Parameter](#parameter) Object.


### Response
Property | Description
--- | ---
message | `entity created`
id | ID of the created Parameter object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
400 | User input does not match Parameter schema | schema mismatch

## Retrieve information of a Parameter

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/parameters/d74c24e6-9451-41f6-93be-afdfc20a7f4c
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
400 | provided `id` is not a valid UUID | malformed UUID
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
Property | Description
--- | ---
message | `entity created`
id | ID of the created Resource object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
400 | User input does not match Resource schema | schema mismatch
400 | `data` does not match ResourceType schema | resource schema mismatch

## Retrieve information of a Resource
```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/resources/6d5dd456-3b04-4590-b51a-c5094c6a5cd7
```

> Successful request returns a Resource object:

```json
{
	"_rtName": "YoutubeVideo",
	"id": "6d5dd456-3b04-4590-b51a-c5094c6a5cd7",
	"data": {
		"link": "https://www.youtube.com/embed/VUaBfYCmJls",
		"title": "What Is Github"
	},
	"resourcetype": [
		"1cd0a97d-195c-43ca-815f-4740ce1e7e97"
	]
}
```
This endpoint retrieves a Resource and can be used to retrieve any resources regardless of
the entity the resource is added to.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/resources/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Resource

### Response

A [Resource](#resource) Object with related `resourcetype`.

Property | Description
--------- | ----------- |
resourcetype | ID of the resource type
\_rtName | Name fo the resource type

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | Resource of specified `id` does not exist | resource not found

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

An array of IDs of [Resource objects](#resource) related to the API.


### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | API of specified `id` does not exist | resource not found

## Add a Resource to a Method

```shell
curl -XPOST -H 'Content-Type: application/json'
http://johnsd.cse.unsw.edu.au:3000/methods/9446bdf1-da5a-4096-98bc-47cfa8c15157/resources -d '{
	"resourceType": "InvokeExample",
	"description": "an example to retrieve gist from the user 'freehaha'"
	"data": {
		"method": "9446bdf1-da5a-4096-98bc-47cfa8c15157",
		"parameters" : {
			"user": "freehaha"
		}
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
Property | Description
--- | ---
message | `entity created`
id | ID of the created Resource object

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
400 | User input does not match Resource schema | schema mismatch
400 | `data` does not match ResourceType schema | resource schema mismatch

## List resources of a Method
```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/methods/9446bdf1-da5a-4096-98bc-47cfa8c15157/resources
```

> Successful request returns a list of ID of resources:

```json
[
	"874720ca-c4ea-4704-a776-f23547848eed",
	"d2cbba9e-7783-4100-a9cf-ee1e0e243d28",
	"dc56b9b6-f2b1-49b9-ad16-360e993b4cb5",
	"f108c273-2687-4d6a-9ad1-996277932d27",
	"c97d5d3c-f417-4869-ab2a-5f1f18893662"
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

An array of IDs of [Resource objects](#resource) related to the Method.


### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | Method of specified `id` does not exist | resource not found
