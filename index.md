---
title: Welcome
---
Cicada is a micro PHP framework, which helps to create various applications that very quickly handles HTTP requests,executes desired functions and returns appropriate response.

### What is the Point ?

The core functionality of Cicada is HTTP handler, which makes it perfect for creating API. Moreover, Cicada can also function as a Web Application. Besides everything, Cicada is a framework which is very understandable and easy to learn. 

### Basic Principle

The first thing to set and run Cicada, a server Apache or Ngix is necessary. The server has to be correctly configured in order to send all requests to the main entry point `index.php`. Inside the entry point (`index.php` in this case), we are creating an instance of Cicada application, which will enable us to create the routes.


```php
<?php
require '../vendor/autoload.php'

// Include Cicada Application
use Cicada\Application;

// Include Resonse and Request Object from Symfony Library
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

//Create Instance of Cicada Application
$app = new Application();

// Add a route
$app->get('/hello/{name}', function (Application $app, Request $request, $name) {
    return new Response("Hello $name");
});
// Run Application
$app->run();
```

### Using 3th party Components

Initially, Cicada Framework uses some of the most reliable third party libraries. The compatibility with other components or libraries is seamless and it works just fine.

### Reading the documentation

This Documentations is designed for both newbies and people who already work with Cicada. So, if you are new to Cicada, I strongly recommend you starting to read this from Start to End. If not, you can skip and jump in any of the sections of your interest.
