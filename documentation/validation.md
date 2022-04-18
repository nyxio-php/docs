# Validation

## Default rules
- `max-len` with param `max`
- `min-len` with param `min`
- `max` with param `max` (only for numeric)
- `min` with param `min` (only for numeric)
- `between` (only for numeric)
- `enum` with params `enum`, `strict` (default `true`)
- `exclude` with params `enum`, `strict` (default `true`)
- `equal` with params `equal`, `strict` (default `true`), `caseSensitive` (default `true`)
- `not-equal` with params `equal`, `strict` (default `true`), `caseSensitive` (default `true`)
- `regex` with param `pattern`
- `date-time` with param `format` (default `Y-m-d\TH:i:sP`)
- `date` with param `format` (default `Y-m-d`)
- `time` with param `format` (default `\TH:i:sP`)

<br>Also you can use [`Nyxio\Contract\Validation\Rule`](https://github.com/nyxio-php/nyxio/blob/main/src/Contract/Validation/Rule.php) enum.

###### Example:
- `Rule::MaxLength`
- `Rule::Enum`
- and others.

## Validation by type
- `isString`
- `isNumeric`
- `isInteger`
- `isFloat`
- `isBool`
- `isEmail`
- `isUrl`
- `isArray`
- `isDateTime` with param `format` (default `Y-m-d\TH:i:sP`)
- `isDate` with param `format` (default `Y-m-d`)
- `isTime` with param `format` (default `\TH:i:sP`)

###### Example:
```php
$validation->field('age')->isInteger();
$validation->field('name')->isString();
$validation->field('weight')
    ->isFloat(message: 'Weight is not float...')
    ->rule(Rule::Between, ['from' => 10, 'to' => 400]);
```

## Custom rules
###### Example:

```php
$validation->field('password')
    ->custom(
        static function (mixed $value): bool {
            return $value !== 'qwerty'; // true - valid, false - invalid
        }, 
        '"qwerty" – it\'s a bad idea for password :c'
    );
```


## Validation
###### Example:
```php
<?php

declare(strict_types=1);

use Nyxio\Contract\Http\MiddlewareInterface;
use Nyxio\Contract\Validation\ValidationInterface;
use Nyxio\Contract\Validation\Rule;
use Nyxio\Http\Request;
use Nyxio\Http\Response;
use Psr\Http\Message\ResponseInterface;

class CreateUserValidation implements MiddlewareInterface
{
    public function __construct(private readonly ValidationInterface $validation)
    {
    }

    public function handle(Request $request, Response $response, \Closure $next): ResponseInterface
    {
        $this->validation->field('firstName')
            ->isString()
            ->rule(Rule::MinLength, ['min' => 3])
            ->notAllowsEmpty(message: 'Empty firstname!')
            ->notNullable();
        $this->validation->field('lastName')
            ->isString()
            ->notAllowsEmpty(message: 'Empty firstname!')
            ->notNullable();
        $this->validation->field('age')->isInteger()->nullable()->required();
        $this->validation->field('contacts.email')
            ->isEmail()
            ->notNullable()
            ->notAllowsEmpty(message: 'Empty email!');

        $this->validation->validateOrException($request->post()); // or $this->validation->getErrors($request->post());
        
        return $next();
    }
}

```
<br>


## Register custom rules for validation
1. Create class with rules:
```php
<?php

declare(strict_types=1);

use Nyxio\Validation\Attribute\Rule;
use Nyxio\Validation\Attribute\RuleGroup;

#[RuleGroup(name: 'app')] // not required attribute
class MyProjectRules
{
    #[Rule('my-custom-rule', 'Attribute is not valid')]
    public function myRule(mixed $value): bool
    {
        return \strlen($value) >= 10;
    }
}
```
2. Register your rules in provider:
```php
<?php

declare(strict_types=1);

use Nyxio\Contract\Provider\ProviderInterface;
use Nyxio\Contract\Validation\RuleExecutorCollectionInterface;

class AppProvider implements ProviderInterface
{
    public function __construct(private readonly RuleExecutorCollectionInterface $ruleExecutorCollection)
    {
    }

    public function process(): void
    {
        $this->ruleExecutorCollection->register(MyProjectRules::class);
    }
}
```
3. Use:

```php
<?php

declare(strict_types=1);

use Nyxio\Contract\Http\MiddlewareInterface;
use Nyxio\Contract\Validation\ValidationInterface;
use Nyxio\Http\Request;
use Nyxio\Http\Response;
use Psr\Http\Message\ResponseInterface;

class CreateUserValidation implements MiddlewareInterface
{
    public function __construct(private readonly ValidationInterface $validation)
    {
    }

    public function handle(Request $request, Response $response, \Closure $next): ResponseInterface
    {
        //...
        $this->validation->field('user_id')->isInteger()->rule('app.my-custom-rule');
        
        $this->validation->validateOrException($request->post());
        
        return $next();
    }
}

```

<br>

## More customization

###### With custom params and formatted message
```php
#[Rule('max-len', 'Field length larger :max')]
public function maxLength(mixed $value, int $max): bool
{
    if ($max <= 0) {
        throw new \InvalidArgumentException('Max cannot be less than or equal to zero');
    }

    return strlen($value) <= $max;
}
```
```php
//...
// Validation middleware
$this->validation->field('my-field')->rule('max-len', ['max' => 5]);
```


###### With access to all data:
```php
#[Rule('custom', 'Field :field is empty')]
public function myEmptyRule(mixed $value, string $field, array $source): bool
{
    return \array_key_exists($field, $source);
}
```
