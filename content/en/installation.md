---
version: 0.1.0
title: Installation
description: "Nebula is a Laravel package and requires Composer, PHP 7.3 and Laravel 8 to be installed."
position: 3
category: Getting started
fullscreen: true
---

## Requirements

Nebula is a Laravel package and requires Composer, PHP 7.3 and Laravel 8 to be installed.

## Installing Nebula

First, open an existing Laravel project or create a new and install the package using Composer:

```bash
composer require larsklopstra/nebula
```

After you have done that, publish the assets and configuration via the command line:

```bash
php artisan nebula:install
```

That is it, you should be prompted with a welcome screen if you visit `/nebula`.
