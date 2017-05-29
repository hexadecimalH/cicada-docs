---
title: Request
category: Diving Into
order: 2
---
Requests are one of the most important objects in Web Programming. They represent the communication between the server and client, hence requests are the most valuable objects. Cicada in itself contains Symfony component that holds HttpFoundation, which has main objects like Request, Response, FileBag, HeaderBag, JsonResponse, Cookie, Sessions etc. For now, we are going to examine Request object, which let us inspect and manipulate HTTP method, body and headers.

### How To Get Request Object?

The request object is injected into Cicada routes as a second parameter \(first parameter is always Application object, which let us access our dependencies once again \). So, in our methods we can capture request like this:

```php
public function dashboard(Application $app, Request $request){
    return "<h1>This is dashboard page</h1>";
}
```

### Getting Parameters

Getting parameters passed from the client has always been common. Request objects in Cicada allow getting parameter one by one or getting all of them in an array.

```php
public function dashboard(Application $app, Request $request){
// getting all parameters as an array
$parameters = $request->request->all();
// getting one parameter by a key
$parameter = $request->request->get("parameter");
    return "<h1>This is dashboard page</h1>";
}
```

### Getting Media Objects

Request has also an option to get all files or just one media file that has been sent from the client side.

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

We can get the name of the method from Request object using method "getMethod\(\)" from request like:

```php
$method = $request->getMethod();
```

If we have certain conditions in our method based on type of the method, request object also offers method "isMethod\(\)" that returns boolean value, comparing request method to a passed string

```php
$isGet = $request->isMethod("GET");
```

### Getting Request URI

Every HTTP request has its URI that identifies the requested application resource. The HTTP request URI has several parts:

* Scheme \(e.g.`http`or `https`\)
* Host \(e.g.`example.com`\)
* Port \(e.g.`80`or`443`\)
* Path \(e.g.`/users/1`\)
* Query string \(e.g.`sort=created`&`dir=asc`\)

You can fetch the Request object’s[ ](http://www.php-fig.org/psr/psr-7/#3-5-psr-http-message-uriinterface) URI with its`getUri()`method, it also contains additional methods for retrieving specific parts:

```php
$uri = $request->getUri(); // retrieving full URI
$port = $request->getPort(); // retrieving Port number
$host = $request->getHost(); // retrieveing Host domain
$scheme = $request->getScheme(); // retrieveing scheme http or https
$fullQuery = $request->query->all(); // retrieveing all query strings
$query = $request->query->get("name"); // retrieveing specific query string
```

### Headers

Cicada has accessability to headers as well as their manipulations. By simply accessing Request object we can get the array with full information about request that arrived

```php
// Get request headers as associative array
$header = $request->headers;
// Get the ACCEPT-ENCODING header
$header = $request->headers->get('accept-encoding');
```

### Other Methods

Request as an object offer full scope of helpfull methods in order to retrieve the User/Server information that are of crucial importance. Some of them are:

* `getCharset()`
* `getContent()`
* `getContentType()`
* `getUser()`
* `getUserInfo()`

... and many others 

### Conclusion

Request object, and its variations dictates the flow of our application. Cicada has full scope of Request manipulation methods that will make our problems seamless to surpass.