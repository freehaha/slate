---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - python
  - javascript

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Introduction ...

# API Manipulation

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

This endpoint adds an API entity to APIBase.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/apis/{id}`

### Parameters
attribute | type | example | description
--- | --- | --- | ---
\*name | string | "Github" | Name of the API
type | "REST" | "REST" | type of the API, always "REST"
version | string | "1.0" | version string
\*provider | string | "Github Inc." | a short sentence representing the provider
tags | ["strings"] | ["development", "git"] | list of tags that identify the API
\*description | string | "github APIs" | description about the API
\*baseUrl |string | "https://api.github.com" | base URL of the API



<aside class="notice">
Attributes marked with * are required
</aside>

<aside class="notice">
If you are using shell, make sure the <code>Content-Type</code> is set correctly to <code>application/json</code>
</aside>

## Retrieve information of an API

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/apis/ee1db224-3331-4b8a-bc11-8839b4e5d6b4
```

This endpoint retrieves an API.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/apis/{id}`

### Parameters

attribute | type | description
--- | --- | --- | ---
id | uuid | id of the API

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

attribute | type | example | description
--- | --- | --- | ---
\*name | string | "GetGist" | Name of the Method
\*method | `POST`, `GET`, `PUT` or `DELETE` | "GET" | HTTP method used
\*path | string | "gist/{gistId}" | path for the endpoint, relative to the `baseURL` of the API, use brackets {} if there for placeholder segments
\*description | string | "get a gist" | description for the Method


## Retrieve information of a Method

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/methods/99d64f8e-eace-417d-8cea-511621aaf57c
```

This endpoint retrieves a Method.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/methods/{id}`

### Parameters

attribute | type | description
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
Locked attribute indicates whether the parameter is intended to be changed by user or not. If it is
set, APIBase will always use the default value for the parameter regardless of what the user input.

This endpoint adds a Parameter entity to a Metdhod.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/methods/{id}/parameters`

### Parameters
attribute | type | example | description
--- | --- | --- | ---
\*name | string |  "gistId" | name of the parameter
\*location | `path`, `query`, `body`, `header` or `form` | "path" | location where the parameter should be
\*type | `string`, `number`, `integer`, `boolean`, `array` or `file` | "string" | type of the parameter
\*required | boolean | true | whether the parameter is required or not
default | same as `type` | "default gist ID" | default value if the parameter is missing or is `locked`
locked | boolean | false | indicate whether the parameter is `locked` or not

