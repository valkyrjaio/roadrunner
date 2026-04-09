<p align="center"><a href="https://valkyrja.io" target="_blank">
    <img src="https://raw.githubusercontent.com/valkyrjaio/art/refs/heads/master/full-logo/orange/php.png" width="400">
</a></p>

# Valkyrja RoadRunner

RoadRunner persistent worker entry point for the [Valkyrja Framework](https://www.valkyrja.io).

About
-----

> This repository provides the RoadRunner persistent worker entry point for the Valkyrja Framework.

Bootstraps the application once at startup, then dispatches every incoming request to an
isolated child container — so request state never bleeds between requests.

## Installation

```bash
composer require valkyrja/roadrunner
```

Requires [RoadRunner](https://docs.roadrunner.dev/docs/php-worker/worker) with the HTTP plugin
(`spiral/roadrunner-http ^4.1.0`).

## Usage

```php
// app/public/index.php
use Valkyrja\Application\Data\HttpConfig;
use Valkyrja\RoadRunner\RoadRunnerHttp;

RoadRunnerHttp::run(new HttpConfig(
    dir: __DIR__ . '/..',
));
```

`run()` bootstraps the application once when the worker process starts, then
enters the RoadRunner request loop. Each request is handled in an isolated child
container so state never bleeds between requests.

## Customising Bootstrap

Override `bootstrapParentServices()` to force-resolve services that are
expensive to create and safe to share across requests:

```php
use Valkyrja\Application\Kernel\Contract\ApplicationContract;
use Valkyrja\Http\Routing\Collection\Contract\CollectionContract;
use Valkyrja\RoadRunner\RoadRunnerHttp;

class App extends RoadRunnerHttp
{
    protected static function bootstrapParentServices(ApplicationContract $app): void
    {
        $container = $app->getContainer();
        $container->getSingleton(CollectionContract::class);
        $container->getSingleton(MyExpensiveSharedService::class);
    }
}
```

## Worker Lifecycle

See the [Valkyrja Framework README](https://github.com/valkyrjaio/valkyrja) for
a full explanation of the persistent worker lifecycle, the child container
isolation model, and configuration options.

## License

MIT