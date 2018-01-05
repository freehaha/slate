# ResourceTypes

Below are a list of resource types available when creating a Resource:

## YoutubeVideo

```json
{
	"title": "Github Tutorial For Beginners - Github Basics for Mac or Windows & Source Control Basics",
	"link" : "https://youtu.be/0fKg7e37bQE" 
}
```

References to Youtube videos.

Attribute | Type | Description
--- | --- | ---
\*title | string | Title of the video
\*link | string | URL to the Youtube video

## InvokeExample

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

Attribute | Type | Description
--- | --- | ---
\*title | string | Title of the invoke example
\*method | string | ID of the Method
\*parameters | object | Example parameters

## URL

```json
{
	"title": "Using YQL and Open Data Tables",
	"url": "https://developer.yahoo.com/yql/guide/yql-users-guide.html"
}
```

External URL links.

Attribute | Type | Description
--- | --- | ---
\*title | string | Title of the URL
\*url | string | URL of the webpage
