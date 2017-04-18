# WP4Laravel - A headless Wordpress concept

> Note: This project is experimental

## Table of contents
   * [WP4Laravel - A headless Wordpress concept](https://github.com/WP4Laravel/Setup/blob/master/README.md#wp4laravel---a-headless-wordpress-concept)
      * [The concept](https://github.com/WP4Laravel/Setup/blob/master/README.md#the-concept)
      * [Dependencies](https://github.com/WP4Laravel/Setup/blob/master/README.md#dependencies)
      * [Installation](https://github.com/WP4Laravel/Setup/blob/master/README.md#installation)
         * [Gitignore](https://github.com/WP4Laravel/Setup/blob/master/README.md#gitignore)
         * [Composer](https://github.com/WP4Laravel/Setup/blob/master/README.md#composer)
         * [Environment file.](https://github.com/WP4Laravel/Setup/blob/master/README.md#environment-file)
         * [Database config](https://github.com/WP4Laravel/Setup/blob/master/README.md#database-config)
         * [Service provider](https://github.com/WP4Laravel/Setup/blob/master/README.md#service-provider)
         * [Publish public data](https://github.com/WP4Laravel/Setup/blob/master/README.md#publish-public-data)
         * [Storage](https://github.com/WP4Laravel/Setup/blob/master/README.md#storage)
         * [Remove unused migrations](https://github.com/WP4Laravel/Setup/blob/master/README.md#remove-unused-migrations)
         * [Install Wordpress](https://github.com/WP4Laravel/Setup/blob/master/README.md#install-wordpress)
      * [Basic usage](https://github.com/WP4Laravel/Setup/blob/master/README.md#basic-usage)
      * [References](https://github.com/WP4Laravel/Setup/blob/master/README.md#references)
      * [Advanced Custom Fields](https://github.com/WP4Laravel/Setup/blob/master/README.md#advanced-custom-fields)
      * [Wordpress configuration](https://github.com/WP4Laravel/Setup/blob/master/README.md#wordpress-configuration)
      * [Add plugins](https://github.com/WP4Laravel/Setup/blob/master/README.md#add-plugins)
         * [How do I use it?](https://github.com/WP4Laravel/Setup/blob/master/README.md#how-do-i-use-it)
      * [Best practices](https://github.com/WP4Laravel/Setup/blob/master/README.md#best-practices)
         * [Create your own models for each post type](https://github.com/WP4Laravel/Setup/blob/master/README.md#create-your-own-models-for-each-post-type)
         * [Register your post types](https://github.com/WP4Laravel/Setup/blob/master/README.md#register-your-post-types)
         * [Pageurl helper](https://github.com/WP4Laravel/Setup/blob/master/README.md#pageurl-helper)
         * [Post template helper](https://github.com/WP4Laravel/Setup/blob/master/README.md#post-template-helper)
         * [Site container](https://github.com/WP4Laravel/Setup/blob/master/README.md#site-container)
         * [Flex class](https://github.com/WP4Laravel/Setup/blob/master/README.md#flex-class)
         * [Caching](https://github.com/WP4Laravel/Setup/blob/master/README.md#caching)
         * [Search](https://github.com/WP4Laravel/Setup/blob/master/README.md#search)
         * [SEO](https://github.com/WP4Laravel/Setup/blob/master/README.md#seo)
         * [Query and sort on ACF date field](https://github.com/WP4Laravel/Setup/blob/master/README.md#query-and-sort-on-acf-date-field)
         * [Render menu's](https://github.com/WP4Laravel/Setup/blob/master/README.md#render-menus)
         * [Thumbnail url](https://github.com/WP4Laravel/Setup/blob/master/README.md#thumbnail-url)
         * [Get URL of post](https://github.com/WP4Laravel/Setup/blob/master/README.md#get-url-of-post)
         * [Social accounts](https://github.com/WP4Laravel/Setup/blob/master/README.md#social-accounts)
         * [AppServiceProvider](https://github.com/WP4Laravel/Setup/blob/master/README.md#appserviceprovider)

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

Start a fresh Laravel 5.4+ install

```
laravel new my-wp-project
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

### Database config

Set the table prefix of the database connection to 'wp\_' in `config/database.php`. 

### Service provider

Add the Service Provider of the WP4Laravel package to your config/app.php

```
WP4Laravel\WP4LaravelServiceProvider::class
```

### Publish public data

Unfortunetly the base theme and config of Wordpress has to be inside the webroot. You can publish these from WP4LaravelServiceProvider.

```
php artisan vendor:publish --provider="WP4Laravel\WP4LaravelServiceProvider"
```

### Storage

All Wordpress media will be saved in the location of the Laravel Public storage. To make this work, run the following Artisan command to make a symbolic link in your webroot.

```
php artisan storage:link
```

### Remove unused migrations
If you're only using tables managed by Wordpress, it's recommended to remove the default migrations generated by Laravel. These files are in `database/migrations/`.

### Install Wordpress

Go to /wp/wp-admin to setup your Wordpress project.

## Basic usage

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

> Note: We use ACF Pro, this is a paid plugin and not available as a composer dependency. We created a private Repo on github where we mirrored the ACF PRO plugin. Add an extra repository to composer.json to get this:

```
{
            "type": "vcs",
            "url": "https://github.com/wp4laravel/acf-pro-5"
 },
 ```

Look at the docs of Corcel (https://github.com/corcel/acf) for the usage of Corcel with ACF.

## Wordpress configuration

Within /public/themes/wp4laravel/library you can update the Wordpress configuration. Most used for configuring post types and taxonomies. Every post type kan be defined in the directory post types. En example is already included. For taxonomies it works the same.

If you want to define your post types and taxonomies with a Wordpress plugin, thats no problem.

## Add plugins

Because Wordpress and his plugins are dependencies, you can only use plugins which are available with composer.

[WordPress Packagist](https://wpackagist.org) comes straight out of the box with WP4Laravel. It mirrors the WordPress [plugin](https://plugins.svn.wordpress.org) as a Composer repository.

### How do I use it?

Require the desired plugin using `wpackagist-plugin` as the vendor name.

```bash
composer require wpackagist-plugin/advanced-custom-fields
```

Plugins are installed to `public/plugins`.

Please visit [WordPress Packagist](https://wpackagist.org) website for more information and examples.

## Best practices

###	Create your own models for each post type

If you want take advantage of the power of Eloquent, we advice to create a Laravel model for each of your post types.

```
namespace App\Models;

use Corcel\Post as Corcel;

class Event extends Corcel
{
    protected $postType = 'event';
}
```

For example, you can add accessors to make life easier.

###	Register your post types

When you access a post type from a specific model, you have to register this. The best way is to do this in the boot method of your AppServiceProvider.

```
\Corcel\Post::registerPostType('event', \App\Models\Event::clas);
```

If you choose to create a new class for your custom post type, you can have this class be returned for all instances of that post type.

### Pageurl helper

Route:
```
Route::get('{url}', 'PageController')->where('url', '(.*)');
```

PageController:
```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Page;

/**
 * Catch all routes which are not defined in the routes file
 * Next search for a page which has the same url structure as the route
 * If not found,
 */
class PageController extends Controller
{
    public function show($url)
    {
        //	Get the post by url or abort
        $post = Page::url($url);

        //	Add post data to the site container
        app('site')->model($post);


        //	Show the template which is possibly chosen in WP
        return view($post->template);
    }
}

```

Page Model:
```
<?php

namespace App\Models;

use WP4Laravel\Corcel\Pageurl;

/*
 * Model for WP pages
 */
class Page extends Post
{
    //	The Pageurl trait has a method to find a page based on the full url.
    use Pageurl;


    /**
     * What is the WP post type for this model?
     * @var string
     */
    protected $postType = 'page';
}

```

### Post template helper

### Site container

### Flex class

### Caching

### Search

### SEO

### Query and sort on ACF date field

### Render menu's

### Thumbnail url

###	Get URL of post

### Social accounts

### AppServiceProvider
