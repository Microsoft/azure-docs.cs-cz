---
title: Generování událostí protokolu z aplikace .NET
description: Přečtěte si, jak přidat protokolování do aplikace .NET Service Fabric hostované v clusteru Azure nebo samostatném clusteru.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a36425acf42a469c7f48b2e954bdacfdfcce1b10
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89011476"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Přidání protokolování do aplikace Service Fabric

Vaše aplikace musí poskytnout dostatek informací, aby je Forensically při vzniku problémů. Protokolování je jedním z nejdůležitějších věcí, které můžete přidat do aplikace Service Fabric. Když dojde k selhání, můžete dobrým protokolováním poskytnout způsob, jak prozkoumat selhání. Analýzou vzorů protokolů můžete najít způsoby, jak vylepšit výkon nebo návrh aplikace. Tento dokument ukazuje několik různých možností protokolování.

## <a name="eventflow"></a>Využitím eventflow

Sada [knihoven využitím eventflow](https://github.com/Azure/diagnostics-eventflow) umožňuje aplikacím definovat diagnostická data, která se mají shromažďovat, a kde by se měla nacházet. Diagnostická data mohou být cokoli z čítačů výkonu až po trasování aplikací. Spouští se v rámci stejného procesu jako aplikace, takže dojde k minimalizaci komunikační režie. Další informace o využitím eventflow a Service Fabric najdete v tématu [agregace událostí Azure Service Fabric pomocí využitím eventflow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Použití strukturovaných událostí EventSource

Definování událostí zprávy pomocí případu použití umožňuje zabalit data o události v kontextu události. Můžete snadněji Hledat a filtrovat podle názvů nebo hodnot zadaných vlastností události. Strukturování výstupu instrumentace usnadňuje čtení, ale vyžaduje přesnější a časové definování události pro každý případ použití. 

Některé definice událostí lze sdílet v celé aplikaci. Například metoda spustit nebo zastavit událost by se v rámci aplikace znovu použila v celé řadě služeb. Služba specifická pro doménu, jako je například systém objednávek, může mít událost **CreateOrder** , která má svou vlastní jedinečnou událost. Tento přístup může generovat mnoho událostí a potenciálně vyžadovat koordinaci identifikátorů napříč projektovými týmy. 

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

### <a name="using-eventsource-generically"></a>Obecné použití EventSource

Vzhledem k tomu, že definování konkrétních událostí může být obtížné, mnoho lidí definuje několik událostí se společnou sadou parametrů, které obvykle mají výstup své informace jako řetězec. Většina strukturovaného aspektu se ztratí a je obtížnější Hledat a filtrovat výsledky. V tomto přístupu jsou definované jenom některé události, které obvykle odpovídají úrovním protokolování. Následující fragment kódu definuje ladění a chybovou zprávu:

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

Použití hybridní struktury strukturovaných a obecných instrumentací také může fungovat dobře. Strukturovaná instrumentace se používá pro hlášení chyb a metrik. Obecné události lze použít pro podrobné protokolování, které technici spotřebují při řešení potíží.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

Protokolování ASP.NET Core ([balíček NuGet Microsoft. Extensions. Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) je protokolovací rozhraní, které pro vaši aplikaci poskytuje standardní protokolovací rozhraní API. Podpora pro jiné back-endy protokolování se dá připojit k ASP.NET Core protokolování. Získáte tak širokou škálu podpory pro protokolování ve vaší aplikaci, aniž byste museli měnit mnohem více kódu.

1. Přidejte balíček NuGet **Microsoft. Extensions. Logging** do projektu, který chcete instrumentovat. Přidejte také všechny balíčky poskytovatele. Další informace najdete v tématu věnovaném [přihlášení ASP.NET Core](/aspnet/core/fundamentals/logging).
2. Přidejte direktivu **using** pro **Microsoft. Extensions. Logging** do souboru služby.
3. Definujte soukromou proměnnou v rámci vaší třídy služby.

   ```csharp
   private ILogger _logger = null;
   ```

4. V konstruktoru třídy služby přidejte tento kód:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Začněte instrumentovat svůj kód v metodách. Tady je několik ukázek:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Používání jiných zprostředkovatelů protokolování

Někteří poskytovatelé třetích stran používají přístup popsaný v předchozí části, včetně [Serilog](https://serilog.net/), [nLOG](https://nlog-project.org/)a [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Každé z nich můžete připojit k ASP.NET Core protokolování, nebo je můžete použít samostatně. Serilog má funkci, která rozšiřuje všechny zprávy odesílané z protokolovacího nástroje. Tato funkce může být užitečná pro výstup názvu služby, typu a informací o oddílu. Pokud chcete tuto funkci použít v infrastruktuře ASP.NET Core, proveďte tyto kroky:

1. Přidejte rozhraní **Serilog**, **Serilog. Extensions. Logging**, **Serilog. umyvadla. přepisy** a **Serilog. sinks. pozorovatelované** balíčky NuGet do projektu. 
2. Vytvořte `LoggerConfiguration` instanci protokolovacího nástroje a.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Přidejte `Serilog.ILogger` argument do konstruktoru služby a předejte nově vytvořený protokolovací nástroj.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. V konstruktoru služby vytvoří rozšíření vlastností pro **ServiceType**, **ServiceName**, **PartitionID** a **InstanceId**.

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

5. Instrumentujte kód stejně, jako kdybyste používali ASP.NET Core bez Serilog.

   >[!NOTE]
   >Doporučujeme, abyste v předchozím příkladu *nepoužívali* static `Log.Logger` . Service Fabric může hostovat více instancí stejného typu služby v rámci jednoho procesu. Použijete-li statickou `Log.Logger` , bude poslední zapisovač rozšíření vlastností zobrazovat hodnoty pro všechny instance, které jsou spuštěny. Toto je jeden z důvodů, proč je proměnná _logger privátní členskou proměnnou třídy služby. Také je nutné zpřístupnit `_logger` běžný kód, který může být použit napříč službami.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [monitorování aplikací v Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Přečtěte si o protokolování pomocí [využitím eventflow](service-fabric-diagnostics-event-aggregation-eventflow.md) a [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).
