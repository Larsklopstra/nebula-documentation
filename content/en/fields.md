---
version: 0.1.0
title: Fields
subtitle: Make CRUD resources more expressive
description: "Make CRUD resources more expressive"
position: 9
category: Core concepts
---

Fields are different types of inputs which allow you to edit Resources, it is easy to get started creating your own custom fields.

## Registering a field

To register a field you have to add it to the resource its `fields` method which returns an array of fields:

```php
// app/Nebula/Resources/UserResource.php

use Larsklopstra\Nebula\Contracts\NebulaResource;
use Larsklopstra\Nebula\Fields\Input;

class UserResource extends NebulaResource
{
    // ...

    public function fields(): array
    {
        return [
            Input::make('name')
                ->type('email')
                ->rules('required'),
            Input::make('email')
                ->type('email')
                ->rules('required|email'),
            Input::make('password')
                ->type('password')
                ->rules('required|min:8'),
        ];
    }
}
```

## Available fields

Out of the box Nebula offers the following fields:

- [Input field](#input-field)
- [Textarea field](#textarea-field)
- [Boolean field](#boolean-field)
- [Color field](#color-field)
- [Select field](#select-field)
- [Date field](#date-field)

## Input field

```php
Input::make('email')
    ->label('Team member email address')
    ->type('email')
    ->placeholder('lars@example.com')
    ->required()
    ->rules('required|email|min:6')
```

![Input field example](/images/fields/input.png)

## Textarea field

```php
TextareaField::make('note')
    ->label('Write a note')
    ->placeholder('Today I did...')
    ->required()
    ->rules('required|email|min:6')
```

![Textarea field example](/images/fields/textarea.png)

## Boolean field

```php
BooleanField::make('is_admin')
    ->label('Admin account')
    ->value(true)
```

![Boolean field example](/images/fields/boolean.png)

## Color field

```php
ColorField::make('color')
    ->label('Organization color')
    ->colors([
        '#1c64f2',
        ...
    ])
    ->value('#1c64f2')
```

![Color field example](/images/fields/color.png)

## Date field

```php
DateField::make('completed_at')
    ->value($task->completed_at)
    ->format('d-m-Y')
```

![Color field example](/images/fields/date.png)

## Creating your own fields

Don't be afraid to create your own fields, it is really simple.

### CLI

Let us start by generating a basic scaffold via the CLI:

```bash
php artisan nebula:field AwesomeField
```

This will generate:

```php
namespace App\Nebula\Fields;

use Larsklopstra\Nebula\Contracts\NebulaField;

class AwesomeField extends NebulaField
{
    public function getFormComponent()
    {
        return 'nebula.forms.field-name';
    }

    public function getDetailsComponent()
    {
        return 'nebula::fields.details.input';
    }
}
```

As you can see, your custom field will inherit from the NebulaField contract and already exposed two methods that you are most likely to override.

### Views

Okay, now. It is time to create some custom views. You can use the default Nebula views like `nebula::fields.details.input`, `nebula::fields.details.textarea`, and `nebula::fields.forms.input` but for most use cases it is more logical to create something yourself.

Nebula suggests creating your views inside `resources/views/components/nebula` so you can easily call it like `nebula.forms.field-name` and `nebula.details.field-name`.

#### Details

The details component is used for displaying data.

```blade
<!-- resources/views/components/nebula/details/awesome.blade.php -->

@props(['field'])

<x-nebula::form-row :field="$field">

    <h3>My awesome Field</h3>

    <p class="text-sm">
        {{ $field->getValue() }}
    </p>

</x-nebula::form-row>
```

<alert type="info">

Make sure your blade component is able to receive the `field` prop and your form is wrapped inside the `<x-nebula::form-row :field="$field">` component. To learn all available methods, look at the `NebulaField` class.

</alert>

#### Form

The form component is used for storing and updating data.

```blade
<!-- resources/views/components/nebula/forms/awesome.blade.php -->

@props(['field', 'item' => null])

<x-nebula::form-row :field="$field">

    <div class="space-y-2">

        <input class="block w-full max-w-lg border border-gray-300 rounded-lg shadow-sm form-input sm:text-sm"
            placeholder="{{ $field->getPlaceholder() }}" id="{{ $field->getName() }}"
            value="{{ old($field->getName()) ?? (Arr::get($item, $field->getName()) ?? $field->getValue()) }}"
            {{ $field->getRequired() ? 'required' : '' }} name="{{ $field->getName() }}" type="{{ $field->getType() }}">

        <x-nebula::error :for="$field->getName()" />

    </div>

</x-nebula::form-row>

```

<alert type="info">

Make sure your blade component is able to receive the `field` prop and your form is wrapped inside the `<x-nebula::form-row :field="$field">` component. To learn all available methods, look at the `NebulaField` class.

</alert>

### Rendering the views

Now that we have created the views, we can simply render them like this:

```php
namespace App\Nebula\Fields;

use Larsklopstra\Nebula\Contracts\NebulaField;

class AwesomeField extends NebulaField
{
    public function getFormComponent()
    {
        return 'nebula.forms.awesome';
    }

    public function getDetailsComponent()
    {
        return 'nebula.details.awesome';
    }
}
```

That's it. You can include them in your resources just like any other field.
