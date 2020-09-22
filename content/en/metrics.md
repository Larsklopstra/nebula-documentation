---
version: 0.1.0
title: Metrics
subtitle: Learn more about your data
description: "Learn more about your data"
position: 10
category: Core concepts
---

Metrics are useful cards which display data of resources, they can be found in standalone dashboards and at resource overviews.

## Creating a value metric

For this example we are going to make a user count metric, to create the metric, open up the CLI and type the following:

```bash
php artisan nebula:value UserCountMetric
```

You will now end up with:

```php
// app/Nebula/Metrics/UserCountMetric.php

namespace App\Nebula\Metrics;

use Larsklopstra\Nebula\Metrics\ValueMetric;

class UserCountMetric extends ValueMetric
{
    public function calculate()
    {
    }

    public function label()
    {
    }
}
```

<alert type="info">

Notice how we are suffixing the metric, please follow this convention. Nebula uses the class name and type to cache the metric: `UserCountMetric.valueMetric`.

</alert>

## Measurements

Now we have the basics, let us explore some methods. The value metric offers a default set of methods we can use to compute data:

- average: Calculate the average column of a model.
- count: Count the records of a model.
- sum: Sum a column of a model.
- max: Get the maximum of a column from a model.
- min: Get the minimum of a column from a model.

All of the results will be relative to last month. (This is how the percentage is calculated.)

```php
// app/Nebula/Metrics/UserCountMetric.php

namespace App\Nebula\Metrics;

use Larsklopstra\Nebula\Metrics\ValueMetric;
use App\Models\User;

class UserCountMetric extends ValueMetric
{
    public function calculate()
    {
        $this->count(User:class);
    }

    public function label()
    {
    }
}
```

## Caching

We do not want to run intensive queries every time we refresh the page. To avoid the database load, we cache the results. By default, Nebula caches the data for one hour. You can override this behavior by overriding the `cacheFor` method:

```php
// app/Nebula/Metrics/UserCountMetric.php

namespace App\Nebula\Metrics;

use Larsklopstra\Nebula\Metrics\ValueMetric;

class UserCountMetric extends ValueMetric
{
    public function calculate()
    {
        $this->count(User:class);
    }

    public function label()
    {
    }

    public function cacheFor()
    {
        return now()->addHours(8);
    }
}
```

## Labels

We are almost there, we are just missing some labels that tell us what the data is about. You are free to add a prefix, suffix, and label. But for this example, a label will be perfect.

```php
// app/Nebula/Metrics/UserCountMetric.php

namespace App\Nebula\Metrics;

use Larsklopstra\Nebula\Metrics\ValueMetric;

class UserCountMetric extends ValueMetric
{
    public function calculate()
    {
        $this->count(User:class);
    }

    public function label()
    {
        return 'New users';
    }

    // Will add a Euro sign before the count
    public function prefix()
    {
        return '€';
    }

    // Will add a dollar sign behind the count
    public function suffix()
    {
        return '$';
    }

    // Determine how much space your card should take
    public function colSpan(): int
    {
        return 4; // out of 12
    }

    public function cacheFor()
    {
        return now()->addHours(8);
    }
}
```

## Registering a metric

You can register (the same) metrics in both dashboards and resource overviews. We are going to add it to our user resource in this example:

```php
// app/Nebula/Resources/UserResource.php

namespace App\Nebula\Resources;

use App\Nebula\Metrics\UserMetric;
use Larsklopstra\Nebula\Contracts\NebulaResource;

class UserResource extends NebulaResource
{
    // ...

    public function metrics(): array
    {
        return [
            new UserCountMetric,
        ];
    }
}
```

## Wrapping up

We are done with the basics, you should end up with this card now:

![Value metric](/images/metrics/value.png)
