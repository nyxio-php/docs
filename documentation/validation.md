# Validation

## Default rules
- `string`
- `integer`
- `numeric`
- `float`
- `bool`
- `array`
- `email`
- `max-len` with param `max`
- `min-len` with param `min`

Also you can use [https://github.com/nyxio-php/nyxio/blob/main/src/Contract/Validation/Rule.php](`Nyxio\Contract\Validation\Rule`) enum.
Example:
- `Nyxio\Contract\Validation\Rule::Integer`
- `Nyxio\Contract\Validation\Rule::String`
- ...


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
        $this->validator->attribute('firstName')->rule(Rule::String)->rule(Rule::MinLength, ['min' => 3])->notAllowsEmpty('Empty firstname!')->notNullable();
        $this->validator->attribute('lastName')->rule(Rule::String)->notAllowsEmpty('Empty firstname!')->notNullable();
        $this->validator->attribute('age')->rule(Rule::Integer)->nullable()->required();
        $this->validator->attribute('contacts.email')->rule(Rule::Email)->notNullable()->notAllowsEmpty('Empty email!');

        $this->validator->validateOrException($request->post()); // or  $this->validator->getErrors($request->post());
        
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
        $this->validator->attribute('user_id')->rule('integer')->rule('app.my-custom-rule');
        
        $this->validator->validateOrException($request->post());
        
        return $next();
    }
}

```

<br>

## More customization

###### With custom params and formatted message
```php
#[Rule('max-len', 'Attribute length larger :max')]
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
$this->validator->attribute('my-attribute')->rule('max-len', ['max' => 5]);
```


###### With access to all data:
```php
#[Rule('custom', 'Attribute :attribute is empty')]
public function myEmptyRule(mixed $value, string $attribute, array $source): bool
{
    return \array_key_exists($attribute, $source);
}
```
