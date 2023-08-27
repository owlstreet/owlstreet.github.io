---
layout: default
title: Router
parent: Developers
nav_order: 10
has_children: false
has_toc: true
---
# The router
The router is the central part of the owlstreet application that will accept messages from one system and send that message to another system with support for the concepts of:

* Routing
* Filtering
* Transformation

There are 7 message types available. Each of those has a well known model in owlstreet with a way of extending those messages with additional information.

{% mermaid %}
graph LR;
    Product --- GW(owlstreet);
    Price --- GW;
    Inventory --- GW;
    GW --- Order;
    GW --- Shipment;
    GW --- Customer;
    GW --- LedgerDocument;
{% endmermaid %}


### Product 
Product related information and structure of items, including resources such as images and files for describing the product. 

This is an example message from an ERP system providing only essential details about the product with a focus on how to sell it and buy it. 

See details in [Product Model](../model/product.html)

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

### Price

Price related information such as sales prices per price list and currency. 

This is an example of such a price for the item above originating from the ERP.

See details in [Price Model](../model/price.html)


```json
{
  "partNo": "321271",
  "source": "businesscentral",
  "priceListId": "STANDARD",
  "currency": "SEK",
  "isPriceInclVat": false,
  "salesPrice": 47.2,
  "vat": 11.8
}
```

### Inventory
Information of inventory level, inventory transactions such as stocktake.

See details in [Inventory Model](../model/inventory.html)


```json
{
  "source": "businesscentral",
  "partNo": "321271",
  "warehouseId": "UGGLEGATAN",
  "quantity": 108
}
```

### Order
Sales orders, purchase order, return orders, invoices, credit memos. 

See details in [Order Model](../model/order.html)


```json
{
  "externalId": "39797",
  "orderId": "20039797",
  "created": "2023-08-26T15:52:27",
  "status": "processing",
  "buyer": {
    "firstName": "First",
    "lastName": "Name",
    "email": "email@address.com",
    "phone": "0000000000",
    "address": {
      "address1": "Hemgatan 24 Lgh 1202",
      "address2": "",
      "city": "BODEN",
      "zip": "96188",
      "state": "",
      "country": "SE",
      "phone": "0000000000",
      "email": "email@address.com"
    }
  },
  "shipTo": {
    "firstName": "First",
    "lastName": "Name",
    "email": "email@address.com",
    "phone": "0000000000",
    "address": {
      "address1": "Hemgatan 24 Lgh 1202",
      "address2": "",
      "city": "BODEN",
      "zip": "96188",
      "state": "",
      "country": "SE",
      "phone": "0000000000",
      "email": "email@address.com"
    }
  },
  "currency": "SEK",
  "payments": [
    {
      "externalId": "svea_checkout",
      "code": "svea_checkout",
      "amount": 99,
      "reference": "1111111111"
    }
  ],
  "deliveryInfo": {
    "externalId": "1230",
    "code": "PAE",
    "extendedInfos": []
  },
  "lines": [
    {
      "partNo": "823807",
      "name": "Lamphållare slät guld E27",
      "quantity": 1,
      "pricePerItemExclVat": 32,
      "pricePerItemVat": 8,
      "totalLineAmount": 40
    }
  ],
  "totalAmountInclVat": 99,
  "totalAmountExclVat": 79.2,
  "totalVat": 19.8,
  "freightExclVat": 47.2,
  "freightVat": 11.8,
  "freightInclVat": 59,
  "extendedInfos": [
    {
      "code": "woo_id",
      "integerValue": 39797,
      "value": "39797"
    }
  ],
  "source": "ecomwp"
}
```

### Shipment 
Deliveries related to an order, such as once an order has been shipped and carries information about what has been shipped and tracking information.

See details in [Shipment Model](../model/shipment.html)

```json
{
  "externalId": "20039786",
  "orderExternalId": "20039786",
  "status": "DELIVERED",
  "shipTo": {
    "lastName": "Test Persson",
    "email": "testperson@example.com",
    "phone": "0000000000",
    "address": {
      "address1": "Centralgatan 4",
      "city": "VINDELN",
      "zip": "92287",
      "country": "SE"
    }
  },
  "lines": [
    {
      "lineNumber": 20000,
      "lineType": "Item",
      "partNo": "FREIGHT",
      "name": "Frakt",
      "quantityShipped": 1
    },
    {
      "lineNumber": 10000,
      "lineType": "Item",
      "partNo": "821319",
      "name": "Disklådspropp Ifö Contura nya Conturaproppen 48-55m",
      "quantityShipped": 2
    }
  ],
  "source": "logistics"
}
```


### Customer
B2B customer information. For B2C the customer data is usually sent through the order.

See details in [Customer Model](../model/customer.html)


### Ledger Document
When there is a need to represent ledger information such as entries from a PSP when making a pay-out to the bank account.

See details in [Ledger Document Model](../model/ledgerdocument.html)

```json
{
  "externalId": "gl-229009",
  "documentDate": "2023-07-07T00:00:00",
  "postingDate": "2023-07-07T00:00:00",
  "documentId": "229009",
  "journalId": "invoiceledger",
  "description": "229009 Test Person 10020053",
  "rows": [
    {
      "lineNumber": 10000,
      "accountId": "3010",
      "creditAmount": 872
    },
    {
      "lineNumber": 20000,
      "accountId": "2611",
      "creditAmount": 218
    },
    {
      "lineNumber": 30000,
      "accountId": "1510",
      "debitAmount": 1090
    }
  ],
  "source": "businesscentral"
}
```
## Queue endpoints

The queue endpoints are available at

* https://api-stage.owlstreet.online/router/api/v1/queue/QUEUETYPE] (stage)
* https://api.owlstreet.online/router/api/v1/queue/QUEUETYPE (production)

Valid queue types are

* inventory
* ledger
* order
* price
* product
* shipment
* customer

## Subscribe to messages from queue

There is one endpoint per message type. To subscribe to messages, it is a GET request to that endpoint.

In the Owlstreet.Client nuget package, there are methods for interacting with the queue.

https://api-stage.owlstreet.online/router/api/v1/queue/product

Optional query string parameters are:

```bash
count: batch size, default 100
acknowledge: acknowledge token to acknowledge the last batch
```

Send headers:

```bash
authorization: Bearer bearertoken
x-tenant: customer
x-integration: ecommerce
```

This will return a result with the messages on queue with a default size of 100 messages per batch and lock them so that if there is an update of a message being locked, it will be added as a new message.

There is information about the total number of entries on the queue, the _count_ property. There is also an _acknowledge_ property which is used as a parameter to the command to acknowledge the previous batch and get a new one.

```json
{
	"queue": "product",
	"integration": "ecommerce",
	"count": 289,
	"acknowledge": "7ad4e9fa-5bb4-45e1-9a67-2f090e948538",
	"entries": [
		{
			"externalId": "825126",
			"...": "other-product-properties"
		}
    ]
}
```

When using messages and subscribing to a queue working one batch at a time, then it is a good idea to send the acknowledge in the end with a count of 0 to not lock any new entries on the queue.

https://api-stage.owlstreet.online/router/api/v1/queue/product?count=0&acknowledge=7ad4e9fa-5bb4-45e1-9a67-2f090e948538

Send headers:

```bash
authorization: Bearer bearertoken
x-tenant: customer
x-integration: ecommerce
```

Query string parameters

```bash
count: 0
acknowledge: 7ad4e9fa-5bb4-45e1-9a67-2f090e948538
```


## Publish messages to queue
There is one endpoint per message type. To publish to messages, it is a POST request to that endpoint.

In the Owlstreet.Client nuget package, there are methods for interacting with the queue.

https://api-stage.owlstreet.online/router/api/v1/queue/product

Send headers:

```bash
authorization: Bearer bearertoken
x-tenant: customer
x-integration: ecommerce
content-type: application/json
```

Body is a list of objects of the type for the queue. Requirements is that each message has an _externalId_ and a _source_ property.

```json
[
    {
        "externalId": "PR1008",
        "source": "pointofsale"
    }
]
```

If you get a status 200 back, then message has been submmitted to the queue and will then be processed by the router. 

An error of status 400 means that there is something wrong with the data that is posted to queue format-wise. 

The message will be routed, filtered and transformed so that it could reach the target system where it will go. 

Even if there is no target system routed, there will be a record of the latest message per source for the message types:

* Product
* Price
* Inventory

## Dead letters

To be able to process all messages in a batch and when some messages are successfully sent, the messages that can not be processed even if they should have been need to be treated as dead letters.

In owlstreet, sending a message as a dead letter is a process where the connector is responsible for creating the message so that a valid message can be stored along with the dead letter message. 

When using dead letters, it is also required to delete the dead letter once a successful message of the same type with the same external id is sent through to the target system and that the error is no longer relevant. 

To post a new dead letter, send it as a POST to the endpoint

https://api-stage.owlstreet.online/router/api/v1/queue/deadletter

Send headers:

```bash
authorization: Bearer bearertoken
x-tenant: customer
x-integration: ecommerce
content-type: application/json
```

Send body

```json
{
  "jobCode": "string",
  "messageType": "string",
  "deleted": false,
  "source": "string",
  "target": "string",
  "externalId": "string",
  "statusAsText": "string",
  "errorMessage": "string",
  "errorStage": "string",
  "payload": "string",
  "sourcePayload": "string"
}
```

Properties are:

* jobCode - the job where there was a problem such as subscribe-product
* messageType is the type of message as one of the 7 pre-defined message types in lower case letters.
* deleted - send deleted=false always except for when removing a message.
* source - the source from where the message originated, take the source from the original message from the queue you are processing.
* target - to where is the message sent, either the integration code in most cases, but for a market on an e-commerce site it could be something different.
* externalId - the external id of the message. It must contain enough data to make the message unique for that queue and integration such as part number and price list for a price.
* statusAsText - is the text message describing why the message is now a dead letter.
* errorMessage - a longer error message
* errorStage - a description of where things went wrong.
* payload - what the message your are trying to the target systems looks like for troubleshooting purposes.
* sourcePayload - is the original message before it is converted to the target format and modified. This is to allow resend of messages to the queue.

It is recommended to use the .NET library from the nuget package to interact with dead letters.
