# Await Task

> Awaitt tasks – it's synchronous tasks. Tasks can throw exceptions, return data and use [Connection Pool](connection-pool.md).


###### Exampme of use:

1. Create a task:
```php
<?php

declare(strict_types=1);

namespace App\Job\Task;

use App\Entity\User;

class GetUser
{
    public function handle(int $userId): User
    {
      // get data from database
      
      return $user;
    }
}
```
2. Use in your app:

```php
<?php

declare(strict_types=1);

namespace App\Job\Service;

use Nyxio\Contract\Kernel\Server\Job\Await\AwaitTaskInterface;
use Nyxio\Kernel\Server\Job\Await\Options;
use App\Entity\User;
use App\Job\Task\GetUser;
use App\Job\Task\UpdateUser;

class GetUserService
{
    public function __construct(private readonly AwaitTaskInterface $awaitTask)
    {
    }
    
    public function getUser(int $userId): User
    {
        $user = $this->awaitTask->run(
            job: GetUser::class, 
            data: ['userId' => $userId], 
            options: new Options(timeout: 2)
        );
        
        if (!$user instanceof User) {
            throw new \Exception('user not found');
        }
        
        return $user;
    }
}
```
