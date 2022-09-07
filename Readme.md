# Pimcore Custom Field Settings

This plugin can be used to add custom configurations to the field definitions of your data objects, object bricks, field collections and classification stores.

## Configuration

You can add custom field settings by configuring them in your `/config/config.yaml` (for Pimcore >= 10) or `/app/config/config.yml` (for Pimcore <= 6), e.g.
```yaml
blackbit_custom_field_settings:
  fields:
    transferToShop:
      field:
        xtype: checkbox
        fieldLabel: 'Transfer to shop'

    shopFieldName:
      useIn:
        fieldType: '^(input|numeric)$' # matches for input or numeric fields
        fieldName: '^erp_' # begins with erp_
      field:
        xtype: textfield
        fieldLabel: 'Shop field name'

    leadingSystem:
      field:
        xtype: combo
        fieldLabel: 'Leading system'
        store: # key-value list of possible options
          erp: ERP system
          pimcore: Pimcore
```

On the level below `blackbit_custom_field_settings.fields` you have to specify your custom fields' names. Below that under `useIn` you can define criteria which have to match for this field to get added to your data object fields:

- `fieldType`: This is the internal fieldType name. As this is not visible anywhere in Pimcore UI, you can find the types in the [Pimcore configuration](https://github.com/pimcore/pimcore/blob/be90820c181894d3224a405c307c02ef6934c528/bundles/CoreBundle/Resources/config/pimcore/default.yaml#L134-L189). You can define the exact type or use a regular expression.
- `fieldName`: Add the custom field only to data object fields which match the given name. Can also be a regular expression.
- `class`: Only add the custom field if the given class name matches. Can also be regular expression. For object brick fields, please prepend `brick.`, e.g. `brick.TechnicalData` when you want to add the custom field to a brick named `TechnicalData`. For field collections prepend `fieldcollection.`.

If you do not provide `useIn`, the custom field will be available on all data object fields.

With the `field` config you can configure the custom field itself. It is mandatory to define the `xtype` which defines the type of the field (e.g. [textfield](https://docs.sencha.com/extjs/6.2.0/classic/Ext.form.field.Text.html#configs), [textarea](https://docs.sencha.com/extjs/6.2.0/classic/Ext.form.field.TextArea.html#configs), [checkbox](https://docs.sencha.com/extjs/6.2.0/classic/Ext.form.field.Checkbox.html#configs), [combo (select field)](https://docs.sencha.com/extjs/6.2.0/classic/Ext.form.field.ComboBox.html#configs), [number](https://docs.sencha.com/extjs/6.2.0/classic/Ext.form.field.Number.html#configs), [date](https://docs.sencha.com/extjs/6.2.0/classic/Ext.form.field.Date.html#configs) etc.). All options from ExtJS (the underlying JS framework which Pimcore uses for admin UI) are supported - as shown for `fieldLabel` above.

![Custom field mappings](doc/custom-fields.png)

## Access custom field settings

The custom field settings are accessible in the field definition, e.g.
```php
$object = new \Pimcore\Model\DataObject\Product;

var_dump($object->getClass()->getFieldDefinition('name')->getCustomFields()); // outputs all custom field values as key-value array
var_dump($object->getClass()->getFieldDefinition('name')->getCustomField('transferToShop')); // outputs true in above example

// access field definition directly via class definition and not via data object's getClass()
ClassDefinition::getById('Product')->getFieldDefinition('name')->getCustomFields()
```