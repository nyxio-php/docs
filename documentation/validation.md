# Validation

###### Example:
```php
<?php

declare(strict_types=1);

use Nyxio\Contract\Http\MiddlewareInterface;
use Nyxio\Contract\Validation\Handler\ValidatorCollectionInterface;
use Nyxio\Http\Request;
use Nyxio\Http\Response;
use Psr\Http\Message\ResponseInterface;

class CreateUserValidation implements MiddlewareInterface
{
    public function __construct(private readonly ValidatorCollectionInterface $validator)
    {
    }

    public function handle(Request $request, Response $response, \Closure $next): ResponseInterface
    {
        $this->validator->attribute('firstName')->rule('string')->notAllowsEmpty('Empty firstname!')->notNullable();
        $this->validator->attribute('lastName')->rule('string')->notAllowsEmpty('Empty firstname!')->notNullable();
        $this->validator->attribute('age')->rule('integer')->nullable();
        $this->validator->attribute('email')->rule('email')->notNullable()->notAllowsEmpty('Empty email!');

        return $next();
    }
}

```
