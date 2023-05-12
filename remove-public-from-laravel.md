# Remove /public from URL (Laravel 9/10)

1. Create a **index.php** file in project root with the following code:
```php
<?php
  $publicPath = getcwd();

  $uri = urldecode(
      parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH) ?? ''
  );

  // This file allows us to emulate Apache's "mod_rewrite" functionality from the
  // built-in PHP web server. This provides a convenient way to test a Laravel
  // application without having installed a "real" web server software here.
  if ($uri !== '/' && file_exists($publicPath.$uri)) {
      return false;
  }

  require_once $publicPath.'/public/index.php';
```

2. Change the file **/vendor/laravel/framework/src/Illuminate/Foundation/helpers.php:147** to:
```php
return app('url')->asset("public/" . $path, $secure);
```

3. Move the file **.htaccess** from **/public** to the project root
