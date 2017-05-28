#
## Diving into Router

##### Routes Collection

Cicada framework has unique tool that enables to group specific routes under determined prefix, plus enabling further manipulation before and after function which are meant to be executed before or after respectively. Let's give an example for kick start

```php
/** @var RouteCollection $storesCollection */
$mainCollection = $app['collection_factory']
->prefix('/main')
});
$mainCollection->get('/index', [ $mainController, 'index' ]);
$mainCollection->get('/dashboard', [ $mainController, 'dashboard' ]);
$mainCollection->get('/login', [ $mainController, 'login' ]);
$mainCollection->get('/modules', [ $mainController, 'modules' ]);
$app->addRouteCollection($mainCollection);
```

Now let's update the index.php file with using RouteCollection instead of directly using Application class

```php
<?php
require 'vendor/autoload.php';

use DemoNamespace\Application;
use DemoNamespace\Controllers\MainController;

$app = new Application();

//Creating an instance of our controller
$mainController = new MainController($app['mainService']);

/** @var RouteCollection $storesCollection */
$mainCollection = $app['collection_factory']
->prefix('/main')
$mainCollection->get('/index', [ $mainController, 'index' ]);
$mainCollection->get('/dashboard', [ $mainController, 'dashboard' ]);
$mainCollection->get('/login', [ $mainController, 'login' ]);
$mainCollection->get('/modules', [ $mainController, 'modules' ]);
$app->addRouteCollection($mainCollection);

$app->run();
```

The function "addRouteCollection\(\)" registers all routes defined within the collection. Now let's take a look on updated MainController:

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

public function login(){
return "<h1>This is login page</h1>";
}

public function modules(){
return "<h1>This is modules page</h1>";
}
}
```

### POST Route

Till now we have just covered, get route with route collection, besides that Cicada offer us full range of Routes known by standards their usage will test on already pre-defined `$mainCollection` . In difference with GET route we are just changing key word, and adding method in our controller respectively.

```php
$mainCollection->post('/sign-in', [ $mainController, 'signIn' ]);
```

Let's add the method in MainController

```php
public function signIn(){
return "Sign In";
}
```

### PUT Route

Cicada enable us to register HTTP put method, which also accepts two parameters first one route URL, and second array which first element is controller name and second name of method inside controller

```php
$mainCollection->put('/sign-in', [ $mainController, 'signIn' ]);
```

### DELETE Route

Cicada enable us to register HTTP delete method, which also accepts two parameters first one route URL, and second array which first element is controller name and second name of method inside controller

```php
$mainCollection->delete('/sign-in', [ $mainController, 'signIn' ]);
```

### OPTIONS Route

Cicada enable us to register HTTP option method, which also accepts two parameters first one route URL, and second array which first element is controller name and second name of method inside controller

```php
$mainCollection->option('/sign-in', [ $mainController, 'signIn' ]);
```

After defining each route, to make a successful HTTP call we need to define specific function defined in route, other way you will get error "Page not found"

### Placeholders

Placeholders let us create route pattern that can help us in further operations. While using delete HTTP method placeholder is perfect for passing ID of product, item or row in DB table that we want to delete, or update which will be the case if we are using update HTTP method. Let show how would that look

```php
$mainCollection->delete('/item/{id}', [ $mainController, 'deleteItem' ]);
```

Placeholder is defined with curly brackets and between them we can set the variable name, variable can be anything but our application will recognize it as a string. So we assume that we have method inside our controller deleteItem like

```php
public function deleteItem($id){
return $id;
}
```

So our function expects "$id" which have to match given name of the pattern that is defined in route, of course with $ sign in front.

### Before & After function

Before and After function can give us a lot of control over requests that our application is receiving. It allow us to open Sessions set cookies and many other functionalities for now we will just demonstrate its functionality over an example. Please note that before and after function let us ether set anonymous function or function from Middleware, also it gives us ability to assign function before/after specific route or before/after collection of routes. Let us test with anonymous function

```php
// adding before function on route collection
$mainCollection = $app['collection_factory']
->prefix('/main')
->before(function(Application $app, Request $request){
var_dump("hello BEFORE");
})->after(function(Application $app, Request $request){
var_dump("hello BEFORE");
});

// adding before function on specific route
$mainCollection->get('/index', [ $mainController, 'index' ])
->before(function(Application $app, Request $request){
var_dump("hello BEFORE")
})->after(function(Application $app, Request $request){
var_dump("hello AFTER")
});
```

### Conclusion

This section introduce us how to manage routes, and what functionalities are available. We went through creating routes for specific HTTP methods. At the end we elaborated Before and After functions that are triggered before/after request is received/processed respectively.

