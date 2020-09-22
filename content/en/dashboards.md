---
version: 0.1.0
title: Dashboards
subtitle: All metrics in one spot
description: "All metrics in one spot"
position: 11
category: Core concepts
---

You can create as many dashboards as you want with all the metrics you need.

## Creating a dashboard

For this example we are going to make a user dashboard, to create the dashboard, open up the CLI, and type the following:

```bash
php artisan nebula:dashboard UserDashboard
```

This will result in:

```php
// app/Nebula/Dashboards/UserrDashboard.php

namespace App\Nebula\Dashboards;

use Larsklopstra\Nebula\Contracts\NebulaDashboard;

class UserDashboard extends NebulaDashboard
{
    public function icon()
    {
        return 'trending-up';
    }

    public function metrics(): array
    {
        return [];
    }
}
```

All we have to do now is to change the label, add a metric, and register it in the configuration.

```php
// app/Nebula/Dashboards/UserrDashboard.php

namespace App\Nebula\Dashboards;

use App\Nebula\Metrics\UserCountMetric;
use Larsklopstra\Nebula\Contracts\NebulaDashboard;

class UserDashboard extends NebulaDashboard
{
    public function icon()
    {
        return 'user-group';
    }

    public function metrics(): array
    {
        return [
            new UserCountMetric,
        ];
    }
}
```

## Registering a dashboard

That looks good to me. Now we are going to tell Nebula we would like to use this dashboard:

```php
// config/nebula.php

use App\Nebula\Dashboards\UserDashboard;

return [

    // ...

    'dashboards' => [
        new UserDashboard,
    ],

];
```
