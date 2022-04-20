# Queues

###### `Job` example:

```php
<?php

declare(strict_types=1);

namespace App\Jobs;

use App\Services\NotificationCenter;
use App\Entity\User;

class SendNotificationJob
{
    public function __construct(private readonly NotificationCenter $notification) 
    {
    }
    
    public function handle(User $user, string $notificationType): void
    {
        $this->notification->notify($user, $notificationType);
    }
}
```

###### Example of use:

```php
<?php

declare(strict_types=1);

namespace App\Jobs;

use App\Entity\User;
use Nyxio\Contract\Queue\QueueInterface;
use App\Jobs\SendNotificationJob;
use Nyxio\Kernel\Server\Queue;

class CreateUserService
{
    public function __construct(private readonly QueueInterface $queue) 
    {
    }
    
    public function create(string $firstName, string $lastName): void
    {
        $user = new User($firstName, $lastName);
        
        if (!$user->save()) {  
            throw new \Exception('Error');
        }
        
         $this->queue->push(
            job: SendNotificationJob::class,
            data: [
                'user' => $user,
                'notificationType' => 'user.created',
            ],
            options: new Queue\Options(
                retryCount: 5, // retry count
                retryDelay: 5000, // retry after 5 sec, if was an exception
                delay: 1000, // perform after 1 sec after push
            ),
        ); 
    }
}
```

###### Example of use:
```php
// Without data and options
$this->queue->push(MyJobName::class);

// With data, without options
$this->queue->push(MyJobName::class, ['my-param' => 'value']);

// All
$this->queue->push(
    job: MyJobName::class, 
    data: ['my-param' => 'value'], 
    new Queue\Options(
        retryCount: 5, // retry count
        retryDelay: 5000, // retry after 5 sec, if was an exception
        delay: 1000, // perform after 1 sec after push
    )
);

$this->queue->push(
    job: MyJobName::class, 
    data: ['my-param' => 'value'], 
    options: new Queue\Options(
        retryCount: 5, // retry count
        retryDelay: 5000, // retry after 5 sec, if was an exception
        delay: 1000, // perform after 1 sec after push
    ),
    finishCallback: static function () {
        echo 'My job is done!' . \PHP_EOL;
    },
);
```
