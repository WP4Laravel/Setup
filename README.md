# WP4Laravel - A headless Wordpress concept

> Note: This project is experimental, don't use it for production.

## The concept
WP4Laravel is by default a standard Laravel project. Instead of using a relational database it uses Wordpress as Content Management System.

The benefits relative to a standard Wordpress Setup:

* Use MVC-principles
* Better performance
* Flexibility
* Sustainability
* Security

The benefits relative to a standard Laravel project:

* No need to create a custom CMS
* Get the best of great Wordpress plugins
* For commercial purposes, you can sell the customer a Wordpress CMS.

## Dependencies

The basics of WP4Laravel is just e fresh Laravel install. Above that we made a mix of 3 open source projects to come to a proof of concept.

* Wordpress as a dependency (https://github.com/johnpbloch/wordpress)
* Corcel: Get Wordpress data with Eloquent (https://github.com/corcel/corcel)
* Wordplate: Standard theme and plugin for Wordpress (only for inspiration)

## Installation

Start a fresh Laravel 5.4+ installatie

```
laravel new my-wp-project
```

### Composer

To use Wordpress as a dependency, you need to extend your composer.json. Add a repositories section to the composer.json and ad the following repositories:

```
"repositories": [
	{
		"type": "composer",
		"url": "https://wpackagist.org"
	},
	{
		"type": "vcs",
		"url": "https://github.com/wp4laravel/wp4laravel"
	},
	{
		"type": "vcs",
		"url": "https://github.com/wp4laravel/wp4laravel-plugin"
	},
	{
		"type": "vcs",
		"url": "https://github.com/wp4laravel/corcel"
	}
],
```

Add an 'extra' section to the composer.json, to determine where to install Wordpress and plugins.

```
"extra": {
	"installer-paths": {
		"public/plugins/{$name}": ["type:wordpress-plugin"]
	},
	"wordpress-install-dir": "public/wp"
}
```

Add the following packages to your require section

```
"composer/installers": "^1.2",
"jgrossi/corcel": "dev-master",
"johnpbloch/wordpress": "^4.7",
"wp4laravel/wp4laravel": "dev-master",
"wp4laravel/wp4laravel-plugin": "dev-master",
```

Now you can update your composer.

```
composer update
```

### Environment file.

* Add the Wordpress salts to your .env file, with http://wordplate.github.io/salt/
* Make sure you added a database and added your database credentials to your .env file.
* Make sure the APP_URL environment variable, matches your local environment.

###	Database config

Copy your database connection in config/database.php, name that connection corcel and fill the prefix attribute with 'wp_'.

```
'wordpress' => [
	   'driver'    => 'mysql',
	   'host'      => 'localhost',
	   'database'  => 'corcel',
	   'username'  => 'admin',
	   'password'  => 'secret',
	   'charset'   => 'utf8',
	   'collation' => 'utf8_unicode_ci',
	   'prefix'    => 'wp_',
	   'strict'    => false,
	   'engine'    => null,
   ],
 ```

### Gitignore

Add the following rules to your .gitignore

```
public/languages
public/plugins
public/mu-plugins
public/upgrade
public/uploads
public/wp
```

### Service provider

Add the Service Provider of the WP4Laravel package to your config/app.php

```
WP4Laravel\WP4LaravelServiceProvider::class
```

### Publish public data

Unfortunetly the base theme and config of Wordpress has to be inside the webroot. You can publish these from WP4LaravelServiceProvider.

```
php artisan vendor:publish —provider="WP4Laravel\WP4LaravelServiceProvider"
```

### Install Wordpress

Go to /wp/wp-admin to setup your Wordpress project.



Ga naar /wp/wp-admin op je lokale url om het Wordpress installatie proces te starten.

##	Basic usage

Edit the default web route in your Laravel

```
Route::get('/', function () {
    $post = Corcel\Post::findOrFail(1);

    return view('welcome', compact($post));
});
```

Replace the 'Laravel' heading in resources/views/welcome.blade.php by  {{ $post->title }}

Open your project in the browser, you will see 'Hello World!' as heading.

## References

* Read the docs of Corcel before you start: https://github.com/corcel/corcel


## Advanced Custom Fields

What makes Wordpress a real CMS? Right: Advanced Custom Fields. To implement this concept we use 2 packages

* The Advanced Custom Fields wordpress plugin
* The Corcel ACF plugin to fetch ACF data from a Corcel / Eloquent model.

To work this out, you have to require to extra packages in your composer.

```
composer require corcel/acf
composer require wpackagist-plugin/advanced-custom-fields
```

> Note: We use ACF Pro, this is a payed plugin and not available as a composer dependency. We created a private Repo on github where we mirrored the ACF PRO plugin. Add an extra repository to composer.json to get this:

```
{
            "type": "vcs",
            "url": "https://github.com/wp4laravel/acf-pro-5"
 },
 ```

Look at the docs of Corcel (https://github.com/corcel/acf) for the usage of Corcel with ACF.


## Best practices

TODO:

Helper voor Page url
Helper voor Post templates
Autoloading functions.php
Site container
Flex library
Content en Post caching
Algolia Search
Yoast SEO



WP Storage
Querieen en sorteren op custom field datum
Render menu’s
Categorieen
Thumbnail URL
Get url of post
Author avatar
Social accounts
Register post types
AppServiceProvider
