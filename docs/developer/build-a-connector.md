---
layout: default
title: Building a connector
parent: Developers
nav_order: 30
has_children: false
---
# Building a connector

## The connector flow

There are a couple of standard endpoints that each connector is supposed to implement. The connector will have URLs if run in the cluster as /connector-CONNECTORNAME/v1/OPERATION

There are standard operations to support:

* ping (required)
* publish/metadata (recommended)
* publish/{product\|price\|inventory\|order\|shipment\|ledger}
* subscribe/{product\|price\|inventory\|order\|shipment\|ledger}

The connector is expected to accept authorization using the headers used by the router and there are support methods for working like that in the nuget packages.


### Subscribe
When a job is started for a connector, this is what is supposed to happen on a subscribe job.

See [Building a subscriber C# example](subscriber-example.html)

* Authenticate 
* Register job in jobstatus endpoint
* Find next batch of messages from queue
  * If there are no messages to process, return
  * If there are messages to process, proceed
* Request metadata from connector support of desired types
* Request current open deadletter ids
* Loop over all entries returned from queue
  * Request transformation of message (you can ask to have pre-transformed messages from the router)
  * Invoke a builder to build the message into a format suitable for the target system
  * Update data in the target system (either in loop or at the end of the loop)
  * If status is unsuccessful and the message is to be treated as a dead letter, post dead letter
  * On successful message handling, remove any existing dead letters
  * Increase the correct jobstatus counter (added, updated, skipped, error)
  * Add message to jobstatus about the entry being processed
  * If the job is running for more than 30 seconds, update the job status so status could be followed
* After the batch is successfull, send acknowledge to the queue with a count of 0
* Update job status
* Return

This is the normal cycle for a job.

### Publish

When a job is started for a connector, this is what is supposed to happen on a subscribe job.

See [Building a publisher C# example](publisher-example.html)

* Authenticate 
* Register job in jobstatus endpoint
* Query the target system for new entries
  * If there are no entries to process, return
  * If there are messages to process, proceed
* Request metadata from connector support of desired types
* Loop over all entries returned from source system
  * Invoke a builder to build the message from the original format to the owlstreet format
  * Add the message to a list or send it directly to the queue
  * If status is unsuccessful and the message is to be treated as a dead letter, post dead letter
  * On successful message handling, remove any existing dead letters
  * Increase the correct jobstatus counter (added, updated, skipped, error)
  * Add message to jobstatus about the entry being processed
  * If the job is running for more than 30 seconds, update the job status so status could be followed
  * If there are more than 10, 50, 100 items (depending on complexity) post the list until so far to router queue
* Post the batch of messages to router (if there are unposted messages waiting to be posted)
* Update job status
* Return

### Metadata

For systems to understand the data between the systems on a common ground, there is a concept of metadata with specific metadata types that contains information from the underlying systems that could be compared to one another such as categories, product attributes etc. 

To have a good transformation configuration experience and avoid configuration errors because of spelling errors, it's generally a good idea to provide as much information as possible about each integrations message model as possible.

Valid metadata category codes are those. It is allowed to create your own, but for mapping purposes it is recommended to provide at least the most important ones about attributes, categories, warehouses etc. 

```csharp
public const string CODE_CATEGORY = "CATEGORY";
public const string CODE_CURRENCY = "CURRENCY";
public const string CODE_PRICELIST = "PRICELIST";
public const string CODE_WAREHOUSE = "WAREHOUSE";
public const string CODE_PAYMENTMETHOD = "PAYMENTMETHOD";
public const string CODE_DELIVERYMETHOD = "DELIVERYMETHOD";
public const string CODE_DELIVERYTERM = "DELIVERYTERMS";
public const string CODE_PAYMENTTERM = "PAYMENTTERMS";
public const string CODE_VATTYPE = "VATTYPE";
public const string CODE_LANGUAGE = "LANGUAGE";
public const string CODE_BRAND = "BRAND";
public const string CODE_SUPPLIER = "SUPPLIER";
public const string CODE_ATTRIBUTE = "ATTRIBUTE";
public const string CODE_REFERENCETYPE = "REFERENCETYPE";
public const string CODE_ITEMTYPE = "ITEMTYPE";
public const string CODE_UOM = "UOM";
public const string CODE_CUSTOMERTYPE = "CUSTOMERTYPE";
public const string CODE_RESOURCETYPE = "RESOURCETYPE";
public const string CODE_STORE = "STORE";
public const string CODE_COSTCENTER = "COSTECENTER";
public const string CODE_SALESPERSON = "SALESPERSON";
public const string CODE_TAG = "TAG";
public const string CODE_ORDERATTRIBUTE = "ORDERATTRIBUTE";
public const string CODE_PRICEATTRIBUTE = "PRICEATTRIBUTE";
public const string CODE_SHIPMENTATTRIBUTE = "SHIPMENTATTRIBUTE";
public const string CODE_CUSTOMERATTRIBUTE = "CUSTOMERATTRIBUTE";
public const string CODE_INVENTORYATTRIBUTE = "INVENTORYATTRIBUTE";
public const string CODE_CATEGORYATTRIBUTE = "CATEGORYATTRIBUTE";
public const string CODE_LEDGERATTRIBUTE = "LEDGERATTRIBUTE";
public const string CODE_RESOURCEATTRIBUTE = "RESOURCEATTRIBUTE";
```

It is recommended to use the C# nuget package to publish metadata.

This is an example for metdata about currencies. Use the metadata type as the base object and then the selectable values as values. 

```json
{
  "name": "Currency",
  "categoryCode": "CURRENCY",
  "code": "currency",
  "values": [
    {
      "externalId": "CNY",
      "code": "CNY",
      "name": "Kinesiska"
    },
    {
      "externalId": "DKK",
      "code": "DKK",
      "name": "Danska Kronor"
    },
    {
      "externalId": "EUR",
      "code": "EUR",
      "name": "Euro"
    }
  ]
}
```

For attributes with values, this example is for how to specify valid values for the field _colourvariant_. 

```json
{
  "name": "Attributes",
  "categoryCode": "ATTRIBUTE",
  "code": "product",
  "values": [
    {
      "externalId": "16315",
      "code": "colourvariant",
      "name": "colour variant",
      "localName": {
        "sv-SE": "colour variant"
      },
      "valueType": "listofvalue",
      "multiple": true,
      "field1": "",
      "field2": "1",
      "field3": "product",
      "field4": "visible",
      "active": true,
      "attributes": [
        {
          "code": "126653",
          "valueCode": "red",
          "localeValue": {
            "sv-SE": "Röd"
          },
          "value": "Röd"
        },
        {
          "code": "126654",
          "valueCode": "blue",
          "stringValue": "Blå",
          "localeValue": {
            "sv-SE": "Blå"
          },
          "value": "Blå"
        },
        {
          "code": "126655",
          "valueCode": "silver",
          "stringValue": "Silver",
          "localeValue": {
            "sv-SE": "Silver"
          },
          "value": "Silver"
        }
      ]
    },
  ]
}
```

## The C# code for a connector

In Program.cs when registering services. Register the default services with the client and the current context instance.

```csharp
services.AddScoped<OwlstreetClient>();
services.AddScoped<ICurrentContext, OwlstreetClientContext>();
CorsUtil.OwlstreetCors(services, builder.Configuration);
services.AddAuthentication("Basic")    
    .AddScheme<BasicAuthenticationOptions, OwlstreetAuthentictionHandler>("Basic", null);

```

Later, when registering the app, make sure to include the Cors, authentication and authorization since that is required to use the provided authentication method.

```csharp
app.UseRouting();
app.UseCors("AllowSpecificOrigin");
app.UseAuthentication();
app.UseAuthorization();
app.UseEndpoints(endpoints => { endpoints.MapControllers(); });
app.Run();

```


