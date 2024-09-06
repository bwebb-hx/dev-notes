# `datastore.items()`

## Request failed with status code 400

> ```Error: Request failed with status code 400: {"response":{"errors":[{"message":"Request failed with status code 400","locations":[{"line":7,"column":5}],"path":["datastoreGetDatastoreItems"],"extensions":{"code":"INTERNAL_SERVER_ERROR","exception":{"config":{"url":"applications/66cd671708aa582e96612373/datastores/66cd7605b20cd4e95904fc10/items/search","method":"post","data":"{\"conditions\":[{\"id\":\"slug\",\"search_value\":\"sample2\",\"exact_match\":true}],\"page\":1,\"per_page\":1,\"use_display_id\":true,\"include_links\":true,\"include_lookups\":true,\"return_number_value\":true,\"format\":\"map\"}","headers":{"Accept":"application/json, text/plain, */*","Content-Type":"application/json;charset=utf-8","Authorization":"Bearer eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MjU4Nzg2MTUsImlhdCI6MTcyNTUzMzAxNSwic3ViIjoiNjZjNWE1NTFmNjFhYTIyM2M5MzVmMDc0IiwidW4iOiJiLndlYmIrZGV2In0.OAyeL32EcVNTgRq32Nf0TpioK8z0jZr2jLlKDOpkhkHJoKmHVgOU1J5FepFcx1mIaIlfIzg53haBvT_UrMtlrVPVa-D5t0CeSVFh-lCXIixBt5r6vXlogsGtlMwYaS1XN17wuc7Xb7BAoBZ9ODbx52I6MPmlY6WMnU_13OJpxnwVKA_HooBi7jDQPR3estyhO2VOodfS1yp-3_2Z1kHlfB7fctcaZq83BfgF0CvzvRA919EK4bKWJgdqRpiXgF1tgN6dNdL5zWfQLrmV9hQH3_xIFqIkxQ9jEn3GWUZLsKBbpoCThqfTgBwYgW_dlphbDJ9gB8FIUh9cBVXM3kIoFg","User-Agent":"axios/0.21.1","Content-Length":204},"baseURL":"http://linker-api:7575/api/v0/","transformRequest":[null],"transformResponse":[null],"timeout":40000,"xsrfCookieName":"XSRF-TOKEN","xsrfHeaderName":"X-XSRF-TOKEN","maxContentLength":-1,"maxBodyLength":-1,"metadata":{"startDate":"2024-09-05T10:52:53.725Z"}}}}}],"data":null,"status":200,"headers":{"map":{"content-length":"1515","content-type":"application/json; charset=utf-8"}}},"request":{"query":"\n  mutation DatastoreGetDatastoreItems(\n    $getItemsParameters: GetItemsParameters!\n    $datastoreId: String!\n    $projectId: String\n  ) {\n    datastoreGetDatastoreItems(\n      getItemsParameters: $getItemsParameters\n      datastoreId: $datastoreId\n      projectId: $projectId\n    ) {\n      items\n      totalItems\n    }\n  }\n","variables":{"getItemsParameters":{"page":1,"per_page":1,"conditions":[{"id":"slug","search_value":"sample2","exact_match":true}],"use_display_id":true,"format":"map","include_links":true,"include_lookups":true,"return_number_value":true},"datastoreId"```

This error doesn't include many details about what actually happened, but apparently it's a bad request. It was caused by the following code:

```typescript
const items = await datastore.items({ 
    page: 1, 
    per_page: 1, 
    conditions: [{ id: 'slug', search_value: stub, exact_match: true }]
});
```

I found the problem by looking at the [API docs](https://apidoc.hexabase.com/en/docs/v0/items-search/ItemList#nest-of-conditions), where it shows an example of how to make a condition:

```json
{
    "conditions": [
        {  
           "conditions": [
                {"id": "FieldA", "search_value": ["X"], "exact_match": true},
                {"id": "FieldB", "search_value": ["Y"]}
           ],
           "use_or_condition": true
        }
    ]
}
```
Notice how `search_value` is always set as an array, not a single value. So, make sure `search_value` is set to a value that is an array, even if it's only a single value you want to check for.

In my case, wrapping `stub` in square brackets fixes the error:

```typescript
const items = await datastore.items({ 
    page: 1, 
    per_page: 1, 
    conditions: [{ id: 'slug', search_value: [stub], exact_match: true }]
});
```

Solution:

Make sure the `search_value` property of a condition is an array, even if it's only one value.

## Error: Unknown data type: wysiwyg

```
Uncaught (in promise) Error: Unknown data type: wysiwyg
    at FieldType.set (index.js:45:15)
    at Field.set (index.js:50:56)
    at eval (HxbAbstract.js:131:18)
    at Array.forEach (<anonymous>)
    at Field.sets (HxbAbstract.js:130:29)
    at Field.fromJson (HxbAbstract.js:118:13)
    at eval (index.js:129:69)
    at Array.map (<anonymous>)
    at Field.eval (index.js:129:53)
    at Generator.next (<anonymous>)
    at fulfilled (index.js:5:58)
```

It seems this happens when a field in a datastore has the data type `wysiwyg` ("what you see is what you get"). Apparently this is a common way to describe rich text editor data types. 

Workarounds:

Changing the field type to something else like `text` fixes it, but that of course changes the field from being a rich text field to a text field, which isn't a great solution.
