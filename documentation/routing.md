# Routing


###### Example:
```php
<?php

use Nyxio\Contract\Http\Method;
use Nyxio\Routing\Attribute\Route;
use Nyxio\Routing\Attribute\RouteGroup;

#[Route(
    method: Method.GET,
    uri: '/user/@userId/order/@orderHash',
    rules: [
        'userId' => 'integer',
        'orderHash' => ['string', 'app.orderHashValidation' => ['my-param' => true],
    ]
)]
#[RouteGroup('api')]
#[RouteGroup('users')]
class MyAction 
{
    // ...
}
```
