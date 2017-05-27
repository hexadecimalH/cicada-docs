---
title: Welcome
---
Cicada is micro PHP framework that helps creating and various applications that very quickly handles HTTP requests execute desired functions and returning appropriate response.

### What is the Point ?

In the core functionality of Cicada is HTTP handler, which makes it perfect for creating API, but never the less Cicada can also function as an Web Application. Besides everything Cicada is framework which is really easy understandable and effortlessly to learn it.

### Basic Principle

First thing, for setting and running Cicada you will need a server Apache or Nginx. The server have to be correctly configured to send all request to main entry point `index.php`. Inside entry point (`index.php` in this case) we are creating an instance of Cicada application which will enable us creating the routes 


```
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

Cicada Framework from the core uses some of the most reliable third party libraries. The compatibility with other components or libraries is seamless and it work just fine.

### Reading the documentation

This Documentations is designed for both newbies and people who already work with Cicada. So, if you are new to Cicada I strongly recommend you starting to read this from Start to End, if not you are free to jump in any of the section for your interest.
