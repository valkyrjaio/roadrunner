<p align="center"><a href="https://valkyrja.io" target="_blank">
    <img src="https://raw.githubusercontent.com/valkyrjaio/art/refs/heads/master/long-banner/orange/php.png" width="100%">
</a></p>

# Valkyrja RoadRunner

RoadRunner persistent worker entry point for the [Valkyrja][Valkyrja url]
PHP framework.

This integration bootstraps the Valkyrja application once at worker startup,
then dispatches every incoming request to an isolated child container so
request state never bleeds between requests. The result is the performance
benefit of a persistent process without the state-contamination risks of
naive long-running PHP.

<p>
    <a href="https://packagist.org/packages/valkyrja/roadrunner"><img src="https://poser.pugx.org/valkyrja/roadrunner/require/php" alt="PHP Version Require"></a>
    <a href="https://packagist.org/packages/valkyrja/roadrunner"><img src="https://poser.pugx.org/valkyrja/roadrunner/v" alt="Latest Stable Version"></a>
    <a href="https://packagist.org/packages/valkyrja/roadrunner"><img src="https://poser.pugx.org/valkyrja/roadrunner/license" alt="License"></a>
    <a href="https://github.com/valkyrjaio/valkyrja-roadrunner-php/actions/workflows/ci.yml?query=branch%3A26.x"><img src="https://github.com/valkyrjaio/valkyrja-roadrunner-php/actions/workflows/ci.yml/badge.svg?branch=26.x" alt="CI Status"></a>
    <a href="https://scrutinizer-ci.com/g/valkyrjaio/valkyrja-roadrunner-php/?branch=26.x"><img src="https://scrutinizer-ci.com/g/valkyrjaio/valkyrja-roadrunner-php/badges/quality-score.png?b=26.x" alt="Scrutinizer"></a>
    <a href="https://coveralls.io/github/valkyrjaio/valkyrja-roadrunner-php?branch=26.x"><img src="https://coveralls.io/repos/github/valkyrjaio/valkyrja-roadrunner-php/badge.svg?branch=26.x" alt="Coverage Status" /></a>
    <a href="https://shepherd.dev/github/valkyrjaio/valkyrja-roadrunner-php"><img src="https://shepherd.dev/github/valkyrjaio/valkyrja-roadrunner-php/coverage.svg" alt="Psalm Shepherd" /></a>
    <a href="https://sonarcloud.io/summary/new_code?id=valkyrjaio_roadrunner"><img src="https://sonarcloud.io/api/project_badges/measure?project=valkyrjaio_roadrunner&metric=sqale_rating" alt="Maintainability Rating" /></a>
</p>

Requirements
------------

- PHP 8.4+
- [RoadRunner][roadrunner docs url] with the HTTP plugin
  (`spiral/roadrunner-http ^4.1.0`)
- An existing [Valkyrja][framework url] application

Installation
------------

```
composer require valkyrja/roadrunner
```

Usage
-----

Wire the RoadRunner entry point into your application's front controller:

```
// app/public/index.php
use Valkyrja\Application\Data\HttpConfig;
use Valkyrja\RoadRunner\RoadRunnerHttp;

RoadRunnerHttp::run(new HttpConfig(
    dir: __DIR__ . '/..',
));
```

`run()` bootstraps the application once when the worker process starts, then
enters the RoadRunner request loop. Each request is handled in an isolated
child container so state never bleeds between requests.

### Customizing Bootstrap

Override `bootstrapParentServices()` to force-resolve services that are
expensive to create and safe to share across requests:

```
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

Worker Lifecycle
----------------

See the [Valkyrja framework repository][framework url] for a full explanation
of the persistent worker lifecycle, the child container isolation model, and
configuration options.

Related Integrations
--------------------

Other persistent-worker runtime integrations for Valkyrja PHP:

- [**OpenSwoole**][openswoole url] — persistent worker via the OpenSwoole
  extension
- [**FrankenPHP**][frankenphp url] — persistent worker via the FrankenPHP
  embedded runtime

Contributing
------------

See [`CONTRIBUTING.md`][contributing url] for the submission process and
[`VOCABULARY.md`][vocabulary url] for the terminology used across Valkyrja.

Security Issues
---------------

If you discover a security vulnerability, please follow our
[disclosure procedure][security vulnerabilities url].

License
-------

Licensed under the [MIT license][MIT license url]. See
[`LICENSE.md`](./LICENSE.md).

[Valkyrja url]: https://valkyrja.io

[framework url]: https://github.com/valkyrjaio/valkyrja-php

[roadrunner docs url]: https://docs.roadrunner.dev/docs/php-worker/worker

[openswoole url]: https://github.com/valkyrjaio/valkyrja-openswoole-php

[frankenphp url]: https://github.com/valkyrjaio/valkyrja-frankenphp-php

[contributing url]: https://github.com/valkyrjaio/.github/blob/master/CONTRIBUTING.md

[vocabulary url]: https://github.com/valkyrjaio/.github/blob/master/VOCABULARY.md

[security vulnerabilities url]: https://github.com/valkyrjaio/.github/blob/master/SECURITY.md

[MIT license url]: https://opensource.org/licenses/MIT
