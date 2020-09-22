---
version: 0.1.0
title: Configuration
description: "Configure the Nebula package"
position: 5
category: Getting started
fullscreen: true
---

## Defaults

By default your Nebula configuration will look like this:

```php
// config/nebula.php

return [

    'name' => 'Nebula',

    'prefix' => '/nebula',

    'auth_strategy' => NebulaIPAuthStrategy::class,

    'allowed_ips' => [
        '127.0.0.1',
    ],

    'allowed_emails' => [
        // 'admin@example.com',
    ],

    'resources' => [
        // new UserResource,
    ],

    'dashboards' => [
        // new UserDashboard,
    ],

];
```

You can change the dashboard display name and dashboard URL by editing `name` and `prefix` to your desired one.

## Learn more about

- [Auth strategies](/authorization)
- [Resources](/resources)
- [Dashboards](/dashboards)
