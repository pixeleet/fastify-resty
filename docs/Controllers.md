# Controllers

**Controllers** are decorated classes designed to handle incoming requests 
to its routes. See the [Bootstrapping](./Bootstrapping.md) section to get 
more information about their register in fastify application.

## Controller creation

Firstly, we need to create our controller class and decorate it with the 
`@Controller` decorator which is provided by **Fastify Resty** core.

```ts
import { Controller } from '@fastify-resty/core';

@Controller('/route')
export default class MyController {}
```

> **Note!** Controller class has to be default exported to be picked-up by 
the autoloading mechanism.

## Controller decorator configuration

The only optional property for the `Controller` decorator is the route URL, 
which will be the root path of our controller's endpoints. If not set, path 
`/` will be used for a controller router.

Handles `/articles` path:

```ts
@Controller('/articles')
```

Handles app root path:

```ts
@Controller('/')
// or
@Controller() // root '/' route is default
```

## Controller's requests methods

Controller is able to handle different HTTP requests methods with different routes. 
For that, we need to declare a controller class method and decorate it with HTTP method decorator.

Method handler receives [Request](https://www.fastify.io/docs/latest/Request/) and 
[Reply](https://www.fastify.io/docs/latest/Reply/) objects and works the same as **Fastify** route handler.

```ts
import { Controller, GET, POST } from '@fastify-resty/core';
import type { FastifyRequest, FastifyResponse } from 'fastify';

@Controller('/route')
export default class MyController {

  @POST('/')
  create(request: FastifyRequest, reply: FastifyReply) {
    // ...
  }

  @GET('/')
  async get(request: FastifyRequest) {
    // ...
    return [];
  }

}
```

Here the list of available HTTP methods decorators:

| Decorator | Arguments |
| --- | --- |
| **@GET** | `route`, `options?` |
| **@HEAD** | `route`, `options?` |
| **@PATCH** | `route`, `options?` |
| **@POST** | `route`, `options?` |
| **@PUT** | `route`, `options?` |
| **@OPTIONS** | `route`, `options?` |
| **@DELETE** | `route`, `options?` |
| **@ALL** | `route`, `methods?`, `options?` |

Each request method decorator has one required string `route` option which 
defines a route path.

Another not mandatory parameter is `options` which allow all the **Fastify** 
route options, except the `url` and `method`. See them on [Fastify Routes Option](https://www.fastify.io/docs/latest/Routes/#routes-option) documentation.

`@ALL` is a bit different than others because it handles all the HTTP methods, 
or just some of them if `methods` strings array was passed.

## Controller's hooks methods

Following [Fastify Hooks](https://www.fastify.io/docs/latest/Hooks/) functionality 
you are able to implement them into your controller using a related hook decorator 
from **Fastify Resty** core.

The declared hook will be applied to all the controller's routes.

```ts
import { Controller, OnRequest } from '@fastify-resty/core';
import type { FastifyRequest } from 'fastify';

@Controller('/route')
export default class MyController {

  @OnRequest
  async onRequest(request: FastifyRequest) {
    // ...
  }

}
```

The list of hooks decorators:

- **@onRequest()**
- **@preParsing()**
- **@preValidation()**
- **@preHandler()**
- **@preSerialization()**
- **@onError()**
- **@onSend()**
- **@onResponse()**
- **@onTimeout()**

## Controller's properties

There might be some cases when you need to have an access to `fastify` instance inside 
the controller's methods. For that, you need to inject it into the controller using `@Inject` 
decorator with `FastifyToken` token. The same rule works for global application configuration
that available with `GlobalConfig` token.

```ts
import { Controller, Inject, FastifyToken, GlobalConfig, IApplicationConfig } from '@fastify-resty/core';
import type { FastifyInstance } from 'fastify';

@Controller('/route')
export class MyController {

  @Inject(FastifyToken)
  instance: FastifyInstance;

  @Inject(GlobalConfig)
  globalConfig: IApplicationConfig;

}
```
