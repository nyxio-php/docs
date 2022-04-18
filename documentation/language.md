# Language

<br>All language configuration files are in `config/lang` path.

#### Example file:

```php
<?php

return [
    'validation' => [
        'required' => 'Field is required'
    ],
    'account' => [
        'created' => 'Account :name is created',
        'createError' => 'Account :name is not created',
    ],
];
```

#### Example use:
```php
<?php

declare(strict_types=1);

use Nyxio\Contract\Kernel\Text\MessageInterface;

class AccountService 
{
    public function __construct(private readonly MessageInterface $message) 
    {
    }
    
    public function create(array $name): string
    {
        $account = new Account($name);
        
        if (!$account->create()) {
            return $this->message->text('account.createError', ['name' => $name]);
        }
        
        return $this->message->text('account.created', ['name' => $name]);
    }
}
```
