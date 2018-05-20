---
title: Generovat protokolu události z aplikace .NET Service Fabric v Azure nebo cluster samostatné
description: Další informace o tom, jak přidat protokolování do vaší aplikace .NET Service Fabric hostovaná v clusteru služby Azure nebo samostatné cluster.
services: service-fabric
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/27/2018
ms.author: adegeo
ms.openlocfilehash: ed9aaf67b4f6749ea6d505a51fbc76e3d1cf0870
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="add-logging-to-your-service-fabric-application"></a>Přidání protokolování do aplikace Service Fabric

Aplikace musí poskytnout dostatek informací k forensically ladění při vzniknou problémy. Protokolování je jedním z nejdůležitějších věcí, které můžete přidat do vaší aplikace Service Fabric. Když dojde k selhání, dobrý protokolování můžete poskytnout způsob, jak prozkoumat selhání. Analýzou vzory protokolu, můžete zjistit způsobů, jak zlepšit výkon nebo návrh aplikace. Tento dokument ukazuje několik různých protokolování možnosti.

## <a name="eventflow"></a>EventFlow

[EventFlow knihovny](https://github.com/Azure/diagnostics-eventflow) suite umožňuje aplikacím definovat jaká data diagnostiky ke shromažďování a kde by měl být výstupem k. Diagnostická data může být cokoli z čítače výkonu pro trasování aplikací. Tak je minimalizován režijní náklady na komunikaci, spustí se v rámci jednoho procesu, jako aplikace. Další informace o EventFlow a Service Fabric najdete v tématu [Azure Service Fabric událostí agregace s EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Používání strukturovaného událostí EventSource

Definující zpráva události podle případ použití umožňuje data balíčku o události v kontextu události. Můžete snadněji Hledat a filtr na základě názvy nebo hodnoty vlastností zadané události. Strukturování díky výstup instrumentace snadněji číst, ale vyžaduje další myšlenku a čas pro definování událost pro každý případ použití. 

Některé události definice mohlo sdílet napříč celou aplikaci. Například metoda spuštění nebo zastavení by být událostí opětovně použít napříč mnoha služeb v rámci aplikace. Může mít specifické pro doménu služby, jako jsou systému pořadí **CreateOrder** událost, která má svůj vlastní jedinečný událostí. Tento přístup může generovat mnoho událostí a potenciálně vyžadují koordinaci identifikátory v rámci projektové týmy. 

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

### <a name="using-eventsource-generically"></a>Pomocí EventSource obecně

Protože definování určité události může být složité, definujte Spousta lidí několik událostí s společnou sadu parametrů, které obvykle výstup své informace jako řetězec. Velká část strukturovaných aspekt dojde ke ztrátě, a je obtížnější k vyhledávání a filtrování výsledků. V tomto přístupu jsou definovány několik události, které obvykle odpovídají úrovně protokolování. Následující fragment kódu definuje ladění a chybové zprávy:

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

Pomocí hybridní strukturovaných a obecná instrumentace můžete použít také. Strukturované instrumentace se používá pro vytváření sestav chyb a metriky. Obecné události lze použít pro podrobné protokolování, který využívá techniky pro řešení potíží.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

ASP.NET Core protokolování ([balíček Microsoft.Extensions.Logging NuGet](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) je rozhraní protokolování, které poskytuje standardní protokolování rozhraní API pro vaši aplikaci. Podpora jiných back-EndY protokolování lze připojit do ASP.NET Core protokolování. Díky tomu můžete zpracovat širokou škálu podporu pro protokolování v aplikaci, aniž by bylo nutné změnit mnohem kód.

1. Přidat **Microsoft.Extensions.Logging** balíček NuGet do projektu je chcete nástrojích. Také přidáte všechny balíčky zprostředkovatele. Další informace najdete v tématu [protokolování v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Přidat **pomocí** direktivy pro **Microsoft.Extensions.Logging** do souboru služby.
3. Definování soukromé proměnné v třídě služby.

   ```csharp
   private ILogger _logger = null;
   ```

4. V konstruktoru třídy vaší služby přidejte tento kód:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Spuštění kódu v vaše metody instrumentace. Tady je několik ukázky:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Použití dalších zprostředkovatelů protokolování

Některé použití poskytovatelů třetích stran přístupu popsané v předchozí části, včetně [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/), a [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Každá z těchto do ASP.NET Core protokolování je možné připojit nebo je můžete použít samostatně. Serilog obsahuje funkce, která vylepšuje všech zpráv odeslaných z protokolovacího nástroje. Tato funkce může být užitečné k vypsání názvu služby, typ a informace o oddílu. Chcete-li použít tuto funkci v infrastruktuře ASP.NET Core, proveďte tyto kroky:

1. Přidat **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**, a **Serilog.Sinks.Observable** balíčků NuGet do projektu. 
2. Vytvoření `LoggerConfiguration` a instance protokolovacího nástroje.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Přidat `Serilog.ILogger` argument konstruktoru služby a předejte jí nově vytvořený protokolovacího nástroje.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. V konstruktoru service vytvoří vlastnost enrichers pro **ServiceTypeName**, **ServiceName**, **PartitionId**, a **identifikátor InstanceId** .

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

5. Instrumentace kód stejné jako kdyby byly pomocí ASP.NET Core bez Serilog.

   >[!NOTE]
   >Doporučujeme vám *nemáte* použít statickou `Log.Logger` s v předchozím příkladu. Service Fabric může být hostitelem více instancí stejného typu služby v rámci jednoho procesu. Pokud používáte statické `Log.Logger`, poslednímu zapisujícímu enrichers vlastnost zobrazí hodnoty pro všechny instance, které jsou spuštěné. Toto je jedním z důvodů, proč proměnnou _logger je proměnná privátního člena třídy služeb. Navíc je nutné `_logger` k dispozici pro společný kód, který může být použit ve službách.

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [aplikace monitorování v nástroji Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Přečtěte si informace o protokolování s [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) a [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).










