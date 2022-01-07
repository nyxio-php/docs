# Dependency Injection (Container)

You can require dependencies in your class constructor method:

###### Example:
```php
<?php

class MyService {
    public function __construct(private readonly AnotherService $another) {
    
    }
    
    public function doSomething(): void
    {
        $this->another->doSomethingElse();
    }
}
```
> Make sure you did singleton or bind your dependency to use DI functionality at full power. 
> <br><br>Good practice is to bind and singleton most of your classes in [Providers](providers.md)

<br>
Also, you can use `Nyx\Container\ContainerInterface` in your class constructor method, for access to application singletons, binds, create instances and getting other methods dependencies.

###### Example:
```php
<?php

use Nyx\Container\ContainerInterface;
use Nyx\Provider\ProviderInterface;

class MyProvider implements ProviderInterface{
    public function __construct(private readonly ContainerInterface $container) {
    }
    
    public function process(): void
    {
        $instance = $this->container->get(MyApp\AnotherService::class);
        
        $instance->doSomethingElse('param1', 'param2');
        
        $this->container->singleton(LoggerInterface::class, MyAppLogger::class);
        $this->container->bind(UserDtoInterface::class, MyUserDto::class);
        // and other manipulation
    }   
}
```
