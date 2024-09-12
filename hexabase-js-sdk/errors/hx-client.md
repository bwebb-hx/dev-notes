# HexabaseClient.download(fileId)

```
TypeError: Cannot read properties of undefined (reading 'data')

TypeError: Failed to execute 'fetch' on 'Window': Request with GET/HEAD method cannot have body.
    at FileObject.eval (HxbAbstract.js:84:49)
    at Generator.next (<anonymous>)
    at eval (HxbAbstract.js:9:71)
    at new Promise (<anonymous>)
    at __awaiter (HxbAbstract.js:5:12)
    at FileObject.exec (HxbAbstract.js:68:16)
    at FileObject.eval (HxbAbstract.js:53:40)
    at Generator.next (<anonymous>)
    at eval (HxbAbstract.js:9:71)
    at new Promise (<anonymous>)
    at __awaiter (HxbAbstract.js:5:12)
    at FileObject.rest (HxbAbstract.js:49:16)
    at FileObject.eval (index.js:131:36)
    at Generator.next (<anonymous>)
    at eval (index.js:8:71)
    at new Promise (<anonymous>)
    at __awaiter (index.js:4:12)
    at FileObject.download (index.js:128:16)
    at FileObject.eval (index.js:123:24)
    at Generator.next (<anonymous>)
    at eval (index.js:8:71)
    at new Promise (<anonymous>)
    at __awaiter (index.js:4:12)
    at FileObject.download (index.js:121:16)
    at HexabaseClient.download (HexabaseClient.js:192:37)
```

I got this error from the following code:

```typescript
export async function downloadFile(fileID: string): Promise<void> {
    const token = Cookies.get("tokenHxb")!;
    const client = await getHexabaseClient(token);
    if (!client) {
        console.error("download failed: failed to get client");
        return;
    }

    // the error occurs inside client.download
    const file = await client.download(fileID);

    //...
}
```

The first thing I can tell from the callstack is it seems to be happening in the API call. More specifically, this line:

https://github.com/hexabase/hexabase-js/blob/31fa255ed184e0e73d37bfcda8eaa9979fbfeb1e/src/HxbAbstract.ts#L70

When debugging and stepping through the (transpiled javascript) code of `FileObject.download()`, I see that the data in the Rest API is the following:

```
body = '{}'
headers = {Authorization: 'Bearer eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVC…DuCCjjEA96j1Eb2Ze1FhrF_IHrcmaqJXw9Mxs0ySw', Content-Type: 'application/json'}
method = 'get'
```

In the source code for `FileObject.download()`, it looks like the method set is `"get"`, and there is an empty object provided as the body. Maybe this is the cause of the error we are getting?

```typescript
async download(): Promise<Blob> {
    if (this.data) return this.data;
    const res = await this.rest('get', `/api/v0/files/${this.id}`, {}, {}, {response: 'blob'});
    this.set('data', res as Blob);
    return res;
}
```
