# DigitalOcean.Net

[ English ](README.md) | [ **中文** ](README.zh-CN.md) | [ 日本語 ](README.ja-JP.md) | [ Français ](README.fr-FR.md)

---

[![NuGet](https://img.shields.io/nuget/v/DigitalOcean.Net.svg)](https://www.nuget.org/packages/DigitalOcean.Net)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Build](https://github.com/m67186636/DigitalOcean.Net/actions/workflows/ci.yml/badge.svg)](https://github.com/m67186636/DigitalOcean.Net/actions/workflows/ci.yml)

一个全面的 DigitalOcean API .NET 客户端库，覆盖**全部 40+ 资源类别**，包括 Droplets、Kubernetes、App Platform、Databases 等。基于 [DigitalOcean 官方 OpenAPI 规范](https://github.com/digitalocean/openapi) 自动生成。

## 功能特性

- ✅ **全量 API 覆盖** — 40+ DigitalOcean 资源类别（Droplets、Apps、Databases、Kubernetes、VPCs 等）
- ✅ **多目标框架** — 支持 `netstandard2.0`、`netstandard2.1`、`net8.0`、`net9.0`、`net10.0`
- ✅ **强类型** — 从 [官方 OpenAPI 规范](https://github.com/digitalocean/openapi) 自动生成
- ✅ **依赖注入** — 一流的 `Microsoft.Extensions.DependencyInjection` 支持
- ✅ **CancellationToken** — 完整的异步/等待与取消支持
- ✅ **SourceLink** — 支持调试时步入库源代码
- ✅ **doctl 集成** — 直接从 `doctl` CLI 配置文件读取令牌

## 安装

```bash
dotnet add package DigitalOcean.Net
```

## 快速开始

### 直接使用

```csharp
using DigitalOcean.Net;

// 使用 API 令牌创建客户端
var client = DigitalOceanClientFactory.Create("dop_v1_your_token_here");

// 或从 doctl CLI 配置文件读取令牌（%APPDATA%\doctl\config.yaml）
var client = DigitalOceanClientFactory.CreateFromAppData();

// 列出所有 Droplets
var dropletsResponse = await client.V2.Droplets.GetAsync();
foreach (var droplet in dropletsResponse?.Droplets ?? [])
{
    Console.WriteLine($"{droplet.Name} - {droplet.Status}");
}

// 获取账户信息
var account = await client.V2.Account.GetAsync();
Console.WriteLine($"Email: {account?.Account?.Email}");

// 列出应用（App Platform）
var apps = await client.V2.Apps.GetAsync();

// 列出 Kubernetes 集群
var k8s = await client.V2.Kubernetes.Clusters.GetAsync();

// 列出数据库
var dbs = await client.V2.Databases.GetAsync();
```

### 使用依赖注入

```csharp
using DigitalOcean.Net.Extensions;

// 在 Program.cs 或 Startup.cs 中
builder.Services.AddDigitalOceanClient(options =>
{
    options.Token = builder.Configuration["DigitalOcean:Token"]!;
});

// 在服务类中注入使用
public class MyService(DigitalOceanApiClient client)
{
    public async Task ListDroplets()
    {
        var response = await client.V2.Droplets.GetAsync();
        // ...
    }
}
```

### 高级配置

```csharp
// 自定义选项
var client = DigitalOceanClientFactory.Create(new DigitalOceanClientOptions
{
    Token = "dop_v1_your_token_here",
    BaseUrl = "https://api.digitalocean.com",
    Timeout = TimeSpan.FromSeconds(60),
    UserAgent = "MyApp/1.0"
});

// 使用自定义 HttpClient（适用于代理、自定义处理程序等）
var httpClient = new HttpClient(new MyCustomHandler());
var client = DigitalOceanClientFactory.Create("dop_v1_token", httpClient);
```

## API 覆盖范围

| 类别 | 描述 | 命名空间 |
|---|---|---|
| Account | 账户信息 | `client.V2.Account` |
| Actions | 操作记录 | `client.V2.Actions` |
| Apps | 应用平台 | `client.V2.Apps` |
| Billing | 账单与发票 | `client.V2.Customers` |
| CDN | 内容分发 | `client.V2.Cdn` |
| Certificates | SSL 证书 | `client.V2.Certificates` |
| Databases | 托管数据库 | `client.V2.Databases` |
| Domains | DNS 管理 | `client.V2.Domains` |
| Droplets | 虚拟机 | `client.V2.Droplets` |
| Firewalls | 云防火墙 | `client.V2.Firewalls` |
| Functions | Serverless 函数 | `client.V2.Functions` |
| Images | 系统镜像 | `client.V2.Images` |
| Kubernetes | K8s 集群 | `client.V2.Kubernetes` |
| Load Balancers | 负载均衡器 | `client.V2.LoadBalancers` |
| Monitoring | 监控告警 | `client.V2.Monitoring` |
| Projects | 项目管理 | `client.V2.Projects` |
| Regions | 可用区域 | `client.V2.Regions` |
| Registry | 容器镜像仓库 | `client.V2.Registry` |
| Reserved IPs | 保留 IP | `client.V2.ReservedIps` |
| Sizes | 规格 | `client.V2.Sizes` |
| Snapshots | 快照 | `client.V2.Snapshots` |
| Spaces | 对象存储 | `client.V2.Spaces` |
| SSH Keys | SSH 密钥管理 | `client.V2.Account` |
| Tags | 资源标签 | `client.V2.Tags` |
| Uptime | 可用性检测 | `client.V2.Uptime` |
| Volumes | 块存储 | `client.V2.Volumes` |
| VPCs | 虚拟私有网络 | `client.V2.Vpcs` |

## 认证

DigitalOcean 使用 OAuth Bearer 令牌认证。从 [DigitalOcean 控制面板](https://cloud.digitalocean.com/account/api/tokens) 生成令牌。

令牌前缀：
- `dop_v1_` — 个人访问令牌
- `doo_v1_` — OAuth 应用令牌
- `dor_v1_` — 刷新令牌

## 技术架构

### 代码生成

本库的客户端代码由 [Microsoft Kiota](https://github.com/microsoft/kiota) 从 [DigitalOcean 官方 OpenAPI 规范](https://github.com/digitalocean/openapi) 自动生成。这意味着：

- **完整性**：API 覆盖与官方规范完全一致，不会遗漏端点
- **准确性**：请求/响应模型严格匹配 API 定义，类型安全
- **可维护性**：当 DigitalOcean 更新 API 时，只需重新生成即可同步更新

### 依赖项说明

本库依赖以下包，均为实现完整功能所必需：

#### Kiota 运行时（API 客户端核心）

这些是 [Microsoft Kiota](https://github.com/microsoft/kiota) 生成的客户端代码所需的运行时库。Kiota 是微软官方的 OpenAPI 客户端生成器，采用模块化设计，每个序列化格式独立为一个包：

| 包 | 用途 | 仓库 |
|---|---|---|
| [Microsoft.Kiota.Abstractions](https://www.nuget.org/packages/Microsoft.Kiota.Abstractions) | Kiota 核心抽象层，定义请求/响应管道 | [microsoft/kiota-abstractions-dotnet](https://github.com/microsoft/kiota-abstractions-dotnet) |
| [Microsoft.Kiota.Http.HttpClientLibrary](https://www.nuget.org/packages/Microsoft.Kiota.Http.HttpClientLibrary) | 基于 `HttpClient` 的 HTTP 传输实现 | [microsoft/kiota-http-dotnet](https://github.com/microsoft/kiota-http-dotnet) |
| [Microsoft.Kiota.Serialization.Json](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Json) | JSON 序列化/反序列化（API 的主要数据格式） | [microsoft/kiota-serialization-json-dotnet](https://github.com/microsoft/kiota-serialization-json-dotnet) |
| [Microsoft.Kiota.Serialization.Text](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Text) | 纯文本序列化（部分 API 返回文本响应） | [microsoft/kiota-serialization-text-dotnet](https://github.com/microsoft/kiota-serialization-text-dotnet) |
| [Microsoft.Kiota.Serialization.Form](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Form) | 表单数据序列化（部分 API 接受表单提交） | [microsoft/kiota-serialization-form-dotnet](https://github.com/microsoft/kiota-serialization-form-dotnet) |
| [Microsoft.Kiota.Serialization.Multipart](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Multipart) | Multipart 数据序列化（文件上传等场景） | [microsoft/kiota-serialization-multipart-dotnet](https://github.com/microsoft/kiota-serialization-multipart-dotnet) |

#### 依赖注入支持

| 包 | 用途 | 仓库 |
|---|---|---|
| [Microsoft.Extensions.DependencyInjection.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection.Abstractions) | DI 容器抽象，提供 `IServiceCollection` 扩展 | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http) | `IHttpClientFactory` 支持，管理 HttpClient 生命周期 | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Extensions.Options](https://www.nuget.org/packages/Microsoft.Extensions.Options) | 选项模式，支持 `IOptions<T>` 配置绑定 | [dotnet/runtime](https://github.com/dotnet/runtime) |

#### 工具支持

| 包 | 用途 | 仓库 |
|---|---|---|
| [YamlDotNet](https://www.nuget.org/packages/YamlDotNet) | YAML 解析，用于读取 `doctl` CLI 配置文件中的令牌 | [aaubry/YamlDotNet](https://github.com/aaubry/YamlDotNet) |

#### 多目标框架兼容（条件依赖）

以下包仅在旧版目标框架下引入，为其补充缺失的 API：

| 包 | 条件 | 用途 | 仓库 |
|---|---|---|---|
| [System.Text.Json](https://www.nuget.org/packages/System.Text.Json) | `netstandard2.0`、`netstandard2.1` | 高性能 JSON 序列化（.NET 8+ 已内置） | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Bcl.AsyncInterfaces](https://www.nuget.org/packages/Microsoft.Bcl.AsyncInterfaces) | 仅 `netstandard2.0` | `IAsyncEnumerable<T>` 等异步接口（.NET Standard 2.1+ 已内置） | [dotnet/runtime](https://github.com/dotnet/runtime) |

## 系统要求

- .NET Standard 2.0+ / .NET 8.0+ / .NET 9.0+ / .NET 10.0+
- DigitalOcean 账户和 API 令牌

## 贡献

欢迎贡献！请创建 Issue 或提交 Pull Request。

## 致谢

本项目由 [GitHub Copilot CLI](https://github.com/github/copilot-cli) + [Claude Opus 4.6](https://www.anthropic.com/) 协助开发。

## 许可证

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。
