---
title: Pomocí vkládání závislostí ve službě .NET Azure Functions
description: Další informace o použití injektáž závislostí pro registraci a používání služeb ve funkcích rozhraní .NET
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, functions, architektury bez serveru
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 1ef688dff65dc11f875f76e2f9127bf89af2f2b9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074587"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Pomocí vkládání závislostí ve službě .NET Azure Functions

Služba Azure Functions podporuje závislost vkládání (DI) software vzor návrhu, což je technika, pro dosažení [řízení IOC (Inversion)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislosti.

Služba Azure Functions je postavená funkce vkládání závislostí ASP.NET Core.  Měli byste pochopit služby, životnost a vzory návrhu [injektáž závislostí ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) před jejich použitím ve funkcích.

## <a name="installing-dependency-injection-packages"></a>Instalace balíčků injektáž závislostí

Pokud chcete používat funkce vkládání závislostí, je potřeba zahrnout balíčku NuGet, který zveřejňuje těchto rozhraní API.

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>Registrace služeb

K registraci služeb, můžete vytvořit metodu konfigurace a přidání součástí do `IFunctionsHostBuilder` instance.  Vytvoří hostitele Azure Functions `IFunctionsHostBuilder` a předá ho přímo do nakonfigurovaného metody.

Pro registraci vaší konfigurace metody, je nutné přidat atribut sestavení, který určuje typ pro vaše konfigurace pomocí metody `FunctionsStartup` atribut.

```csharp
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

## <a name="service-lifetimes"></a>Životnost služby

Aplikace Azure Function App, zadejte stejný životní cyklus služby jako [injektáž závislostí ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)přechodné, s vymezeným oborem a typu singleton.

V aplikaci function app odpovídá doba platnosti vymezené služby životního cyklu spouštění funkce. Vymezené služby se vytvoří jednou za spuštění.  Vyšší požadavky na tuto službu během provádění znovu použít tuto instanci.  Doba platnosti služby typu singleton odpovídá životního cyklu hostitelů a je opětovně použít napříč provádění funkcí v této instanci.

Deklarace služeb typu singleton životnost se doporučují pro připojení a klientů, třeba `SqlConnection`, `CloudBlobClient`, nebo `HttpClient`.

Zobrazení nebo stažení [vzorek jinou službu životnosti](http://aka.ms/functions/di-sample).

## <a name="logging-services"></a>Protokolování služby

Pokud budete potřebovat poskytovatele protokolování, je doporučený postup registrace `ILoggerProvider`.  Pro službu Application Insights funkce Application Insights automaticky přidá za vás.  

> [!WARNING]
> Nepřidávejte `AddApplicationInsightsTelemetry()` službám kolekce jako ho k registraci služeb, které budou v konfliktu s co poskytuje prostředí. 
 
## <a name="function-app-provided-services"></a>Aplikace, kterou poskytují služby – funkce

Funkce hostitele se registrovat mnoho služeb.  Níže jsou služby, které jsou bezpečné závislí.  K registraci nebo závisí na nejsou podporovány další hostitele služby.  Pokud existují další služby, kterou chcete zavést závislost na, prosím [vytvoření problému a diskuze na Githubu](https://github.com/azure/azure-functions-host).

|Typ služby|Životnost|Popis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Konfigurace modulu runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Odpovídají za poskytování ID instance hostitele|

### <a name="overriding-host-services"></a>Přepsání hostitele služby

Přepsání služby poskytované hostitelem se aktuálně nepodporuje.  Pokud nejsou služby, které chcete přepsání, [vytvoření problému a diskuze na Githubu](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

* [Jak monitorovat aplikace function app](functions-monitoring.md)
* [Osvědčené postupy pro službu functions](functions-best-practices.md)