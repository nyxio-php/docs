# nyxio documentation

## nyxio
<br>nyxio – microframework for development microservices. Using [Open Swoole](https://openswoole.com/) for async server.

#### Requirements

- PHP **8.1** or *higher*
- [composer](https://getcomposer.org/)
- [ext-openswoole](https://openswoole.com/)
- [ext-mbstring](https://www.php.net/manual/en/book.mbstring.php)

<br>

#### Current restrictions
- You cannot get files from request, and send files to response. 

<br>

[Getting started](documentation/getting-started.md) – first step to up your application
<br>[Configuration](documentation/configuration.md) – install and configure

<br>

## Http

[Actions](documentation/actions.md) – "Controllers"
<br>[Routing](documentation/routing.md) – Simple and fast. Uses [PHP 8.1 Attributes](https://www.php.net/manual/en/language.attributes.overview.php)
<br> [Middlewares](documentation/middlewares.md) – Uses [PHP 8.1 Attributes](https://www.php.net/manual/en/language.attributes.overview.php)
<br> [Validation](documentation/validation.md) – For request data and url

<br>

## Components

[Container](documentation/container.md) – Dependency Injection
<br>[Providers](documentation/providers.md) – Bootstrap application, setup container singletons and binds, and other things
<br>[Translate](documentation/language.md) – Application messages
<br>[Events](documentation/events.md) – Framework and application events

<br>

## Jobs

[Connection Pool](documentation/server/connection-pool.md) – Connection Pool

#### Async
[Queue](documentation/server/queue-jobs.md) – Queue jobs
<br>[Scheduled](documentation/server/scheduled-jobs.md) – Scheduled jobs

#### Await
[AwaitTask](documentation/server/await-tasks.md) – Await tasks
