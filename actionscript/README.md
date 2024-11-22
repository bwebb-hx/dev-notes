# ActionScript

## Tips and Tricks

ActionScript in its current form (Nov 2024) can be tricky to use, so here are some helpful things I've learned as I've been using it.

### Debugging

#### Runtime Errors

ActionScript logs at the time of writing (Nov 2024) will not give you much information besides what you explicitly log. To manually log things, you can do the following:

`logger.log()` - log something notable, e.g. a message to the user about a specific thing that has occurred.

`logger.info()` - log something ordinary that is just for giving the log reader some contextual information, e.g. "this function was called", etc.

**However**, one problem you may run into with ActionScript logs is that, if a runtime error occurs, nothing is logged, and the logging just abruptly stops.
For example, somewhere in the ActionScript, you are accessing a property of a null or undefined object. You won't see anything in the logs - not even a vague statement that an error occurred.
This is not ideal at all, and one (sort of naive) way to solve this is to just play a game of binary search with `logger` statements until you manage to narrow down approximately where in the code the error is occurring.
I was doing this initially, since that just seemed like my only option, but one simple work around will help you find out what errors are occurring and perhaps even where:

This is how every ActionScript starts:
```javascript
async function main(data) {
    // ...
}
```

What if you simply wrap this function in a `try - catch`? For example, this is something I often add at the beginning of ActionScripts:

```javascript
async function main(data) {
    try {
        await main1(data);
    } catch (err) {
        logger.log("runtime error:", err)
    }
}

async function main1(data) {
    // actual logic goes here
    // ...
}
```

Now, you will always get a log if something crashes during execution. Awesome!
