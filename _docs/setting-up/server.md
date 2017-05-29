---
title: Server
category: Setting Up
order: 2
---
Each web application has to be served with HTTP request that is served by Web Server, such as Apache or Nginx.

### Configuring Apache

We can set up Apache in 2 ways:
- placing project in root directory
- placing project as an Virtual Host

In both cases you have to enable `rewrite_mod` in Apache for Linux users that would be just typing into terminal `a2enmod rewrite`, for Windows users in file `httpd.conf` find line

```apache2.conf
#LoadModule rewrite_module modules/mod_rewrite.so
```
> httpd.conf

Remove the Hash in front of the sentence, which is sign for starting the comment. Also make sure that the option `AllowOverride` is set to `all`. Next step is placing `.htaccess` file in root project directory for using rewrite option. The content of `.htaccess` file is as follows:

```.htaccess
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^ index.php [QSA,L]
```
> .htaccess

Please do NOT forget to restart Apache after configuration edit, so the server can load new changes.

So far, our root three looks like this:

```text
+-- project
| +-- index.php
| +-- .htaccess
```
Our file `.htaccess` plays the main role in rewriting the requests and sending them to `index.php`.

### Configuring Nginx

Setting virtual host in Nginx seems much easier. By entering in `/etc/nginx/sites-available/` we should create an instance of host, which has to look like this:
```
server {
    listen 80;
    server_name example.com;
    index index.php;
    error_log /path/to/example.error.log;
    access_log /path/to/example.access.log;
    root /path/to/public;

    location / {
        try_files $uri /index.php$is_args$args;
    }

    location ~ \.php {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param SCRIPT_NAME $fastcgi_script_name;
        fastcgi_index index.php;
        fastcgi_pass 127.0.0.1:9000;
    }
}
```
> example

You should update the `server_name`, `error_log`, `access_log`, and root directives with your own values.

