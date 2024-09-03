# Authentication-related Errors

## `login()`

9/3/2024

> ```Uncaught (in promise) Error: Http Fetch Exception: {"response":{"errors":[{"message":"Http Fetch Exception","locations":[{"line":3,"column":5}],"path":["login"],"extensions":{"code":"INTERNAL_SERVER_ERROR","exception":{"response":{},"status":500,"message":"Http Fetch Exception","name":"HttpFetchException","stacktrace":["HttpFetchException: Http Fetch Exception","    at AuthService.login (/app/dist/auth/services/auth.service.js:33:19)","    at runMicrotasks (<anonymous>)","    at processTicksAndRejections (node:internal/process/task_queues:96:5)","    at async AuthResolver.login (/app/dist/auth/auth.resolver.js:34:16)","    at async target (/app/node_modules/@nestjs/core/helpers/external-context-creator.js:77:28)","    at async /app/node_modules/@nestjs/core/helpers/external-proxy.js:9:24"]}}}],"data":null,"status":200,"headers":{"map":{"content-length":"755","content-type":"application/json; charset=utf-8"}}},"request":{"query":"\n  mutation Login($loginInput: LoginInput!) {\n    login(loginInput: $loginInput) {\n      token\n    }\n  }\n","variables":{"loginInput":...```

Steps to reproduce:
1. use `login` API with hexabase-js sdk with invalid credentials
2. upon calling the `login` API, instead of returning with a false boolean like I expect, an error occurs.

No error occurs if I use valid credentials. Is it supposed to throw an error if credentials are bad?  or just return false?

The type description of the function makes me assume it'll handle an authentication failure just by returning false:

```typescript
HexabaseClient.login({ email, password, token }: LoginParams): Promise<boolean>
```

Workaround:

```typescript
try {
    const loginSuccess = await client.login({ email, password });
    if (!loginSuccess) {
        return { success: false, err: "Login failed. Please confirm your credentials and try again." };
    }
} catch (err) {
    console.error(err);
    return { success: false, err: "Login failed. Please confirm your credentials and try again." };
}
```

Wrapping the API in a try/catch block handles the error appropriately.

> Q: When will an error not occur, but `login` returns false? 
