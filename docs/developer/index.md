---
layout: default
title: Developers
nav_order: 40
has_children: true
category: Developers
sidebar: toc
---

# Starting point for developers

## Tenants, integrations and configurations
Each customer is a tenant in the solution since it is a multi tenant SaaS solution. There are two separate environments where each tenant has it's own configuration so that you are able to do testing in a stage environment before deploying changes to the production environment.

{% mermaid %}
graph TD;
    TENANT[Tenant] --> |1..n| INTEGRATION[Integration];
    INTEGRATION --> CONNECTOR[Connector];
    INTEGRATION -->  PUBLISHERS[Publishers];
    PUBLISHERS --> |0..n| PUBMSGTYPE[Message Publish Definitions];
    INTEGRATION --> SUBSCRIBERS[Subscribers];
    SUBSCRIBERS --> |0..n| SUBMSGTYPE[Message Subscription Definition];
{% endmermaid %}


## The Dotnet nuget packages
To be able to do development using the pre-packaged development nuget packages, there is a feed with the packages that makes development easier. There is also API documentation for the core modules in Swagger.

Add the feed directly in Visual Studio or add it in a _nuget.config_ file

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <clear />
    <add key="owlstreet" 
    value="https://pkgs.dev.azure.com/UnifiedCommerceEcom/Owlstreet/_packaging/owlstreet/nuget/v3/index.json" />
  </packageSources>
</configuration>
```

There are two packages that are the most essential to use when developing an external integration towards Owlstreet. By using them, you will be able to connect and develop towards the Owlstreet services.

* Owlstreet.Common.Model
* Owlstreet.Client

The packages are expecting a dotnet version 6 or later.

## The owlstreet services to connect with

There are two main APIs that you will be connecting to when working with Owlstreet. The main function that does the routing, transformation and all of those queue-related operations is the router service. This is an API for interacting with the router at:

https://api-stage.owlstreet.online/router/swagger/index.html

When developing a connector, there is a collection of services underneath the connector-support service to support building a custom connector.

https://api-stage.owlstreet.online/connector-support/swagger/index.html

## Authentication and authorization

There are two ways to provide authentication credentials to be able to access owlstreet by using three headers.

### OAuth2
This is the recommended method where you will request a token that will have a lifetime. This lifetime is is designed in a way that a batch will be possible to run in full before it is expired. 

Use the POST method to the endpoint:

https://api-stage.owlstreet.online/router/api/token/generate

With this, send a JSON body with the properties _username_ and _password_.


```json
{
	"username": "registered.username@owlstreet.io",
	"password": "seCreTPasSw0rddd"
}
```

In response, you will be getting an access_token that you can use for further requests.

```json
{
	"access_token": "eyJ...hHt3g",
	"id_token": "eyJh...YLGw",
	"scope": "openid email profile read:groups",
	"expires_in": 86400,
	"token_type": "Bearer"
}
```

If there is a limit to which integrations within a tenant user user account has access to, then the key also will carry this information.

To authorize using such a token, send it as a Bearer token using a request header. For all authorized requests, it is also required to provide the tenant and the integration as x- request headers.

```bash
authorization: Bearer eyJ...hHt3g
x-tenant: customername
x-integration: ecommerce
```


## API Key
You can register for en API key in the owlstreet Users and Keys module. If an API key is to be used only by a single integration, then make sure to limit the key to that integration and it will only be able to access data in that integration.

```bash
x-api-key: R6....w7A
x-tenant: customername
x-integration: ecommerce
```

Once you create an API-key, you will only be able to see the value once, so make sure to copy it. An API-key can then be re-generated so that it keeps the same privileges but you will need to replace it where it is being used.


