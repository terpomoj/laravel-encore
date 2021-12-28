<p align="center">
  <h1 align="center">Laravel Encore</h1>
  <p align="center">
    <a href="https://packagist.org/packages/terpomoj/laravel-encore">
      <img alt="Latest version on Packagist" src="https://img.shields.io/packagist/v/terpomoj/laravel-encore.svg" />
    </a>
    &nbsp;
    <a href="https://packagist.org/packages/terpomoj/laravel-encore">
      <img alt="Total Downloads" src="https://img.shields.io/packagist/dt/terpomoj/laravel-encore.svg" />
    </a>
    &nbsp;
    <a href="https://github.com/terpomoj/laravel-encore/actions">
      <img alt="Build" src="https://github.com/terpomoj/laravel-encore/workflows/CI/badge.svg" />
    </a>
  </p>
</p>

This package helps to integrate [Webpack Encore](https://symfony.com/doc/current/frontend/encore) with Laravel.

> This package was originally authored by [@innocenzi](https://github.com/innocenzi). As they claimed to not going to maintain it anymore, I decided to fork it and continue to provide support for it.

## Installation

You can install the package via composer:

```bash
composer require terpomoj/laravel-encore
```

## Installing Encore

### Manually

Remove `laravel-mix` and add `@symfony/webpack-encore`.

```bash
npm remove laravel-mix
npm install @symfony/webpack-encore --dev
```

Remove your `webpack.mix.js` and create a `webpack.config.js`. Here is an example:

<!-- prettier-ignore -->
```js
const Encore = require('@symfony/webpack-encore');

if (!Encore.isRuntimeEnvironmentConfigured()) {
  Encore.configureRuntimeEnvironment(process.env.NODE_ENV || 'dev');
}

Encore
  .setOutputPath('public/build/')
  .setPublicPath('/build')
  .addEntry('app', './resources/js/app.js')
  .splitEntryChunks()
  .enableSingleRuntimeChunk()
  .enablePostCssLoader()
  .cleanupOutputBeforeBuild()
  .enableSourceMaps(!Encore.isProduction())
  .enableVersioning(Encore.isProduction());

module.exports = Encore.getWebpackConfig();
```

Make sure `resources/js/app.js` exists. Ideally, it should import your CSS as well, but if you don't want to, you can add [`addStyleEntry`](https://symfony.com/doc/current/frontend/encore/simple-example.html#compiling-only-a-css-file) to your Encore configuration.

```js
// resources/js/app.js
import '../css/app.css';
```

Make sure you add `public/build/` (or whatever output path you set) to your `.gitignore`.

Last, but not least, you should replace the scripts in your `package.json` with the following:

```json5
// package.json
{
  "scripts": {
    "dev-server": "encore dev-server",
    "dev": "encore dev",
    "watch": "encore dev --watch",
    "build": "encore production --progress"
  }
}
```

## Usage

In your blade components, use the `@encoreStyles` and `@encoreScripts` directives to include the assets generated by Encore.

```blade
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Laravel</title>
    <!-- Include assets -->
    @styles
    @scripts
  </head>
  <body>
    Hello.
  </body>
</html>
```

By default, it will look for the `app` entries, but you can change them by passing an entry name in each directive.

```blade
@encoreStyles('app')
@encoreScripts('admin')
```

If you used [static assets](https://symfony.com/doc/current/frontend/encore/copy-files.html), you can use `Encore::asset('build/path/to/your/asset.png')` to include it. Under the hood, it's just a mapping to the [manifest.json](https://symfony.com/doc/current/frontend/encore/versioning.html).

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
