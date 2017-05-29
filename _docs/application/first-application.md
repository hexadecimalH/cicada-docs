---
title: Application
category: Your First Cicada Application
order: 3
---
Before stepping up, make sure you have created the environment for application, otherwise please refer to [Setting Up Server](/setting-up/server/) and [Setting up Cicada](/setting-up/cicada/) section. This section introduces the main principle of Cicada application, which includes setting the namespace, dependency injection, project directory structure, etc.

---

So far, we have set up our desired server and installed Cicada via composer. Our project directory working tree, now looks like this:

```
+-- project
| +-- index.php
| +-- .htaccess
| +-- vendor/
| +-- composer.json
| +-- composer.lock
```

If we inspect our `composer.json` file, it will look like this:

```json
{
    "require": {
        "cicada/cicada": "@stable"
    }
}
```

Now we need to create separate routes and different classes/controllers to handle those calls, that we can achieve with namespace

## Defining Namespace

Namespace in Cicada Framework is defined inside `composer.json`by adding new json property "autoload" in which we will embed namespace that we need . Composer file now looks like this

```json
{
    "require": {
        "cicada/cicada": "@stable"
    },
    "autoload": {
        "psr-4": {
        "DemoNamespace\\": "src/",
        "DemoTestNamespace\\": "tests/"
        }
    }
}
```

As you may notice, inside "autoload" property we have another "psr-4" property, which is the holder of our namespaces. Each namespace has its own root folder within the project root folder, from where it will develop namespace tree. The composer lets us create as many namespaces as we need. In this case, we have defined "DemoNamespace" which root folder is "src/" in our project directory. We also have defined "DemoTestNamespace" a namespace which we will later use for creating and running Tests and its root folder is "tests/".

## Project Directory Structure

Keeping project Directory well-organized,it helps us to be more productive. Hereby, I present you the directory structure of my choice. The main point here is to represent the way environment can be organized.

```
+-- project/
| +--src/
| | +--Controllers/
| | +--Services/
| | +--Models/
| | +--Application.php
| +-- tests/
| +-- index.php
| +-- .htaccess
| +-- vendor/
| +-- composer.json
| +-- composer.lock
```

Inside root directory we have created "src/" folder where we are going to place full logic of the application. On the other hand, in "tests/" folder we are placing test cases after we are done with app logic. Whereas, `index.php` serves as a connector or router. Its main function is to recognize url and send it to the right controller and method.

## Application

#### Enabling Controller

Let's create `MainController.php` inside "src/Controllers/" folder and add few functions. It will look like this:

```php
<?php

namespace DemoNamespace\Controllers;


class MainController
{
    public function __construct(){

    }

    public function index(){
        return "<h1>This is index page</h1>"
    }

    public function dashboard(){
        return "<h1>This is dashboard page</h1>"
    }
}
```

Now, we have controller that serves two main views of our application \(basically, they are just functions that return string-for now\), and in order to get them working we have to create an instance of this MainController in`index.php`and define routes for specific url.

Let's take a look at `index.php`

```php
<?php
require 'vendor/autoload.php';

use Cicada\Application;

// Within Cicada package included Symfony Request objects
use Symfony\Component\HttpFoundation\Request; 

// Within Cicada package included Symfony Request objects
use Symfony\Component\HttpFoundation\Response 
// including our new Controller using namespace
use DemoNamespace\Controllers\MainController;

$app = new Application();

//Creating an instance of our controller
$mainController = new MainController();

//Add a route that will serve string from Controller
// inside GET function we first place URL than inside
$app->get('/', [$mainController, "index"]); 
// array we place instance of Controller and the name
// method

// Add a route
$app->get('/hello/{name}', function (Application $app, Request $request, $name) {
    return new Response("Hello $name");
});

$app->run();
```

If we want to add our next page to route, simply add it after our last entry line

```php
$app->get('/dashboard', [$mainController, "dashboard"]);
```

## Dependencies Injection

As our project expands, we are including more and more libraries, plugins or custom classes that will execute desired functions. In order not to complicate dependencies injection we are going to create new class Application.php in which we will create all necessary instances. Let us create Application.php inside "src/" folder with namespace "DemoNamespace"

```php
<?php

namespace DemoNamespace;

// Our Application class is child object of Cicada\Application
// and inherits every property of our Cicada\Application
class Application extends \Cicada\Application 
{ 

    public function __construct($configPath, $domain, $protocol)
    {
        parent::__construct();
    }
}
```

So our index.php can be changed in order to look like this:

```php
<?php

require 'vendor/autoload.php';

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

//Instead including Cicada\Application we are including our object
use DemoNamespace\Application;
use DemoNamespace\Controllers\MainController;

$app = new Application();

//Creating an instance of our controller
$mainController = new MainController();

// inside GET function we first place URL than inside
$app->get('/', [$mainController, "index"]);
$app->get('/dashboard', [$mainController, "dashboard"]);

$app->run();
```

Functionality stays the same, except our class application brings us new set of functionalities. When it comes to passing of dependencies, say, we have class` MainService.php` it has a method which returns array of names.

```php
<?php


namespace DemoNamespace\Services;

class MainService
{
    public function __construct(){

    }

    public function getNames(){
        return [ "Haris", "John", "Dave", "Mary", "Sara"];
    }
}
```

These names has to be displayed on the client side, so the MainService is necessary for MainController, which we will edit it like this:

```php
<?php

namespace DemoNamespace\Controllers;

use DemoNamespace\Services\MainService;

class MainController
{
    /** @var MainService $mainService **/
    private $mainService;

    public function __construct($mainService){
        $this->mainService = $mainService;
    }

    public function index(){
        $names = $this->mainService->getNames();
        $htmlString = "<ul>";
        foreach($names as $name){
            $htmlString = $htmlString. ("<li>".$name."</li>");
        }
        $htmlString = $htmlString."</ul>";
        return "<h1>This is index page</h1>".$htmlString;
    }

    public function dashboard(){
        return "<h1>This is dashboard page</h1>";
    }
}
```

We are not done yet, because we have to initialize MainService inside our Depenedency Injection. In our `Application.php` we will create a function, which will in turn create MainService

```php
<?php

namespace DemoNamespace;

use DemoNamespace\Services\MainService;

class Application extends \Cicada\Application
{

    public function __construct()
    {
        parent::__construct();
        $this->setUpServices();
    }

    private function setUpServices(){
        $this['mainService'] = function(){
            return new MainService();
        };
    }
}
```

Now, we are back again at index.php. Here we are passing MainService to MainController

```php
<?php
require 'vendor/autoload.php';

use DemoNamespace\Application;

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use DemoNamespace\Controllers\MainController;

// Instance of MainService is created within Application
$app = new Application();

//Creating an instance of our controller with MainService included into constructor
$mainController = new MainController($app['mainService']);

// inside GET function we first place URL than inside
$app->get('/', [$mainController, "index"]);
$app->get('/dashboard', [$mainController, "dashboard"]);

$app->run();
```

Let's check the result of our application:![](/images/screenshot.png) Before we move to conclusion,let's check our directory root

```
+-- project/
| +--src/
| | +--Controllers/
| | | +--MainController.php
| | +--Services/
| | | +--MainService.php
| | +--Models/
| | +--Application.php
| +-- tests/
| +-- index.php
| +-- .htaccess
| +-- vendor/
| +-- composer.json
| +-- composer.lock
```

---

## Conclusion

All in all, we learned how to define the namespace of our application, and we saw various examples of using it; we saw how dependency is centralized in Cicada, by creating instances of object that we need inside of our Application class where we defined it earlier. 



