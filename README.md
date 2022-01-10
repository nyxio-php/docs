# nyxio documentation

#### Requirements

- PHP **8.1** or *higher*
- [composer](https://getcomposer.org/)
- [ext-mbstring](https://www.php.net/manual/en/book.mbstring.php)

<br>

#### Current restrictions
- By default you cannot get files from request. You can extend `Nyxio\Http\Request` and `Nyxio\Http\Factory\RequestFactory` (extend or implement PSR-17 `ServerRequestFacotryInterface`) and implement your functionality. See [Providers](documentation/providers.md) for register your implementation of `Nyxio\Http\Factory\RequestFactory` in container)

<br>

[Getting started](documentation/getting-started.md) – install and configure

## Http

[Actions](documentation/actions.md) – "Controllers"
<br>[Request](documentation/request.md) – HTTP request (decorator for PSR-7)
<br>[Response](documentation/response.md) – HTTP response (decorator for PSR-7)
<br>[Routing](documentation/routing.md) – Simple and fast. Uses [PHP 8.1 Attributes](https://www.php.net/manual/en/language.attributes.overview.php)
<br> [Middlewares](documentation/middlewares.md) – Uses [PHP 8.1 Attributes](https://www.php.net/manual/en/language.attributes.overview.php)
<br> [Validation](documentation/validation.md) – For request data and url

<br>

## Kernel

[Container](documentation/container.md) – Dependency Injection
<br>[Providers](documentation/providers.md) – Bootstrap application, setup container singletons and binds, and other things
