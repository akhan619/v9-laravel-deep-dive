# Deepdive - Laravel 9 Framework

This and the related set of documents is meant to provide a deep look into how the Laravel framework is structured and what exactly is going on behind the scenes. As we will be looking at the core files and processes of the framework, this is **not** a suitable guide to learn the framework nor is it an intro for beginners. A working understanding or familiarity with the various features of the framework will be beneficial to appreciate the material fully.

# Structure

Since there are going to be multiple files/classes/methods that will be touched upon in this document, it is not possible nor feasibile to put everything into 1 file or 1 TOC. Hence, the navigation will be split into 2 parts:
- A TOC which will cover the contents of a given document.
- A Back link to navigate back.

As there a lot of back and forth movement within files, and limitations of github, this structure seemed to be the best to me.

# TOC

- [Request](#request)

# Request

Any time a request hits the webserver, the server (be it Apache or Nginx) looks up the root .htaccess. This file has a rewrite rule which basically routes all requests to the file called `index.php` which is in the root of the `public` folder.

Here is the content of the file `index.php`:

```php
<?php

use Illuminate\Contracts\Http\Kernel;
use Illuminate\Http\Request;

define('LARAVEL_START', microtime(true));

if (file_exists($maintenance = __DIR__.'/../storage/framework/maintenance.php')) {
    require $maintenance;
}

require __DIR__.'/../vendor/autoload.php';

$app = require_once __DIR__.'/../bootstrap/app.php';

$kernel = $app->make(Kernel::class);

$response = $kernel->handle(
    $request = Request::capture()
)->send();

$kernel->terminate($request, $response);

```

That's it! These few lines of code is all that is needed to boostrap the application, handle the request and return a response. Of course there is a lot going on here in these few lines of code, so let us go over them.

```php
if (file_exists($maintenance = __DIR__.'/../storage/framework/maintenance.php')) {
    require $maintenance;
}
```

This piece here checks if we are in maintainence mode and executes the file if we are. We will ignore this for now as we will cover maintanence mode later.

```php
require __DIR__.'/../vendor/autoload.php';
```

All those `composer dump-autoload` commands are in a nutshell meant to generate this file. Laravel requires them here and this is why we don't manually `require` the files.

> Helps explain why so many php sdks mention the `require` statement in their code, but we don't do that when using `composer`.

Next we create an instance of the application through:

```php
$app = require_once __DIR__.'/../bootstrap/app.php';
```

The above line is the **heart** of the framework. We will be spending a ton of time looking at the `$app` variable and what's going on there. For now we have an instance of our application.

And finally this.
```php
$kernel = $app->make(Kernel::class);

$response = $kernel->handle(
    $request = Request::capture()
)->send();

$kernel->terminate($request, $response);
```
We resolve an instance of the `Kernel` class. This isn't just any `Kernel` but an `Illuminate\Contracts\Http\Kernel`. Even though this a Contract (Fancy Laravel term for an interface) we can actually get a concrete class implementing this interface. We will soon see how. Pass on the incoming request to this class for it to handle. And once we are done, terminate the request. And this is all there is.

So to summarize:
- Load all the classes that will be required by the application.
- Create the application instance.
- Create an instance of a Kernel class.
- Create a request variable through a static class method.
- Handle the request.
- Terminate the app.

Of course, this is just scratching the surface. So next you may navigate to any of the following sections to see more.

- [WIP - Creating the application](https://github.com/akhan619/v9-laravel-deep-dive/blob/main/create-the-application.md)

# Author
- [Aman Khan](https://github.com/akhan619)
