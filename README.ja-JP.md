# DigitalOcean.Net

[ English ](README.md) | [ 中文 ](README.zh-CN.md) | [ **日本語** ](README.ja-JP.md) | [ Français ](README.fr-FR.md)

---

[![NuGet](https://img.shields.io/nuget/v/DigitalOcean.Net.svg)](https://www.nuget.org/packages/DigitalOcean.Net)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Build](https://github.com/m67186636/DigitalOcean.Net/actions/workflows/ci.yml/badge.svg)](https://github.com/m67186636/DigitalOcean.Net/actions/workflows/ci.yml)

[DigitalOcean API](https://docs.digitalocean.com/reference/api/reference/) 向けの包括的な .NET クライアントライブラリです。Droplets、Kubernetes、App Platform、Databases など、**40以上のリソースカテゴリすべて**をカバーしています。[DigitalOcean 公式 OpenAPI 仕様](https://github.com/digitalocean/openapi) から自動生成されています。

## 機能

- ✅ **完全な API カバレッジ** — 40以上の DigitalOcean リソースカテゴリ（Droplets、Apps、Databases、Kubernetes、VPCs など）
- ✅ **マルチターゲット** — `netstandard2.0`、`netstandard2.1`、`net8.0`、`net9.0`、`net10.0` をサポート
- ✅ **厳密な型付け** — [公式 OpenAPI 仕様](https://github.com/digitalocean/openapi) から自動生成
- ✅ **依存性注入** — `Microsoft.Extensions.DependencyInjection` のファーストクラスサポート
- ✅ **CancellationToken** — 完全な async/await とキャンセルのサポート
- ✅ **SourceLink** — ライブラリのソースコードにステップインしてデバッグ可能
- ✅ **doctl 統合** — `doctl` CLI 設定ファイルからトークンを直接読み取り

## インストール

```bash
dotnet add package DigitalOcean.Net
```

## クイックスタート

### 直接使用

```csharp
using DigitalOcean.Net;

// API トークンでクライアントを作成
var client = DigitalOceanClientFactory.Create("dop_v1_your_token_here");

// または doctl CLI 設定ファイルからトークンを読み取り（%APPDATA%\doctl\config.yaml）
var client = DigitalOceanClientFactory.CreateFromAppData();

// すべての Droplets を一覧表示
var dropletsResponse = await client.V2.Droplets.GetAsync();
foreach (var droplet in dropletsResponse?.Droplets ?? [])
{
    Console.WriteLine($"{droplet.Name} - {droplet.Status}");
}

// アカウント情報を取得
var account = await client.V2.Account.GetAsync();
Console.WriteLine($"Email: {account?.Account?.Email}");

// アプリを一覧表示（App Platform）
var apps = await client.V2.Apps.GetAsync();

// Kubernetes クラスターを一覧表示
var k8s = await client.V2.Kubernetes.Clusters.GetAsync();

// データベースを一覧表示
var dbs = await client.V2.Databases.GetAsync();
```

### 依存性注入の使用

```csharp
using DigitalOcean.Net.Extensions;

// Program.cs または Startup.cs にて
builder.Services.AddDigitalOceanClient(options =>
{
    options.Token = builder.Configuration["DigitalOcean:Token"]!;
});

// サービスクラスで注入して使用
public class MyService(DigitalOceanApiClient client)
{
    public async Task ListDroplets()
    {
        var response = await client.V2.Droplets.GetAsync();
        // ...
    }
}
```

### 詳細設定

```csharp
// カスタムオプション
var client = DigitalOceanClientFactory.Create(new DigitalOceanClientOptions
{
    Token = "dop_v1_your_token_here",
    BaseUrl = "https://api.digitalocean.com",
    Timeout = TimeSpan.FromSeconds(60),
    UserAgent = "MyApp/1.0"
});

// 独自の HttpClient を使用（プロキシ、カスタムハンドラーなど）
var httpClient = new HttpClient(new MyCustomHandler());
var client = DigitalOceanClientFactory.Create("dop_v1_token", httpClient);
```

## API カバレッジ

| カテゴリ | 説明 | 名前空間 |
|---|---|---|
| Account | アカウント情報 | `client.V2.Account` |
| Actions | アクション履歴 | `client.V2.Actions` |
| Apps | アプリプラットフォーム | `client.V2.Apps` |
| Billing | 請求・請求書 | `client.V2.Customers` |
| CDN | コンテンツ配信 | `client.V2.Cdn` |
| Certificates | SSL 証明書 | `client.V2.Certificates` |
| Databases | マネージドデータベース | `client.V2.Databases` |
| Domains | DNS 管理 | `client.V2.Domains` |
| Droplets | 仮想マシン | `client.V2.Droplets` |
| Firewalls | クラウドファイアウォール | `client.V2.Firewalls` |
| Functions | サーバーレス関数 | `client.V2.Functions` |
| Images | OS イメージ | `client.V2.Images` |
| Kubernetes | K8s クラスター | `client.V2.Kubernetes` |
| Load Balancers | ロードバランサー | `client.V2.LoadBalancers` |
| Monitoring | 監視・アラート | `client.V2.Monitoring` |
| Projects | プロジェクト管理 | `client.V2.Projects` |
| Regions | 利用可能リージョン | `client.V2.Regions` |
| Registry | コンテナレジストリ | `client.V2.Registry` |
| Reserved IPs | 予約済み IP | `client.V2.ReservedIps` |
| Sizes | サイズ | `client.V2.Sizes` |
| Snapshots | スナップショット | `client.V2.Snapshots` |
| Spaces | オブジェクトストレージ | `client.V2.Spaces` |
| SSH Keys | SSH キー管理 | `client.V2.Account` |
| Tags | リソースタグ | `client.V2.Tags` |
| Uptime | アップタイムチェック | `client.V2.Uptime` |
| Volumes | ブロックストレージ | `client.V2.Volumes` |
| VPCs | 仮想プライベートネットワーク | `client.V2.Vpcs` |

## 認証

DigitalOcean は OAuth Bearer トークン認証を使用します。[DigitalOcean コントロールパネル](https://cloud.digitalocean.com/account/api/tokens) からトークンを生成してください。

トークンのプレフィックス：
- `dop_v1_` — 個人アクセストークン
- `doo_v1_` — OAuth アプリケーショントークン
- `dor_v1_` — リフレッシュトークン

## 技術アーキテクチャ

### コード生成

本ライブラリのクライアントコードは、[Microsoft Kiota](https://github.com/microsoft/kiota) を使用して [DigitalOcean 公式 OpenAPI 仕様](https://github.com/digitalocean/openapi) から自動生成されています。これにより：

- **完全性**：API カバレッジは公式仕様と完全に一致し、エンドポイントの漏れがありません
- **正確性**：リクエスト/レスポンスモデルが API 定義と厳密に一致し、型安全です
- **保守性**：DigitalOcean が API を更新した際、再生成するだけで同期できます

### 依存パッケージの説明

本ライブラリは以下のパッケージに依存しており、すべて完全な機能を実現するために必要です：

#### Kiota ランタイム（API クライアントコア）

[Microsoft Kiota](https://github.com/microsoft/kiota) で生成されたクライアントコードに必要なランタイムライブラリです。Kiota はマイクロソフト公式の OpenAPI クライアントジェネレーターで、モジュラー設計を採用しており、各シリアライゼーション形式が独立したパッケージになっています：

| パッケージ | 用途 | リポジトリ |
|---|---|---|
| [Microsoft.Kiota.Abstractions](https://www.nuget.org/packages/Microsoft.Kiota.Abstractions) | Kiota コア抽象レイヤー、リクエスト/レスポンスパイプラインの定義 | [microsoft/kiota-abstractions-dotnet](https://github.com/microsoft/kiota-abstractions-dotnet) |
| [Microsoft.Kiota.Http.HttpClientLibrary](https://www.nuget.org/packages/Microsoft.Kiota.Http.HttpClientLibrary) | `HttpClient` ベースの HTTP トランスポート実装 | [microsoft/kiota-http-dotnet](https://github.com/microsoft/kiota-http-dotnet) |
| [Microsoft.Kiota.Serialization.Json](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Json) | JSON シリアライゼーション/デシリアライゼーション（API の主要データ形式） | [microsoft/kiota-serialization-json-dotnet](https://github.com/microsoft/kiota-serialization-json-dotnet) |
| [Microsoft.Kiota.Serialization.Text](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Text) | プレーンテキストシリアライゼーション（一部の API がテキストレスポンスを返す） | [microsoft/kiota-serialization-text-dotnet](https://github.com/microsoft/kiota-serialization-text-dotnet) |
| [Microsoft.Kiota.Serialization.Form](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Form) | フォームデータシリアライゼーション（一部の API がフォーム送信を受け付ける） | [microsoft/kiota-serialization-form-dotnet](https://github.com/microsoft/kiota-serialization-form-dotnet) |
| [Microsoft.Kiota.Serialization.Multipart](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Multipart) | Multipart データシリアライゼーション（ファイルアップロードなど） | [microsoft/kiota-serialization-multipart-dotnet](https://github.com/microsoft/kiota-serialization-multipart-dotnet) |

#### 依存性注入サポート

| パッケージ | 用途 | リポジトリ |
|---|---|---|
| [Microsoft.Extensions.DependencyInjection.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection.Abstractions) | DI コンテナ抽象、`IServiceCollection` 拡張の提供 | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http) | `IHttpClientFactory` サポート、HttpClient のライフサイクル管理 | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Extensions.Options](https://www.nuget.org/packages/Microsoft.Extensions.Options) | オプションパターン、`IOptions<T>` 設定バインディングのサポート | [dotnet/runtime](https://github.com/dotnet/runtime) |

#### ツールサポート

| パッケージ | 用途 | リポジトリ |
|---|---|---|
| [YamlDotNet](https://www.nuget.org/packages/YamlDotNet) | YAML 解析、`doctl` CLI 設定ファイルからのトークン読み取り用 | [aaubry/YamlDotNet](https://github.com/aaubry/YamlDotNet) |

#### マルチターゲットフレームワーク互換性（条件付き依存）

以下のパッケージは古いターゲットフレームワークでのみ導入され、不足している API を補完します：

| パッケージ | 条件 | 用途 | リポジトリ |
|---|---|---|---|
| [System.Text.Json](https://www.nuget.org/packages/System.Text.Json) | `netstandard2.0`、`netstandard2.1` | 高性能 JSON シリアライゼーション（.NET 8+ では組み込み） | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Bcl.AsyncInterfaces](https://www.nuget.org/packages/Microsoft.Bcl.AsyncInterfaces) | `netstandard2.0` のみ | `IAsyncEnumerable<T>` などの非同期インターフェース（.NET Standard 2.1+ では組み込み） | [dotnet/runtime](https://github.com/dotnet/runtime) |

## システム要件

- .NET Standard 2.0+ / .NET 8.0+ / .NET 9.0+ / .NET 10.0+
- DigitalOcean アカウントと API トークン

## コントリビューション

コントリビューションを歓迎します！Issue を作成するか、Pull Request を送信してください。

## 謝辞

本プロジェクトは [GitHub Copilot CLI](https://github.com/github/copilot-cli) + [Claude Opus 4.6](https://www.anthropic.com/) の支援を受けて開発されました。

## ライセンス

本プロジェクトは MIT ライセンスの下で提供されています。詳細は [LICENSE](LICENSE) ファイルをご覧ください。
