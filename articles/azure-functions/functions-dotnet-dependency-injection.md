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
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 1ebb2fd77830074648a580dddad98e05e10c9c75
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850022"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Použití injektáže závislosti v rozhraní .NET Azure Functions

Azure Functions podporuje vzor návrhu pro vkládání závislostí (DI), což je technika pro dosažení [inverze řízení (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

Azure Functions sestaví nad ASP.NET Core funkcí injektáže závislostí. Doporučujeme, abyste si před použitím funkcí DI v aplikaci Azure Functions zvyklí na služby, životnosti a vzory návrhu pro [vkládání závislostí ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) .

Podpora vkládání závislostí začíná Azure Functions 2. x.

## <a name="prerequisites"></a>Požadavky

Než budete moci použít vkládání závislostí, je nutné nainstalovat následující balíčky NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Balíček Microsoft. NET. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) verze 1.0.28 nebo novější

- Volitelné: [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) se vyžaduje jenom pro registraci HttpClient při spuštění.

## <a name="register-services"></a>Registrovat služby

Chcete-li registrovat služby, můžete vytvořit metodu konfigurace a přidání součástí do `IFunctionsHostBuilder` instance.  Hostitel Azure Functions vytvoří instanci `IFunctionsHostBuilder` a předá ji přímo do vaší metody.

Chcete-li zaregistrovat metodu, přidejte `FunctionsStartup` atribut Assembly, který určuje název typu použitý při spuštění. Kód také odkazuje na předběžnou verzi [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) na NuGet.

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

## <a name="use-injected-dependencies"></a>Použít vložené závislosti

ASP.NET Core používá vkládání konstruktoru k zpřístupnění vašich závislostí vaší funkci. Následující příklad ukazuje, jak jsou `IMyService` tyto `HttpClient` závislosti vloženy do funkce aktivované protokolem HTTP.

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

Použití injektáže konstruktoru znamená, že pokud chcete využít výhod vkládání závislostí, neměli byste používat statické funkce.

## <a name="service-lifetimes"></a>Životnost služeb

Azure Functions aplikace poskytují stejné životnosti služeb jako [vkládání závislostí ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): přechodné, vymezené a singleton.

V aplikaci Functions se rozsah životnosti služby shoduje s dobou trvání spuštění funkce. Oborové služby se pro každé spuštění vytvoří jednou. Pozdější požadavky na tuto službu během opakovaného použití existující instance služby. Doba životnosti služby typu Singleton odpovídá době životnosti hostitele a je znovu použita v rámci provádění funkcí v této instanci.

Pro připojení a klienty jsou doporučovány služby životnosti singleton, `SqlConnection` `CloudBlobClient`například nebo `HttpClient` instance.

Zobrazit nebo stáhnout [ukázku různých životností služby](https://aka.ms/functions/di-sample) na GitHubu.

## <a name="logging-services"></a>Protokolovací služby

Pokud potřebujete vlastního zprostředkovatele protokolování, doporučuje se, abyste `ILoggerProvider` instanci zaregistrovali. Application Insights automaticky přidá Azure Functions.

> [!WARNING]
> Nepřidávat `AddApplicationInsightsTelemetry()` do kolekce služeb při registraci služeb, které jsou v konfliktu se službami poskytovanými prostředím.

## <a name="function-app-provided-services"></a>Poskytnuté služby Function App

Hostitel funkce registruje mnoho služeb. V rámci vaší aplikace je možné v aplikaci provést zabezpečení těchto služeb:

|Typ služby|Životnost|Popis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Konfigurace modulu runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Zodpovídá za poskytnutí ID instance hostitele.|

Pokud existují další služby, na kterých chcete převzít závislost, [vytvořte problém a navrhněte je na GitHubu](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Přepsání hostitelských služeb

Přepsání služeb poskytovaných hostitelem není aktuálně podporováno.  Pokud existují služby, které chcete přepsat, [vytvořte problém a navrhněte je na GitHubu](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Další postup

Další informace naleznete v následujících materiálech:

- [Jak monitorovat aplikaci Function App](functions-monitoring.md)
- [Osvědčené postupy pro funkce](functions-best-practices.md)
