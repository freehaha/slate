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
  - errors

search: true
---

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
and in different `locations`.


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
409 | API with the specified name already exists | entity of that name already exists

## Retrieve information of an API

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/apis/ee1db224-3331-4b8a-bc11-8839b4e5d6b4
```

> Successful request returns an API object:

```json
{
    "_created": 1404866619,
    "_type": "API",
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


This endpoint adds a Method entity to an API.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/apis/{id}/methods`

### Parameters

Parameter | Type | Example | Description
--- | --- | --- | ---
\*name | string | "GetGist" | Name of the Method
\*method | `POST`, `GET`, `PUT` or `DELETE` | "GET" | HTTP method used
\*path | string | "gist/{gistId}" | Path for the endpoint, relative to the `baseURL` of the API, use brackets {} if there for placeholder segments
\*description | string | "get a gist" | Description for the Method


## Retrieve information of a Method

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/methods/99d64f8e-eace-417d-8cea-511621aaf57c
```

This endpoint retrieves a Method.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/methods/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | id of the Method



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

Parameters describe the parameters of a Method, their types, whether they are optional and the place
where the parameters should be placed (in url as path/query, in the header, or in the request body).
Locked attribute indicates whether the parameter is intended to be changed by users or not. If it is
set, APIBase will always use the default value for the parameter regardless of what the user input.

This endpoint adds a Parameter entity to a Method.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/methods/{id}/parameters`

### Parameters
Parameter | Type | Example | Description
--- | --- | --- | ---
\*name | string |  "gistId" | Name of the parameter
\*location | `path`, `query`, `body`, `header` or `form` | "path" | Location where the parameter should be
\*type | `string`, `number`, `integer`, `boolean`, `array` or `file` | "string" | Type of the parameter
\*required | boolean | true | Whether the parameter is required or not
default | same as `type` | "default gist ID" | Default value if the parameter is missing or is `locked`
locked | boolean | false | Indicate whether the parameter is `locked` or not

