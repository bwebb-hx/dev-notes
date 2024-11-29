# Using APIs in ActionScript

This page will describe how you can use APIs - both Hexabase and external - in ActionScript.

## Calling Hexabase APIs

There are a few general types of Hexabase APIs you can call from ActionScript:

- The General API, as shown in the developer guide
- Functions; the Application-scope Functions that are not tied to specific items.
- Item Actions (i.e. other actionscripts for the same or other items, actions for changing an item's status, etc)

All of these can be called a few different ways, but the way I commonly do so is with `callAPIAsync`.

### Calling a Function

The below calls a Function called `SomeFunction` for the given application/project (`p_id`).

```javascript
const url = `api/v0/applications/${data.p_id}/functions/SomeFunction`; // calls "SomeFunction" - an Application-scoped Function.
const result = await callAPIAsync("POST", url, {}); // passes an empty object {} as params.
logger.log("result:", result.data); // if the Function returns anything, it will appear in the `data` property
```

### Calling an Item Action

The below calls an Action called `ItemAction` for the given item (`i_id`), datastore (`d_id`), etc.

```javascript
const url = `api/v0/applications/${data.p_id}/datastores/${data.d_id}/items/action/${data.i_id}/ItemAction`;
// same as the other example
```

Note that when calling these APIs, you don't specify a domain. This is because Hexabase handles that for you - so you only need to specify the URI of the API or function.

### Passing Params

In the above snippet, we call a Function. We pass it an empty object to pass to the Function, but if that Function requires extra data, you can
pass any data you like here.

If we call the Function while passing an object like this:

```javascript
const result = await callAPIAsync("POST", url, { someData: "123" });
```

Then in `SomeFunction`, we can access these parameters like this:

```javascript
async function main(data) {
  logger.log(data.params.someData);
}
```

Data passed to another ActionScript function will be accessible under `data.params`.
