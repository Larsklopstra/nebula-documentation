---
version: 0.1.0
title: Resources
subtitle: Create CRUD interfaces in seconds
description: "Create CRUD interfaces in seconds"
position: 7
category: Core concepts
---

Resources are a key part of Nebula, they easily allow you to scaffold CRUD interfaces through a CLI. You can visit a resource by going to `/nebula/resources/{resource_plural}`.

## Creating a resource

For this example we will go to create a user resource, Creating a resource is as simple as typing:

```bash
php artisan nebula:resource UserResource
```

This will create a Nebula Resource inside `App\Nebula\Resources`.

<alert type="info">

Notice how we are naming the resource, user is singular and should have the same name as your model, the suffix indicates it is a resource. Please stick to this convention as we use this to obtain the model and generate names.

</alert>

## Register a resource

To register a resource append your resource in `config/nebula.php`:

```php
// config/nebula.php

use App\Nebula\Resources\UserResource;

return [

    // ...

    'resources' => [
        new UserResource,
    ],
];
```

You will now see your resource show up on the menu. You are free to override the icon, singular, and, plural name. We are using [Heroicons](https://heroicons.dev/) made by [@steveschoger](https://twitter.com/steveschoger).

```php
// app/Nebula/Resources/UserResource.php

use Larsklopstra\Nebula\Contracts\NebulaResource;

class UserResource extends NebulaResource
{
    // ...

    public function icon()
    {
        return 'user-group';
    }

    public function singularName()
    {
        return 'user';
    }

    public function pluralName()
    {
        return 'users';
    }
}

```

## Displaying table columns

When you visit `/nebula/resources/users` you will either see an empty state which asks you to create a user or an awkward-looking card. To fix this you will have to specify which columns you want to display.

Go to your resource and specify the columns:

```php
// app/Nebula/Resources/UserResource.php

class UserResource extends NebulaResource
{
    // ...

    public function columns(): array
    {
        return [
            InputField::make('name'),
            InputField::make('email'),
        ];
    }
}
```

You should now see the columns appear once you have refreshed the page.

## Adding fields

Great, we can now see users. Now it is time to create some data. Nebula offers three methods to display fields:

- `fields` will display fields for the details, create, and edit views.
- `createFields` this will display fields for the create view, if it is not explicitly set it will inherit from `fields`.
- `editFields` this will display fields for the edit view, if it is not explicitly set it will inherit from `fields`.

For this example, we will just stick with fields:

```php
// app/Nebula/Resources/UserResource.php

use Larsklopstra\Nebula\Contracts\NebulaResource;
use Larsklopstra\Nebula\Fields\InputField;

class UserResource extends NebulaResource
{
    // ...

    public function fields(): array
    {
        return [
            InputField::make('name'),
            InputField::make('email'),
        ];
    }
}
```

<alert type="info">

[Learn more](/fields) about fields and how to create your own.

</alert>

## Adding filters

Let us create a simple filter and add it to our user resource.

```bash
php artisan make:filter WhereNameStartsWithAFilter
```

Once the filter is created, let us make it do something useful.

```php
// app/Nebula/Filters/WhereNameStartsWithAFilter.php

use Illuminate\Database\Eloquent\Builder;
use Illuminate\Http\Request;
use Larsklopstra\Nebula\Contracts\NebulaFilter;

class WhereNameStartsWithAFilter extends NebulaFilter
{
    public function build(
        Builder $query,
        Request $request
    ): Builder {
        return $query->where('name', 'like', 'A%');
    }
}
```

Awesome, now we are going to tell our user resource that we would like to use this filter.

```php
// app/Nebula/Resources/UserResource.php

class UserResource extends NebulaResource
{
    // ...

    public function filters(): array
    {
        return [
            new WhereNameStartsWithAFilter
        ];
    }
}
```

We should now see "Where name starts with a" filter appear in the "Select filter" dropdown.

<alert type="info">

[Learn more](/filters) about filters.

</alert>

## Making columns searchable

Edit the `searchable` method to specify which columns you want to search in, please make sure you have an index set on those columns to avoid performance issues.

```php
// app/Nebula/Resources/UserResource.php

class UserResource extends NebulaResource
{
    // ...

    protected $searchable = ['name', 'email'];
}
```

## Adding metrics

Metrics allow quick insight into your models. Let us create a simple value-based metric to display the current user count relative to last month.

```bash
php artisan nebula:value UserCountMetric
```

Now we are going to change the scaffold to:

```php
// app/Nebula/Metrics/UserCountMetric

use App\User;
use Larsklopstra\Nebula\Metrics\ValueMetric;

class UserCountMetric extends ValueMetric
{
    public function calculate()
    {
        return $this->count(User::class);
    }

    public function cacheFor()
    {
        return now()->addHours(4);
    }

    public function label()
    {
        return 'New users';
    }
}
```

<alert type="info">

[Learn more](/metrics) about metrics and how to create your own.

</alert>

## Manually adding your model

Sometimes Nebula might not be able to resolve your model, by default it will check the `App\` and `App\Models` namespace to check if the model exists. You can override this behavior by modifying the resource like so:

```php
// app/Nebula/Resources/UserResource.php

use Larsklopstra\Nebula\Contracts\NebulaResource;
use App\Models\User;

class UserResource extends NebulaResource
{
    // ...

    public function model()
    {
        return User::class;
    }
}

```

## Manually adding store, update, and delete methods.

Nebula will do this by default for you, but sometimes you require custom logic for things such as detaching all users from a team. You can override the methods like this:

```php
// app/Nebula/Resources/UserResource.php

use Larsklopstra\Nebula\Contracts\NebulaResource;

class UserResource extends NebulaResource
{
    // ...
    
    public function indexQuery()
    {
        return $this->model()::query()
            ->withoutGlobalScopes()
            ->with($this->with);
    }

    public function updateQuery($model, $data)
    {
        $model->update($data);
    }

    public function storeQuery($model, $data)
    {
        $model::create($data);
    }

    public function destroyQuery($model)
    {
        $model->delete();
    }
}

```
