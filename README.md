# FruitLinkIt plugin for Craft CMS ![Craft 2.5](https://img.shields.io/badge/craft-2.5-red.svg?style=flat-square)

One link field to replace them all, a multi-purpose link fieldtype for Craft CMS.

## On Craft 3

Linkit for Craft 3 is now available in the Plugin Store ([Linkit on GitHub](https://github.com/fruitstudios/craft-linkit))

## Upgrading Pre 2.3.0?

See upgrade details below.


## FruitLinkIt Overview

This plugin adds a fieldtype which links to all sorts of stuff, Link It can currently link to:

* Entries
* Assets
* Categories
* Products (Requires Commerce)
* Emails
* Phone numbers
* Custom URLs

Link It supports Matrix fields and allows you to:

* Configure what elements each field can link to.
* Set which element sources are available to each field.
* Allow fields to set custom link text.
* Allow fields to set links to open in new window.
* Set default link text.

## Installation

To install FruitLinkIt, follow these steps:

1. Download & unzip the file and place the `fruitlinkit` directory into your `craft/plugins` directory
2. Install plugin in the Craft Control Panel under Settings > Plugins

FruitLinkIt requires Craft 2.5, for pre 2.5 support see releases.

## Upgrading

Always ensure you have tested any new releases in a development environment.

### Upgrading FruitLinkIt (Pre Version 2.3.0)

Upgrading from a version prior to 2.3.0 please take extra care to backup and test in a development environment. FruitLinkIt will run a migration that automatically updates any existing field settings and content after which it will remove the old version.

NB. Existing templates will not break but you will get a load of deprecator errors, take a look at the readme file for updated usage guides.

## Configuring FruitLinkIt

Add a new Link It field and configure it. Easy.

## Using FruitLinkIt


### Template Variables (Basic Use)

Just output the custom field to get a ready built html link

    {{ entry.linkItField }}

or in full

    {{ entry.linkItField.htmlLink }} or {{ entry.linkItField.getHtmlLink() }}

Customised html link

    {% set attributes = {
        title: 'Custom Title',
        target: '_self',
        class: 'my-class',
        "data-custom": 'custom-data-attribute'
    } %}
    {{ entry.linkItField.htmlLink(attributes) }}


### Template Variables (Advanced Use)

Each Link it field returns a LinkIt model with the following attributes / methods available

    {{ entry.linkItField.type }} (email, custom, tel, entry, category or asset)
    {{ entry.linkItField.target }}
    {{ entry.linkItField.url }} or {{ entry.linkItField.getUrl() }}
    {{ entry.linkItField.text }} or {{ entry.linkItField.getText() }}

If your link is an element link (asset, entry, category) you also have access to the following:

    {{ entry.linkItField.element }} or {{ entry.linkItField.getElement() }}

or specific element types

    {{ entry.linkItField.entry }} or {{ entry.linkItField.getEntry() }}
    {{ entry.linkItField.asset }} or {{ entry.linkItField.getAsset() }}
    {{ entry.linkItField.category }} or {{ entry.linkItField.getCategory() }}

### Deprecated Template Variables

    {{ linkItField.linkText }} use {{ linkItField.text }} instead.
    {{ linkItField.link }} use {{ linkItField }} or {{ linkItField.htmlLink }} instead.
    {{ linkItField.email }} use {{ linkItField.url }} instead.
    {{ linkItField.custom }} use {{ linkItField.url }} instead.
    {{ linkItField.tel }} use {{ linkItField.url }} instead.


### Hooks

There are two hooks that allow plugins to add their own Element Types to Link It:

#### `linkit_registerElementTypes`

Registers the ElementType(s) with LinkIt and provides all the data needed for the field settings.

It should return an array of element types you want to register and should be in the following format:

```php
/**
 * @return array
 */
public function linkit_registerElementTypes()
{

  return array(
   'my_element' => array(
     'name'                   => Craft::t('My Element'),
     'pluralName'             => Craft::t('My Elements'),
     'selectionLabelDefault'  => Craft::t('Select an element'),
     'emptyInputErrorMessage' => Craft::t('Please select an element'),
     'elementType'            => 'MyPlugin_MyElement',
     'sources' => array(
       array(
         'label' => Craft::t('All of my elements'),
         'value' => '*'
       ),
       array(
         'label' => Craft::t('Element source 1'),
         'value' => 'myElementGroup:1'
       ),
       array(
         'label' => Craft::t('Element source 2'),
         'value' => 'myElementGroup:2'
       )
     )
   )
  ),
  'my_other_element' => array(
    ...
  );

}
```

#### `linkit_getElementData`

Fetches the element data for a given `$type` and `$id`:

```php
/**
 * @param  string $type
 * @param  int    $id
 * @return array|false
 */
public function linkit_getElementData($type, $id)
{

  // Return false if there is no `$type` or `$id` set
  if (!$type || !$id) {
    return false;
  }

  switch ($type) {
    case 'my_element':
      $myElement = craft()->myPlugin_myElements->getMyElementById($id);

      if ($myElement) {
        return array(
          'url'     => $myElement->getUrl(),
          'text'    => $myElement->title,
          'element' => $myElement
        );
      } else {
        // There was no element, so be sure to return false
        return false;
      }
      break;

    case 'my_other_element':
      ...
      break;

    // We don’t want to return anything for unsupported types
    default:
      return false;
      break;
  }

}
```

## FruitLinkIt Roadmap

Some things to do, and ideas for potential features:

* Add front end template support
* More validation options
* Force download options
* Improved cp field layout

## FruitLinkIt Changelog

### 2.3.4

* Added: Allow plugins to add their own Element Types (Thanks to Josh Angell)

### 2.3.3

* Added: Support For Commerce Products (Thanks to Isaac Gray)

### 2.3.2

* Fixed: Migration error when some settings values not defined.


### 2.3.1

* Fixed: Target not retaining it's value

### 2.3.0

Complete rewrite for Craft 2.5.x, same functionality with some UI tweaks and

* Improved: Now returns a custom validated link model to the template
* Improved: Validation of the link model
* Improved: Template usage - __toSting method now returns full html link
* Improved: Field settings layout
* Added: Facility to customise the html link attributes
* Added: Fully translate Link It
* Fixed: Locale settings bug

Amongst other stuff :)


### 1.0

* Updated: Plugin renamed to bring it inline with the rest of our plugins.


### 0.9.1

* Fixed: Input field not displaying correctly when set to single type when field had previously been saved.
* Fixed: Custom text returning false.

### 0.9

* Added: Removed the requirement to use the |raw filter when using the link variable.
* Fixed: Input field now correctly displays when on one link type is setup.

### 0.8.1

* Added: Hide the Link To... select when only one link type has been selected for the field.

### 0.8

* Added: Category Support

### 0.7

* Added: Default Text - Fieldtype setting to add default link text for a link.

### 0.6

* Fix: PHP Error when returning an entry or asset that has subsequently been deleted

### 0.5

* Initial beta release

Brought to you by [Fruit Studios](http://fruitstudios.co.uk)

## Licence

Copyright 2014 Fruit Studios Ltd
