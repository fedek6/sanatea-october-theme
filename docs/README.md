# October tips & tricks

## Namespace helpers
Install https://octobercms.com/plugin/flynsarmy-idehelper and run:

```
php artisan ide-helper:generate
```

## Plugin example

Great source example to writing plugins:
https://github.com/responsiv/pay-plugin

## Good tutorials & docs

* Component basics: https://octobercms.com/docs/cms/components 
* Building plugin components: https://octobercms.com/docs/plugin/components
* Static pages api: https://octobercms.com/plugin/rainlab-pages
* Good video series on October: https://watch-learn.com/video-tutorials/making-websites-with-october-cms-part-12-repeater-field
* How to add menu on top of list controller: https://stackoverflow.com/questions/58286673/export-a-list-of-users-in-octobercms-to-csv
* How to export data: https://octobercms.com/docs/backend/import-export

## Good theme example

https://github.com/responsiv/clean-theme


## How to check if ajax request

```
// Check if ajax request
if (\Request::ajax()) {
    $this['isAjax'] = true;
} else {
    $this['isAjax'] = false;
}
```

## Building components

### How to access vars in components?

If you have _$this->message_ (and it's public) in your component class you can acces it like that:

```twig
{{ __SELF__.message }}
```

## Static pages plugin

### How to get children and children of parent?
```
$this['parent'] = $this->page['apiBag']['staticPage']->getParent();
$this['children'] = $this->page['apiBag']['staticPage']->getParent()->getChildren();
```

### How to show sideselector with specific post loaded?
```
{% partial "sideselector/sideselector" 
    tagline=page.tagline 
    children=children 
    main_category=main_category 
    auto_load=true 
    post_number=3
%}
```

## Form widgets
Check: https://octobercms.com/docs/backend/forms#form-widgets

## Extending CMS forms

Add to your plugin (__warning!__ This cannot be translated):

```
    public function boot()
    {
        // Extend all backend form usage
        \Event::listen('backend.form.extendFields', function($widget) {

            // Only for the CMS Index controller
            if (!$widget->getController() instanceof \Cms\Controllers\Index) {
                return;
            }

            // Only for the Page model
            if (!$widget->model instanceof \Cms\Classes\Page) {
                return;
            }

            // Add custom fields...
            $widget->addTabFields([
                'viewBag[canonical_url]' => [
                    'label'   => 'Canonical Url',
                    'type'    => 'text',
                    'tab'     => 'cms::lang.editor.meta'
                ],
                'viewBag[robots_noindex]' => [
                    'label'   => 'No Index?',
                    'type'    => 'checkbox',
                    'tab'     => 'cms::lang.editor.meta'
                ],
            ]);
        });
    } 
```

__List of available fields you can find here:__
https://octobercms.com/docs/backend/forms#field-types

### Twig snippets 

With default value (if not set):
```
{{ page.text_color|default('#FFFFFF') }}
```

## UI

Check https://octobercms.com/docs/ui/form

# l18n

## Native plugin translation

Use namespace, filename and key to acces vars.

For example, if you have such file in your plugin directory _plugins/realhero/iqsextensions/lang/pl/lang.php_. 

With following contents:

```php
return [
    'plugin' => [
        'name' => 'IQS Extensions',
        'description' => '',
        'contact_us' => 'Skontaktuj się z nami'
    ],

    'msg' => [

    ]
];
```


In PHP:

```php
\Lang::get("realhero.iqsextensions::plugin.contact_us");
```

In Twig:

```twig
{{ "realhero.iqsextensions::lang.msg.contact_us"|trans }}
```

Always use _msg_ or component name as the main key. 

## Rainlab tranlsate plugin

Translations are in _iqs/l18n/lang-[SO 3166-1 alfa-2].yaml_ for example _iqs/l18n/lang-pl.yaml_.

Handler strings:
```
{{ 'site.name'|_ }}
```

Interpolated:
```
<a class="#">{{ "btn.#{page.btn_text}"|_ }}</a>
```

### How to check if model attribute is translated?

```twig
{% set isReportTranslated = (record.noFallbackLocale.lang(selectedLanguage).report is empty) ? false : true %}
```

The same in PHP:

```php
$user->noFallbackLocale()->lang('fr');
```

### Content tables

Slug column must be present in content tables to generate URL.

Besides that _slug_ in model must be indexable.

```php
public $translatable = ['title','tagline', 'content', 'cover', ['slug', 'index' => true] ];
```

For example if you translate URL:
```
/en/reports/bio-a-market-opportunity
/pl/raporty/bio-szansa-rynkowa
```

## Validation rules translation

Create validation lang file, for example:

```
plugins/realhero/iqsextensions/lang/en/validation.php
```

Put your messages like that:

```php
return [
    'telephone.required' =>     'Prosimy o podanie numeru telefonu',
    'email' =>                  'Prosimy o podanie adresu e-mail',
    'email.required' =>         'Prosimy o podanie poprawnego adresu e-mail',
    'acceptance.accepted' =>    'Musisz zaakceptować warunki aby pobrać plik',
    'content.max' =>            'Zapytanie może mieć maksymalnie :max znaków',
];
```

You can also put filter values into your messages:

```php
$messages = [
    'same'    => 'The :attribute and :other must match.',
    'size'    => 'The :attribute must be exactly :size.',
    'between' => 'The :attribute must be between :min - :max.',
    'in'      => 'The :attribute must be one of the following types: :values',
];
```

Finally construct your validation object, just like that:

```php
/** @var object $validation */
$validation = \Validator::make($formData, $rules,  \Lang::get('realhero.iqsextensions::validation') )
```

## Pagination example

https://octobercms.com/docs/services/pagination