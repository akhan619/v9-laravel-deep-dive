# Creating the Application
[Back](https://github.com/akhan619/v9-laravel-deep-dive/blob/main/README.md)

# TOC

- [Introduction](#introduction)
- [The Application Instance](#the-application-instance)


# Introduction

So as we saw previously, the first step is to create our application using
```php
$app = require_once __DIR__.'/../bootstrap/app.php';
```

The contents of this file is again pretty small:

```php
<?php

$app = new Illuminate\Foundation\Application(
    $_ENV['APP_BASE_PATH'] ?? dirname(__DIR__)
);

$app->singleton(
    Illuminate\Contracts\Http\Kernel::class,
    App\Http\Kernel::class
);

$app->singleton(
    Illuminate\Contracts\Console\Kernel::class,
    App\Console\Kernel::class
);

$app->singleton(
    Illuminate\Contracts\Debug\ExceptionHandler::class,
    App\Exceptions\Handler::class
);

return $app;
```

We create an instance of `Illuminate\Foundation\Application` class and call the `singleton` method 3 times. Then we return the instance. So as you can imagine, the creation of the instance is where the magic happens.

# The Application Instance

The constructor of the `Application` class is as below:

```php
public function __construct($basePath = null)
{
    if ($basePath) {
        $this->setBasePath($basePath);
    }

    $this->registerBaseBindings();
    $this->registerBaseServiceProviders();
    $this->registerCoreContainerAliases();
}
```
