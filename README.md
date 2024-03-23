# LPD - Just Have a Look 

```json
"autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/",
            "Onex\\Todo\\": "packages/onextodo/src/",
            "Onex\\Valiper\\": "packages/onex-validation-helper/src/",
            "Onex\\Responsestructure\\": "packages/onex-response-structure/src/",
            "Onex\\Sysinfo\\": "packages/onex-sysinfo/src/",
            "Onex\\Utility\\": "packages/onex-utility/src/",
            "CreativeSyntax\\LogViewer\\": "packages/creative-syntax-log-viewer/src/",
            "CreativeSyntax\\ArtisanUi\\": "packages/creative-syntax-artisan-ui/src/",
            "CreativeSyntax\\VisitorInfo\\": "packages/creative-syntax-visitor-info/src/"
        }
    },
```
```
composer dump-autoload
```

```
Arindam\GsheetAppScript\GsheetAppScriptServiceProvider::class,
'GsheetAppScript' => Arindam\GsheetAppScript\Gsheet\GsheetAppScriptClassFacade::class,
```

```json
{
    "name": "creative-syntax/artisan-ui",
    "description": "A simple laravel package for artisan commands with user interface",
    "type": "laravel-package",
    "license": "MIT",
    "keywords": [
        "artisan",
        "artisan-commands",
        "laravel-commands",
        "artisan-user-interface",
        "artisan-ui",
        "artisan-gui",
        "artisan-web",
        "web-artisan",
        "creative-syntax",
        "onexcrm",
        "arindam-roy"
    ],
    "authors": [
        {
            "name": "Arindam Roy",
            "email": "arindam.roy.developer@gmail.com"
        }
    ],
    "minimum-stability": "stable",
    "require": {
        "php": ">=5.4.0",
        "illuminate/support": "5.*|^6.0|^7.0|^8.0|^9.0|^10.0",
        "laravel/framework": "^5.0|^6.0|^7.0|^8.0|^9.0|^10.0"
    },
    "autoload": {
        "psr-4": {
            "CreativeSyntax\\ArtisanUi\\": "src/"
        }
    },
    "extra": {
        "laravel": {
            "providers": [
                "CreativeSyntax\\ArtisanUi\\CreativeSyntaxArtisanUi"
            ]
        }
    }
}

```

```php
<?php

namespace CreativeSyntax\VisitorInfo;

use Illuminate\Support\ServiceProvider;
use CreativeSyntax\VisitorInfo\Visitor\Visitor;
use Illuminate\Support\Facades\View;
use Illuminate\Contracts\Http\Kernel;
use CreativeSyntax\VisitorInfo\Http\Middleware\RequestLogMiddleware;

class CreativeSyntaxVisitorInfo extends ServiceProvider
{

    /**
     * Register services.
     */
    public function register(): void
    {
        //
    }

    /**
     * Bootstrap services.
     */
    public function boot(Kernel $kernel): void
    {
        $this->app->bind('visitor',function() {
            return new Visitor();
        });

        $this->loadRoutesFrom(__DIR__ . '/routes/web.php');

        $this->loadViewsFrom(__DIR__ . '/resources/views', 'visitor-info');

        $kernel->appendMiddlewareToGroup('web', RequestLogMiddleware::class);

        //$this->loadMigrationsFrom(__DIR__ . '/database/migrations');

        $this->mergeConfigFrom(
            __DIR__ . '/config/visitor-info.php', 'visitor-info'
        );

        $this->publishes([
            __DIR__ . '/config/visitor-info.php' => config_path('visitor-info.php')
        ], 'visitor-info:config');

        $this->publishes([
            __DIR__ .'/database/migrations/creative_syntax_create_visitor_info_table.php' => database_path("/migrations/" . date('Y_m_d_His', time()) . "_create_visitor_info_table.php"),
        ], 'visitor-info:migrations');

        //php artisan vendor:publish --provider="CreativeSyntax\VisitorInfo\CreativeSyntaxVisitorInfo" --force
        //php artisan vendor:publish --tag="visitor-info:config"
        //php artisan vendor:publish --tag="visitor-info:migrations"
    }
}
```

```php
<?php

namespace vicgonvt\Press;

use Illuminate\Support\Facades\Route;
use Illuminate\Support\ServiceProvider;
use vicgonvt\Press\Facades\Press;

class PressBaseServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any package services.
     *
     * @return void
     */
    public function boot()
    {
        if ($this->app->runningInConsole()) {
            $this->registerPublishing();
        }

        $this->registerResources();
    }

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->commands([
            Console\ProcessCommand::class,
        ]);
    }

    /**
     * Register the package resources.
     *
     * @return void
     */
    private function registerResources()
    {
        $this->loadMigrationsFrom(__DIR__.'/../database/migrations');
        $this->loadViewsFrom(__DIR__.'/../resources/views', 'press');

        $this->registerFacades();
        $this->registerRoutes();
        $this->registerFields();
    }

    /**
     * Register the package's publishable resources.
     *
     * @return void
     */
    protected function registerPublishing()
    {
        $this->publishes([
            __DIR__.'/../config/press.php' => config_path('press.php'),
        ], 'press-config');

        $this->publishes([
            __DIR__.'/Console/stubs/PressServiceProvider.stub' => app_path('Providers/PressServiceProvider.php'),
        ], 'press-provider');
    }

    /**
     * Register the package routes.
     *
     * @return void
     */
    protected function registerRoutes()
    {
        Route::group($this->routeConfiguration(), function () {
            $this->loadRoutesFrom(__DIR__.'/../routes/web.php');
        });
    }

    /**
     * Get the Press route group configuration array.
     *
     * @return array
     */
    private function routeConfiguration()
    {
        return [
            'prefix' => Press::path(),
            'namespace' => 'vicgonvt\Press\Http\Controllers',
        ];
    }

    /**
     * Register any bindings to the app.
     *
     * @return void
     */
    protected function registerFacades()
    {
        $this->app->singleton('Press', function ($app) {
            return new \vicgonvt\Press\Press();
        });
    }

    /**
     * Register any default fields to the app.
     *
     * @return void
     */
    private function registerFields()
    {
        Press::fields([
            Fields\Body::class,
            Fields\Date::class,
            Fields\Description::class,
            Fields\Extra::class,
            Fields\Title::class,
        ]);
    }
}
```

```php
<?php

use Illuminate\Support\Facades\Route;

$isRouteEnabled = true;
$routePrefix = 'onex';
$routeName = 'gsheet';
$routeMiddleware = ['web'];

$publishedConfigFilePath = config_path('gsheet-appscript.php');
if (file_exists($publishedConfigFilePath)) {
    $isRouteEnabled = !empty(config('gsheet-appscript.is_route_enabled')) ? config('gsheet-appscript.is_route_enabled') : $isRouteEnabled;
    $routePrefix = !empty(config('gsheet-appscript.route_prefix')) ? config('gsheet-appscript.route_prefix') : $routePrefix;
    $routeName = !empty(config('gsheet-appscript.route_name')) ? config('gsheet-appscript.route_name') : $routeName; 
}

if ($isRouteEnabled) {
    Route::group(['namespace' => 'Arindam\GsheetAppScript\Http\Controllers', 'prefix' => $routePrefix, 'middleware' => $routeMiddleware], function() use($routeName) {
        Route::get($routeName, 'GsheetAppScriptController@index')->name('gsheet-appscript.index');
        Route::post($routeName.'/delete-all', 'GsheetAppScriptController@deleteAll')->name('gsheet-appscript.deleteAll');
        Route::post($routeName.'/save-row', 'GsheetAppScriptController@saveRow')->name('gsheet-appscript.saveRow');
        Route::post($routeName.'/delete-row', 'GsheetAppScriptController@removeRow')->name('gsheet-appscript.deleteRow');
        Route::post($routeName.'/access-login', 'GsheetAppScriptController@accessLogin')->name('gsheet-appscript.accessLogin');
        Route::get($routeName.'/access-off', 'GsheetAppScriptController@accessOff')->name('gsheet-appscript.accessOff');
    });
}
```

