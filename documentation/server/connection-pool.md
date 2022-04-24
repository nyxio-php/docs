# Connection Pool

All jobs (Queue, Scheduled and Await) use `Open Swoole` task worker pool. In [server settings](../configuration.md#serverphp), you can setup the number of task workers (`task_worker_num` option).

<p/>

`Connection Pool` consists of independent connections to your sql database, redis server or to others servers. 
<br>It means that twenty task worker - twenty connections. 

<p/>

The connection is established at the time of application initialization, and can be used **ONLY** in jobs (Queue, Scheduled and Await).


#### Register connection by `ConnectionPoolProviderInterface` via [Provider](../providers.md):
```php
<?php

declare(strict_types=1);

use Nyxio\Contract\Provider\ProviderInterface;
use Nyxio\Contract\Kernel\Server\Job\Pool\ConnectionPoolProviderInterface;

class ConnectionProvider implements ProviderInterface
{
    public function __construct(private readonly ConnectionPoolProviderInterface $connectionPoolProvider)
    {
    }
    
    public function process(): void
    {
        $this->connectionPoolProvider->register('database', function () {
            $pdo = new \PDO('your dsn');
            // pdo settings
            
            return $pdo;
        });
        
        $this->connectionPoolProvider->register('redis', function () {
            return new RedisServer('.....');
        });
    }
}
```


#### Use connection:

```php
<?php

declare(strict_types=1);

use Nyxio\Contract\Kernel\Server\Job\Pool\ConnectionPoolInterface;
use App\Entity\User;

class UserCreateJob
{
    private \PDO $database;
    
    public function __construct(private readonly ConnectionPoolInterface $connectionPool)
    {
        $this->database = $connectionPool->get('database');
    }
    
    public function handle(array $data): User
    {
        return $this->database->insert($data);
    }
}
```

