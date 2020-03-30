---
title: Generovat události protokolu z aplikace .NET
description: Přečtěte si, jak přidat protokolování do aplikace .NET Service Fabric hostované v clusteru Azure nebo v samostatném clusteru.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: 8c4721584e74bd7f7111c516f2d16bd190392bb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614362"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Přidání protokolování do aplikace Service Fabric

Aplikace musí poskytnout dostatek informací k forenzní ladění, když vzniknou problémy. Protokolování je jednou z nejdůležitějších věcí, které můžete přidat do aplikace Service Fabric. Dojde-li k selhání, dobré protokolování může poskytnout způsob, jak prozkoumat selhání. Analýzou vzory protokolu, můžete najít způsoby, jak zlepšit výkon nebo návrh aplikace. Tento dokument ukazuje několik různých možností protokolování.

## <a name="eventflow"></a>EventFlow

Sada [knihovny EventFlow](https://github.com/Azure/diagnostics-eventflow) umožňuje aplikacím definovat, jaká diagnostická data mají být shromažďována a kam by měla být výstupována. Diagnostická data mohou být cokoli od čítačů výkonu až po trasování aplikací. Běží ve stejném procesu jako aplikace, takže režie komunikace je minimalizována. Další informace o EventFlow a Service Fabric, najdete [v tématu Azure Service Fabric event agregace s EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Použití strukturovaných událostí EventSource

Definování událostí zprávy podle případu použití umožňuje zabalit data o události v kontextu události. Můžete snadněji vyhledávat a filtrovat na základě názvů nebo hodnot zadaných vlastností události. Strukturování výstupu instrumentace usnadňuje čtení, ale vyžaduje více myšlení a čas k definování události pro každý případ použití. 

Některé definice událostí lze sdílet v celé aplikaci. Například metoda start nebo stop událost by být znovu použity v mnoha službách v rámci aplikace. Služba specifická pro doménu, jako je systém objednávek, může mít událost **CreateOrder,** která má vlastní jedinečnou událost. Tento přístup může generovat mnoho událostí a potenciálně vyžadovat koordinaci identifikátorů mezi projektovými týmy. 

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

### <a name="using-eventsource-generically"></a>Použití zdroje událostí obecně

Vzhledem k tomu, že definování konkrétních událostí může být obtížné, mnoho lidí definuje několik událostí se společnou sadou parametrů, které obecně vyvozují své informace jako řetězec. Velká část strukturovaného aspektu je ztracena a je obtížnější vyhledávat a filtrovat výsledky. V tomto přístupu je definováno několik událostí, které obvykle odpovídají úrovním protokolování. Následující výstřižek definuje ladicí a chybovou zprávu:

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

Použití hybridstrukturované a obecné instrumentace také může fungovat dobře. Strukturované instrumentace se používá pro hlášení chyb a metriky. Obecné události lze použít pro podrobné protokolování, které je spotřebováno inženýry pro řešení potíží.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Protokolování

Protokolování ASP.NET Core ([Microsoft.Extensions.Logging NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) je architektura protokolování, která poskytuje standardní rozhraní API pro protokolování pro vaši aplikaci. Podporu pro další protokolování back-endů lze zapojit do ASP.NET protokolování Core. To vám dává širokou škálu podpory pro protokolování v aplikaci je zpracována, aniž by bylo třeba měnit mnoho kódu.

1. Přidejte balíček **Microsoft.Extensions.Logging** NuGet do projektu, který chcete instrumentovat. Také přidejte všechny balíčky zprostředkovatele. Další informace naleznete [v tématu Protokolování ASP.NET jádra](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Přidejte **do** souboru **služby direktivu using pro Microsoft.Extensions.Logging.**
3. Definujte soukromou proměnnou v rámci třídy služeb.

   ```csharp
   private ILogger _logger = null;
   ```

4. V konstruktoru třídy služby přidejte tento kód:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Začněte instrumentovat kód ve svých metodách. Zde je několik vzorků:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Použití jiných poskytovatelů protokolování

Někteří poskytovatelé třetích stran používají přístup popsaný v předchozí části, včetně [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/)a [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Můžete připojit každý z nich do ASP.NET core protokolování, nebo je můžete použít samostatně. Serilog má funkci, která obohacuje všechny zprávy odeslané z úhozu. Tato funkce může být užitečná pro výstup názvu služby, typu a informací o oddílu. Chcete-li tuto funkci použít v ASP.NET základní infrastruktury, postupujte takto:

1. Přidejte balíčky **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**a **Serilog.Sinks.Observable** NuGet do projektu. 
2. Vytvořte `LoggerConfiguration` a a logger instance.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Přidejte `Serilog.ILogger` argument do konstruktoru služby a předat nově vytvořené protokolovací protokol.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. V konstruktoru služby vytvoří obohacovače vlastností pro **ServiceTypeName**, **ServiceName**, **PartitionId**a **InstanceId**.

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

5. Instrumentovat kód stejně, jako kdybyste používali ASP.NET Core bez Serilogu.

   >[!NOTE]
   >Doporučujeme *nepoužívat* statické `Log.Logger` s předchozím příkladem. Service Fabric může hostit více instancí stejného typu služby v rámci jednoho procesu. Pokud použijete `Log.Logger`statické , poslední zapisovač vlastnosti enrichers zobrazí hodnoty pro všechny instance, které jsou spuštěny. To je jeden z důvodů, proč _logger proměnná je proměnná soukromého člena třídy služby. Také je nutné `_logger` zpřístupnit společný kód, který může být použit napříč službami.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [monitorování aplikací v Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Přečtěte si o protokolování pomocí [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) a [Diagnostiky Windows Azure](service-fabric-diagnostics-event-aggregation-wad.md).










