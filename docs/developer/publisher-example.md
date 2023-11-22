---
layout: default
parent: Building a connector
title: Publisher C# Example
nav_order: 30
has_children: false
---
# Building a publisher

## The configuration files

### nuget.config

### appsettings.json

```json
{
  "Owlstreet": {
    "AllowedOrigins": [ "http://localhost:3000"],
    "BaseUrl": "https://api-stage.owlstreet.online"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "BasePath": "/connector-example",
  "Rmq": {
    "Hostname": "",
    "Password": "",
    "User": "",
    "Virtualhost": ""
  }
}
```

### The Program.cs

```csharp
using Owlstreet.Client;
using Owlstreet.Common.Model.Common;
using MediatR;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.ApiExplorer;
using Microsoft.AspNetCore.Mvc.Versioning;
using Microsoft.FeatureManagement;
using Owlstreet.Communication.Services;
using System.Reflection;
using Microsoft.Extensions.Configuration;
using Owlstreet.WebApiUtil.Util;
using Microsoft.AspNetCore.Builder;
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using System.Linq;
using Owlstreet.Connector.Example.Controllers;
using Owlstreet.Connector.Example.Managers;
using Owlstreet.Connector.Example.Models;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
var services = builder.Services;
services.AddMediatR(Assembly.GetExecutingAssembly(), typeof(SendCommandBase).Assembly);


services.AddScoped<OwlstreetClient>();
services.AddScoped<ICurrentContext, OwlstreetClientContext>();

// Configuration and manager to communicate with target system
services.AddScoped<ExampleSystemConnectionManager>();
services.AddScoped<ExampleConfiguration>();

services.AddAuthentication("Basic")
    .AddScheme<BasicAuthenticationOptions, OwlstreetAuthentictionHandler>("Basic", null);

services.AddFeatureManagement();
services.AddHttpClient();
services.AddHttpContextAccessor();
services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddDistributedMemoryCache();
CorsUtil.OwlstreetCors(services, builder.Configuration);

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
}

using (var serviceScope = app.Services.CreateScope())
{
    var config = serviceScope.ServiceProvider.GetRequiredService<IConfiguration>();
    var basePath = config.GetValue<string>("BasePath");
    if (!string.IsNullOrEmpty(basePath))
    {
        app.UsePathBase(basePath);
    }

    var logging = serviceScope.ServiceProvider.GetRequiredService<ILogger<Program>>();
    var versionNumber = typeof(Program).Assembly.GetName().Version.ToString();

    Console.WriteLine("STARTUP Version " + versionNumber + ", path=" + basePath ?? string.Empty);
}

app.UseRouting();
app.UseCors("AllowSpecificOrigin");

app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
});

app.Run();

```

### Controllers/PublishProductController.cs

```csharp
using System.Collections.Specialized;
using System.Globalization;
using MediatR;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.VisualBasic;
using Owlstreet.Client;
using Owlstreet.Client.Services;
using Owlstreet.Common.Model.Common;
using Owlstreet.Common.Model.Connector;
using Owlstreet.Common.Model.Product;
using Owlstreet.Common.Model.Queues;
using Owlstreet.Connector.Example.Builders;
using Owlstreet.Connector.Example.Managers;
using Owlstreet.Connector.Example.Models;
using Owlstreet.WebApiUtil.Controllers;
using Owlstreet.WebApiUtil.Model;

namespace Owlstreet.Connector.Example.Controllers;

[Authorize]
[Produces("application/json")]
[Route("v1/publish/product")]
[ApiController]
public class PublishProductController : ConnectorControllerBase
{
    private OwlstreetClient _client;
    private IMediator _mediator;
    private ICurrentContext _currentContext;
    private ExampleConfiguration _configuration;
    private ExampleSystemConnectionManager _connectionManager;

    public PublishProductController(OwlstreetClient client,
        ICurrentContext currentContext,
        ILogger<PublishProductController> logger,
        IMediator mediator,
        ExampleSystemConnectionManager connectionManager,
        ExampleConfiguration configuration) :
        base(client, currentContext, logger)
    {
        _client = client;
        _mediator = mediator;
        _currentContext = currentContext;
        _configuration = configuration;
        _connectionManager = connectionManager;
    }

    [HttpGet]
    public ActionResult<JobStatus> PublishProduct()
    {
        JobStatus jobStatus;
        try
        {
            jobStatus = RegisterRun("publish-product");
        }
        catch (JobAlreadyRunningException)
        {
            return AlreadyRunning();
        }

        jobStatus.Added = 0;
        jobStatus.MessageType = MessageTypes.PRODUCT;

        // Find next indicator to know where to query remote system for changes.
        // if the value does not exist, the next indicator will be created for us.
        var nextIndicator = _client.ConnectorSupport.NextIndicatorFor("product-timestamp");

        var nextProducts = _connectionManager.FindModifiedAfter(nextIndicator.NextValue);
        if (nextProducts.Count == 0)
        {
            return OkNothingToProcessJson();
        }

        var lastDateTime = DateTime.MinValue;
        if (!string.IsNullOrEmpty(nextIndicator.NextValue) )
        {
            lastDateTime = DateTime.Parse(nextIndicator.NextValue, CultureInfo.InvariantCulture);
        }

        foreach (var exampleProduct in nextProducts)
        {
            var owlProduct = ExampleProductBuilder.Build(exampleProduct, _configuration);
            owlProduct.Source = _currentContext.IntegrationCode;

            if (exampleProduct.ModifiedAt > lastDateTime)
            {
                lastDateTime = exampleProduct.ModifiedAt;
            }

            _client.ProductService.Post(new List<Product>() { owlProduct });
        }

        nextIndicator.NextValue = lastDateTime.ToUniversalTime().ToString(CultureInfo.InvariantCulture);
        _client.ConnectorSupport.SetNextIndicator(nextIndicator);

        jobStatus.Status = $"Done, added {jobStatus.Added} products to queue.";

        return OkJson(jobStatus);
    }
}
```

### Models/ExampleConfiguration.cs

```csharp
using Owlstreet.Common.Model.Common;

namespace Owlstreet.Connector.Example.Models;

public class ExampleConfiguration
{
    public const string DEFAULT_LOCALE = "defaultLocale";
    public string DefaultLocale { get; set; }
    
    public ExampleConfiguration(ICurrentContext currentContext)
    {
        DefaultLocale = currentContext.GetSettingValue(DEFAULT_LOCALE, null);

        if (DefaultLocale == null)
            throw new ArgumentNullException($"Setting {DEFAULT_LOCALE} is missing from configuration");
    }
}
```

### Models/ExampleProduct.cs

```csharp
namespace Owlstreet.Connector.Example.Models;

public class ExampleProduct
{
    public string Name { get; set; }
    public string Description { get; set; }
    public List<ExampleVariation> Variants { get; set; }
    public List<string> options { get; set; }
    public string ImageUrl { get; set; }
    public DateTime ModifiedAt { get; set; }
}

public class ExampleVariation
{
    public string Sku { get; set; }
    public string Option1 { get; set; }
    public string Option2 { get; set; }
    public string Option3 { get; set; }
    public string Name { get; set; }
}
```


### Builders/ExampleProductBuilder.cs

```csharp
using Owlstreet.Common.Model.Product;
using Owlstreet.Connector.Example.Models;

namespace Owlstreet.Connector.Example.Builders;

public class ExampleProductBuilder
{
    public static Product Build(ExampleProduct productIn, ExampleConfiguration configuration)
    {
        var productOut = new Product();

        productOut.ExternalId = productIn.Variants?.Select(x => x.Sku)
            .OrderBy(x => x ).FirstOrDefault();

        if (!string.IsNullOrEmpty(productIn.Name))
        {
            productOut.Name = new Dictionary<string, string>()
            {
                { configuration.DefaultLocale, productIn.Name }
            };
        }

        if (!string.IsNullOrEmpty(productIn.Description))
        {
            productOut.Description = new Dictionary<string, string>()
            {
                { configuration.DefaultLocale, productIn.Description }
            };
        }

        if (!string.IsNullOrEmpty(productIn.ImageUrl))
        {
            productOut.PrimaryImage = new Resource()
            {
                Url = productIn.ImageUrl
            };
        }

        productOut.VariantAttributeCodes = productIn.options;
        productOut.Items = new List<Item>();
        
        foreach (var variation in productIn.Variants ?? new List<ExampleVariation>())
        {
            var itemOut = new Item(variation.Sku);
            if (!string.IsNullOrEmpty(variation.Name))
            {
                itemOut.Name = new Dictionary<string, string>()
                {
                    { configuration.DefaultLocale, variation.Name }
                };
            }

            itemOut.Attributes = new List<AttributeValue>();

            if (!string.IsNullOrEmpty(variation.Option1) && productIn.options.Count > 0)
            {
                itemOut.Attributes.Add(new AttributeValue(productIn.options[0], variation.Option1));
            }
            if (!string.IsNullOrEmpty(variation.Option2) && productIn.options.Count > 1)
            {
                itemOut.Attributes.Add(new AttributeValue(productIn.options[1], variation.Option2));
            }
            if (!string.IsNullOrEmpty(variation.Option3) && productIn.options.Count > 2)
            {
                itemOut.Attributes.Add(new AttributeValue(productIn.options[2], variation.Option3));
            }
            
            productOut.Items.Add(itemOut);
        }
        
        return productOut;
    }
}
```

### Managers/ExampleSystemConnectionManager.cs

```csharp
using Owlstreet.Connector.Example.Models;

namespace Owlstreet.Connector.Example.Managers;

public class ExampleSystemConnectionManager
{


    public List<ExampleProduct> FindModifiedAfter(string timestamp)
    {
        List<ExampleProduct> list = new List<ExampleProduct>();

        list.Add(new ExampleProduct()
        {
            Name = "Stylish T-Shirt manual style",
            Description = "A remarkable T-shirt with the manual of vim printed on it upside-down.",
            ImageUrl = "https://www.whaterver.se/images/product/vim-manual-tshirt.jpg",
            options = new List<string>() {"color", "size"},
            Variants = new List<ExampleVariation>()
            {
                new ExampleVariation()
                {
                    Name = "Black L",
                    Option1 = "black",
                    Option2 = "L",
                    Sku = "1000-100-L"
                },
                new ExampleVariation()
                {
                    Name = "Black XL",
                    Option1 = "black",
                    Option2 = "XL",
                    Sku = "1000-100-XL"
                },
                new ExampleVariation()
                {
                    Name = "White L",
                    Option1 = "white",
                    Option2 = "L",
                    Sku = "1000-120-L"
                },
                new ExampleVariation()
                {
                    Name = "Black XL",
                    Option1 = "white",
                    Option2 = "XL",
                    Sku = "1000-120-XL"
                }
            }
        });

        return list;
    }
    
}
```

