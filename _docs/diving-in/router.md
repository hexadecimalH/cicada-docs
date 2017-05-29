---
title: Router
category: Diving Into
order: 1
---
##### Routes Collection

Cicada framework has a unique tool that enables group specific routes under determined prefix, plus it enables further manipulation before and after function which is meant to be executed before or after recieveing the request. Let's give an example for the start.

```php
// here we have initialized route collection and added prefix
/** @var RouteCollection $storesCollection */
$mainCollection = $app['collection_factory']
    ->prefix('/main');
$app->get('/index',     [ $mainController, 'index' ]);
$app->get('/dashboard', [ $mainController, 'dashboard' ]);
$app->get('/login',     [ $mainController, 'login' ]);
$app->get('/modules',   [ $mainController, 'modules' ]);

$app->addRouteCollection($mainCollection);
```

Now let's update the index.php file with using RouteCollection instead of directly using Application class.

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
    ->prefix('/main');
$mainCollection->get('/index',      [ $mainController, 'index' ]);
$mainCollection->get('/dashboard',  [ $mainController, 'dashboard' ]);
$mainCollection->get('/login',      [ $mainController, 'login' ]);
$mainCollection->get('/modules',    [ $mainController, 'modules' ]);

$app->addRouteCollection($mainCollection);

$app->run();
```

The function "addRouteCollection\(\)" registers all routes defined within the collection. Now the route to our main router will be "/main/" + one of the routes. Now let's take a look on the updated MainController:

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

Until now, we have covered just GET method. Besides that, Cicada offers us full range of HTTP methods known by standards. The usage of HTTP methods will test on `$mainCollection`, which we already created in one of the previous examples. The difference between POST and GET route lays just in key word which comes after collection name. Please don't forget to add method in Controller with same name that you defined in Route, since Cicada Lookup is case sensitive.

```php
$mainCollection->post('/sign-in', [ $mainController, 'signIn' ]);
```

Let's add the method in MainController.

```php
public function signIn(){
    return "Sign In";
}
```

### PUT Route

Cicada enables us to register HTTP put method, which accepts two parameters: first, one route URL, and second, the array. The first element is controller name and second is the name of the method inside of the controller. 

```php
$mainCollection->put('/sign-in', [ $mainController, 'signIn' ]);
```

### DELETE Route

Cicada enables us to register HTTP delete method, which accepts two parameters: first, one route URL, and second,the array. The first element is controller name and second is the name of method inside of the controller.

```php
$mainCollection->delete('/sign-in', [ $mainController, 'signIn' ]);
```

### OPTIONS Route

Cicada enables us to register HTTP option method, which accepts two parameters: first, one route URL, and second, the array. The first element is controller name and second is the name of method inside of the controller.

```php
$mainCollection->option('/sign-in', [ $mainController, 'signIn' ]);
```

After defining each route, to make a successful HTTP call we need to define specific function defined in route, other way you will get the common error "Page not found"

### Placeholders

Placeholders let us create route pattern that can help us in further operations. Using placeholder with delete HTTP method is perfect for mapping ID of row that we want to be deleted. If we want to update our DB, the placeholder would be perfect for correct mapping with update HTTP method. Let's show this by this example:

```php
$mainCollection->delete('/item/{id}', [ $mainController, 'deleteItem' ]);
```

Placeholder is defined with curly brackets and between them we can set the variable name. The variable can be anything but our application will recognize it as a string. So, we assume that we have the method inside our controller `deleteItem` like:

```php
public function deleteItem($id){
    return $id;
}
```

So our function expects "$id" which has to match given name of the pattern that is defined in route. Of course with $ sign in front.

### Before & After Function

Before and After function can give us a lot of control over requests that our application is receiving. It allows us to open Sessions set cookies and many other functionalities. For now, we will just demonstrate its functionality over an example. Please note that Before and After function lets us either set anonymous function or function from Middleware or a class that we previously have defined. It also gives us the ability to assign function Before/After specific route or Before/After collection of routes. Let us test with anonymous function.

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
        var_dump("hello BEFORE");
    })->after(function(Application $app, Request $request){
        var_dump("hello AFTER");
    });
```

### Conclusion

This section introduced us how to manage routes and what functionalities are available. We went through creating routes for specific HTTP methods. At the end, we elaborated Before and After functions that are triggered Before/After request is received/processed respectively.

