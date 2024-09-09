# How to do various things

Compiling notes here about how to do various things in SDK. Especially focused on things that are either not fully implemented yet, or haven't been documented so far.

## Files in Hexabase

### Get File Metadata

In the Hexabase management console, you can see information about files using the GetItem API:

```json
"file": {
      "field_id": "file",
      "field_name": "file",
      "dataType": "file",
      "value": [
        {
          "_id": "66d6bd6728c4541c1e7b01df",
          "contentType": "image/png",
          "created_at": "2024-09-03T07:40:23.052Z",
          "d_id": "66cd7605b20cd4e95904fc10",
          "datastore_id": "66cd7605b20cd4e95904fc10",
          "deleted": false,
          "display_order": 0,
          "field_id": "66d6bb0c396e48fa83916137",
          "file_id": "66d6bd6728c4541c1e7b01df",
          "filename": "ai-driven-development-PC.png",
          "filepath": "some/path/...",
          "i_id": "66cd760bb20cd4e95904fc2b",
          "item_id": "66cd760bb20cd4e95904fc2b",
          "mediaLink": "/storage/linkerprodstorage/...",
          "name": "ai-driven-development-PC.png",
          "p_id": "66cd671708aa582e96612373",
          "selfLink": "linkerprodstorage/...",
          "size": 4709534,
          "temporary": false,
}
```

However, if you load the file using the `datastore.items()` function, you actually are missing all the data except for `id`:

```json
{
    "id": "66d6bd6728c4541c1e7b01df"
}
```

So, how do we get all the other data, such as `contentType`, `filename`, `size`, etc?

#### Investigation (wrong answers that I've tried)

I looked at `HexabaseClient.file(id?: string)`, since it looked to me like this function might return me the `FileObject` for the specified `id`. This function is defined here: 

https://github.com/hexabase/hexabase-js/blob/31fa255ed184e0e73d37bfcda8eaa9979fbfeb1e/src/HexabaseClient.ts#L207-L210

However, upon closer look, it seems that all this does is set the `id` property of the `FileObject` and return it. So, not really what we want.

#### Solution

I learned that if you use `datastore.items()` (**items**, plural), it will load all items, but it won't load the fine details of fields that are `file` data type. All it loads in this case seems to be just the `id` property of the file.
If you instead use `datastore.item(item_id)`, it will load all the fields, including the rest of the file data, such as `contentType`, `filename`, etc.


