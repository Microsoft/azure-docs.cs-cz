---
title: Použití injektáže závislosti v rozhraní .NET Azure Functions
description: Naučte se používat vkládání závislostí k registraci a používání služeb ve funkcích .NET.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, Functions, architektura bez serveru
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: de8782edcc8b9c64621f1ca67d4bb810c926afaf
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973387"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Použití injektáže závislosti v rozhraní .NET Azure Functions

Azure Functions podporuje vzor návrhu pro vkládání závislostí (DI), což je technika pro dosažení [inverze řízení (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

- Vkládání závislostí v Azure Functions je postavené na funkcích injektáže .NET Core Dependency vstřik. Doporučuje se používat [vkládání závislostí .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) . Existují však rozdíly v tom, jak potlačíte závislosti a jak jsou čteny konfigurační hodnoty pomocí Azure Functions v plánu spotřeby.

- Podpora vkládání závislostí začíná Azure Functions 2. x.

## <a name="prerequisites"></a>Předpoklady

Než budete moci použít vkládání závislostí, je nutné nainstalovat následující balíčky NuGet:

- [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Balíček Microsoft. NET. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) verze 1.0.28 nebo novější

## <a name="register-services"></a>Registrovat služby

Chcete-li registrovat služby, vytvořte metodu pro konfiguraci a přidání součástí do instance `IFunctionsHostBuilder`.  Hostitel Azure Functions vytvoří instanci `IFunctionsHostBuilder` a předá ji přímo do vaší metody.

Chcete-li zaregistrovat metodu, přidejte atribut Assembly `FunctionsStartup`, který určuje název typu použitý při spuštění.

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

Série kroků registrace se spouští před a po zpracování spouštěcí třídy. Proto Pamatujte na následující položky:

- *Spouštěcí třída je určena pouze pro instalaci a registraci.* Vyhněte se používání služeb zaregistrovaných při spuštění během procesu spuštění. Nesnažte se třeba protokolovat zprávu do protokolovacího nástroje, který se registruje při spuštění. Tento bod procesu registrace je příliš brzy, aby byly vaše služby k dispozici pro použití. Po spuštění metody `Configure` bude modul runtime funkcí nadále registrovat další závislosti, které mohou ovlivnit fungování služeb.

- *Kontejner pro vkládání závislostí obsahuje pouze explicitně registrované typy*. Jediné služby, které jsou k dispozici jako vložené typy, jsou to, co je nastavení v metodě `Configure`. V důsledku toho typy specifické pro funkce, například `BindingContext` a `ExecutionContext`, nejsou během instalace nebo jako vložené typy k dispozici.

## <a name="use-injected-dependencies"></a>Použít vložené závislosti

K dispozici jsou závislosti pomocí injektáže konstruktoru ve funkci. Použití injektáže konstruktoru vyžaduje, abyste nepoužívali statické třídy.

Následující příklad ukazuje, jak jsou do funkce aktivované protokolem HTTP vloženy závislosti `IMyService` a `HttpClient`. V tomto příkladu se používá balíček [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) vyžadovaný k registraci `HttpClient` při spuštění.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
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

## <a name="service-lifetimes"></a>Životnost služeb

Azure Functions aplikace poskytují stejné životnosti služeb jako [vkládání závislostí ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). U aplikace Functions se chovají různé životnosti služby následujícím způsobem:

- **Přechodný**: přechodné služby se vytvářejí při každém požadavku služby.
- **Vymezené**: rozsah platnosti služby odpovídá době trvání spuštění funkce. Oborové služby se pro každé spuštění vytvoří jednou. Pozdější požadavky na tuto službu během opakovaného použití existující instance služby.
- **Singleton**: životnost služby typu Singleton odpovídá době životnosti hostitele a je znovu používána napříč prováděním funkce v dané instanci. Pro připojení a klienty jsou doporučovány služby životnosti singleton, například `SqlConnection` nebo `HttpClient` instance.

Zobrazit nebo stáhnout [ukázku různých životností služby](https://aka.ms/functions/di-sample) na GitHubu.

## <a name="logging-services"></a>Protokolovací služby

Pokud potřebujete vlastního zprostředkovatele protokolování, zaregistrujte vlastní typ jako instanci `ILoggerProvider`. Application Insights automaticky přidá Azure Functions.

> [!WARNING]
> - Nepřidávat do kolekce služeb `AddApplicationInsightsTelemetry()`, protože registruje služby, které jsou v konfliktu se službami poskytovanými prostředím.
> - Pokud používáte integrované Application Insights funkce, neregistrujte vlastní `TelemetryConfiguration` nebo `TelemetryClient`.

## <a name="function-app-provided-services"></a>Poskytnuté služby Function App

Hostitel funkce registruje mnoho služeb. V rámci vaší aplikace je možné v aplikaci provést zabezpečení těchto služeb:

|Typ služby|Platné|Popis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Konfigurace modulu runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Zodpovídá za poskytnutí ID instance hostitele.|

Pokud existují další služby, na kterých chcete převzít závislost, [vytvořte problém a navrhněte je na GitHubu](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Přepsání hostitelských služeb

Přepsání služeb poskytovaných hostitelem není aktuálně podporováno.  Pokud existují služby, které chcete přepsat, [vytvořte problém a navrhněte je na GitHubu](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Práce s možnostmi a nastaveními

Hodnoty definované v [nastavení aplikace](./functions-how-to-use-azure-function-app-settings.md#settings) jsou k dispozici v instanci `IConfiguration`, která umožňuje číst hodnoty nastavení aplikace ve třídě Startup.

Hodnoty z instance `IConfiguration` můžete extrahovat do vlastního typu. Kopírování hodnot nastavení aplikace do vlastního typu usnadňuje testování služeb tím, že se tyto hodnoty vloží. Nastavení číst do instance konfigurace musí být jednoduché páry klíč-hodnota.

Vezměte v úvahu následující třídu, která obsahuje vlastnost s názvem konzistentní s nastavením aplikace.

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

Z uvnitř metody `Startup.Configure` můžete extrahovat hodnoty z instance `IConfiguration` do vlastního typu pomocí následujícího kódu:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.Bind(settings);
                                           });
```

Volání `Bind` kopíruje hodnoty, které mají odpovídající názvy vlastností z konfigurace do vlastní instance. Instance Options je teď k dispozici v kontejneru IoC pro vkládání do funkce.

Objekt Options je vložen do funkce jako instance obecného rozhraní @no__t 0. Pro přístup k hodnotám nalezeným ve vaší konfiguraci použijte vlastnost `Value`.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _service = service;
        _settings = options.Value;
    }
}
```

Další podrobnosti týkající se práce s možnostmi najdete [v tématu vzor možností v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) .

> [!WARNING]
> Vyhněte se pokusu o čtení hodnot ze souborů, jako je *Local. Settings. JSON* nebo *appSettings. { Environment}. JSON* pro plán spotřeby Hodnoty načtené z těchto souborů souvisejících s triggery triggeru nejsou k dispozici, protože hostitelská infrastruktura nemá přístup k informacím o konfiguraci.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

- [Jak monitorovat aplikaci Function App](functions-monitoring.md)
- [Osvědčené postupy pro funkce](functions-best-practices.md)
