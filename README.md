<p align="center"><a href="https://valkyrja.io" target="_blank">
    <img src="https://raw.githubusercontent.com/valkyrjaio/art/refs/heads/master/long-banner/orange/php.png" width="100%">
</a></p>

# Valkyrja RoadRunner

RoadRunner persistent worker entry point for the [Valkyrja Framework](https://www.valkyrja.io).

About
-----

> This repository provides the RoadRunner persistent worker entry point for the Valkyrja Framework.

Bootstraps the application once at startup, then dispatches every incoming request to an
isolated child container — so request state never bleeds between requests.

<p>
    <a href="https://packagist.org/packages/valkyrja/roadrunner"><img src="https://poser.pugx.org/valkyrja/roadrunner/require/php" alt="PHP Version Require"></a>
    <a href="https://packagist.org/packages/valkyrja/roadrunner"><img src="https://poser.pugx.org/valkyrja/roadrunner/v" alt="Latest Stable Version"></a>
    <a href="https://packagist.org/packages/valkyrja/roadrunner"><img src="https://poser.pugx.org/valkyrja/roadrunner/license" alt="License"></a>
    <!-- <a href="https://packagist.org/packages/valkyrja/roadrunner"><img src="https://poser.pugx.org/valkyrja/roadrunner/downloads" alt="Total Downloads"></a>-->
    <a href="https://scrutinizer-ci.com/g/valkyrjaio/roadrunner/?branch=master"><img src="https://scrutinizer-ci.com/g/valkyrjaio/roadrunner/badges/quality-score.png?b=master" alt="Scrutinizer"></a>
    <a href="https://coveralls.io/github/valkyrjaio/roadrunner?branch=master"><img src="https://coveralls.io/repos/github/valkyrjaio/roadrunner/badge.svg?branch=master" alt="Coverage Status" /></a>
    <a href="https://shepherd.dev/github/valkyrjaio/roadrunner"><img src="https://shepherd.dev/github/valkyrjaio/roadrunner/coverage.svg" alt="Psalm Shepherd" /></a>
    <a href="https://sonarcloud.io/summary/new_code?id=valkyrjaio_roadrunner"><img src="https://sonarcloud.io/api/project_badges/measure?project=valkyrjaio_roadrunner&metric=sqale_rating" alt="Maintainability Rating" /></a>
</p>

Build Status
------------

<table>
    <tbody>
        <tr>
            <td>Linting</td>
            <td>
                <a href="https://github.com/valkyrjaio/roadrunner/actions/workflows/phpcodesniffer.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/roadrunner/actions/workflows/phpcodesniffer.yml/badge.svg?branch=master" alt="PHP Code Sniffer Build Status"></a>
            </td>
            <td>
                <a href="https://github.com/valkyrjaio/roadrunner/actions/workflows/phpcsfixer.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/roadrunner/actions/workflows/phpcsfixer.yml/badge.svg?branch=master" alt="PHP CS Fixer Build Status"></a>
            </td>
        </tr>
        <tr>
            <td>Coding Rules</td>
            <td>
                <a href="https://github.com/valkyrjaio/roadrunner/actions/workflows/phparkitect.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/roadrunner/actions/workflows/phparkitect.yml/badge.svg?branch=master" alt="PHPArkitect Build Status"></a>
            </td>
            <td>
                <a href="https://github.com/valkyrjaio/roadrunner/actions/workflows/rector.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/roadrunner/actions/workflows/rector.yml/badge.svg?branch=master" alt="Rector Build Status"></a>
            </td>
        </tr>
        <tr>
            <td>Static Analysis</td>
            <td>
                <a href="https://github.com/valkyrjaio/roadrunner/actions/workflows/phpstan.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/roadrunner/actions/workflows/phpstan.yml/badge.svg?branch=master" alt="PHPStan Build Status"></a>
            </td>
            <td>
                <a href="https://github.com/valkyrjaio/roadrunner/actions/workflows/psalm.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/roadrunner/actions/workflows/psalm.yml/badge.svg?branch=master" alt="Psalm Build Status"></a>
            </td>
        </tr>
        <tr>
            <td>Testing</td>
            <td>
                <a href="https://github.com/valkyrjaio/roadrunner/actions/workflows/phpunit.yml?query=branch%3Amaster"><img src="https://github.com/valkyrjaio/roadrunner/actions/workflows/phpunit.yml/badge.svg?branch=master" alt="PHPUnit Build Status"></a>
            </td>
            <td></td>
        </tr>
    </tbody>
</table>
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
