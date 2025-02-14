# Nova Astrotomic Translatable

[![Latest Version on Packagist](https://img.shields.io/packagist/v/kiritokatklian/nova-astrotranslatable.svg?style=flat-square)](https://packagist.org/packages/kiritokatklian/nova-astrotranslatable)
[![Total Downloads](https://img.shields.io/packagist/dt/kiritokatklian/nova-astrotranslatable.svg?style=flat-square)](https://packagist.org/packages/kiritokatklian/nova-astrotranslatable)

This [Laravel Nova](https://nova.laravel.com) allows you to make any input field [astrotomic/laravel-translatable](https://github.com/astrotomic/laravel-translatable) compatible and localizable.

## Requirements

- `PHP: >=8.2`
- `laravel/nova: ^5.0`
- `astrotomic/laravel-translatable: ^11.10`

## Version Compatibility

With the release of Nova 5.0, there are now two separate versions of Nova Translatable. Unfortunately due to the nature of the update, the new one isn't backwards compatible. So please choose your version accordingly.

| Laravel Nova | Nova Translatable |
|:------------:|:-----------------:|
|     4.0      |        2.1        |
|     5.0      |        5.0        |

## Features

- **Supports almost all fields** (including third party ones)
- **Supports default validation automatically**
- **Simple to implement** with minimal code changes (after `astrotomic/laravel-translatable` support)
- Locale tabs to switch between different locale values of the same field
- **Double-click** on a tab to switch all fields to that locale
- Supports [nova-settings](https://github.com/outl1ne/nova-settings) package

## Known non-working fields

- `Image` and `File`
  - Workarounds:
    - [outl1ne/nova-media-hub](https://github.com/outl1ne/nova-media-hub)
    - or any library that uploads images/files using XHR

## Limitations

- The following methods can not be used, as this package uses them internally:
  - `resolveUsing`
  - `fillUsing`

## Screenshots

<details open>
  <summary>Form View</summary>
  <img src="./docs/detail.png" alt="Detail view" />
</details>

<details>
  <summary>Form View</summary>
  <img src="./docs/form.png" alt="Form view" />
</details>

<details>
  <summary>Form View w/ Validation Errors</summary>
  <img src="./docs/validation.png" alt="Form view with validation errors" />
</details>

## Installation

Firstly, set up [astrotomic/laravel-translatable](https://github.com/astrotomic/laravel-translatable).

Install the package in a Laravel Nova project via Composer:

```bash
# Install nova-astrotranslatable
composer require kiritokatklian/nova-astrotranslatable

# Publish configuration (optional, but useful for setting default locales)
php artisan vendor:publish --tag="nova-translatable-config"
```

## Usage

Call `->translatable()` on any field, like so:

```php
// Any Nova field
Text::make('Name')
  ->rules('required', 'min:2')
  ->translatable(),

// Any third-party input field
Multiselect::make('Football teams')
  ->rules('required')
  ->translatable(),

// Optionally pass custom locales on a per-field basis
Number::make('Population')
  ->translatable([
    'en' => 'English',
    'et' => 'Estonian',
  ]),
```

## Validation

It's possible to define locale specific validation rules.

To do so, add the `->rulesFor()` on your field and the `HandlesTranslatable` trait to your Nova resource.

`->rulesFor` accepts `array|string|callable` locales and `array|callable` rules.

```php
use Kiritokatklian\NovaAstrotranslatable\HandlesTranslatable;

class Product extends Resource
{
    use HandlesTranslatable;

    public function fields(Request $request)
    {
        return [
            Text::make(__('Name'), 'name')
                ->sortable()
                ->translatable()
                ->rules(['max:255'])
                ->rulesFor('en', [
                    'required',
                ])
                ->rulesFor(['en', 'et'], function ($locale) {
                    return ["unique:products,name->$locale{{resourceId}}"];
                }),
        ];
    }
}
```

#### In this example, rules will be added to the following values

```dotenv
max: name.*
required: name.en
unique: name.en & name.et
```

## Configuration

You can define default locales for all the `translatable` fields in the config file. The config file can be published using:

```bash
php artisan vendor:publish --tag="nova-astrotranslatable-config"
```

### Fill other locales from config option

The configuration option `fill_other_locales_from` allows you to pre-fill other locales from just one locale. This requires the resources to also have the `HandlesTranslatable` trait.

### One select for all fields on a page

If you don't want to display the locale select next to each field, you can set the `display_type` to `none` and add a `Kiritokatklian\NovaAstrotranslatable\Fields\LocaleSelect` field to your Nova resource. This will render a single select for all fields.

## Edge cases

#### BelongsToMany allowDuplicateRelations corner-case

When using this field inside a BelongsToMany as a pivot field with `->allowDuplicateRelations()` and you want to filter out exact matches using the `NotExactlyAttached` rule, use the `BelongsToManyTranslatable` field instead of the regular `BelongsToMany`.

## Credits

- [Tarvo Reinpalu](https://github.com/Tarpsvo)
- [Optimist Digital](https://github.com/optimistdigital)

## License

This project is open-sourced software licensed under the [MIT license](LICENSE.md).
