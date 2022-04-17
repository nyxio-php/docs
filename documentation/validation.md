# Validation

## Default rules
- `max-len` with param `max`
- `min-len` with param `min`
- `max` with param `max` (only for numeric)
- `min` with param `min` (only for numeric)
- `between` (only for numeric)
- `enum`
- `exclude`
- `equal`
- `not-equal`

<br>Also you can use [`Nyxio\Contract\Validation\Rule`](https://github.com/nyxio-php/nyxio/blob/main/src/Contract/Validation/Rule.php) enum.
Example:
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

###### Example:
```php
$validator->field('age')->isInteger();
$validator->field('name')->isString();
$validator->field('weight')
    ->isFloat(message: 'Weight is not float...')
    ->rule(Rule::Between, ['from' => 10, 'to' => 400]);
```



## Validator
###### Example:
```php
<?php

declare(strict_types=1);

use Nyxio\Contract\Http\MiddlewareInterface;
use Nyxio\Contract\Validation\Handler\ValidatorCollectionInterface;
use Nyxio\Http\Request;
use Nyxio\Http\Response;
use Psr\Http\Message\ResponseInterface;
use Nyxio\Contract\Validation\Rule;

class CreateUserValidation implements MiddlewareInterface
{
    public function __construct(private readonly ValidatorCollectionInterface $validator)
    {
    }

    public function handle(Request $request, Response $response, \Closure $next): ResponseInterface
    {
        $this->validator->field('firstName')
            ->isString()
            ->rule(Rule::MinLength, ['min' => 3])
            ->notAllowsEmpty(message: 'Empty firstname!')
            ->notNullable();
        $this->validator->field('lastName')
            ->isString()
            ->notAllowsEmpty(message: 'Empty firstname!')
            ->notNullable();
        $this->validator->field('age')->isInteger()->nullable()->required();
        $this->validator->field('contacts.email')
            ->isEmail()
            ->notNullable()
            ->notAllowsEmpty(message: 'Empty email!');

        $this->validator->validateOrException($request->post()); // or $this->validator->getErrors($request->post());
        
        return $next();
    }
}

```
<br>


## Register custom rules for validator
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
use Nyxio\Contract\Validation\Handler\ValidatorCollectionInterface;
use Nyxio\Http\Request;
use Nyxio\Http\Response;
use Psr\Http\Message\ResponseInterface;

class CreateUserValidation implements MiddlewareInterface
{
    public function __construct(private readonly ValidatorCollectionInterface $validator)
    {
    }

    public function handle(Request $request, Response $response, \Closure $next): ResponseInterface
    {
        //...
        $this->validator->field('user_id')->isInteger()->rule('app.my-custom-rule');
        
        $this->validator->validateOrException($request->post());
        
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
$this->validator->field('my-field')->rule('max-len', ['max' => 5]);
```


###### With access to all data:
```php
#[Rule('custom', 'Field :field is empty')]
public function myEmptyRule(mixed $value, string $field, array $source): bool
{
    return \array_key_exists($field, $source);
}
```
