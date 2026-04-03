# DigitalOcean.Net

[ **English** ](README.md) | [ 中文 ](README.zh-CN.md) | [ 日本語 ](README.ja-JP.md) | [ Français ](README.fr-FR.md)

---

[![NuGet](https://img.shields.io/nuget/v/DigitalOcean.Net.svg)](https://www.nuget.org/packages/DigitalOcean.Net)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Build](https://github.com/m67186636/DigitalOcean.Net/actions/workflows/ci.yml/badge.svg)](https://github.com/m67186636/DigitalOcean.Net/actions/workflows/ci.yml)

A comprehensive .NET client library for the [DigitalOcean API](https://docs.digitalocean.com/reference/api/reference/), covering **all 40+ resource categories** including Droplets, Kubernetes, App Platform, Databases, and more.

## Features

- ✅ **Full API Coverage** — All 40+ DigitalOcean resource categories (Droplets, Apps, Databases, Kubernetes, VPCs, etc.)
- ✅ **Multi-targeting** — Supports `netstandard2.0`, `netstandard2.1`, `net8.0`, `net9.0`, `net10.0`
- ✅ **Strongly-typed** — Auto-generated from the [official OpenAPI specification](https://github.com/digitalocean/openapi)
- ✅ **Dependency Injection** — First-class support for `Microsoft.Extensions.DependencyInjection`
- ✅ **CancellationToken** — Full async/await with cancellation support
- ✅ **SourceLink** — Debug into library source code
- ✅ **doctl Integration** — Read token directly from `doctl` CLI config

## Installation

```bash
dotnet add package DigitalOcean.Net
```

## Quick Start

### Direct Usage

```csharp
using DigitalOcean.Net;

// Create client with your API token
var client = DigitalOceanClientFactory.Create("dop_v1_your_token_here");

// Or read token from doctl CLI config (~/.config/doctl/config.yaml)
var client = DigitalOceanClientFactory.CreateFromAppData();

// List all Droplets
var dropletsResponse = await client.V2.Droplets.GetAsync();
foreach (var droplet in dropletsResponse?.Droplets ?? [])
{
    Console.WriteLine($"{droplet.Name} - {droplet.Status}");
}

// Get account info
var account = await client.V2.Account.GetAsync();
Console.WriteLine($"Email: {account?.Account?.Email}");

// List Apps (App Platform)
var apps = await client.V2.Apps.GetAsync();

// List Kubernetes clusters
var k8s = await client.V2.Kubernetes.Clusters.GetAsync();

// List databases
var dbs = await client.V2.Databases.GetAsync();
```

### With Dependency Injection

```csharp
using DigitalOcean.Net.Extensions;

// In Program.cs or Startup.cs
builder.Services.AddDigitalOceanClient(options =>
{
    options.Token = builder.Configuration["DigitalOcean:Token"]!;
});

// In your service class
public class MyService(DigitalOceanApiClient client)
{
    public async Task ListDroplets()
    {
        var response = await client.V2.Droplets.GetAsync();
        // ...
    }
}
```

### Advanced Configuration

```csharp
// Custom options
var client = DigitalOceanClientFactory.Create(new DigitalOceanClientOptions
{
    Token = "dop_v1_your_token_here",
    BaseUrl = "https://api.digitalocean.com",
    Timeout = TimeSpan.FromSeconds(60),
    UserAgent = "MyApp/1.0"
});

// Use your own HttpClient (for proxies, custom handlers, etc.)
var httpClient = new HttpClient(new MyCustomHandler());
var client = DigitalOceanClientFactory.Create("dop_v1_token", httpClient);
```

## API Coverage

| Category | Description | Namespace |
|---|---|---|
| Account | Account info | `client.V2.Account` |
| Actions | Action history | `client.V2.Actions` |
| Apps | App Platform | `client.V2.Apps` |
| Billing | Billing & invoices | `client.V2.Customers` |
| CDN | Content delivery | `client.V2.Cdn` |
| Certificates | SSL certificates | `client.V2.Certificates` |
| Databases | Managed databases | `client.V2.Databases` |
| Domains | DNS management | `client.V2.Domains` |
| Droplets | Virtual machines | `client.V2.Droplets` |
| Firewalls | Cloud firewalls | `client.V2.Firewalls` |
| Functions | Serverless functions | `client.V2.Functions` |
| Images | OS images | `client.V2.Images` |
| Kubernetes | K8s clusters | `client.V2.Kubernetes` |
| Load Balancers | Load balancers | `client.V2.LoadBalancers` |
| Monitoring | Alerts & metrics | `client.V2.Monitoring` |
| Projects | Project management | `client.V2.Projects` |
| Regions | Available regions | `client.V2.Regions` |
| Registry | Container registry | `client.V2.Registry` |
| Reserved IPs | Static IPs | `client.V2.ReservedIps` |
| Sizes | Droplet sizes | `client.V2.Sizes` |
| Snapshots | Volume snapshots | `client.V2.Snapshots` |
| Spaces | Object storage | `client.V2.Spaces` |
| SSH Keys | SSH key management | `client.V2.Account` |
| Tags | Resource tagging | `client.V2.Tags` |
| Uptime | Uptime checks | `client.V2.Uptime` |
| Volumes | Block storage | `client.V2.Volumes` |
| VPCs | Virtual networks | `client.V2.Vpcs` |

## Authentication

DigitalOcean uses OAuth Bearer tokens. Generate a token from the [DigitalOcean Control Panel](https://cloud.digitalocean.com/account/api/tokens).

Token prefixes:
- `dop_v1_` — Personal access token
- `doo_v1_` — OAuth application token
- `dor_v1_` — Refresh token

## Technical Architecture

### Code Generation

The client code in this library is auto-generated by [Microsoft Kiota](https://github.com/microsoft/kiota) from the [DigitalOcean official OpenAPI specification](https://github.com/digitalocean/openapi). This means:

- **Completeness**: API coverage is perfectly aligned with the official spec — no missing endpoints
- **Accuracy**: Request/response models strictly match API definitions, ensuring type safety
- **Maintainability**: When DigitalOcean updates their API, simply regenerate to sync

### Dependencies Explained

This library depends on the following packages, all necessary for full functionality:

#### Kiota Runtime (API Client Core)

These are the runtime libraries required by [Microsoft Kiota](https://github.com/microsoft/kiota)-generated client code. Kiota is Microsoft's official OpenAPI client generator with a modular design where each serialization format is an independent package:

| Package | Purpose | Repository |
|---|---|---|
| [Microsoft.Kiota.Abstractions](https://www.nuget.org/packages/Microsoft.Kiota.Abstractions) | Kiota core abstraction layer, defines request/response pipeline | [microsoft/kiota-abstractions-dotnet](https://github.com/microsoft/kiota-abstractions-dotnet) |
| [Microsoft.Kiota.Http.HttpClientLibrary](https://www.nuget.org/packages/Microsoft.Kiota.Http.HttpClientLibrary) | HTTP transport implementation based on `HttpClient` | [microsoft/kiota-http-dotnet](https://github.com/microsoft/kiota-http-dotnet) |
| [Microsoft.Kiota.Serialization.Json](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Json) | JSON serialization/deserialization (primary API data format) | [microsoft/kiota-serialization-json-dotnet](https://github.com/microsoft/kiota-serialization-json-dotnet) |
| [Microsoft.Kiota.Serialization.Text](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Text) | Plain text serialization (some APIs return text responses) | [microsoft/kiota-serialization-text-dotnet](https://github.com/microsoft/kiota-serialization-text-dotnet) |
| [Microsoft.Kiota.Serialization.Form](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Form) | Form data serialization (some APIs accept form submissions) | [microsoft/kiota-serialization-form-dotnet](https://github.com/microsoft/kiota-serialization-form-dotnet) |
| [Microsoft.Kiota.Serialization.Multipart](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Multipart) | Multipart data serialization (file uploads, etc.) | [microsoft/kiota-serialization-multipart-dotnet](https://github.com/microsoft/kiota-serialization-multipart-dotnet) |

#### Dependency Injection Support

| Package | Purpose | Repository |
|---|---|---|
| [Microsoft.Extensions.DependencyInjection.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection.Abstractions) | DI container abstraction, provides `IServiceCollection` extensions | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http) | `IHttpClientFactory` support, manages HttpClient lifecycle | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Extensions.Options](https://www.nuget.org/packages/Microsoft.Extensions.Options) | Options pattern, supports `IOptions<T>` configuration binding | [dotnet/runtime](https://github.com/dotnet/runtime) |

#### Tool Support

| Package | Purpose | Repository |
|---|---|---|
| [YamlDotNet](https://www.nuget.org/packages/YamlDotNet) | YAML parsing, for reading tokens from `doctl` CLI configuration file | [aaubry/YamlDotNet](https://github.com/aaubry/YamlDotNet) |

#### Multi-Target Framework Compatibility (Conditional Dependencies)

The following packages are only included for older target frameworks to supplement missing APIs:

| Package | Condition | Purpose | Repository |
|---|---|---|---|
| [System.Text.Json](https://www.nuget.org/packages/System.Text.Json) | `netstandard2.0`, `netstandard2.1` | High-performance JSON serialization (built-in for .NET 8+) | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Bcl.AsyncInterfaces](https://www.nuget.org/packages/Microsoft.Bcl.AsyncInterfaces) | `netstandard2.0` only | Async interfaces like `IAsyncEnumerable<T>` (built-in for .NET Standard 2.1+) | [dotnet/runtime](https://github.com/dotnet/runtime) |

## Requirements

- .NET Standard 2.0+ / .NET 8.0+ / .NET 9.0+ / .NET 10.0+
- A DigitalOcean account and API token

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## Acknowledgements

This project was built with the assistance of [GitHub Copilot CLI](https://github.com/github/copilot-cli) + [Claude Opus 4.6](https://www.anthropic.com/).

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.