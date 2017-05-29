---
title: Views
category: Diving Into
order: 4
---

Cicada does not have a view layer like in full MVC framework, but instead it implements almost every PHP view parsers. In this documentation, we will cover implementing the two most widely used by PHP developers. Those are Twig and Blade. Implementing any of these two, gives us a feeling of a native MVC application by means that we can render views pass variables and render HTML before reaching the client side.

### Cicada/Twig-View Component

Twig is a fast, secure and flexible template engine. Twig uses its one file extension `.twig` and reduces usage of PHP code as much as possible. In this documentation, we will cover adding Twig to Cicada Application. For further instructions, you can refer to a Twig documentation available [here](https://twig.sensiolabs.org/).

The first step of adding twig component to your app requires a composer. 
```
composer require twig/twig:~2.0
```
The next step is implementing twig environment into Cicada app. The best way to do it is by setting up Twig in Application class so that the consistency of the application is kept. Before this, let's take a look at our `Application.php` file. 
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

Adding Twig into our Dependency Collection is simple. The main paramater for Twig Environment is the path to our public folder where we are going to store our templates. Initially, we are creating under the root folder /public_html folder and inside him the template for views. Let's now review our project tree.

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
| +-- public_html/
| | +-- templates/
| +-- index.php
| +-- .htaccess
| +-- vendor/
| +-- composer.json
| +-- composer.lock
```
After that we are adding function in `Application.php` file
```php
    public function __construct(){
        .....
        // please do not forget to call the 
        // function in Application constructor
        $this->setupTwig();
    }
    private function setupTwig() {

        //initializing twig component to be 
        // accessable from $app variable to every Controller
        $this['twig'] = function() {

            // creating Twig Loader Filesystem to 
            // define the location of our teplates
            $loader = new Twig_Loader_Filesystem('public_html/templates');

            // initializing Twig Environment which 
            // accepts Twig Loader and array in which we can
            // initialize path to cashed templates which 
            // for now leave empty since we do not need it 
            $twig = new  Twig_Environment($loader,[]);

            return $twig;
        };
    }
```

The next step is to initialize it into our controller and to use it as a private variable.
```php
<?php

namespace DemoNamespace\Controllers;

use Twig_Environment;
use DemoNamespace\Application;
use DemoNamespace\Services\MainService;

class MainController
{
    /** @var MainService $mainService **/
    private $mainService;

    /** @var Twig_Environment $twig */
    private $twig;

    public function __construct($mainService, $twig){
        $this->mainService = $mainService;
        $this->twig = $twig;
    }

    public function index(Application $app, Request $request){
        $names = $this->mainService->getNames();

        // Twig render function besides 
        // initializing the name of the template 
        // it also allow to pass variables which 
        ///we previously passes as html string 
        return $this->twig->render('index.twig', ['names' => $names]);
    }
```

Twig has shortend our code making it very easy for reading. Let's take a look at `index.twig`. 
```
<html>
    <head>
        <title>Twig Template</title>
    </head>
    <body>
        <h1>Twig Template</h1>
        <h2>This is Index page</h2>
        <ol>
            {% for name in names %}
                <li>{{ name }}</li>
            {% endfor %}
        </ol>
    </body>
</html>
```

And the result is:![](/images/twigenv.png)

Twig offers many other functionalities for parsing data which you can check out [here](https://twig.sensiolabs.org/).

### Cicada/Blade Component

Blade is a simple, yet powerful templating engine. Blade does not restrict you from using plain PHP code in your views. All Blade views are compiled into plain PHP code and cached until they are modified, meaning Blade adds essentially zero overhead to your application. Blade view files use the `.blade.php` file extension. This documentation will cover just connecting blade with Cicada App and a basic example. For further information please refer to the Blade [documentation](https://laravel.com/docs/5.1/blade).

Assume that we have same project tree like previously presented.

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
| +-- public_html/
| | +-- templates/
| +-- index.php
| +-- .htaccess
| +-- vendor/
| +-- composer.json
| +-- composer.lock
```

The first step would be requiring the blade component into our project using composer by simply entering in our terminal.
```

```



