# Getting started

## Installation

1. Open terminal, create project and start server:

```shell
composer create-project nyx/app example-app
```

2. Open your directory:

```shell
cd example-app
```

3. Run [workerman](https://github.com/walkor/workerman) server:

```shell
php server.php start
```

4. Open http://localhost:8080/

<br>
That's all you need to create and run the application.

## Only framework
You can also use framework without creation [nyx/app](https://github.com/nyx-php/app), and use only [nyx/framework](https://github.com/nyx-php/framework)


1. Install composer package `composer require nyx/framework`
2. Create class **Hello**:

###### Hello.php
```php
<?php

declare(strict_types=1);

namespace YourAppNamespace;

use Nyx\Http;
use Nyx\Routing\Attribute\Route;
use Psr\Http\Message\ResponseInterface;

#[Route(
    method: Http\Method::GET,
    uri: '/',
)]
class Hello
{
    public function handle(Http\Request $request, Http\Response $response): ResponseInterface
    {
        return $response->json(
            [
                'hello' => 'world'
            ]
        );
    }
}

```

<br>

3. Create `server.php` at a root directory of a project:

###### server.php
```php
<?php

require_once __DIR__ . '/vendor/autoload.php';

$worker = new \Workerman\Worker('http://0.0.0.0:8080');

$worker->onWorkerStart = static function (\Workerman\Worker $worker) {
    $application = (new Nyx\Kernel\Kernel())->bootstrap(
        actions: [
            YourAppNamespace\Hello::class
        ],
        providers: [
            Nyx\Kernel\Provider\KernelProvider::class,
            Nyx\Kernel\Provider\WorkermanProvider::class,
            Nyx\Kernel\Provider\ValidationProvider::class,
        ]
    );

    $worker->onMessage = $application->message();
};
```

<br>

4. Run `php server.php start` in terminal.
5. Open http://localhost:8080/


<br>

### Configuration *(optional)*

> Application can be launched without configuration

Create `app.php`, `http.php` and `worker.php` files inside your configuration path.

######  app.php
```php
<?php

use Nyx\Kernel;

return [
    'env' => 'local', // your application env
    'debug' => false, // debug mode
    'timezone' => 'UTC', // application timezone

    // List of providers
    'providers' => [
        Kernel\Provider\KernelProvider::class,
        Kernel\Provider\WorkermanProvider::class,
        Kernel\Provider\ValidationProvider::class,
    ]
];
```

###### http.php
```php
<?php

return [
    // List of Action's
    'actions' => [
    ],
];
```

###### worker.php
```php
<?php

return [
    'host' => '0.0.0.0',
    'port' => 8080,
    'workers' => 4,
];

```

<br>

Declare the path to your configuration files:

  ```php
$config = new Config(rootDirectory: '/root/directory', configDirectory: '/your/config/directory');
$config->preloadConfigs(['app', 'http', 'worker']);
  ```

<br>

Inject config:
```php
new Kernel(config: $config)
```
