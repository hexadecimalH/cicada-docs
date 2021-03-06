---
title: Response
category: Diving Into
order: 3
---

Your Cicada application response objects are given by Symfony\HttpFoundation library and are aimed to present the current HTTP response that it is aimed to be returned to the client side. Response object gives you the ability to inspect, manipulate the HTTP response status, headers and body.

### Implementing Response Object

Response object must be included in controller by, and then in function there must be created a new instance of response which accepts two parameters: first, html string and second, response code.

```php
use Symfony\Component\HttpFoundation\Response;

class MainController
{
    public function index(Application $app, Request $request){
        $names = $this->mainService->getNames();
        $htmlString = "<ul>";
        foreach($names as $name){
            $htmlString = $htmlString. ("<li>".$name."</li>");
        }
        $htmlString = $htmlString."</ul>";
        return new Response("<h1>This is index page</h1>".$htmlString, 200);
    }
}
```

#### Response Codes

Each Response object has its own response code, by default Response object has status 200. Response status can be checked via method that is implemented in object.
```php
// By default response status is 200
$response = new Response();
$status = $response->getStatusCode();
```
We can manipulate status code and change it on the way, by simply passing status code to method as an Integer. 

```php
//Our response now has status 302 
$newResponse = $response->setStatusCode(302);
```

### The Response Headers

Every HTTP response has headers. These are metadata that describe the HTTP response but are invisible in the response’s body. Cicada’s Response object provides several methods to inspect and manipulate its headers.

#### Get All Headers

You can fetch all HTTP response headers as an associative array with the Response object’s `$response->headers->all()` method. The resultant associative array’s keys are the header names and their values are themselves a numeric array of string values for their respective header name.
```php
$headers = $response->headers->all();
foreach ($headers as $name => $values) {
    echo $name . ": " . implode(", ", $values);
}
```
#### Get One Header

You can get a single header’s value(s) with the Response object’s `$response->headers->get($key)` method. This returns an array of values for the given header name. Remember, a single HTTP header may have more than one value!
```php
$headerValueArray = $response->headers->get('Vary');
```

#### Detect Header

You can test for the presence of a header with the Response object’s headers->has($name) method.
```php
if ($response->headers->has('Vary')) {
    // Do something
}
```
#### Set Header

You can set a header value with the Response object’s `headers->set($name, $value)` method.
```php
$newResponse = $oldResponse->headers->set('Content-type', 'application/json');
```
#### Keep On Mind

The Response object is immutable. This method returns a copy of the Response object that has the new header value. This method is destructive, and it replaces existing header values already associated with the same header name.

#### Remove Header

You can remove a header with the Response object’s withoutHeader($name) method.
```php
$newResponse = $oldResponse->headers->remove('Allow');
```

### JSON Response

Cicada JsonResponse object constructor accepts three parameters: `$data`,`$statuscode` and `$headers` which happen to be array,and help in simplifying the process of returning JSON data.

The $data parameter contains the data structure you wish to be returned as JSON. $status is optional, and can be used to return a custom HTTP code. $headers parameter is optional, and is the same as encoding options used for json_encode().

In it’s simplest form, JSON data can be returned with a default 200 HTTP status code.
```php
$data = array('name' => 'Bob', 'age' => 40);
$jsonResponse = new JsonResponse($data);
```
We can also return JSON data with a custom HTTP status code.
```php
$data = array('name' => 'Rob', 'age' => 40);
$jsonResponse = new JsonResponse($data, 201);
```
The Content-Type of the Response is automatically set to `application/json;charset=utf-8.

### Conclusion

As Request object, Response object plays a vital role in returning data, especially when Client side of logic is dependent on data that has been returned. Cicada's Symfony package offers various Response variations suitable to satisfy almost every kind of client side technologies. 
