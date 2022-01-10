# Middlewares

###### Example:
```php
<?php

declare(strict_types=1);

namespace App\Http\Middleware;

use Nyxio\Contract\Http\HttpStatus;
use Nyxio\Contract\Http\MiddlewareInterface;
use Nyxio\Http\Exception\HttpException;
use Nyxio\Http\Request;
use Nyxio\Http\Response;
use Psr\Http\Message\ResponseInterface;

class Auth implements MiddlewareInterface
{
    public function handle(Request $request, Response $response, \Closure $next): ResponseInterface
    {
        if ($request->header('Auth') !== 'super-secret-token') {
            throw new HttpException(HttpStatus::Unauthorized, 'Unauthorized');
        }

        return $next($request, $response);
    }
}
```
