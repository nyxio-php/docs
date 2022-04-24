# Scheduled tasks

#### How to use
1. Create job:
```php
<?php

declare(strict_types=1);

namespace App\Job;

use Nyxio\Contract\Kernel\Server\Job\ScheduledJobInterface;
use \Nyxio\Kernel\Server\Job\Schedule\Attribute\Schedule;

#[Schedule(expression: '*/1 * * * *')]
class MyCronJob implements ScheduledJobInterface
{
    public function handle(): void
    {
        echo 'My job being execute every minutes!' . \PHP_EOL;
    }
}
```
> Argument `expression` in `Schedule` attribute powered by [dragonmantank/cron-expression](https://github.com/dragonmantank/cron-expression) package. See package page to explore possible expressions.

2. Add job to [schedule.php](configuration.md#schedulephp) config.

<br>Affter start the server and scheduled job has been success registred, terminal output the message about this:

```Scheduled job App\Job\Sheduled\MyJob is registered | */1 * * * *```


# Events

| Trigger                       | Event Name                                    | Event Class                  |
|-------------------------------|:----------------------------------|:-----------------------------------------|
| Schedule job complete         | kernel.job.schedule.complete      | `\Nyxio\Kernel\Event\ScheduleComplete`   |
| Schedule job throw exception  | kernel.job.schedule.exception     | `\Nyxio\Kernel\Event\ScheduleException`  |

