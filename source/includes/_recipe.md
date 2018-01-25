# Recipe
## Models
### Recipe

> A Recipe entity

``` json
{
    "id": "f95c28be-f3d9-459b-b204-b00bd31c981f",
    "name": "get first gist",
    "operations": {
        "listGists": "9446bdf1-da5a-4096-98bc-47cfa8c15157",
        "getGist": "99d64f8e-eace-417d-8cea-511621aaf57c"
    },
    "sequence": [
        {
            "operation": "listGists",
            "parameters": {
                "user": ".username"
            },
			"output": {
				"gists": "."
			}
        },
        {
            "condition": ".gists | length > 1",
            "operation": "getGist",
            "parameters": {
                "gistId": ".gists[0].id"
            }
        }
    ],
    "parameters": {
        "username": "freehaha"
    }
}
```

A Recipe is a sequence of actions to be executed with optionally conditions. It consists of following attributes:

Attribute | Type | Description
--- | --- | --- | ---
\*name | string | Name of the Recipe
\*operations | object | Object containing Methods that are used in the recipe and their alias as key. They can also refer to other Recipes.
\*sequence | array | Sequence of actions to be run
parameters | object | The context used throughout the recipe

#### Sequence

`sequence` contains an array of actions to be run. Each action is described by an object with the following attributes:

Attribute | Type | Description
--- | --- | --- | ---
\*operation | string | Name of the action to be executed, can be the aliases specified in `operations` or special operator prefixed with `$`, listed below.
\*parameters | object | Parameters of the `operations`
condition | string | A query expression. When specified, it will be evaluated and if the result is `false` the action will be skipped, otherwise run as normal.
output | object | A mapping specifying whether the output should be saved as variables and how.

By default, the result of each operation is stored as `._results[#]` in the context. `.results[0]` being the result of the first operation, `_.results[1]` the second and so on.

##### $ operators

- `$query`: perform a query on the context
- `$exit`: terminate the recipe, no further actions will be executed
- `$sequence`: excuted a sequence of operations

##### Output

The `output` contains a list of key-value pairs where the values are expressions to be evaluated against the result of the operation and the keys are where the evaluated 
values will be stored in the context. In the example recipe on the right, the result of the first `listGists` operation is stored as `gists` in the context. 

#### Recipe/Parameters

The `parameters` in a Recipe will be used as the context when running the Recipe. All expressions will be evaluated against this context. 

## Create Recipe

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/recipes
	-H 'Content-Type: application/json'
	-d '{
		"sequence": [
			{
				"operation": "listGists",
				"parameters": {
					"user": ".username"
				}
			},
			{
				"condition": "._results[\"0\"] | length > 1",
				"operation": "getGist",
				"parameters": {
					"gistId": "._results[\"0\"][0].id"
				}
			}
		],
		"name": "get first gist with condition",
		"parameters": {
			"username": "freehaha"
		},
		"operations": {
			"listGists": "9446bdf1-da5a-4096-98bc-47cfa8c15157",
			"getGist": "99d64f8e-eace-417d-8cea-511621aaf57c"
		},
	}'
```

> Successful requst returns the following response

```json
{
	"message": "entity created",
	"id": "<id_of_created_entity>"
}
```

This endpoint creates a Recipe.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/recipes`

### Parameters

A [Recipe](#recipe) Object.

### Response

Attribute | Description
--- | ---
message | `entity created`
id | ID of the created Recipe entity

### Errors

Code | Reason | Message
--- | --- | ---
400 | User input does not match Recipe schema | schema mismatch
409 | Recipe with the specified name already exists | entity of that name already exists

## Retrieve information of a Recipe

```shell
curl -XGET http://johnsd.cse.unsw.edu.au:3000/recipe/f95c28be-f3d9-459b-b204-b00bd31c981f
```

> Successful request returns a [Recipe object](#recipe):

```json
{
    "id": "f95c28be-f3d9-459b-b204-b00bd31c981f",
    "name": "get first gist with condition",
    "sequence": [
        {
            "operation": "listGists",
            "parameters": {
                "user": ".username"
            }
        },
        {
            "condition": "._results[\"0\"] | length > 1",
            "operation": "getGist",
            "parameters": {
                "gistId": "._results[\"0\"][0].id"
            }
        }
    ],
    "parameters": {
        "username": "freehaha"
    },
    "operations": {
        "listGists": "9446bdf1-da5a-4096-98bc-47cfa8c15157",
        "getGist": "99d64f8e-eace-417d-8cea-511621aaf57c"
    }
}
```

This endpoint retrieves a Recipe.

### HTTP Request

`GET http://johnsd.cse.unsw.edu.au:3000/recipes/{id}`

### Parameters

Parameter | Type | Description
--- | --- | --- | ---
id | uuid | ID of the Recipe

### Response

A [Recipe](#recipe) Object.

### Errors
Possible error codes and reasons:

Code | Reason | Message
--- | --- | ---
400 | provided `id` is not a valid UUID | malformed UUID
404 | Recipe of specified `id` does not exist | resource not found

## Run Recipe

```shell
# Make sure the `Content-Type` is set correctly
curl -XPOST http://johnsd.cse.unsw.edu.au:3000/recipes/f95c28be-f3d9-459b-b204-b00bd31c981f/run
	-H 'Content-Type: application/json'
	-d '{
		"parameters": {
			"username": "freehaha"
		}
	}'
```

> Successful requst returns the result of the recipe:

```json
{
	"username": "freehaha",
	"_results": {
		"0": {
			"...": "results of the first operation"
		},
		"1": {
			"...": "results of the second operation"
		}
	}
}
```

This endpoint executes a Recipe.

### HTTP Request

`POST http://johnsd.cse.unsw.edu.au:3000/recipes/{id}/run`

### Parameters

Parameter | Type | Description
--- | --- | ---
parameters | object | Parameter to be passed to the recipe, overwriting any default parameters and will be used as the context



### Response

The resulting context of the Recipe. The `_result` object contains result of
each opeartion with `._result["0"]` being the result of the first operation,
`._result["1"]` being the second and so on.

### Errors

Code | Reason | Message
--- | --- | ---
404 | Recipe with the specified ID does not exist | recipe not found
400 | Error running recipe | message related to the operation failing
