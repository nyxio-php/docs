# Events

> Listen, dispatch, enjoy. Very simple.

Events are powerful tools for handling application (and framework!) events. 
<br>User create an acount? Create Even, attach listeners for notifications, logs and other things and dispatch ehen it happend.
<br>User make an order? Do the same. 
<br>And many more cases for using `Events` in your app.

###### Exampme of use:

1. Create `Event` class:

```php
<?php

declare(strict_types=1);

use Nyxio\Event\Event;


class UserCreatedEvent extends Event
{
    public const NAME = 'app.user.created';
    
    public function __construct(public readonly User $user)
    {
    }
}

```

2. Attach listeners, who will handle this `Event`

```php
<?php

declare(strict_types=1);

namespace App\Provider;

use Nyxio\Contract\Event\EventDispatcherInterface;
use Nyxio\Contract\Provider\ProviderInterface;

class AppProvider implements ProviderInterface
{
    public function __construct(private readonly EventDispatcherInterface $eventDispatcher)
    {
    }
    
    public function handle(): void
    {
        $this->eventDispatcher->addListener(UserCreatedEvent::NAME, function (UserCreatedEvent $event) {
            // send notification
            // save logs
        });
    }
}

```

3. Trigger your `Event` using the `EventDispetcherInterface`

```php
<?php

declare(strict_types=1);

use Nyxio\Contract\Event\EventDispatcherInterface;

class UserCreateService
{
    public function __construct(private readonly EventDispatcherInterface $eventDispatcher)
    {
    }
    
    public function create(string $name, string $email, string $password): User
    {
        $user = new User($name, $email, $password);
        $user->create();
        
        $this->eventDispatcher->dispatch(UserCreatedEvent::NAME, new UserCreatedEvent($user));
        
        return $user;
    }
}


```

### Event
> All Events clasees must extended the [Event](https://github.com/nyxio-php/event/blob/main/Event.php) class and change value of public constant `NAME` to the name of your event.

<br>In class constructor, or via getters/setters and properties, bind your Event data.
<br>Parent `Event` class have method `stopPropagation`, which can be called in listeners and stop further dispatching this event.

### Listener
### Dispatcher
