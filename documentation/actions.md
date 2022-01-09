# Actions

>**Actions** its almost like **Controllers** in *MVC*, but with one important difference:
><br>One **Action** handle only one **URL**.

### Implementation rules
- **Action** MUST HAVE `handle` method with 2 arguments: `$request` and `$response`.

- `handle` method MUST return `Psr\Http\Message\ResponseInterface`.
- If you want to throw an exception, you SHOULD use `Nyx\Http\Exception\HttpException`.

- `$request` MUST BE typed by `Nyx\Http\Request` OR inheritor class.

- `$response` MUST BE typed by `Nyx\Http\Response` OR inheritor class.
- **Action** is a singleton, DO NOT use state properties. 

<br>

Example simple Action with [Routing](routing.md), [Validation](validation.md) and [Middlewares](middlewares.md):
```php
<?php

use Nyxio\Http;
use Nyxio\Routing\Attribute\Middleware;
use Nyxio\Routing\Attribute\Route;
use Nyxio\Routing\Attribute\RouteGroup;
use Nyxio\Validation\Attribute\Validation;
use Psr\Http\Message\ResponseInterface;

#[Route(
    method: Http\Method::POST,
    uri: '/user',
)]
#[Middleware(AdminAuthMiddleware::class)]
#[Middleware(AnotherMiddleware::class)]
#[Validation(CreateUserValidation::class)]
#[RouteGroup('api')]
class CreateUser {

    public function __construct(private readonly CreateUserService $createUser) {
    
    }
    
    public function handle(Http\Request $request, Http\Response $response): ResponseInterface
    {
        $user = $this->createUser($request->post());
        
        return $response->json(['user' => $user], 201);
    }
}
```
