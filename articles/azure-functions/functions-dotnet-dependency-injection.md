---
title: Pomocí vkládání závislostí ve službě .NET Azure Functions
description: Další informace o použití injektáž závislostí pro registraci a používání služeb ve funkcích rozhraní .NET
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functions, architektury bez serveru
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, cshoe
ms.openlocfilehash: 781bcdc158cb362b7c46e1ba9771b6a92ebc56a8
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479621"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Pomocí vkládání závislostí ve službě .NET Azure Functions

Služba Azure Functions podporuje závislost vkládání (DI) software vzor návrhu, což je technika, abyste dosáhli [řízení IOC (Inversion)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislosti.

Služba Azure Functions je postavená funkce vkládání závislostí ASP.NET Core. Informovanost o služby, životnost a vzory návrhu [injektáž závislostí ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) před použitím DI funkcí ve službě Azure Functions se doporučuje aplikace.

Podpora pro vkládání závislostí začíná s využitím Azure Functions 2.x.

## <a name="prerequisites"></a>Požadavky

Před použitím injektáž závislostí, je třeba nainstalovat následující balíčky NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Balíček Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) verze 1.0.28 nebo novější

- Volitelné: [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) vyžaduje jenom u registrace HttpClient při spuštění

## <a name="register-services"></a>Registrace služeb

K registraci služeb, můžete vytvořit metodu ke konfiguraci a přidat k součásti `IFunctionsHostBuilder` instance.  Vytvoří instanci hostitele Azure Functions `IFunctionsHostBuilder` a předá ho přímo do vaší metody.

Chcete-li zaregistrovat metodu, přidejte `FunctionsStartup` během spouštění používá sestavení atribut, který určuje název typu. Také kód odkazuje na zkušební verzi [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) na webu Nuget.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Použití vloženého závislosti

ASP.NET Core pomocí konstruktoru injektáž závislostí k dispozici pro vaši funkci. Následující příklad ukazuje, jak `IMyService` a `HttpClient` závislosti jsou vloženy do funkci aktivovanou protokolem HTTP.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();;
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

Použití konstruktoru vkládání znamená, že statické funkce byste neměli používat, pokud chcete využít výhod vkládání závislostí.

## <a name="service-lifetimes"></a>Životnost služby

Aplikace Azure Function App, zadejte stejný životní cyklus služby jako [injektáž závislostí ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): přechodné, s vymezeným oborem a typu singleton.

V aplikaci funkcí odpovídá doba platnosti vymezené služby životního cyklu spouštění funkce. Vymezené služby se vytvoří jednou za spuštění. Vyšší požadavky na tuto službu během provádění znovu použít existující instanci služby. Doba platnosti služby typu singleton odpovídá životního cyklu hostitelů a je opětovně použít napříč provádění funkcí v této instanci.

Deklarace služeb typu singleton životnost se doporučují pro připojení a klientů, třeba `SqlConnection`, `CloudBlobClient`, nebo `HttpClient` instancí.

Zobrazení nebo stažení [vzorek jinou službu životnosti](https://aka.ms/functions/di-sample) na Githubu.

## <a name="logging-services"></a>Protokolování služby

Pokud budete potřebovat poskytovatele protokolování, je doporučený postup registrace `ILoggerProvider` instance. Služba Azure Functions je automaticky přidán Application Insights.

> [!WARNING]
> Nepřidávejte `AddApplicationInsightsTelemetry()` ke kolekci služby jako jeho služby registrů konfliktu s služeb poskytovaných prostředím.

## <a name="function-app-provided-services"></a>Aplikace, kterou poskytují služby – funkce

Funkce hostitele zaregistruje mnoho služeb. Tyto služby jsou bezpečné trvat v závislosti na vaší aplikace:

|Typ služby|Životnost|Popis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Konfigurace modulu runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Odpovídají za poskytování ID instance hostitele|

Pokud existují jiné služby, které chcete zavést závislost na, [vytvoření problému a navrhnout na Githubu](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Přepsání hostitele služby

Přepsání služby poskytované hostitelem se aktuálně nepodporuje.  Pokud nejsou služby, kterou chcete přepsat, [vytvoření problému a navrhnout na Githubu](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

- [Jak monitorovat aplikace function app](functions-monitoring.md)
- [Osvědčené postupy pro službu functions](functions-best-practices.md)
