---
layout: default
title: Data Model
nav_order: 60
has_children: true
---

This is the home for the data model. Besides from the data model for base objects, there are a couple of cases that are common for all types of objects.

## Special properties

### externalId
Every object has an external id. This is how each system tries to represent the object to be identified by other integrations. 

### source
For every root object, there must be a source together with the data. This is used for routing in the system and is especially useful for orders where a source in an e-commerce solution may be the ecom-se for the Swedish site, ecom-eu for the European site and the ecom-gl for the global site. 

If you don't know what to put in the source attribute, set it to the integration code which is used to identify the integration.

## Represent locale data
To represent a local value, the name, description and locale value of attributes are represented as an object where the key is the locale.

```json
"name": {
    "sv-SE": "T-shirt",
    "en-US" "T-shirt",
    "fi-FI": "T-paita"
}
```

Even if the source system does not distinguish data with locale, you still need to have a _defaultLocale_ that is used to set the data in the global model, since there may be several local systems aggregating up to a complete description such as if you have separate ERP-systems in your different markets.

## Extended Infos
This is the way to extend the model with properties that does not fit the standard model or where it is not possible to set the property in the standard model from a source system or in a target system. 

Every object has support for extended infos.

This is an array of objects where the extended info properties are typed to better understand the data in a target system. The router will try to automatically parse data in several formats unless the format is provided by the source system specifically. 

An extended info is a more basic object than the Attribute and supports values of the types:

* string
* number
* bool
* integer
* date

Each type has it's own property and then there is a generic value property that is calculated by the system for how to render the value as a string.

The identifier for a value is the code and it is expected to be unique in the list of values.

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

## Attribute Value
This is the representation of a value that is or may be intended for end users to understand. 

```json
{
    "source": "string to identify from where the attribute originates",
    "code": "string to identify the attribute",
    "name": "string human readable name in default language",
    "localName": {
        "sv-SE": "swedish name",
        "en-UK": "english name"
    },
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
    "numberRange": {
        "min": 120.0,
        "max": 250.0
    },
    "listOfValueEntry": {
        "code": "string entry-code, attribute code in code for attribute object",
        "value": "string representation of value",
        "localeValue": {
            "sv-SE": "swedish value",
            "en-UK": "english value"
        },
        "extendedInfos": []
    },
    "multiValue": [],
    "uom": "unit-of-measure-string",
    "extendedInfos": []
}
```
