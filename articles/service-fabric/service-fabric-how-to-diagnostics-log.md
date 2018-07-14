---
title: Generování protokolu událostí z aplikace .NET pro Service Fabric v Azure nebo samostatného clusteru
description: Další informace o tom, jak přidat přihlášení do aplikace .NET pro Service Fabric hostované v Azure cluster nebo samostatného clusteru.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/27/2018
ms.author: ryanwi
ms.openlocfilehash: 42a6430162f3bafd3ec3ce2a3c523f6f5755914a
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001374"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Přidání protokolování do aplikace Service Fabric

Aplikace musí poskytnout dostatek informací k forensically ladění při vzniknou problémy. Protokolování je jedním z nejdůležitějších věcí, které přidáte do aplikace Service Fabric. Pokud dojde k selhání, dobré protokolování můžete poskytnout způsob, jak zjistit selhání. Díky analýze protokolu vzory, může se stát způsoby, jak vylepšit výkon nebo návrhu vaší aplikace. Tento dokument ukazuje několik různých protokolování.

## <a name="eventflow"></a>Využitím EventFlow

[Využitím EventFlow knihovny](https://github.com/Azure/diagnostics-eventflow) suite umožňuje aplikacím k definování jaká diagnostická data ke shromažďování a kde by měl obsahovat výstup do. Diagnostická data může být cokoli z čítačů výkonu do trasování aplikací. Poběží ve stejném procesu jako aplikace, takže minimalizaci režie komunikace. Další informace o využitím EventFlow a Service Fabric najdete v tématu [Azure Service Fabric události agregace s využitím EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Použití strukturovaného událostí EventSource

Data balíčku o události v rámci události vám umožní definující zpráva, že události podle případ použití. Můžete snadněji Hledat a filtr podle jména nebo hodnot vlastností zadané události. Strukturování vytvoří výstup instrumentace lépe čitelný, ale vyžaduje další myšlenek a čas definovat událost pro každý případ použití. 

Některé události definice mohlo sdílet v celé aplikaci. Například metoda spuštění nebo zastavení událostí by použít opakovaně napříč řadou služeb v rámci aplikace. Službu specifického pro doménu, třeba při nasazení pořadí systému, pravděpodobně **CreateOrder** událost, která má svůj vlastní jedinečný událost. Tento přístup může generovat mnoho událostí a potenciálně vyžadují koordinaci identifikátorů mezi projektovými týmy. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>Obecně pomocí EventSource

Vzhledem k tomu, že definujete konkrétní události může být obtížné, mnoho lidí definovat několik událostí s společnou sadu parametrů, které obecně výstup své informace jako řetězec. Velká část strukturovaných aspekt dojde ke ztrátě a je obtížnější k vyhledávání a filtrování výsledků. V takovém případě jsou definovány několik událostí, které obvykle odpovídají úrovní protokolování. Následující fragment kódu definuje ladění a chybové zprávy:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Použití hybridních strukturovaných a obecná instrumentace můžete použít také. Strukturované instrumentace se používá pro hlášení chyb a metriky. Obecné události je možné pro podrobné protokolování, který využívá techniky pro řešení potíží.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

ASP.NET Core protokolování ([balíček Microsoft.Extensions.Logging NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) je protokolovacího rozhraní, která poskytuje standardní protokolování rozhraní API pro vaši aplikaci. Podpora jiných back-EndY protokolování může být připojeno do ASP.NET Core protokolování. To poskytuje širokou podporu pro protokolování ve vaší aplikaci se zpracovává, aniž byste museli změnit, jaká část kódu.

1. Přidat **Microsoft.Extensions.Logging** balíčků NuGet do projektu je chcete instrumentovat. Přidejte také všechny balíčky, které zprostředkovatele. Další informace najdete v tématu [protokolování v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Přidat **pomocí** směrnice pro **Microsoft.Extensions.Logging** do souboru služby.
3. Definujte soukromé proměnné v rámci třídy vaší služby.

   ```csharp
   private ILogger _logger = null;
   ```

4. V konstruktoru třídy služby přidejte tento kód:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Spusťte kód do metody instrumentace. Tady je několik ukázek:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Pomocí dalších poskytovatelů protokolování

Použití externích poskytovatelů přístup je popsáno v předchozí části, včetně některých [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/), a [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Můžete připojit každou z nich do ASP.NET Core protokolování nebo je můžete využít samostatně. Serilog obsahuje funkce, která vylepšuje všechny zprávy odeslané z protokolovací nástroj. Tato funkce může být užitečné pro výstup, název služby, typ a informace o oddílu. Pokud chcete tuto funkci použít v infrastruktuře ASP.NET Core, proveďte tyto kroky:

1. Přidat **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**, a **Serilog.Sinks.Observable** balíčky NuGet do projektu. 
2. Vytvoření `LoggerConfiguration` a instanci protokolovače.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Přidat `Serilog.ILogger` argument pro konstruktor služby a předejte nově vytvořený protokolovacího nástroje.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. V konstruktoru service vytvoří vlastnost enrichers pro **ServiceTypeName**, **ServiceName**, **PartitionId**, a **InstanceId** .

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Instrumentujte kód stejné jako by byly pomocí ASP.NET Core bez Serilog.

   >[!NOTE]
   >Doporučujeme vám *není* použít statické `Log.Logger` z předchozího příkladu. Service Fabric může být hostitelem více instancí stejného typu služby v rámci jednoho procesu. Pokud používáte statické `Log.Logger`, poslednímu zapisujícímu enrichers vlastnost zobrazí hodnoty v rámci všech instancí, které jsou spuštěny. Toto je jeden důvod, proč _logger proměnnou soukromého členské proměnné třídy service. Kromě toho je třeba `_logger` k dispozici pro společný kód, který může být použit napříč službami.

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [aplikace monitorování v Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Přečtěte si informace o protokolování pomocí [využitím EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) a [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).










