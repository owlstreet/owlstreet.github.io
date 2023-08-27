---
layout: default
title: Product data model
parent: Data Model
nav_order: 10
has_children: false
---

# Product Data Model

The product is the main model for working with product information. This is quite extensive model with a lot of attributes to represent a product. 

{% mermaid %}
graph LR;
    Product --- Categories;
    Product --- Attributes;
    Product --- CrossReferences;
    Product --- Items;
    Product --- Ranges;
    Product --- Resources;
    Product --- Tags;
    Product --- ExtendedInfos;
    Items --- ItemAttributes[Attributes];
    Items --- ItemCrossReferences[CrossReferences];
    Items --- ItemRanges[Ranges];
    Items --- ItemTags[Tags];
    Items --- ItemExtendedInfos[ExtendedInfos];
    Items --- ItemResources[Resources];
    Items --- Prices;
    Items --- Inventories;
{% endmermaid %}

This is an example of how a simple product from an ERP is in JSON format.

```json
{
  "externalId": "321271",
  "source": "businesscentral",
  "name": {
    "sv-SE": "BLINDBRICKA LÅG MED FÖRHÖJNINGSRING"
  },
  "manufacturerCode": "GELIA",
  "items": [
    {
      "partNo": "321271",
      "type": "GOODS",
      "uom": "PCS",
      "vatType": "FULL",
      "name": {
        "sv-SE": "BLINDBRICKA LÅG MED FÖRHÖJNINGSRING"
      },
      "crossReferences": [
        {
          "type": "vendor",
          "typeValue": "33017GELIA",
          "targetValue": "10.0000149"
        },
        {
          "type": "GTIN",
          "targetValue": "7318270001492"
        }
      ]
    }
  ],
  "categories": [
    {
      "code": "ARMATURTILLBEHOR"
    }
  ],
  "extendedInfos": [
    {
      "code": "supplierCode",
      "stringValue": "33017GELIA",
      "value": "33017GELIA"
    }
  ]
}
```

## The Product object

### externalId string (required)
The external identifier of the product as being a cluster of variations. For products with no variations, the externalId is often the PartNo of the product that is being sold and purchased. For groups of items, this is either the same as for the first variant unless there is a natural identifier for the group, like 12345 where the variations has a suffix such as 10-L, 10-M, 10-XL etc.

If it not possible to identify an external id, use the part number from any of the items and the system will locate the product for you.

### localId string
A local identifier in a source system for the product such as a product id

### localSlug localeValue

Local slug of the product if there is one. This is then represented in the object model where the locale is the key and the value is the value

```json
"localSlug": {
    "sv-SE": "t-shirt-tjusig",
    "en-US": "t-shirt-pretty"
}
```

See [LocalValue](index.md#represent-locale-data) for details.

### source string
The source system providing the product data. If you are not sure what to put here, use the integration code.

See [Source](index.md#source) for details.

### status string
The status value. It is good to use standard values such as active, inactive, expiring, coming so that other systems may recognize it. 

### name localeValue
The name as a local value. [LocalValue](index.md#represent-locale-data)

### description localeValue
The description as a local value. [LocalValue](index.md#represent-locale-data)

### manufacturerCode string
The manufacturer code. This is recommended that at least one system is providing this in combination with the
manufacturer part number on the item level.

### items Item
A list of Item objects. See [Item](#item)

For the item, at least a partNo is required.

### primaryCategory Category
The primary category. This is a category object to represent the primary category if it is known. Otherwise, place all categories in the categories property instead.

```json
"primaryCategory": {
    "code": "string category code",
    "externalId": "external id of category code",
    "parentExternalId": "string referring to parent if any",
    "name": {
        "sv-SE": "kategorinamn",
        "en-US": "category name"
    }
}
```

### categories Array of Category objects
All the categories in which this product is referred to. If there is a metadata type for categories, then there will be a connection to retrieve additional properties from the metadata types for the target systems.

If categories are synchronized as part of the metadata, then there is no need to add more than the category code or the externalId and it will be decorated with data from metadata.

```json
"categories": [
    {
        "code": "string category code",
        "externalId": "external id of category code",
        "parentExternalId": "string referring to parent if any",
        "name": {
            "sv-SE": "kategorinamn",
            "en-US": "category name"
        }
    }
]
```

### attributes Array of Attribute objects
Attributes are properties of the product. Use the product properties for properties that are common to all variations of the product. This is an array of attribute objects.

See [Attribute Value](index.md#attribute-value)




