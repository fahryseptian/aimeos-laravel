<a href="https://aimeos.org/">
    <img src="https://aimeos.org/fileadmin/template/icons/logo.png" alt="Aimeos logo" title="Aimeos" align="right" height="60" />
</a>

# Aimeos Laravel package
[![Build Status](https://travis-ci.org/aimeos/aimeos-laravel.svg)](https://travis-ci.org/aimeos/aimeos-laravel)
[![Coverage Status](https://coveralls.io/repos/aimeos/aimeos-laravel/badge.svg?branch=master&service=github)](https://coveralls.io/github/aimeos/aimeos-laravel?branch=master)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/aimeos/aimeos-laravel/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/aimeos/aimeos-laravel/?branch=master)

:star: Star us on GitHub — it helps!

[Aimeos](https://aimeos.org/Laravel) is THE professional, full-featured and
ultra fast e-commerce package for Laravel!  You can install it in your
existing Laravel application within 5 minutes and can adapt, extend, overwrite
and customize anything to your needs.

[![Aimeos Laravel demo](https://aimeos.org/fileadmin/aimeos.org/images/aimeos-github.png)](http://laravel.demo.aimeos.org/)

## Table of content

- [Supported versions](#supported-versions)
- [Basic application](#basic-application)
- [Database](#database)
- [Installation](#installation)
- [Setup](#setup)
- [Admin](#admin)
- [Hints](#hints)
- [License](#license)
- [Links](#links)

## Supported versions

This document is for the Aimeos Laravel package **2018.10 and later**.

- Beta release: 2019.01 (Laravel 5.3 to 5.8)
- LTS release: 2018.10 (Laravel 5.3 to 5.8)

If you want to **upgrade between major versions**, please have a look into the
[upgrade guide](https://aimeos.org/docs/Laravel/Upgrade)!

## Basic application

If you want to set up a new application or test Aimeos, we recommend the
[Aimeos distribution](https://github.com/aimeos/aimeos). It will install a
complete shop system including demo data for a quick start without the need
to follow the steps described in this readme.

The Aimeos Laravel online shop package is a composer based library that can be
installed easiest by using [Composer](https://getcomposer.org). For manual setup
of the application, you need to **install a Laravel skeleton application** if
you don't have one already:

```
composer create-project --prefer-dist laravel/laravel myshop
```

## Database

Make sure that you've **created the database** in advance and added the configuration
to the `.env` file in your application directory. Sometimes, using the .env file makes
problems and you will get exceptions that the connection to the database failed. In that
case, add the database credentials to the **resource/db section of your ./config/shop.php**
file too!

For MySQL, you should change the database charset/collation in your `config/database.php`
file **before the tables are created** to:

```php
'connections' => [
    'mysql' => [
        // ...
        'charset' => 'utf8mb4',
        'collation' => 'utf8mb4_bin',
        // ...
    ]
]
```

If you don't have at least MySQL 5.7 installed, you will probably get an error like

```Specified key was too long; max key length is 767 bytes```

To circumvent this problem, drop the new tables if there have been any created and
change the charset/collation setting to these values before installing Aimeos again:

```php
'connections' => [
    'mysql' => [
        // ...
        'charset' => 'utf8',
        'collation' => 'utf8_bin',
        // ...
    ]
]
```

If you want to use a database server other than MySQL, please have a look into the article about
[supported database servers](https://aimeos.org/docs/Developers/Library/Database_support)
and their specific configuration.

## Installation

Then, add these lines to the composer.json of the **Laravel skeleton application**:

```
    "prefer-stable": true,
    "minimum-stability": "dev",
    "require": {
        "aimeos/aimeos-laravel": "~2018.10",
        ...
    },
    "scripts": {
        "post-update-cmd": [
            "@php artisan vendor:publish --tag=public --force",
            "@php artisan migrate"
        ],
        ...
    }
```

Afterwards, install the Aimeos shop package using

`composer update`

Next, the Aimeos provider class must be added to the `providers` array of the
`config/app.php` file so the application and Laravel command task will work:

```php
return array(
    'providers' => array(
        /*
         * Package Service Providers...
         */
        Aimeos\Shop\ShopServiceProvider::class,

        /*
         * Application Service Providers...
         */
    ),
);
```

In the last step you must now execute these artisan commands to get a working
or updated Aimeos installation:

```
php artisan vendor:publish --all
php artisan migrate
php artisan aimeos:setup --option=setup/default/demo:1
php artisan aimeos:cache
```

In a production environment or if you don't want that the demo data gets
installed, leave out the `--option=setup/default/demo:1` option.

## Setup

To see all components and get everything working, you also need to create your
main Blade template in `resources/views/app.blade.php`. This is a working
example using the [Twitter bootstrap CSS framework](http://getbootstrap.com/):

```html
<!DOCTYPE html>
<html lang="en" class="no-js">
<head>
	<meta charset="utf-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content="width=device-width, initial-scale=1">
@yield('aimeos_header')
	<title>Aimeos on Laravel</title>

	<link type="text/css" rel="stylesheet" href='https://fonts.googleapis.com/css?family=Roboto:400,300'>
    <link type="text/css" rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
@yield('aimeos_styles')
</head>
<body>
	<nav class="navbar navbar-default">
	<div class="container-fluid">
			<div class="navbar-header">
				<button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
					<span class="sr-only">Toggle Navigation</span>
					<span class="icon-bar"></span>
					<span class="icon-bar"></span>
					<span class="icon-bar"></span>
				</button>
				<a class="navbar-brand" href="#">Laravel</a>
			</div>

			<div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
				<ul class="nav navbar-nav">
					<li><a href="/">Home</a></li>
				</ul>

				<div class="nav navbar-nav navbar-right">
@yield('aimeos_head')
				</div>
			</div>
		</div>
	</nav>
    <div class="col-xs-12">
@yield('aimeos_nav')
@yield('aimeos_stage')
@yield('aimeos_body')
@yield('aimeos_aside')
@yield('content')
	</div>

	<!-- Scripts -->
	<script type="text/javascript" src="https://code.jquery.com/jquery-3.1.1.min.js"></script>
	<script type="text/javascript" src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
@yield('aimeos_scripts')
	</body>
</html>
```

Afterwards, you should clear the Laravel cache files. Otherwise, you might get
an exception due to old cached data.

```php artisan cache:clear```

To reference images correctly, you have to adapt your `.env` file and set the `APP_URL`
to your real URL, e.g.

```APP_URL=http://127.0.0.1:8000```

Then, you should be able to call the catalog list page in your browser. For a
quick start, you can use the integrated web server that is available since PHP 5.4.
Simply execute this command in the base directory of your application:

```php artisan serve```

Point your browser to the list page of the shop using:

http://127.0.0.1:8000/index.php/list

**Note:** Integrating the Aimeos package adds some routes like `/list` or `/admin` to your
Laravel installation but the **home page stays untouched!**

**Caution:** CSRF protection is enabled by default but for the ```/confirm``` and ```/update```
routes, you may have to [disable CSRF](http://laravel.com/docs/5.1/routing#csrf-excluding-uris)
if one of the payment providers is sending data via POST requests.

## Admin

To use the admin interface, you have to set up Laravel authentication first:

```php artisan make:auth```

For more information, please follow the Laravel documentation:
* [Laravel 5.8](https://laravel.com/docs/5.8/authentication)
* [Laravel 5.7](https://laravel.com/docs/5.7/authentication)
* [Laravel 5.6](https://laravel.com/docs/5.6/authentication)
* [Laravel 5.5](https://laravel.com/docs/5.5/authentication)
* [Laravel 5.4](https://laravel.com/docs/5.4/authentication)
* [Laravel 5.3](https://laravel.com/docs/5.3/authentication)

Test if your authentication setup works before you continue. Create an admin account
for your Laravel application so you will be able to log into the Aimeos admin interface:

```php artisan aimeos:account --admin <email>```

The e-mail address is the user name for login and the account will work for the
frontend too. To protect the new account, the command will ask you for a password.
The same command can create limited accounts by using "--editor" or "--api" instead of
"--admin". If you use "--super" the account will have access to all sites.

As a last step, you need to extend the `boot()` method of your
`App\Providers\AuthServiceProvider` class and add the lines to define how
authorization for "admin" is checked in `app/Providers/AuthServiceProvider.php`:

```php
public function boot()
{
    // Keep the lines before

    Gate::define('admin', function($user, $class, $roles) {
        if( isset( $user->superuser ) && $user->superuser ) {
            return true;
        }
        return app( '\Aimeos\Shop\Base\Support' )->checkUserGroup( $user, $roles );
    });
}
```

If your `./public` directory isn't writable by your web server, you have to create these
directories:
```
mkdir public/files public/preview public/uploads
chmod 777 public/files public/preview public/uploads
```

In a production environment, you should be more specific about the granted permissions!
If you've still started the internal PHP web server (`php artisan serve`)
you should now open this URL in your browser:

http://127.0.0.1:8000/index.php/admin

Enter the e-mail address and the password of the newly created user and press "Login".
If you don't get redirected to the admin interface (that depends on the authentication
code you've created according to the Laravel documentation), point your browser to the
`/admin` URL again.

**Caution:** Make sure that you aren't already logged in as a non-admin user! In this
case, login won't work because Laravel requires to log out first.

## Hints

To simplify development, you should configure to use no content cache. You can
do this in the `config/shop.php` file of your Laravel application by adding
these lines at the bottom:

```php
    'madmin' => array(
        'cache' => array(
            'manager' => array(
                'name' => 'None',
            ),
        ),
    ),
```

## License

The Aimeos Laravel package is licensed under the terms of the MIT license and
is available for free.

## Links

* [Web site](https://aimeos.org/Laravel)
* [Documentation](https://aimeos.org/docs/Laravel)
* [Forum](https://aimeos.org/help/laravel-package-f18/)
* [Issue tracker](https://github.com/aimeos/aimeos-laravel/issues)
* [Composer packages](https://packagist.org/packages/aimeos/aimeos-laravel)
* [Source code](https://github.com/aimeos/aimeos-laravel)
