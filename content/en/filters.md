---
version: 0.1.0
title: Filters
subtitle: Make presets of your favorite queries
description: "Make presets of your favorite queries"
position: 8
category: Core concepts
---

Sometimes you need to search for specific records in tons of data, filters will make your life easier.

## Creating a filter

Open your terminal and write the following:

```bash
php artisan nebula:filter IsCompletedFilter
```

In this example, we will create a filter where we can search for completed records (based on a boolean field.) Now we should see the following:

```php
// app/Nebula/Filters/IsCompletedFilter.php

use Illuminate\Database\Eloquent\Builder;
use Illuminate\Http\Request;
use Larsklopstra\Nebula\Contracts\NebulaFilter;

class IsCompletedFilter extends NebulaFilter
{
    public function build(
        Builder $query,
        Request $request,
    ): Builder {
        return $query->latest();
    }
}
```

We are going to edit the display label and query to make more sense. By default the label will be "Is completed" and the query will sort the data.

```php
// app/Nebula/Filters/IsCompletedFilter.php

use Illuminate\Database\Eloquent\Builder;
use Illuminate\Http\Request;
use Larsklopstra\Nebula\Contracts\NebulaFilter;

class IsCompletedFilter extends NebulaFilter
{
    public function label()
    {
        return 'Show completed';
    }

    public function build(
        Builder $query,
        Request $request,
    ): Builder {
        return $query
            ->whereIsCompleted(true)
            ->latest();
    }
}
```

## Adding a filter to your Resource

You can add a filter to your resource by adding it to the `filters` methods which return an array of filter:

```php
// app/Nebula/Resources/ ...

public function filters(): array
{
    return [
        new WhereNameStartsWithAFilter,
        new IsCompletedFilter,
    ];
}
```
