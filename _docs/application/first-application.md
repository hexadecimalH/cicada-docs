#
# First Application

Before we approach to next step please make sure that you have created environment for application, if not please refer to Setting Up Server and Setting Up Cicada section. This section will introduce the main principle of Cicada application, which includes setting the namespace, dependency injection, project directory structure and etc.

---

So far we have set up our desired server and installed cicada via composer. Our project directory working tree now looks like

```
+-- project
| +-- index.php
| +-- .htaccess
| +-- vendor/
| +-- composer.json
| +-- composer.lock
```

If we inspect our `composer.json` file it will look like this

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
"require": {
"cicada/cicada": "@stable"
},
"autoload": {
"psr-4": {
"DemoNamespace\\": "src/",
"DemoTestNamespace\\": "tests/"
}
}
```

As you may notice inside "autoload" property we have another "psr-4" property which is the holder of our namespaces, each namespace have its own root folder within the project root folder, from where it will develop namespace tree. Composer let us create as many namespaces as we need. In this case we have defined "DemoNamespace" which root is "src/" folder in our project directory, also "DemoTestNamespace" is namespace which we will later use for creating and runing Tests and its root folder is "tests/" folder.

## Project Directory Structure

Keeping project Directory well organized help us to be more productive. Hereby I present you directory structure of my choice, the point here is represent how environment can be organised

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

Inside root directory we have created "src/" folder where we are going to place full logic of the application, on the other hand in "tests/" folder we are placing tast cases after we are done with app logic. On the other hand, `index.php` serves as an connector or router its main function is to recognize url and send it to right, controller and method.

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

No we have controller that serves 2 main views of our application \( basically they are just function that return string-for now\), in order to get it working we have to create an instance of this MainController in`index.php`and define routes for specific url.

Let's take a look at `index.php`

```php
<?php
require 'vendor/autoload.php';

use Cicada\Application;
use Symfony\Component\HttpFoundation\Request; // Within Cicada package included Symfony Request objects
use Symfony\Component\HttpFoundation\Response // Within Cicada package included Symfony Request objects
// including our new Controller using namespace
use DemoNamespace\Controllers\MainController;

$app = new Application();

//Creating an instance of our controller
$mainController = new MainController();

//Add a route that will serve string from Controller
$app->get('/', [$mainController, "index"]); // inside GET function we first place URL than inside
// array we place instance of Controller and the name
// method

// Add a route
$app->get('/hello/{name}', function (Application $app, Request $request, $name) {
return new Response("Hello $name");
});

$app->run();
```

If we want to add our next page to route we can simply add after our last entry line

```php
$app->get('/dashboard', [$mainController, "dashboard"]);
```

## Dependencies Injection

As our project expands, we are including more and more libraries, plugins or custom classes that will execute desired functions. In order not to complicate dependencies injection we are going to create new class Application.php in which we will create all necessary instances. So let's create Application.php inside "src/" folder with namespace "DemoNamespace"

```php
<?php

namespace DemoNamespace;

class Application extends \Cicada\Application // Our Application class is child object of Cicada\Application
{ // and inherits every property of our Cicada\Application

public function __construct($configPath, $domain, $protocol)
{
parent::__construct();
}
}
```

So our index.php can be changed to look like this:

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

Functionality stays the same, except our class application bring us new set of functionalities when it comes to passing of dependencies, let say we have class` MainServce.php` which have method which returns array of names.

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

These names got to be displayed on client side so the MainService is necessary for MainController, which we will edit like this

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

But still we are not finished we have to initialize MainService inside our Depenedency Injection. In our `Application.php` create function that will create MainService

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

Now back to index.php where we are passing MainService to MainController

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

Let's check the result of our application:![](/assets/Screenshot from 2017-05-27 21-09-12.png)Before we move to conclusion let's check our directory root

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

To sum up what we learned till now, we learned how to define namespace of our application, and we have seen various example of using it. Furthermore, we have seen how dependency is centralized in Cicada, by creating instances of object that we need inside our Application class where we defined.



