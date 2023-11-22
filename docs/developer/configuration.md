---
layout: default
title: Configuration
parent: Developers
nav_order: 100
has_children: false
---
# The Tenant Configuration
There is a full configuration that could be read and stored in JSON format. The configuration file contains information about the different integrations that are in use for a specific tenant (customer). When working with the configuration as a developer, you will only have access to the part of the configuration that is relevant to the current integration.

In the configuration file, the sensitive data is encrypted and there are ways to decrypt them by using the C# library. If you are not using the C# library, you will have to invoke a special endpoint to retrieve the configurations encrypted values. Such values include:

* Passwords
* User names
* API Tokens
* Sensitive URLs

## The Integration Configuration

Each integration configuration has it's unique _code_ within a configuration to identify the integration. It is often named the same as the system you are integrating towards, i.e. if you are integrating to the Business Central ERP, then you would call it businesscentral. Only lower case letters are allowed, numbers, dashes and underscore. 

Properties of the integration are as follows.

### code string (required)
The integration code. Every integration need to have a unique code within the same Tenant Configuration.

### active boolean (default true)
Indicating wether this integration is active or not. If it is inactive, it is not allowed to run any jobs so this property will be looked at by the connector.

### debug boolean (default false)
Debug mode wether to preserve additional trace information during processing. This will for instance publish more verbose run logs when a connector does it's job so it is possible to find the steps taken towards running the integration jobs.

### connector Connector
This is the connector settings that contains information for how to connect to the integrated system. There are a couple of fixed properties of the connector that could be used, but it must in the documentation for the connector clearly be stated which properties that are allowed and which configuration is mandatory. 

When creating a connector, it is also required to validate that configuration is valid before attempting to start a job that may be failed or cause data not being correct because of a faulty configuration.

Properties of the connector includes

* connectorCode - the pre-configured connector code. Such a code need to be registered with owlstreet. If you are building a connector of your own, you may start with the connectorCode of _custom_ to use the APIs.
* primaryUrl - The primary URL that will allow the connector to connect to the integrating system. The format of the URL and what it's purpose is must be clearly documented in the setup of the connector.
* secondaryURl - This is the secondary url when an integration requires multiple urls such as an enpoint for requesting a new token which may be customer specific.
* apiToken - Any API token if the connection is using that for authorization. This property will be encrypted.
* username - Any username if being used by the connector. This property will be encrypted.
* password - Any password that is being used by the connector. This property will be encrypted.
* settings - An array of [Setting](#settings-array-of-setting) objects where you do specific configuration related to the connector. Note the difference that any directives not only related to the connector but for transformation etc are stored on the integration level while properties relating to the target system are stored as connector settings.

### settings Array of Setting
This is an array of settings. Settings has a couple of properties.

* active - boolean wether to consider this setting or not. It is used to be able to deactivate a setting without removing it. Default value is true unless specified as false.
* code - string for the code of the setting. Use camelCase for setting names. 
* value - a string value for the setting
* values - an array of strings of values for the setting
* description - string to describe the purpose or use of this setting to make the configuration easier to understand.

### subscribers Array of Subscriber
A Subscriber is a definition of how to subscribe to messages. The default subscriber will be using the integrated ways within owlstreet to consume messages and no external message queueing system is exposed unless specified. There are more properties available, but only the most vital ones are documented here.

* messageType - string (required) one of the values product, price, inventory, order, shipment, customer, ledgerDocument
* queueType - string (required) set it to queue for the internal message queue
* active - boolean set to false to disable a subscriber temporary. No messages will be queued.
* debug - boolean set to true to enable more verbose logging.
* deduplicate - boolean when to use deduplication of messages. Set to true as default with a limit of 12 hours for the same message to be sent again.
* filters - array of Filter objects. 
* transformations - array of Transformation objects. 
* settings - array of [Settings](#settings-array-of-setting)
* topicRegex - the topic regular expression to listen to. The topic regular expression will only be applied for messages of the message type of the subscriber. 

### publisher Array of Publisher
A Publisher describes how messages are published. For messages to be picked up by a subscriber, the message type, the topic and any filters on first the publisher side and then on the subscriber side will be processed before the message is checked for duplicates and then delivered. 

* messageType - string (required) one of the values product, price, inventory, order, shipment, customer, ledgerDocument
* queueType - string (required) set it to queue for the internal message queue. Other settings are required to publish i.e. to an azure service bus. 
* active - boolean set to false to disable a publisher temporary. No messages will be queued.
* debug - boolean set to true to enable more verbose logging.
* deduplicate - boolean when to use deduplication of messages already before being processed by the subscribers. Set to true as default with a limit of 12 hours for the same message to be sent again.
* filters - array of Filter objects. 
* transformations - array of Transformation objects. 
* settings - array of [Settings](#settings-array-of-setting)
* topic - the topic for messages published from here. When a message is published, the router will try to deliver the message to any subscriber that subscribes to a pattern of messages that matches the topic of the subscriber.

## Sample integration configuration

```json
{
            "code": "woocommerce",
            "active": true,
            "connector": {
                "id": "faef8d29-c7d6-4a9f-9304-badb68ccad4a",
                "connectorCode": "woocommerce",
                "authType": "basic",
                "primaryUrl": "******",
                "username": "*******",
                "password": "*******",
                "settings": [
                    {
                        "active": true,
                        "code": "locale",
                        "value": "en-US"
                    },
                    {
                        "active": true,
                        "code": "useProductTransformation",
                        "value": "false"
                    }
                ]
            },
            "subscribers": [
                {
                    "topicRegex": "products-businesscentral",
                    "id": "2c44b8f8-7468-4acd-9257-dccce2adea04",
                    "messageType": "product",
                    "queueType": "queue"
                },
                {
                    "topicRegex": "products-akeneo",
                    "id": "c8615942-e756-4329-ad0d-af009a2ac493",
                    "messageType": "product",
                    "queueType": "queue"
                },
                {
                    "topicRegex": "prices-businesscentral",
                    "id": "4396ae0a-58cf-48c2-8521-4defd2e04686",
                    "messageType": "price",
                    "queueType": "queue"
                },
                {
                    "topicRegex": "inventory-businesscentral",
                    "id": "adcc3ca9-5505-49e8-a0d7-ed9e853c8d27",
                    "messageType": "inventory",
                    "queueType": "queue"
                }
            ],
            "publishers": [
                {
                    "topic": "orders-wordpress",
                    "id": "d563e333-7ac0-4399-8fbb-d1c8048462db",
                    "messageType": "order",
                    "queueType": "queue",
                    "active": true
                },
                {
                    "topic": "order-wordpress-northamerica",
                    "id": "884bcafd-b922-447a-cfb4-edfda1a25fae",
                    "messageType": "order",
                    "queueType": "queue",
                    "active": true,
                    "filters": [
                        {
                            "id": "6b2d240a-c828-2805-cc6d-60dee6119fc4",
                            "name": "new-filter",
                            "filterType": "constant",
                            "active": true,
                            "expression": "shipTo.country=US"
                        }
                    ]
                }
            ]
        }
```



