---
title: Sledování vlastních operací pomocí Azure Application Insights .NET SDK | Dokumentace Microsoftu
description: Sledování vlastních operací pomocí .NET SDK služby Azure Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/30/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 8295fb58bdf92ca8688f5f7b6270dc1b48632a73
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057071"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Sledování vlastních operací pomocí Application Insights .NET SDK

Sady SDK služby Azure Application Insights automaticky sledovat příchozích požadavků HTTP a volání závislé služby, jako jsou požadavky HTTP a dotazy SQL. Sledování a korelace mezi požadavky a závislosti získáte přehled o rychlosti odezvy celou aplikaci a spolehlivost napříč všechny mikroslužby, které kombinují této aplikace. 

Existuje třída vzorů aplikace, které nelze podporovat obecně. Správné monitorování tyto vzory vyžaduje ruční kód instrumentace. Tento článek se týká několik vzorových postupů, které můžou vyžadovat ruční instrumentace, jako jsou vlastní fronty, zpracování a spouštění dlouhotrvajících úloh na pozadí.

Tento dokument obsahuje pokyny o tom, jak sledování vlastních operací pomocí Application Insights SDK. Tato dokumentace je relevantní pro:

- Application Insights pro .NET (označované také jako základní sady SDK) verze 2.4 +.
- Application Insights pro webové aplikace (spouštějící ASP.NET) verze 2.4 +.
- Application Insights pro ASP.NET Core verze 2.1 +.

## <a name="overview"></a>Přehled
Operace je logický část práce spuštění aplikace. Má název, čas spuštění, dobu trvání, výsledku a kontextu spuštění, jako je uživatelské jméno, vlastnosti a výsledek. Pokud byla zahájena operace A operace B, než operace B je nastaven jako nadřazený pro A. Operace může obsahovat pouze jednu nadřazenou položku, ale může mít mnoho podřízených operací. Další informace o operacích a korelace telemetrie najdete v tématu [korelace telemetrie Azure Application Insights](application-insights-correlation.md).

V Application Insights .NET SDK, je popsán operaci abstraktní třída [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) a jejích potomků [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) a [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Příchozí provoz, sledování 
Application Insights web SDK automaticky shromažďuje požadavků HTTP pro aplikace ASP.NET, které běží v kanálu služby IIS a všech aplikací ASP.NET Core. Existují – podporované komunitou řešení pro jiné platformy a rozhraní. Nicméně pokud aplikace není podporován žádným z řešení standardní nebo – podporované komunitou, vám umožňuje instrumentovat ji ručně.

Další příklad, který vyžaduje vlastní sledování se pracovní proces, který přijímá položky z fronty. Pro některé fronty volání pro přidání zprávy do této fronty jsou sledovány jako závislost. Základní operace, která popisuje zpracování zpráv nejsou ale shromažďovány automaticky.

Podívejme se, jak může sledovat tyto operace.

Na vysoké úrovni je úloha vytvoření `RequestTelemetry` a známé vlastnosti. Po dokončení operace sledování telemetrická data. Následující příklad ukazuje tuto úlohu.

### <a name="http-request-in-owin-self-hosted-app"></a>Požadavek protokolu HTTP v místním prostředí aplikace Owin
V tomto příkladu se šíří kontextu trasování podle [protokolu HTTP pro korelaci](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Měli byste očekávat přijímat hlavičky, které jsou popsány zde.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Protokol HTTP pro korelaci také deklaruje `Correlation-Context` záhlaví. Ale to je tady jsme ji vynechali pro zjednodušení.

## <a name="queue-instrumentation"></a>Instrumentace fronty
Zatímco je [protokolu HTTP pro korelaci](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) předat podrobnosti korelace požadavku HTTP, má každý protokol fronty k definování, jak jsou stejné informace předají zprávy fronty. Některé protokoly fronty (jako je například AMQP) umožňuje předání dalších metadat a jiné (tyto fronty Azure Storage) vyžaduje kontext, který má být zakódován do datové části zprávy.

### <a name="service-bus-queue"></a>Fronta Service Bus
Application Insights sleduje volání zasílání zpráv Service Bus s novými [klienta služby Service Bus Microsoft Azure pro .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) verze 3.0.0 nebo novější.
Pokud používáte [vzor obslužné rutiny zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) ke zpracování zpráv, skončíte: všechna volání služby Service Bus provádí vaše služba se automaticky sledovat a korelují s ostatními položkami telemetrická data. Odkazovat [trasování pomocí nástroje Microsoft Application Insights klienta služby Service Bus](../service-bus-messaging/service-bus-end-to-end-tracing.md) ručně během zpracování zprávy.

Pokud používáte [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) balíčku, si můžete přečíst další - následující příklady ukazují, jak sledovat (a korelovat) volání služby Service Bus a fronty služby Service Bus používá protokol AMQP nebude Application Insights automaticky sledujte operace fronty.
Korelace identifikátory jsou předány ve vlastnostech zprávy.

#### <a name="enqueue"></a>Zařazení do fronty

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Proces
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Fronty Azure Storage
Následující příklad ukazuje, jak sledovat [fronty Azure Storage](../storage/queues/storage-dotnet-how-to-use-queues.md) operace a korelovat telemetrie mezi výrobce, příjemce a Azure Storage. 

Fronta úložiště má rozhraní API protokolu HTTP. Všechna volání do fronty jsou sledovány pomocí Application Insights závislost Collector pro požadavky HTTP.
Ujistěte se, že máte `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` v `applicationInsights.config`. Pokud ho nemáte, přidejte ho programově, jak je popsáno v [filtrování a předběžné zpracování v Azure Application Insights SDK](app-insights-api-filtering-sampling.md).

Pokud je ruční konfigurace Application Insights, ujistěte se, že vytváření a inicializace `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` podobně jako:
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Můžete také chtít korelace ID operace Application Insights s ID úložiště požadavku. Informace o tom, jak nastavit a získat požadavek klienta úložiště a ID požadavku serveru najdete v tématu [monitorování, Diagnostika a řešení problémů s Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Zařazení do fronty
Protože front služby Storage podporuje rozhraní API HTTP, všechny operace s frontou automaticky sleduje provedené Application Insights. V mnoha případech tato instrumentace by vám měly dostatečně. Ale ke korelaci trasování na stranu zákazníka pomocí trasování výrobce, musíte předat některé korelace kontextové podobně jak můžeme udělat v protokolu HTTP pro korelaci. 

Tento příklad ukazuje, jak sledovat `Enqueue` operace. Můžete:

 - **Proveďte korelaci opakovaných pokusů (pokud existuje)**: všichni mají jeden společný nadřazený, který má `Enqueue` operace. V opačném případě budete sledovat jako podřízené objekty příchozího požadavku. Pokud existují logický vícenásobných do fronty, může být obtížné najít, která volá výsledkem opakovaných pokusů.
 - **Korelovat protokoly úložiště (pokud a v případě potřeby)**: máte korelují s telemetrii Application Insights.

`Enqueue` Operace je podřízený, nadřazené operace (například s příchozím požadavkem HTTP). Volání závislostí protokolu HTTP je podřízeným `Enqueue` operace a podřízený příchozího požadavku:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Snížit objem telemetrických dat aplikace sestavy nebo pokud nechcete sledovat `Enqueue` operace z jiných důvodů, použijte `Activity` přímo rozhraní API:

- Vytvořit (a začít) novou `Activity` místo spouští se operace Application Insights. Provedete *není* potřeba přiřadit všechny vlastnosti v něm s výjimkou název operace.
- Serializace `yourActivity.Id` do datové části zprávy místo `operation.Telemetry.Id`. Můžete také použít `Activity.Current.Id`.


#### <a name="dequeue"></a>Odstranění z fronty
Podobně jako `Enqueue`, vlastní požadavek HTTP do fronty úložiště je automaticky sleduje provedené Application Insights. Ale `Enqueue` operace pravděpodobně dochází v rámci nadřazené, jako je například příchozí kontext požadavku. Sady SDK služby Application Insights automaticky sladit takovou operaci (a její části HTTP) s nadřazené žádosti a další telemetrie hlášená ve stejném oboru.

`Dequeue` Operace je velmi obtížné. Application Insights SDK automaticky sleduje požadavky HTTP. Ale neví kontextu korelace, dokud je analyzovat zprávu. Není možné má být korelována žádost HTTP, zobrazí se zpráva s využitím rest telemetrická data.

V mnoha případech může být užitečné pro požadavek HTTP do fronty je možné korelovat s i další trasování. Následující příklad ukazuje, jak to udělat:

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Proces

V následujícím příkladu příchozí zprávy se sleduje v podobě podobně pro příchozí požadavek protokolu HTTP:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Podobně můžete instrumentována jiné operace fronty. Operace peek by měl instrumentována podobným způsobem jako operace odebrání z fronty. Operace správy front instrumentace není nutné. Application Insights sleduje operace, jako jsou HTTP a ve většině případů to stačí.

Při instrumentaci odstranění zprávy, ujistěte se, že nastavíte operaci identifikátory (korelace). Alternativně můžete použít `Activity` rozhraní API. Potom není potřeba nastavit identifikátory operace u položek telemetrie, protože Application Insights SDK to udělá za vás:

- Vytvořte nový `Activity` po máte položky z fronty.
- Použití `Activity.SetParentId(message.ParentId)` korelovat protokoly příjemce a výrobce.
- Spustit `Activity`.
- Sledovat odstranění z fronty, zpracování a operace odstranění s využitím `Start/StopOperation` pomocné rutiny. Proveďte i ze stejné tok řízení asynchronní (kontextu spuštění). Tímto způsobem že jste korelační správně.
- Zastavit `Activity`.
- Použití `Start/StopOperation`, nebo se telefonicky `Track` telemetrická data ručně.

### <a name="batch-processing"></a>Dávkové zpracování
V případě některých front může odstranění z fronty více zpráv s jeden požadavek. Zpracování takové zprávy je pravděpodobně nezávislé a patří do různých logických operací. V takovém případě není možné korelovat `Dequeue` operace zpracování konkrétní zprávu.

Každou zprávu by měl být zpracovány v svůj vlastní tok řízení asynchronní. Další informace najdete v tématu [odchozí závislosti sledování](#outgoing-dependencies-tracking) oddílu.

## <a name="long-running-background-tasks"></a>Dlouho běžící úlohy na pozadí
Některé aplikace spusťte dlouhotrvající operace, které mohou být způsobeny požadavky uživatelů. Z pohledu trasování a instrumentace se neliší od požadavku nebo závislost instrumentace: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

V tomto příkladu `telemetryClient.StartOperation` vytvoří `RequestTelemetry` a vyplní kontextu korelace. Řekněme, že máte nadřazené operace, který byl vytvořen příchozích požadavků, které naplánované operace. Tak dlouho, dokud `BackgroundTask` starty v této asynchronní řízení toku jako příchozího požadavku, jsou korelována se nadřazené operace. `BackgroundTask` a všechny vnořené telemetrie položky se automaticky korelují s požadavkem, který způsobil, i po ukončení požadavku.

Spuštění úkolu z vlákna na pozadí, který nemá žádné operace (`Activity`) přidružen, `BackgroundTask` nemá žádné nadřazené. Však to mohou vnořené operace. Všechny položky telemetrie hlášená z úlohy jsou korelována `RequestTelemetry` vytvořené v `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Odchozí závislosti sledování
Můžete sledovat vlastní typ závislosti nebo operace, která není podporována službou Application Insights.

`Enqueue` Metoda fronty služby Service Bus nebo fronty úložiště může sloužit jako příklady takových vlastní sledování.

Obecný postup pro vlastní sledování závislostí je:

- Volání `TelemetryClient.StartOperation` – metoda (rozšíření), který vyplní `DependencyTelemetry` vlastnosti, které jsou potřeba ke korelaci a některé vlastnosti (čas zahájení razítko, dobu trvání).
- Nastavit další vlastní vlastnosti `DependencyTelemetry`, jako jsou název a další kontext, budete potřebovat.
- Ujistěte se, volání a počkejte na její závislosti.
- Zastavení operace s `StopOperation` po jeho dokončení.
- Zpracování výjimek.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Rušení operace způsobí, že na zastavit, operace, můžete to provést, bez volání `StopOperation`.

*Upozornění*: v některých případech může unhanded výjimka [zabránit](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` říkalo tak operace nemusí být sledovány.

### <a name="parallel-operations-processing-and-tracking"></a>Zpracování paralelních operací a sledování

`StopOperation` pouze zastaví prováděnou operaci, která byla spuštěna. Pokud aktuální operaci správy spuštěného neodpovídá tomu chcete zastavit, `StopOperation` nemá žádný účinek. Tato situace může nastat, pokud spustit více operací paralelně ve stejném kontextu spuštění:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Ujistěte se, že jste vždy volat `StartOperation` a zpracování operace ve stejné **asynchronní** metoda izolace operací spuštěných současně. Pokud operace je synchronní (nebo není asynchronní), proces zabalení a sledovat s `Task.Run`:

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="next-steps"></a>Další postup

- Seznamte se se základy [korelace telemetrie](application-insights-correlation.md) ve službě Application Insights.
- Zobrazit [datový model](application-insights-data-model.md) pro typy a datový model Application Insights.
- Vlastní sestavy [události a metriky](app-insights-api-custom-events-metrics.md) do Application Insights.
- Podívejte se na standard [konfigurace](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) kontextu vlastnosti kolekce.
- Zkontrolujte [System.Diagnostics.Activity uživatelská příručka](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) zobrazíte, jak jsme korelovat telemetrie.
