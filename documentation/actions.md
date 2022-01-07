# Actions

>**Actions** its almost like **Controllers** in *MVC*, but with one important difference:
><br>One **Action** handle only one URL.

<br>

Example simple Action with [Routing](routing.md), [Validation](validation.md) and [Middlewares](middlewares.md):
```php
<?php

use Nyx\Http;
use Nyx\Routing\Attribute\Middleware;
use Nyx\Routing\Attribute\Route;
use Nyx\Validation\Attribute\Validation;
use Psr\Http\Message\ResponseInterface;

#[Route(
    method: Http\Method::POST,
    uri: '/user',
)]
#[Middleware(AdminAuthMiddleware::class)]
#[Middleware(AnotherMiddleware::class)]
#[Validation(CreateUserValidation::class)]
class CreateUser {

    public function __construct(private readonly CreateUserService $createUser) {
    
    }
    
    public function handle(Request $request, Response $response): ResponseInterface
    {
        $user = $this->createUser($request->post());
        
        return $response->json(['user' => $user], 201);
    }
}
```
