---
title: Request
category: Diving into
order: 1
---
## Requests

Requests are one of the most important objects in Web Programming, they represent literary the communication between server and client, so we can freely say that requests are the most valuable objects. Cicada in itself contain Symfony component that holds HttpFoundation which has main objects like Request, Response, FileBag, HeaderBag, JsonResponse, Cookie, Sessions and etc. For now we gonna examine Request object, which let as inspect and manipulate HTTP method, body and headers.

### How to get Request object?

The request object is injected into Cicada routes as a second parameter \(first parameter is allways Application object which let us access our dependencies once again \). So in our methods we can capture request like this

```php
public function dashboard(Application $app, Request $request){
return "<h1>This is dashboard page</h1>";
}
```

### Getting parameters

Getting parameters passed from client side hasn't been easier. Request object allow getting parameter one by one or getting all of them in an array.

```php
public function dashboard(Application $app, Request $request){
// getting all parameters as an array
$parameters = $request->request->all();
// getting one parameter by a key
$parameter = $request->request->get("parameter");
return "<h1>This is dashboard page</h1>";
}
```

### Getting media objects

Request has also option to get all files or just one media file that has been sent from client side.

```php
public function dashboard(Application $app, Request $request){
// getting all files as an array
$parameters = $request->files->all();
// getting one file by a key
$parameter = $request->files->get("parameter");
return "<h1>This is dashboard page</h1>";
}
```

### Getting Request Method

Every HTTP request has method that is specific for it like

* GET
* PUT
* POST
* PATCH
* DELETE
* HEAD
* OPTIONS
* PATCH

We can get name of the method from Request object using method "getMethod\(\)" from request like

```php
$method = $request->getMethod();
```

If we have certain conditions in our method based on type of the method, request object also offers method "isMethod\(\)" that returns boolean value, comparing request method to a passed string

```php
$isGet = $request->isMethod("GET");
```

### Getting Request URI

Every HTTP request has it's URI that identifies the requested application resource. . The HTTP request URI has several parts:

* Scheme \(e.g.`http`or `https`\)
* Host \(e.g.`example.com`\)
* Port \(e.g.`80`or`443`\)
* Path \(e.g.`/users/1`\)
* Query string \(e.g.`sort=created`&`dir=asc`\)

You can fetch the Request object’s[ ](http://www.php-fig.org/psr/psr-7/#3-5-psr-http-message-uriinterface) URI with its`getUri()`method, it also contains additional methods for retrieving specific parts :

```php
$uri = $request->getUri(); // retrieving full URI
$port = $request->getPort(); // retrieving Port number
$host = $request->getHost(); // retrieveing Host domain
$scheme = $request->getScheme(); // retrieveing scheme http or https
$fullQuery = $request->query->all(); // retrieveing all query strings
$query = $request->query->get("name"); // retrieveing specific query string
```



