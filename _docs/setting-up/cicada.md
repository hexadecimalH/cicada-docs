---
title: Cicada
category: Setting Up
order: 1
---
#Installation

### System Requierments
- Web Server with URL rewriting
- PHP 5.5 or newer

### Installing Cicada

Cicada libraries with Composer, from project folder please execute. If you don't have [Composer](https://getcomposer.org/) installed please refer to this [link](https://getcomposer.org/doc/00-intro.md).

```
composer require "cicada/cicada=@stable"
```

After requiring the Cicada package include the autoload file inside entry point

{% sample lang='php'%}
```
<?php
require 'vendor/autoload.php';
```
> index.php




