# Magic Make

This package automatically creates and registers Artisan commands for all of your custom stub files in a Laravel project.

It searches through the `stubs/` directory in a Laravel project to find custom stub files and parses those files for placeholders, then provides an Artisan command for generating said files.

## Installation

You can install this package using Composer:

```sh
composer require ryangjchandler/laravel-magic-make
```

## Usage

Start by creating a custom stub file inside of `stubs` – let's use `action.stub` as an example.

```php
<?php

namespace {{ namespace }};

class {{ class }}
{
    /**
     * Execute the action.
     */
    public function handle(): void
    {
        //
    }
}
```

Magic Make will recognise that this file isn't a standard Laravel stub and create a special `make` command for it.

The `namespace` and `class` placeholders are considered "special" by the command, since these are commonly used by Laravel's own stubs.

* `namespace` will determine where the file is placed after being generated, e.g. `App\Actions` will place the file in `app/Actions`.
* `class` will determine the name of the generated file and whether or not the namespace needs to be modified to accomodate a nested class name, e.g. `SendInvite` will place the file in `app/Actions/SendInvite.php`, `Users/SendInvite` will place the file in `app/Actions/Users/SendInvite.php`.

### Default placeholder values

Some placeholders should have default values - `namespace` for example might default to `App\Actions` and be modified based on the class name the user enters. There are two ways to achieve this with Magic Make.

You can provide a default value, or base value, as part of the stub itself by assigning a value to the placeholder.

```php
<?php

namespace {{ namespace = App\Actions }};

class {{ class }}
{
    /**
     * Execute the action.
     */
    public function handle(): void
    {
        //
    }
}
```

Alternatively, use the `MagicMake` facade inside of a service provider's `boot` method to assign a default value in the desired stub file.

```php
use RyanChandler\MagicMake\Facades\MagicMake;

public function boot(): void
{
    MagicMake::stub('action')
        ->default('namespace', 'App\\Actions');
}
```

When you run the `make:action` command, the `namespace` argument will no longer be available and will instead be an option `--namespace` with a default value of `App\Actions`. Since Magic Make treats the `class` placeholder as a special value, it will process the provided value and modify the namespace accordingly.
