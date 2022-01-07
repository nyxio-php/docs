# Providers

> Provider allows you to make presets when initializing the application

<br>

###### Example

```php
<?php 

use Nyx\Container\ContainerInterface;
use Nyx\Provider\ProviderInterface;

class MyServiceProvider implements ProviderInterface {

    public function __construct(private readonly ContainerInterface $container) {
    
    }
    
    public function process(): void
    {
        $this->container
            ->singleton('service-logger', MyLogger::class)
            ->addArgument('channel', 'service-logger');
            
        $this->container
            ->singleton(MyService::class)
            ->addArgument('logger', $this->container->get('service-logger'));
    }
}
```
