# DigitalOcean.Net

[ English ](README.md) | [ 中文 ](README.zh-CN.md) | [ 日本語 ](README.ja-JP.md) | [ **Français** ](README.fr-FR.md)

---

[![NuGet](https://img.shields.io/nuget/v/DigitalOcean.Net.svg)](https://www.nuget.org/packages/DigitalOcean.Net)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Build](https://github.com/m67186636/DigitalOcean.Net/actions/workflows/ci.yml/badge.svg)](https://github.com/m67186636/DigitalOcean.Net/actions/workflows/ci.yml)

Une bibliothèque client .NET complète pour l'[API DigitalOcean](https://docs.digitalocean.com/reference/api/reference/), couvrant **les 40+ catégories de ressources**, y compris Droplets, Kubernetes, App Platform, Databases et plus encore. Générée automatiquement à partir de la [spécification OpenAPI officielle de DigitalOcean](https://github.com/digitalocean/openapi).

## Fonctionnalités

- ✅ **Couverture API complète** — 40+ catégories de ressources DigitalOcean (Droplets, Apps, Databases, Kubernetes, VPCs, etc.)
- ✅ **Multi-ciblage** — Supporte `netstandard2.0`, `netstandard2.1`, `net8.0`, `net9.0`, `net10.0`
- ✅ **Typage fort** — Généré automatiquement à partir de la [spécification OpenAPI officielle](https://github.com/digitalocean/openapi)
- ✅ **Injection de dépendances** — Support natif de `Microsoft.Extensions.DependencyInjection`
- ✅ **CancellationToken** — Support complet async/await avec annulation
- ✅ **SourceLink** — Débogage pas à pas dans le code source de la bibliothèque
- ✅ **Intégration doctl** — Lecture directe du token depuis le fichier de configuration CLI `doctl`

## Installation

```bash
dotnet add package DigitalOcean.Net
```

## Démarrage rapide

### Utilisation directe

```csharp
using DigitalOcean.Net;

// Créer un client avec votre token API
var client = DigitalOceanClientFactory.Create("dop_v1_your_token_here");

// Ou lire le token depuis le fichier de configuration doctl (%APPDATA%\doctl\config.yaml)
var client = DigitalOceanClientFactory.CreateFromAppData();

// Lister tous les Droplets
var dropletsResponse = await client.V2.Droplets.GetAsync();
foreach (var droplet in dropletsResponse?.Droplets ?? [])
{
    Console.WriteLine($"{droplet.Name} - {droplet.Status}");
}

// Obtenir les informations du compte
var account = await client.V2.Account.GetAsync();
Console.WriteLine($"Email: {account?.Account?.Email}");

// Lister les applications (App Platform)
var apps = await client.V2.Apps.GetAsync();

// Lister les clusters Kubernetes
var k8s = await client.V2.Kubernetes.Clusters.GetAsync();

// Lister les bases de données
var dbs = await client.V2.Databases.GetAsync();
```

### Avec l'injection de dépendances

```csharp
using DigitalOcean.Net.Extensions;

// Dans Program.cs ou Startup.cs
builder.Services.AddDigitalOceanClient(options =>
{
    options.Token = builder.Configuration["DigitalOcean:Token"]!;
});

// Dans votre classe de service
public class MyService(DigitalOceanApiClient client)
{
    public async Task ListDroplets()
    {
        var response = await client.V2.Droplets.GetAsync();
        // ...
    }
}
```

### Configuration avancée

```csharp
// Options personnalisées
var client = DigitalOceanClientFactory.Create(new DigitalOceanClientOptions
{
    Token = "dop_v1_your_token_here",
    BaseUrl = "https://api.digitalocean.com",
    Timeout = TimeSpan.FromSeconds(60),
    UserAgent = "MyApp/1.0"
});

// Utiliser votre propre HttpClient (pour les proxys, gestionnaires personnalisés, etc.)
var httpClient = new HttpClient(new MyCustomHandler());
var client = DigitalOceanClientFactory.Create("dop_v1_token", httpClient);
```

## Couverture API

| Catégorie | Description | Espace de noms |
|---|---|---|
| Account | Informations du compte | `client.V2.Account` |
| Actions | Historique des actions | `client.V2.Actions` |
| Apps | Plateforme d'applications | `client.V2.Apps` |
| Billing | Facturation | `client.V2.Customers` |
| CDN | Diffusion de contenu | `client.V2.Cdn` |
| Certificates | Certificats SSL | `client.V2.Certificates` |
| Databases | Bases de données managées | `client.V2.Databases` |
| Domains | Gestion DNS | `client.V2.Domains` |
| Droplets | Machines virtuelles | `client.V2.Droplets` |
| Firewalls | Pare-feu cloud | `client.V2.Firewalls` |
| Functions | Fonctions serverless | `client.V2.Functions` |
| Images | Images OS | `client.V2.Images` |
| Kubernetes | Clusters K8s | `client.V2.Kubernetes` |
| Load Balancers | Équilibreurs de charge | `client.V2.LoadBalancers` |
| Monitoring | Surveillance et alertes | `client.V2.Monitoring` |
| Projects | Gestion de projets | `client.V2.Projects` |
| Regions | Régions disponibles | `client.V2.Regions` |
| Registry | Registre de conteneurs | `client.V2.Registry` |
| Reserved IPs | IPs réservées | `client.V2.ReservedIps` |
| Sizes | Tailles | `client.V2.Sizes` |
| Snapshots | Instantanés | `client.V2.Snapshots` |
| Spaces | Stockage objet | `client.V2.Spaces` |
| SSH Keys | Gestion des clés SSH | `client.V2.Account` |
| Tags | Étiquetage des ressources | `client.V2.Tags` |
| Uptime | Vérification de disponibilité | `client.V2.Uptime` |
| Volumes | Stockage bloc | `client.V2.Volumes` |
| VPCs | Réseaux privés virtuels | `client.V2.Vpcs` |

## Authentification

DigitalOcean utilise l'authentification par token Bearer OAuth. Générez un token depuis le [Panneau de contrôle DigitalOcean](https://cloud.digitalocean.com/account/api/tokens).

Préfixes des tokens :
- `dop_v1_` — Token d'accès personnel
- `doo_v1_` — Token d'application OAuth
- `dor_v1_` — Token de rafraîchissement

## Architecture technique

### Génération de code

Le code client de cette bibliothèque est généré automatiquement par [Microsoft Kiota](https://github.com/microsoft/kiota) à partir de la [spécification OpenAPI officielle de DigitalOcean](https://github.com/digitalocean/openapi). Cela signifie :

- **Exhaustivité** : La couverture API est parfaitement alignée avec la spécification officielle, sans endpoint manquant
- **Exactitude** : Les modèles de requête/réponse correspondent strictement aux définitions de l'API, assurant la sécurité des types
- **Maintenabilité** : Lorsque DigitalOcean met à jour son API, il suffit de régénérer pour synchroniser

### Explication des dépendances

Cette bibliothèque dépend des packages suivants, tous nécessaires pour une fonctionnalité complète :

#### Runtime Kiota (cœur du client API)

Ce sont les bibliothèques d'exécution requises par le code client généré par [Microsoft Kiota](https://github.com/microsoft/kiota). Kiota est le générateur officiel de clients OpenAPI de Microsoft, avec une conception modulaire où chaque format de sérialisation est un package indépendant :

| Package | Utilisation | Dépôt |
|---|---|---|
| [Microsoft.Kiota.Abstractions](https://www.nuget.org/packages/Microsoft.Kiota.Abstractions) | Couche d'abstraction Kiota, définition du pipeline requête/réponse | [microsoft/kiota-abstractions-dotnet](https://github.com/microsoft/kiota-abstractions-dotnet) |
| [Microsoft.Kiota.Http.HttpClientLibrary](https://www.nuget.org/packages/Microsoft.Kiota.Http.HttpClientLibrary) | Implémentation du transport HTTP basée sur `HttpClient` | [microsoft/kiota-http-dotnet](https://github.com/microsoft/kiota-http-dotnet) |
| [Microsoft.Kiota.Serialization.Json](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Json) | Sérialisation/désérialisation JSON (format principal de l'API) | [microsoft/kiota-serialization-json-dotnet](https://github.com/microsoft/kiota-serialization-json-dotnet) |
| [Microsoft.Kiota.Serialization.Text](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Text) | Sérialisation texte brut (certaines API retournent du texte) | [microsoft/kiota-serialization-text-dotnet](https://github.com/microsoft/kiota-serialization-text-dotnet) |
| [Microsoft.Kiota.Serialization.Form](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Form) | Sérialisation de données de formulaire (certaines API acceptent les soumissions de formulaire) | [microsoft/kiota-serialization-form-dotnet](https://github.com/microsoft/kiota-serialization-form-dotnet) |
| [Microsoft.Kiota.Serialization.Multipart](https://www.nuget.org/packages/Microsoft.Kiota.Serialization.Multipart) | Sérialisation de données multipart (téléchargement de fichiers, etc.) | [microsoft/kiota-serialization-multipart-dotnet](https://github.com/microsoft/kiota-serialization-multipart-dotnet) |

#### Support de l'injection de dépendances

| Package | Utilisation | Dépôt |
|---|---|---|
| [Microsoft.Extensions.DependencyInjection.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection.Abstractions) | Abstraction du conteneur DI, fournit les extensions `IServiceCollection` | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http) | Support `IHttpClientFactory`, gestion du cycle de vie HttpClient | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Extensions.Options](https://www.nuget.org/packages/Microsoft.Extensions.Options) | Modèle d'options, support de la liaison de configuration `IOptions<T>` | [dotnet/runtime](https://github.com/dotnet/runtime) |

#### Support d'outils

| Package | Utilisation | Dépôt |
|---|---|---|
| [YamlDotNet](https://www.nuget.org/packages/YamlDotNet) | Analyse YAML, pour lire le token depuis le fichier de configuration CLI `doctl` | [aaubry/YamlDotNet](https://github.com/aaubry/YamlDotNet) |

#### Compatibilité multi-framework (dépendances conditionnelles)

Les packages suivants ne sont introduits que pour les anciennes versions de framework cible, afin de compléter les API manquantes :

| Package | Condition | Utilisation | Dépôt |
|---|---|---|---|
| [System.Text.Json](https://www.nuget.org/packages/System.Text.Json) | `netstandard2.0`, `netstandard2.1` | Sérialisation JSON haute performance (intégrée dans .NET 8+) | [dotnet/runtime](https://github.com/dotnet/runtime) |
| [Microsoft.Bcl.AsyncInterfaces](https://www.nuget.org/packages/Microsoft.Bcl.AsyncInterfaces) | `netstandard2.0` uniquement | Interfaces asynchrones comme `IAsyncEnumerable<T>` (intégrées dans .NET Standard 2.1+) | [dotnet/runtime](https://github.com/dotnet/runtime) |

## Configuration requise

- .NET Standard 2.0+ / .NET 8.0+ / .NET 9.0+ / .NET 10.0+
- Un compte DigitalOcean et un token API

## Contribution

Les contributions sont les bienvenues ! Veuillez créer une Issue ou soumettre une Pull Request.

## Remerciements

Ce projet a été développé avec l'assistance de [GitHub Copilot CLI](https://github.com/github/copilot-cli) + [Claude Opus 4.6](https://www.anthropic.com/).

## Licence

Ce projet est sous licence MIT. Voir le fichier [LICENSE](LICENSE) pour plus de détails.
