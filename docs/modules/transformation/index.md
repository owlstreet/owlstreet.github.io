---
layout: default
title: Transformation
parent: Built in modules
nav_order: 80
has_children: true
---
# Transformation

## Objects being handled by the filter and transformation

For filtering and transformation, all the values will be represented as AttributeValue object values. The values will look different depending on the source representation of that value.

```json
{
    "code": "string to identify the attribute",
    "valueCode": "string the optional value code if it has a key as part of a list of values etc",
    "stringValue": "string representation of the value",
    "dateValue": "date representation of the value",
    "booleanValue": false,
    "numberValue": 42.0,
    "integerValue": 42,
    "localeValue": {
        "sv-SE": "swedish value",
        "en-UK": "english value"
    },
    "multiValue": [],
    "uom": "unit-of-measure-string",
    "extendedInfos": [],
    "value": "read-only string representation of the value"
}
```

Additional properties of an attribute will be provedd as extended info in the array of extendedInfos.

```json
{
    "type": "string",
    "code": "string",
    "numberValue": 19.4,
    "integerValue": 42,
    "booleanValue": true,
    "dateValue": "2023-08-27",
    "stringValue": "string",
    "value": "read-only-property-with-string-representation-of-value"
}
```

## Filters on a field level
Filters are used on a field level to decide if a field is to be selected for processing or not. This does not apply to the entire
entity, which is done in the routing step. 

### Javascript filters

To implement a filter on a field on the base object such as the product, the sales order, the price etc. This is the filter method to implement. The method need to return either true for a successful evaluation or false for invalid evaulation. The filter is used to decide
if the value of this attribute is to be selected for further procesing. If not, the next value in priority for the target field will be
tested for validity.

The sourceFieldValue is of type AttributeValue (see above). For a simple string validation of the value to see if it is value, you could always use the value.value to check the property. In the debugger mode, you will also see the log output of the source value.

Parameters are javascript objects for currentObject, parentObject and value. The sourceField is a string.

```javascript
function filterCurrentObjectFieldValue(currentObject, sourceField, value) {
    return true
}
```

If the filter is applied on a child object such as a line item in an order or an item in a product, then there is a way either to use
the same filter method above, but if the parent is required it is possible to use.

```javascript
function filterParentCurrentObjectFieldValue(parentObject, currentObject, sourceField, value) {
    return true
}
```

## Transformation on field level

### Javscript transformations

The purpose is to run a rule to transform a value. Somethimes it is required to not only have the value but also the context in where
the value is used. The method will return a value which is an AttributeValue as above. 

Parameters are javascript objects for currentObject, parentObject and value. The sourceField is a string.

```javascript
function transformCurrentObjectFieldValue(currentObject, sourceField, value) {
    return value
}
```

For objects with a parent object such as an item within a product, there is another method that could be implemented that would contain
data both from the product (parent object) and the item (current object). Use the debugger to see the format of the source value, since it
depends on what type of value the source is.

```javascript
function transformParentCurrentObjectFieldValue(parentObject, currentObject, sourceField, value) {
    return value
}
```

## Transformation on entity level

This is the last thing that will happen before returning. There may be multiple transformations happening and they will be invoked in order.

The transformation must return an object. The sourceObject is the entity before it was sent for transformation and the current is the way the object is represented now after filters and transformations.

```javascript
function  transformEntity(sourceObject, current) {
    return current
}
```

To add a custom attribute, use this example

```javascript
function  transformEntity(sourceObject, current) {
    if( !current.attributes ) {
        current.attributes = []
    }

    current.attributes.push({
        code: 'customAttribute1',
        stringValue: 'This is the custom string value'
    })

    return current
}
```








