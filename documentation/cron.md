# Cron

#### How to use
1. Create job:
```php
<?php

declare(strict_types=1);

namespace App\Job;

use Nyxio\Contract\Kernel\Server\CronInterface;
use Nyxio\Kernel\Server\Cron\Attribute\Cron;

#[Cron(expression: '*/1 * * * *')]
class MyCronJob implements CronInterface
{
    public function handle(): void
    {
        echo 'My cron being execute every minutes!' . \PHP_EOL;
    }
}
```
> Argument `expression` in `Cron` attribute powered by [dragonmantank/cron-expression](https://github.com/dragonmantank/cron-expression) package. See package page to explore possible expressions.

2. Add job to [cron.php](configuration.md#cronphp) config.

<br>Affter start the server and cron job has been success registred, terminal output the message about this:

```Cron job App\Job\Cron\MyCronJob is registered | */1 * * * *```


# Events

| Trigger               | Event Name                                    | Event Class                            |
|-----------------------|:----------------------------------------------|:---------------------------------------|
| Cron complete         | kernel.cron.completed                         | `\Nyxio\Kernel\Event\CronJobCompleted` |
| Cron throw exception  | kernel.cron.error                             | `\Nyxio\Kernel\Event\CronJobError`     |

