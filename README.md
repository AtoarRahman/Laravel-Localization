# Laravel Localization

- <a href="#installation">Installation</a>
- <a href="#configure-the-locale">Configure The Locale</a>
- <a href="#configure-middleware">Configure Middleware</a>
- <a href="#configure-routes">Configure Routes</a>
- <a href="#defining-translation-strings">Defining Translation Strings</a>
- <a href="#using-translation-strings-as-keys">Using Translation Strings As Keys</a>
- <a href="#retrieving-translation-strings">Retrieving Translation Strings</a>
- <a href="#configure-navigation">Configure Navigation</a>


## Installation

Install the package via composer: `composer create-project laravel/laravel example-app`


### Configure The Locale

The default language for your application is stored in the `config/app.php` configuration file's locale configuration option. You are free to modify this value to suit the needs of your application.

You may modify the default language for a single HTTP request at runtime using the setLocale method provided by the App facade:

```php
use Illuminate\Support\Facades\App;

Route::get('/greeting/{locale}', function ($locale) {
    if (! in_array($locale, ['en', 'bn'])) {
        abort(400);
    }

    App::setLocale($locale);

    //
});
```
You may configure a "fallback language", which will be used when the active language does not contain a given translation string. Like the default language, the fallback language is also configured in the `config/app.php` configuration file:

`'fallback_locale' => 'en',`

### Configure Middleware

You may configure the middleware in the `app/Http/Middleware/Localization.php` file:

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\App;
use Illuminate\Support\Facades\Session;


class Localization
{
    public function handle($request, Closure $next)
    {
        if(Session::has('language')){
            App::setLocale(\Session::get('language'));
        }else{
            Session::put('language','bn');
            App::setLocale('bn');

        }
        return $next($request);
    }
}

```

### Configure Routes

You may configure the route in the `routes/web.php` file:

```php
Route::get('/language/{language}',function($language){
    Session::put('language',$language);
    return redirect()->back();
})->name('language');
```

### Defining Translation Strings

Typically, translation strings are stored in files within the `resources/lang` directory. Within this directory, there should be a subdirectory for each language supported by your application. This is the approach Laravel uses to manage translation strings for built-in Laravel features such as validation error messages:

`resources/lang/en/messages.php`
`resources/lang/bn/messages.php`

For example. English language files return an array of keyed strings. `resources/lang/en/messages.php`:

```php
<?php

return [
    'welcome' => 'Welcome to our application!',
];
```

For example. Bangla language files return an array of keyed strings. `resources/lang/bn/messages.php`:

```php
<?php

return [
    'welcome' => 'আমাদের এপ্লিকেশনে আপনাকে স্বাগতম!',
];
```

### Using Translation Strings As Keys

For applications with a large number of translatable strings, defining every string with a "short key" can become confusing when referencing the keys in your views and it is cumbersome to continually invent keys for every translation string supported by your application.

For this reason, Laravel also provides support for defining translation strings using the "default" translation of the string as the key. Translation files that use translation strings as keys are stored as JSON files in the `resources/lang` directory. For example, if your application has a Spanish translation, you should create a `resources/lang/bn.json` file:

```php
{
    "Welcome to our application!": "আমাদের এপ্লিকেশনে আপনাকে স্বাগতম!"
}
```

### Retrieving Translation Strings

You may retrieve translation strings from your language files to blade files

Blade View For Array Strings

```php
@lang('messages.welcome') or {{ __('messages.welcome') }}

```
Blade View For JSON Strings

```php
@lang('Welcome to our application!') or {{ __('Welcome to our application!') }}
```

### Configure Navigation

Configure navigation for swiching the language

```php
<ul class="navbar-nav ml-auto">
    <li class="nav-item">
        @if(session()->get('language') =='bn')
            <a class="nav-link btn btn-sm" href="{{route('language','en')}}">English</a>
        @else
            <a class="nav-link btn btn-sm" href="{{route('language','bn')}}">বাংলা</a>
        @endif
    </li>
</ul>
```

## License

Laravel Localization is an open-sourced laravel package licensed under the MIT license
