---
version: 0.1.0
title: Authorization
subtitle: Control who is allowed to view the administration panel
description: "Control who is allowed to view the administration panel"
category: Core concepts
position: 6
---

Nebula authorizes requests with the middleware set in `auth_strategy` property, this property can be found in `config/nebula.php`. The given middleware will be used all Nebula routes to prevent unauthorized actors from viewing your dashboard.

## Default strategies

Out of the box, Nebula offers two authorization strategies to validate if you are allowed to view the administration panel. by default, `NebulaIPAuthStrategy` is used.

## IP Auth Strategy

You can restrict access to the dashboard by only allowing certain IP addresses, this might be useful if you only want to allow access from a certain location.

To set this up you have to edit the Nebula configuration file like so:

```php
// config/nebula.php

use Larsklopstra\Nebula\Http\Middleware\NebulaIPAuthStrategy;

return [

    // ...

    'auth_strategy' => NebulaIPAuthStrategy::class,

    'allowed_ips' => [
        '127.0.0.1',
    ],

];
```

## Email Auth Strategy

The second option, Email Auth Strategy is a more flexible method of authorizing compared to the IP Auth Strategy. This strategy requires the user to be authenticated before they visit `/nebula`, Nebula does not handle login, registration, and redirects. It will simply throw a 404 page not found exception. This is because you do not want the user to know you are using a (separate) administration panel like Nebula.

To set this up you have to edit the Nebula configuration file like so:

```php
// config/nebula.php

use Larsklopstra\Nebula\Http\Middleware\NebulaEmailAuthStrategy;

return [

    // ...

    'auth_strategy' => NebulaEmailAuthStrategy::class,

    'allowed_emails' => [
        'admin@example.com',
    ],

];
```

## Creating your own

Every application is different and sometimes the defaults will not fulfill your needs. As explained before, you can easily implement your own strategy by adding your own middleware.

```php
// config/nebula.php

use App\Http\Middleware\MyMiddleware;

return [

    // ...

    'auth_strategy' => MyMiddleware::class,

];
```
