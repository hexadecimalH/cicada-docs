---
title: Views
category: Diving into
order: 4
---

Cicada does not have a view layer like in full MVC framework, but instead it implements almost every PHP view parsers. In this documentation we will cover implementing two most widly used by PHP developers those are Twig and Blade. Implementing any of these give us a feeling of a nativ MVC application by means that we can render views pass variables and render HTML before reaching client side.

### Cicada/Twig-view Component

Twig is fast, secure and flexible template engine. Twig uses its one file extension `.twig` and reduces usage of PHP code as much as possible. In this documentation will cover adding Twig to Cicada Application, for further instructions you can refer to a Twig documentation available [here](https://twig.sensiolabs.org/).

First step of adding twig component to your app is requiring it with composer 
```
composer require twig/twig:~2.0
```
Next step is implementing twig environment into Cicada app, best way would be setting up Twig in Application class so we can keep consistency of application. Before that let's take a look at our `Application.php` file 
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

Adding Twig into our Dependency Collection is pretty much simple, main paramater for Twig Environment is path to our public folder where we are going to store our templates, so first thing we are creating under the root folder /public_html folder and inside him templates for views, let's review now our project tree.

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

Next step is to initialize it into our controller and to use it as a private variable
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

Twig has shortend our code makeing it very easy for reading,now lets take a look at `index.twig`
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

And the result is:[](/images/twig_env.png)

Twig offers many other functionalities for parsing data which you can check out [here](https://twig.sensiolabs.org/).

### Cicada/Blade Component

Blade is the simple, yet powerful templating engine provided with Laravel. Blade does not restrict you from using plain PHP code in your views. All Blade views are compiled into plain PHP code and cached until they are modified, meaning Blade adds essentially zero overhead to your application. Blade view files use the `.blade.php` file extension. In this documentation will cover just connecting blade with Cicada App for further information please refer to the Blade [documentation](https://laravel.com/docs/5.1/blade).

Assume that we have same project tree like previously presented

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


