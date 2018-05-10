---
title: Sledování vlastní operace pomocí .NET SDK služby Azure Application Insights | Microsoft Docs
description: Sledování vlastní operace pomocí .NET SDK služby Azure Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/30/2017
ms.author: sergkanz;mbullwin
ms.openlocfilehash: e66d5e8674d6564d3910daddb1119c22a8b8bd9f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Sledování vlastní operace s Application Insights .NET SDK

Azure sadách Application Insights SDK automaticky sledovat příchozí požadavky HTTP a volání závislých služeb, jako je například požadavků HTTP a dotazy SQL. Sledování a korelace mezi požadavky a závislosti a získáte přehled o rychlost reakce a spolehlivost celou aplikaci přes všechny mikroslužeb, které spojují této aplikace. 

Existuje třída schémat aplikace, které nepodporují se obecně. Správné sledování tyto vzory vyžaduje ruční kód instrumentace. Tento článek se zabývá několik vzorů, které můžou vyžadovat ruční instrumentace, jako je například vlastní fronty zpracování a spuštění úlohy dlouho běžící na pozadí.

Tento dokument obsahuje pokyny o tom, jak sledovat vlastní operace s Application Insights SDK. Tato dokumentace je důležité pro:

- Application Insights pro rozhraní .NET (také označované jako základní sady SDK) verze 2.4 +.
- Application Insights pro webové aplikace (používající technologii ASP.NET) verze 2.4 +.
- Application Insights pro ASP.NET Core verze 2.1 +.

## <a name="overview"></a>Přehled
Operace je logické část práce spustit aplikace. Má název, čas spuštění, doba trvání, výsledek a kontext spuštění jako uživatelské jméno, vlastnosti a výsledek. Pokud byla zahájena operace A operace B, pak operaci B je nastaven jako nadřazený pro A. Operace může mít jen jednu nadřazenou položku, ale může mít mnoho podřízené operací. Další informace o operacích a telemetrie korelace najdete v tématu [Azure Application Insights telemetrie korelace](application-insights-correlation.md).

V Application Insights .NET SDK, je popsán operaci abstraktní třída [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) a jeho následníky [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) a [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Příchozí operace sledování 
Webové služby Application Insights SDK automaticky shromažďuje požadavky protokolu HTTP pro aplikace ASP.NET, které běží v kanálu služby IIS a všechny aplikace ASP.NET Core. Nejsou podporované komunity řešení pro jiné platformy a rozhraní. Ale pokud aplikace nepodporují žádné řešení standardní nebo podporována komunity, můžete instrumentovat ji ručně.

Další příklad, který vyžaduje vlastní sledování je pracovní proces, který přijme položky z fronty. Pro některé fronty volání přidání zprávy do této fronty sledován jako závislost. Základní operace, která popisuje zpracování zprávy se však nejsou shromažďovány automaticky.

Podívejme se, jak se tyto operace může sledovat.

Na vysoké úrovni, úloha je vytvoření `RequestTelemetry` a nastavte známých vlastností. Po dokončení operace je sledovat telemetrii. Následující příklad ukazuje tuto úlohu.

### <a name="http-request-in-owin-self-hosted-app"></a>Požadavek HTTP ve vlastním hostováním aplikace Owin
V tomto příkladu je rozšíří kontextu trasování podle požadavků [protokolu HTTP pro korelačního](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Jste měli očekávat hlavičky, které jsou popsány existuje.

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

Protokol HTTP pro korelačního také deklaruje `Correlation-Context` záhlaví. Nicméně je vynechaný sem pro jednoduchost.

## <a name="queue-instrumentation"></a>Fronty instrumentace
Zatímco je [protokolu HTTP pro korelačního](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) předat podrobnosti korelace požadavku HTTP, musí každý protokol fronty definovat, jak jsou stejné podrobnosti předají zprávy ve frontě. Některé protokoly fronty (jako je například AMQP) povolení předávání dalších metadat a jiná (takové fronty Azure Storage) vyžadují kontext, který má být zakódován do datové části zprávy.

### <a name="service-bus-queue"></a>Fronta Service Bus
Application Insights sleduje zasílání zpráv Service Bus volání s novým [sběrnice klienta Microsoft Azure pro .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) verze 3.0.0 a vyšší.
Pokud používáte [vzorce obslužné rutiny zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) ke zpracování zpráv, dokončení: všechna volání služby Service Bus provádí služby se automaticky sledovat a korelační s další položky telemetrie. Odkazovat [trasování pomocí služby Microsoft Application Insights Service Bus klienta](../service-bus-messaging/service-bus-end-to-end-tracing.md) Pokud ruční zpracování zprávy.

Pokud používáte [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) balíčku, přečtěte si další - následující příklady ukazují, jak sledovat (a korelovat) volá na sběrnici služby jako fronty Service Bus používá protokol AMQP a nemá Application Insights automaticky sledovat operace fronty.
Identifikátory korelace jsou předané vlastnosti zprávy.

#### <a name="enqueue"></a>Zařazování

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
Následující příklad ukazuje, jak sledovat [fronty Azure Storage](../storage/queues/storage-dotnet-how-to-use-queues.md) operace a correlate telemetrie mezi Autor, příjemce a Azure Storage. 

Fronty úložiště používá rozhraní API HTTP. Všechna volání do fronty jsou sledovány kolekce Application Insights závislostí pro požadavky HTTP.
Zajistěte, aby byla `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` v `applicationInsights.config`. Pokud ji nemáte, přidejte ho programově, jak je popsáno v [filtrování a předběžného zpracování v Azure Application Insights SDK](app-insights-api-filtering-sampling.md).

Pokud nakonfigurujete Application Insights ručně, ujistěte se, vytvoření a inicializace `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` podobně jako:
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Můžete také chtít korelovat Application Insights ID operace s ID úložiště požadavku. Informace o tom, jak nastavit a získat požadavek klienta úložiště a ID požadavku serveru najdete v tématu [monitorování, Diagnostika a řešení potíží s Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Zařazování
Protože toto rozhraní API HTTP podporují fronty úložiště, všechny operace s fronty sledovány automaticky pomocí Application Insights. V mnoha případech tato instrumentace by vám měly dostatečně. Ale ke korelaci trasování na straně příjemce s producent trasování, je nutné předat některé korelace kontextu podobně do jak jsme se v protokolu HTTP pro korelačního. 

Tento příklad ukazuje, jak sledovat `Enqueue` operaci. Můžete:

 - **Korelovat opakovaných pokusů (pokud existuje)**: budou všechny mít jeden společný nadřazených, který má `Enqueue` operaci. Jinak se sledují jako podřízené objekty příchozího požadavku. Pokud se několik logické požadavků do fronty, může být obtížné vyhledat, které volání výsledkem opakování.
 - **Korelovat protokoly úložiště (Pokud je potřeba)**: jste korelační s telemetrie Application Insights.

`Enqueue` Operaci je podřízeným nadřazené operace (například příchozí žádosti HTTP). Závislost volání protokolu HTTP je podřízeným `Enqueue` operace a pod podřízená příchozích požadavků:

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

Ke snížení objemu telemetrie aplikace sestavy nebo pokud nechcete, aby ke sledování `Enqueue` operace z jiných důvodů, použijte `Activity` přímo rozhraní API:

- Vytvořit (a spuštění) novou `Activity` místo spuštění operace Application Insights. Provedete *není* je třeba přiřadit žádné vlastnosti v něm kromě název operace.
- Serializovat `yourActivity.Id` do datové části zprávy místo `operation.Telemetry.Id`. Můžete také použít `Activity.Current.Id`.


#### <a name="dequeue"></a>Dequeue –
Podobně jako `Enqueue`, vlastní požadavek HTTP do fronty úložiště je automaticky sledován pomocí Application Insights. Ale `Enqueue` operaci pravděpodobně dojde v kontextu nadřazené, jako je například kontextu příchozí žádosti. Sadách Application Insights SDK automaticky korelovat takovou operaci (a jeho součástí HTTP) s nadřazené žádosti a další telemetrií hlášené ve stejném oboru.

`Dequeue` Operace je složité. Application Insights SDK automaticky sleduje požadavky HTTP. Ale neví kontext korelace, dokud je analyzován zprávu. Není možné ke korelaci požadavku HTTP k získání zprávy se zbytkem telemetrii.

V mnoha případech může být užitečné ke korelaci s také další trasování požadavku HTTP do fronty. Následující příklad ukazuje, jak to udělat:

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

V následujícím příkladu příchozí zprávy sledován způsobem podobně do příchozího požadavku HTTP:

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

Podobně můžete instrumentovány jiné operace fronty. Funkce Náhled operace by měla instrumentována podobným způsobem jako operace dequeue. Instrumentace operace fronty správy není nezbytné. Application Insights sleduje operací, jako je například HTTP a ve většině případů je dost.

Když jste instrumentace odstranění zprávy, nezapomeňte že nastavit operaci identifikátory (korelace). Alternativně můžete použít `Activity` rozhraní API. Pak nemusíte na položky telemetrie nastavit identifikátory operaci, protože Application Insights SDK provede za vás:

- Vytvořte novou `Activity` po vám položky z fronty.
- Použití `Activity.SetParentId(message.ParentId)` ke korelaci protokolů příjemce a výrobce.
- Spuštění `Activity`.
- Sledování dequeue – zpracovat a operace odstranění pomocí `Start/StopOperation` pomocné rutiny. To proveďte z stejného asynchronní řízení toku (kontext spuštění). Tímto způsobem že jste korelační správně.
- Zastavit `Activity`.
- Použití `Start/StopOperation`, nebo volejte `Track` telemetrie ručně.

### <a name="batch-processing"></a>Dávkové zpracování
Některé fronty můžete dequeue – více zpráv s jeden požadavek. Zpracování takové zprávy je pravděpodobně z důvodu nezávislé a patří do různých logických operací. V takovém případě není možné ke korelaci `Dequeue` na konkrétní zprávu zpracování operace.

Každá zpráva, měla by být zpracována v jeho vlastní asynchronní řízení toku. Další informace najdete v tématu [sledování závislosti odchozí](#outgoing-dependencies-tracking) části.

## <a name="long-running-background-tasks"></a>Dlouho běžící úlohy na pozadí
Některé aplikace spusťte dlouhotrvající operace, které mohou být způsobeny požadavků uživatele. Z pohledu trasování nebo instrumentace se neliší od instrumentace požadavku nebo závislost: 

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

V tomto příkladu `telemetryClient.StartOperation` vytvoří `RequestTelemetry` a výplní kontext korelace. Řekněme, že máte nadřazené operace, který byl vytvořen příchozích požadavků, které naplánované operace. Tak dlouho, dokud `BackgroundTask` spustí ve stejném asynchronní řízení toku jako příchozí žádosti, je vztažen v této operaci nadřazené. `BackgroundTask` a všechny vnořené telemetrie položky se automaticky korelační s požadavkem, který způsobuje její neočekávané, i po ukončení požadavku.

Při spuštění úlohy ze vlákně na pozadí, který nemá všechny operace (`Activity`) přidružený `BackgroundTask` nemá některé z nadřazených. Ale ho můžete vnořené operace. Všechny položky telemetrie nahlásila úlohy jsou korelována `RequestTelemetry` vytvořené v `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Odchozí závislosti sledování
Můžete sledovat vlastní typ závislosti nebo operace, který není podporován nástrojem Application Insights.

`Enqueue` Metoda v Service Bus fronty nebo fronty úložiště může sloužit jako příklady takových vlastní sledování.

Je obecný přístup k vlastní závislost sledování:

- Volání `TelemetryClient.StartOperation` metoda (rozšíření), který vyplní celé `DependencyTelemetry` vlastnosti, které jsou potřeba ke korelaci a některé další vlastnosti (čas spuštění razítka, doba trvání).
- Nastavit další vlastní vlastnosti, na `DependencyTelemetry`, třeba název a další kontext, je nutné.
- Ujistěte se, volání a počkejte na její závislosti.
- Zastavte operaci s `StopOperation` po jeho dokončení.
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

Uvolnění operace způsobí, že na zastavit, operace, můžete to provést místo volání `StopOperation`.

*Upozornění*: v některých případech může unhanded výjimka [zabránit](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` má být volána, nemusí být sledován operace.

### <a name="parallel-operations-processing-and-tracking"></a>Paralelní zpracování operací a sledování

`StopOperation` zastaví pouze operace, která byla spuštěna. Pokud aktuální běžící operace neodpovídá ten, který chcete zastavit, `StopOperation` se nic nestane. Tato situace může dojít, pokud spustíte více operací paralelně ve stejném kontextu spuštění:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
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

Ověřte, že je vždy volat `StartOperation` a zpracování operace ve stejné **asynchronní** metoda izolovat operace paralelně. Pokud operace je synchronní (nebo není asynchronní), zabalení proces a sledování s `Task.Run`:

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

- Seznámíte se základy [telemetrie korelace](application-insights-correlation.md) ve službě Application Insights.
- Najdete v článku [datový model](application-insights-data-model.md) Application Insights typy a data modelu.
- Sestavy vlastní [události a metriky](app-insights-api-custom-events-metrics.md) Application insights.
- Podívejte se na standardní [konfigurace](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) pro kolekci vlastností kontextu.
- Zkontrolujte [System.Diagnostics.Activity uživatelská příručka](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) zobrazíte, jak jsme korelovat telemetrii.
