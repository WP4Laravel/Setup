# WP4Laravel - A headless Wordpress concept

> Note: This project is experimental

## Table of contents
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
    * [Rendering \<picture\> tags](https://github.com/WP4Laravel/Setup/blob/master/README.md#rendering-picture-tags)
    * [Using the MenuBuilder to construct menus](https://github.com/WP4Laravel/Setup#using-the-menubuilder-to-construct-menus)

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

The basis of WP4Laravel is just a fresh Laravel install. We add three open source projects in the mix:
* Wordpress as a dependency (https://github.com/johnpbloch/wordpress)
* Corcel: Get Wordpress data with Eloquent (https://github.com/corcel/corcel)
* Wordplate: Standard theme and plugin for Wordpress (only for inspiration, not actually installed)

## Installation

Start a fresh Laravel 5.4+ install

```bash
laravel new my-wp-project
```

### Gitignore

Add the following rules to your `.gitignore`

```gitignore
public/languages
public/plugins
public/mu-plugins
public/upgrade
public/uploads
public/wp
```

### Composer

To use Wordpress as a dependency, you need to extend your composer.json. Add a repositories section to the composer.json and ad the following repositories:

```json
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
	}
],
```

Add an 'extra' section to the composer.json, to determine where to install Wordpress and plugins.

```json
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

```php
WP4Laravel\WP4LaravelServiceProvider::class
```

### Publish public data

Unfortunately, the base theme and config of Wordpress has to be inside the webroot. You can publish these from WP4LaravelServiceProvider.

```bash
php artisan vendor:publish --provider="WP4Laravel\WP4LaravelServiceProvider"
```

### Storage

All Wordpress media will be saved in the location of the Laravel Public storage. To make this work, run the following Artisan command to make a symbolic link in your webroot.

```bash
php artisan storage:link
```

### Remove unused migrations
If you're only using tables managed by Wordpress, it's recommended to remove the default migrations generated by Laravel. These files are in `database/migrations/`.

### Install Wordpress

Go to /wp/wp-admin to setup your Wordpress project.

## Basic usage

Edit the default web route in your Laravel

```php
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

To work this out, you have to require to extra packages in your composer. Add a custom repository:

```json
{
    "type": "vcs",
    "url": "https://github.com/wp4laravel/acf-pro-5"
},
{
    "type": "vcs",
    "url": "https://github.com/wp4laravel/corcel"
}
```
And require the packages:
```bash
composer require corcel/acf
composer require wp4laravel/acf-pro-5
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

```php
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

```php
\Corcel\Post::registerPostType('event', \App\Models\Event::clas);
```

If you choose to create a new class for your custom post type, you can have this class be returned for all instances of that post type.

### Pageurl helper

Route:
```php
Route::get('{url}', 'PageController')->where('url', '(.*)');
```

PageController:
```php
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
```php
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

### Rendering \<picture\> tags
WP4Laravel includes a helper template and ViewProvider to correctly render \<picture\>-tags with crops, etc. This works correctly for both ThumbnailMeta and Image-classes. 

#### Configuration
A configuration file `config/picture.php` can be published to set the URL-prefix of your uploads folder. The default is `/storage/`. Publish the configuration file to adapt:

```bash
php artisan vendor:publish --tag=config --provider="WP4Laravel\WP4LaravelServiceProvider"
```

#### Usage
Crops must be named 'header_desktop_1x', 'header_mobile_2x', 'header_mobile_1x', 'header_mobile_14x' etc. Configure in Wordpress as follows:

```php
add_image_size('header_desktop_1x', 1000, 445, true);
add_image_size('header_desktop_2x', 2000, 890, true);
add_image_size('header_mobile_1x', 400, 400, true);
add_image_size('header_mobile_2x', 800, 800, true);
```

Use the following snippet in your blade views:
```blade
@include('wp4laravel::picture', [
    'picture' => $post->thumbnail,
    'breakpoints' => [
        '(min-width: 768px)' => 'header_desktop',
        '(max-width: 767px)' => 'header_mobile',
    ],
])
```

This results for example in the following output:
```html
<picture>
  <source srcset="/storage/kinderdijk-when-the-sky-is-on-fire-1000x445.jpg 1x, /storage/kinderdijk-when-the-sky-is-on-fire-2000x890.jpg 2x" media="(min-width: 768px)">
  <source srcset="/storage/kinderdijk-when-the-sky-is-on-fire-400x400.jpg 1x, /storage/kinderdijk-when-the-sky-is-on-fire-800x800.jpg 2x" media="(max-width: 767px)">
  <img src="/storage/kinderdijk-when-the-sky-is-on-fire.jpg" alt="Kinderdijk with a couple of windmills, viewed at dusk with a stunning red-orange sky">
</picture>
```

Configuring breakpoints is optional: not setting them will result in no <source> tags in
the output. **This is probably not what you want:** you should nearly always set at
least one breakpoint to utilize crops as responsive images. If you set only one
breakpoint, omit the media query:
```blade
@include('wp4laravel::picture', [
    'picture' => $post->thumbnail,
    'breakpoints' => ['header_desktop'],
])
```

Note that using multiple \<source\>-tags requires the use of media queries, so while the following example will generate output and not crash, the [W3 Validator](https://validator.w3.org/) will throw an error on your generated HTML. Don't do this.
```blade
@include('wp4laravel::picture', [
    'picture' => $post->thumbnail,
    'breakpoints' => ['header_desktop', 'header_mobile'],
])
```

### Using the MenuBuilder to construct menus
WP4Laravel supplies a MenuBuilder utility class that can calculate the correct menu for you. You can use the class in a ViewComposer for example. This class correctly deals with using the custom title of a menu item or the post title when none is set. 

The MenuBuilder class has a single public method `itemsIn($menu)` which returns a Collection of top-level items in the menu. Each entry has an `id` (int), `title` (string), `active` (whether this item should be "selected", boolean) and `url` (string) property. 

This class supports a single level of nesting (two levels in total). Root-level items have a `children` (Collection) property with a list of their immediate child entries. Additionally, a root-level item has a boolean `childActive` property which is true if any of its children have the `active` flag set.

The MenuBuilder requires that your model has a `url` property that contains the canonical URL of an instance of the model.

### Example usage
Add a URL property on your model. For example, when using a custom slug in the URL and a multilanguage-based setup:
```php
use App\Models\Traits\Translatable;

class Post extends \Corcel\Post
{
    protected $postType = 'post';
    protected $urlScope = 'nieuws';

    /**
     * Full URL to a post object
     * @return string
     */
    public function getUrlAttribute()
    {
        $url = '/' . $this->slug;

        // Prepend URL scope
        if (!empty($this->urlScope)) {
            $url = '/' . $this->urlScope . $url;
        }

        // Prepend the language if it's not the default language
        if (!empty($this->language) && $this->language !== config('app.supported-locales')[0]) {
            $url = '/' . $this->language . $url;
        }

        return $url;
    }
}

```

Add a view:
```blade
<nav>
    <ul class=menu>
        @foreach ($menu as $item)
            <li>
                <a class="{{ $item->active || $item->childActive ? 'active' : '' }}" 
                    href="{{ $item->url }}">
                    {{ $item->title }}
                </a>

                <ul class=submenu>
                    @foreach ($item->children as $child)
                        <li>
                            <a href="{{ $child->url }}" class="{{ $child->active ? 'active' : '' }}">
                                {{ $child->title }}
                            </a>
                        </li>
                    @endforeach
                </ul>
            </li>
        @endforeach
    </ul>
</nav>
```

Add a ViewComposer for that view:
```php
<?php

namespace App\Http\ViewComposers;

use Corcel\Menu;
use Illuminate\Http\Request;
use Illuminate\View\View;
use WP4Laravel\MenuBuilder;

class Navigation
{
    private $builder;

    public function __construct(MenuBuilder $builder)
    {
        $this->builder = $builder;
    }

    public function compose(View $view)
    {
    	$menu = Menu::slug('main-menu')->first();
        $view->with('menu', $this->builder->itemsIn($menu));
    }
}
```

