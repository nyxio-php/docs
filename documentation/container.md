# Dependency Injection (Container)

> Container based on [PHP Reflection](https://www.php.net/manual/en/book.reflection.php) to simplify create instances and resolve dependencies.

## Usage

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

Also, you can use ` Nyx\Container\ContainerInterface ` in your class constructor method, for access to application
singletons, binds, create instances and getting other methods dependencies.

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

<br>

And of course, you can resolve dependency on methods.

###### Example:
```php
<?php 

$container = new Container();

class User {
}

class MyClass {
    public function myMethod(User $user, bool $sendEmail) {
        // user create
    }
}

$classReflection = new \ReflectionClass(MyClass::class);
$method = $classReflection->getMethod('myMethod');

$arguments = $container->getMethodArguments($method, ['sendEmail' => true])
$method->invokeArgs($container->get(MyClass::class), $arguments);
//
```
