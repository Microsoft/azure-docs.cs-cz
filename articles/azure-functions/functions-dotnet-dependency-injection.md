---
title: Použití injektáže závislostí ve službě Azure Functions pro .NET
description: Naučte se používat vkládání závislostí pro registraci a používání služeb ve funkcích rozhraní .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678441"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Použití injektáže závislostí ve službě Azure Functions pro .NET

Funkce Azure podporuje vzor návrhu softwaru vkládání závislostí (DI), což je technika k dosažení [inverze řízení (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostí.

- Vkládání závislostí ve funkcích Azure je postavené na funkcích vkládání závislostí .NET Core. Doporučuje se seznámit s [vkládáním závislostí .NET Core.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) Existují rozdíly v tom, jak přepsat závislosti a jak se hodnoty konfigurace číst pomocí funkce Azure v plánu spotřeby.

- Podpora vkládání závislostí začíná na Azure Functions 2.x.

## <a name="prerequisites"></a>Požadavky

Před použitím vkládání závislostí je nutné nainstalovat následující balíčky NuGet:

- [Rozšíření Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Balíček Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) verze 1.0.28 nebo novější

## <a name="register-services"></a>Zaregistrovat služby

Chcete-li zaregistrovat služby, vytvořte metodu konfigurace a přidání součástí do `IFunctionsHostBuilder` instance.  Hostitel Azure Functions vytvoří `IFunctionsHostBuilder` instanci a předá ji přímo do vaší metody.

Chcete-li metodu `FunctionsStartup` zaregistrovat, přidejte atribut sestavení, který určuje název typu použitý při spuštění.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Upozornění

Řada kroků registrace spustit před a po runtime zpracovává spouštěcí třídy. Proto mějte na paměti následující položky:

- *Třída spuštění je určena pouze pro nastavení a registraci.* Nepoužívejte služby registrované při spuštění během procesu spouštění. Například nepokoušejte se protokolovat zprávu v protokolovacím panelu, který je registrován během spuštění. Tento bod registračního procesu je příliš brzy na to, aby vaše služby byly k dispozici pro použití. Po `Configure` spuštění metody functions runtime nadále zaregistrovat další závislosti, které mohou ovlivnit, jak vaše služby fungují.

- *Kontejner vkládání závislostí obsahuje pouze explicitně registrované typy*. Pouze služby, které jsou k dispozici `Configure` jako injectable typy jsou jaké jsou nastavení v metodě. V důsledku toho funkce specifické `BindingContext` `ExecutionContext` typy jako a nejsou k dispozici během instalace nebo jako injekční typy.

## <a name="use-injected-dependencies"></a>Použití vložených závislostí

Vkládání konstruktoru se používá k zpřístupnění závislostí ve funkci. Použití vstřikování konstruktoru vyžaduje, abyste nepoužívali statické třídy.

Následující ukázka ukazuje, `IMyService` `HttpClient` jak jsou vloženy a závislosti do funkce spouštěné http. Tento příklad používá [microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) balíček `HttpClient` potřebný k registraci při spuštění.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Životnost služby

Aplikace Azure Functions poskytují stejnou dobu životnosti služby jako [ASP.NET vkládání závislostí](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). U aplikace Functions se různé doby trvání služby chovají takto:

- **Přechodné**: Přechodné služby jsou vytvořeny na základě každé žádosti služby.
- **Rozsah**: Životnost služby s vymezeným oborem odpovídá životnosti spuštění funkce. Služby s vymezením oborů jsou vytvořeny jednou za spuštění. Pozdější požadavky na tuto službu během provádění znovu použít existující instance služby.
- **Singleton**: Životnost služby singleton odpovídá životnosti hostitele a je znovu použita napříč spuštěními funkcí v této instanci. Služby životnosti Singleton jsou doporučeny `SqlConnection` `HttpClient` pro připojení a klienty, například nebo instance.

Prohlédněte nebo si stáhněte [ukázku různých životností služby](https://aka.ms/functions/di-sample) na GitHubu.

## <a name="logging-services"></a>Logovací služby

Pokud potřebujete vlastního poskytovatele protokolování, zaregistrujte `ILoggerProvider` vlastní typ jako instanci. Application Insights se automaticky přidávají pomocí Azure Functions.

> [!WARNING]
> - Nepřidávejte `AddApplicationInsightsTelemetry()` do kolekce služeb, protože registruje služby, které jsou v konfliktu se službami poskytovanými prostředím.
> - Neregistrujte vlastní `TelemetryConfiguration` `TelemetryClient` nebo pokud používáte předdefinované funkce Application Insights. Pokud potřebujete nakonfigurovat `TelemetryClient` vlastní instanci, vytvořte ji pomocí injektáže, `TelemetryConfiguration` jak je znázorněno na [monitoru Azure Functions](./functions-monitoring.md#version-2x-and-later-2).

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> a ILoggerFactory

Hostitel bude `ILogger<T>` inject `ILoggerFactory` a služby do konstruktory.  Ve výchozím nastavení však budou tyto nové filtry protokolování filtrovány z protokolů funkcí.  Budete muset upravit `host.json` soubor, abyste se přihlásili k dalším filtrům a kategoriím.  Následující ukázka ukazuje `ILogger<HttpTrigger>` přidání s protokoly, které budou vystaveny hostitelem.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

A `host.json` soubor, který přidá filtr protokolu.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>Služby poskytované aplikací Function App

Hostitel funkce registruje mnoho služeb. Následující služby jsou bezpečné jako závislost ve vaší aplikaci:

|Typ služby|Doba platnosti|Popis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Konfigurace runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Odpovědný za poskytnutí ID instance hostitele|

Pokud existují další služby, na kterých chcete mít závislost, [vytvořte problém a navrhněte je na GitHubu](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Přepsání hostitelských služeb

Přepsání služeb poskytovaných hostitelem není aktuálně podporováno.  Pokud existují služby, které chcete přepsat, [vytvořte problém a navrhněte je na GitHubu](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Práce s možnostmi a nastaveními

Hodnoty definované v [nastavení](./functions-how-to-use-azure-function-app-settings.md#settings) aplikace `IConfiguration` jsou k dispozici v instanci, která umožňuje číst hodnoty nastavení aplikace ve třídě po spuštění.

Hodnoty z `IConfiguration` instance můžete extrahovat do vlastního typu. Kopírování hodnot nastavení aplikace do vlastního typu usnadňuje testování služeb tím, že tyto hodnoty lze vložit. Nastavení přečtené do instance konfigurace musí být jednoduché dvojice klíč/hodnota.

Zvažte následující třídu, která obsahuje vlastnost s názvem konzistentní s nastavením aplikace:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

A `local.settings.json` soubor, který může strukturovat vlastní nastavení takto:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Z metody `Startup.Configure` můžete extrahovat hodnoty `IConfiguration` z instance do vlastního typu pomocí následujícího kódu:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Volání `Bind` kopií hodnot, které mají odpovídající názvy vlastností z konfigurace do vlastní instance. Instance možností je nyní k dispozici v kontejneru IoC pro injekci do funkce.

Options Objekt je vložen do funkce jako instance `IOptions` obecné rozhraní. Pomocí `Value` vlastnosti můžete získat přístup k hodnotám, které se nacházejí ve vaší konfiguraci.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Další podrobnosti o práci s možnostmi naleznete [ve vzoru možnosti v ASP.NET jádra.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

> [!WARNING]
> Nepokoušejte se číst hodnoty ze souborů, jako je *local.settings.json* nebo *appsettings.{ prostředí}.json* v plánu Spotřeba. Hodnoty přečtené z těchto souborů související s aktivačními připojeními nejsou k dispozici, protože se škáluje infrastruktura pro hostování, protože hostitelská infrastruktura nemá přístup k informacím o konfiguraci.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

- [Jak sledovat aplikaci funkcí](functions-monitoring.md)
- [Osvědčené postupy pro funkce](functions-best-practices.md)
