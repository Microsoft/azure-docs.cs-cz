---
title: Sledování vlastních operací pomocí sady Azure Application Insights .NET SDK
description: Sledování vlastních operací pomocí sady Azure Application Insights .NET SDK
ms.topic: conceptual
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 31c1fb366e7b109ea1fa4977d8e2f908e766e0f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276098"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Sledování vlastních operací pomocí sady Application Insights .NET SDK

Sady Azure Application Insights SDK automaticky sledují příchozí požadavky HTTP a volání závislých služeb, jako jsou požadavky HTTP a dotazy SQL. Sledování a korelace požadavků a závislostí poskytují přehled o odezvě celé aplikace a spolehlivosti napříč všemi mikroslužbami, které kombinují tuto aplikaci. 

Existuje třída vzorů aplikací, které nelze obecně podporovat. Správné sledování těchto vzorů vyžaduje ruční instrumentaci kódu. Tento článek popisuje několik vzorů, které mohou vyžadovat ruční instrumentace, jako je například vlastní zpracování fronty a spuštění dlouhotrvající úlohy na pozadí.

Tento dokument obsahuje pokyny ke sledování vlastních operací pomocí sady Application Insights SDK. Tato dokumentace je relevantní pro:

- Application Insights pro rozhraní .NET (označované také jako Base SDK) verze 2.4+.
- Application Insights pro webové aplikace (spuštěné ASP.NET) verze 2.4+.
- Application Insights pro ASP.NET core verze 2.1+.

## <a name="overview"></a>Přehled
Operace je logická část práce spuštěná aplikací. Má název, čas zahájení, dobu trvání, výsledek a kontext spuštění, jako je uživatelské jméno, vlastnosti a výsledek. Pokud byla operace A zahájena operací B, je operace B nastavena jako nadřazená pro A. Operace může mít pouze jeden nadřazený, ale může mít mnoho podřízených operací. Další informace o operacích a korelaci telemetrie najdete v [tématu Azure Application Insights telemetrická korelace](correlation.md).

V application insights .NET SDK operace je popsána abstraktní třídy [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) a jeho potomci [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) a [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Sledování příchozích operací 
Webová sada Application Insights web SDK automaticky shromažďuje požadavky HTTP pro ASP.NET aplikace, které běží v kanálu služby IIS a všechny ASP.NET základní aplikace. Existují řešení podporovaná komunitou pro jiné platformy a architektury. Pokud však aplikace není podporována žádným standardním nebo komunitně podporovaným řešením, můžete ji instrumentovat ručně.

Dalším příkladem, který vyžaduje vlastní sledování je pracovník, který přijímá položky z fronty. U některých front je volání pro přidání zprávy do této fronty sledováno jako závislost. Operace vysoké úrovně, která popisuje zpracování zpráv však není automaticky shromažďována.

Podívejme se, jak by tyto operace mohly být sledovány.

Na vysoké úrovni je úkolem `RequestTelemetry` vytvořit a nastavit známé vlastnosti. Po dokončení operace můžete sledovat telemetrii. Následující příklad ukazuje tento úkol.

### <a name="http-request-in-owin-self-hosted-app"></a>Požadavek HTTP v samoobslužné aplikaci Owin
V tomto příkladu je kontext trasování šířen podle [protokolu HTTP pro korelaci](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Měli byste očekávat, že obdržíte záhlaví, které jsou zde popsány.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
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

Protokol HTTP pro korelaci `Correlation-Context` také deklaruje záhlaví. Nicméně, to je vynecháno zde pro jednoduchost.

## <a name="queue-instrumentation"></a>Přístrojové vybavení fronty
Zatímco existují [W3C trasovací kontext](https://www.w3.org/TR/trace-context/) a [protokol HTTP pro korelaci](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) předat podrobnosti korelace s požadavkem HTTP, každý protokol fronty musí definovat, jak jsou předány stejné podrobnosti podél zprávy fronty. Některé protokoly fronty (například AMQP) umožňují předávání dalších metadat a některé další (například Fronta úložiště Azure) vyžadují, aby byl kontext zakódován do datové části zprávy.

> [!NOTE]
> * **Trasování mezi součástmi zatím není pro fronty podporováno.** S HTTP, pokud váš výrobce a příjemce odesílat telemetrie do různých prostředků Application Insights, zkušenosti diagnostiky transakcí a mapa aplikací zobrazit transakce a mapování od konce do konce. V případě front to zatím není podporováno. 

### <a name="service-bus-queue"></a>Fronta služby Service Bus
Application Insights sleduje volání služby Service Bus zasílání zpráv s novým [klientem Microsoft Azure ServiceBus pro .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) verze 3.0.0 a vyšší.
Pokud ke zpracování zpráv použijete [vzor obslužné rutiny zpráv,](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) je hotovo: všechna volání služby Service Bus provedená vaší službou jsou automaticky sledována a korelována s jinými položkami telemetrie. Pokud zprávy zpracováváte ručně, vyhledejte [trasování klienta služby Microsoft Application Insights pomocí služby Microsoft Application Insights.](../../service-bus-messaging/service-bus-end-to-end-tracing.md)

Pokud používáte balíček [WindowsAzure.ServiceBus,](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) přečtěte si další – následující příklady ukazují, jak sledovat (a korelovat) volání service bus jako fronta service bus používá protokol AMQP a Application Insights automaticky nesleduje operace fronty.
Identifikátory korelace jsou předány ve vlastnostech zprávy.

#### <a name="enqueue"></a>Enqueue

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
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
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

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

### <a name="azure-storage-queue"></a>Fronta úložiště Azure
Následující příklad ukazuje, jak sledovat operace [fronty služby Azure Storage](../../storage/queues/storage-dotnet-how-to-use-queues.md) a korelovat telemetrii mezi výrobcem, spotřebitelem a úložištěm Azure. 

Fronta úložiště má rozhraní HTTP API. Všechna volání do fronty jsou sledována kolekcí závislostí Přehledy aplikací pro požadavky HTTP.
Je konfigurován ve výchozím nastavení na ASP.NET a ASP.NET základní aplikace, s jinými druhy aplikací, můžete odkazovat na [dokumentaci konzolových aplikací](../../azure-monitor/app/console.md)

Můžete také chtít korelovat ID operace Application Insights s ID požadavku úložiště. Informace o tom, jak nastavit a získat klienta požadavku úložiště a ID požadavku serveru, najdete v [tématu Monitorování, diagnostika a řešení potíží s Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Enqueue
Vzhledem k tomu, že fronty úložiště podporují rozhraní HTTP API, všechny operace s frontou jsou automaticky sledovány pomocí Application Insights. V mnoha případech by tato instrumentace měla stačit. Však korelovat trasování na straně spotřebitele s výrobcem trasování, musíte předat některé korelace kontextu podobně jako jak to děláme v protokolu HTTP pro korelaci. 

Tento příklad ukazuje, `Enqueue` jak sledovat operaci. Můžete:

 - **Korelační opakování (pokud existuje)**: Všechny mají jeden `Enqueue` společný nadřazený, který je operace. V opačném případě jsou sledovány jako děti příchozí žádosti. Pokud existuje více logických požadavků do fronty, může být obtížné najít, které volání vedlo k opakování.
 - **Korelovat protokoly úložiště (pokud a v případě potřeby)**: Jsou korelovány s telemetrií Application Insights.

Operace `Enqueue` je podřízená nadřazená operace (například příchozí požadavek HTTP). Volání závislosti HTTP je podřízeným objektem `Enqueue` operace a vnukem příchozího požadavku:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
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

Chcete-li snížit množství telemetrie, kterou aplikace hlásí, `Enqueue` nebo pokud nechcete `Activity` sledovat operaci z jiných důvodů, použijte rozhraní API přímo:

- Vytvořte (a spusťte) nový `Activity` namísto spuštění operace Application Insights. *Není* nutné přiřadit žádné vlastnosti na něj kromě názvu operace.
- Serializovat `yourActivity.Id` do datové části zprávy `operation.Telemetry.Id`namísto . Můžete také `Activity.Current.Id`použít .


#### <a name="dequeue"></a>Dequeue
Podobně jako `Enqueue`skutečný požadavek HTTP do fronty úložiště je automaticky sledován application insights. Operace však `Enqueue` pravděpodobně probíhá v nadřazeném kontextu, jako je například kontext příchozího požadavku. Sady SDK Application Insights automaticky korelují takovou operaci (a její část HTTP) s nadřazeným požadavkem a další telemetrií hlášenou ve stejném oboru.

Operace `Dequeue` je ošemetná. Sada Application Insights SDK automaticky sleduje požadavky HTTP. Však nezná kontext korelace, dokud je zpráva analyzována. Není možné korelovat požadavek HTTP získat zprávu se zbytkem telemetrie, zejména při přijetí více než jednu zprávu.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Proces

V následujícím příkladu je příchozí zpráva sledována podobně jako příchozí požadavek HTTP:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
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

Podobně ostatní operace fronty lze instrumentovat. Operace náhledu by měla být instrumentována podobným způsobem jako operace dequeue. Instrumentace operací správy fronty není nutná. Application Insights sleduje operace, jako je HTTP, a ve většině případů to stačí.

Při odstranění zprávy instrumentace, ujistěte se, že jste nastavili identifikátory operace (korelace). Případně můžete použít `Activity` rozhraní API. Pak není nutné nastavit identifikátory operací na položky telemetrie, protože Application Insights SDK to za vás:

- Po zobrazení `Activity` položky z fronty vytvořte novou položku.
- Slouží `Activity.SetParentId(message.ParentId)` ke korelaci protokolů spotřebitelů a výrobců.
- Spusťte . `Activity`
- Sledujte operace vyřazení z fronty, zpracování a odstranění pomocí `Start/StopOperation` pomocníků. Udělejte to ze stejného toku asynchronního řízení (kontext spuštění). Tímto způsobem jsou korelovány správně.
- Zastavte . `Activity`
- Použijte `Start/StopOperation`nebo `Track` volejte telemetrii ručně.

### <a name="dependency-types"></a>Typy závislostí

Application Insights používá typ závislostí k cusomize prostředí ui. U front rozpozná následující `DependencyTelemetry` typy, které zlepšují zkušenosti s [diagnostikou transakcí](/azure/azure-monitor/app/transaction-diagnostics):
- `Azure queue`pro fronty úložiště Azure
- `Azure Event Hubs`pro Centra událostí Azure
- `Azure Service Bus`pro Azure Service Bus

### <a name="batch-processing"></a>Dávkové zpracování
U některých front můžete s jedním požadavkem odřadovat do fronty více zpráv. Zpracování těchto zpráv je pravděpodobně nezávislé a patří k různým logickým operacím. Není možné korelovat `Dequeue` operaci s konkrétní zprávy, které jsou zpracovávány.

Každá zpráva by měla být zpracována ve vlastním toku asynchronního řízení. Další informace naleznete v části [Sledování odchozích závislostí.](#outgoing-dependencies-tracking)

## <a name="long-running-background-tasks"></a>Dlouhotrvající úlohy na pozadí

Některé aplikace spouštějí dlouhotrvající operace, které mohou být způsobeny požadavky uživatelů. Z hlediska trasování/instrumentace se neliší od nástroje požadavku nebo závislosti: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
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

V tomto `telemetryClient.StartOperation` příkladu vytvoří `DependencyTelemetry` a vyplní kontext korelace. Řekněme, že máte nadřazenou operaci, která byla vytvořena příchozími požadavky, které operaci naplánovaly. Tak dlouho, dokud `BackgroundTask` začíná ve stejném toku asynchronního řízení jako příchozí požadavek, je korelován s nadřazenou operací. `BackgroundTask`a všechny vnořené položky telemetrie jsou automaticky koreluje s požadavek, který ji způsobil, i po ukončení požadavku.

Když úkol začíná z vlákna na pozadí,`Activity`které nemá žádnou operaci ( ) s ním spojené, `BackgroundTask` nemá žádné nadřazené. Však může mít vnořené operace. Všechny položky telemetrie hlášené z `DependencyTelemetry` úkolu `BackgroundTask`korelují s vytvořenými položkami v aplikaci .

## <a name="outgoing-dependencies-tracking"></a>Sledování odchozích závislostí
Můžete sledovat vlastní druh závislostí nebo operaci, která není podporována Application Insights.

Metoda `Enqueue` ve frontě service bus nebo ve frontě úložiště může sloužit jako příklady pro takové vlastní sledování.

Obecný přístup pro vlastní sledování závislostí je:

- Volání `TelemetryClient.StartOperation` (rozšíření) metoda, která `DependencyTelemetry` vyplňuje vlastnosti, které jsou potřebné pro korelaci a některé další vlastnosti (počáteční časové razítko, doba trvání).
- Nastavte další vlastní `DependencyTelemetry`vlastnosti na , jako je například název a jakýkoli jiný kontext, který potřebujete.
- Proveďte volání závislosti a počkejte na něj.
- Zastavte `StopOperation` operaci, až bude hotová.
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

Likvidace operace způsobí, že operace bude zastavena, `StopOperation`takže to můžete udělat místo volání .

*Upozornění*: v některých případech může [nestranná](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` výjimka zabránit volání, takže operace nemusí být sledovány.

### <a name="parallel-operations-processing-and-tracking"></a>Zpracování a sledování paralelních operací

`StopOperation`pouze zastaví operaci, která byla spuštěna. Pokud aktuální spuštěná operace neodpovídá operaci, kterou `StopOperation` chcete zastavit, neprovede nic. K této situaci může dojít, pokud spustíte více operací paralelně ve stejném kontextu spuštění:

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

Ujistěte se, `StartOperation` že vždy volat a proces operace ve stejné **asynchronní** metody izolovat operace spuštěné paralelně. Pokud je operace synchronní (nebo není asynchronní), zalomit proces a sledovat s `Task.Run`:

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

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights operace vs System.Diagnostics.Activity
`System.Diagnostics.Activity`představuje kontext distribuovaného trasování a používá se rámci a knihovny k vytvoření a šíření kontextu uvnitř i vně procesu a korelovat položky telemetrie. Aktivita spolupracuje `System.Diagnostics.DiagnosticSource` s - mechanismus oznamování mezi framework/library upozornit na zajímavé události (příchozí nebo odchozí požadavky, výjimky, atd.).

Aktivity jsou prvotřídní občané v Application Insights a automatické závislosti a kolekce `DiagnosticSource` žádostí závisí do značné míry na nich spolu s událostmi. Pokud vytvoříte Aktivita ve vaší aplikaci – to by nemělo za následek Application Insights telemetrie se vytváří. Application Insights potřebuje přijímat DiagnosticSource události a znát názvy událostí a datové části převést aktivity do telemetrie.

Každá operace Application Insights (požadavek `Activity` nebo `StartOperation` závislost) zahrnuje - když je volána, vytvoří aktivitu pod. `StartOperation`je doporučený způsob, jak sledovat telemetry požadavků nebo závislostí ručně a zajistit, aby vše korelovalo.

## <a name="next-steps"></a>Další kroky

- Naučte se základy [korelace telemetrie](correlation.md) v Application Insights.
- Podívejte se, jak korelované údaje pomohou [zkušenosti diagnostiky transakcí](../../azure-monitor/app/transaction-diagnostics.md) a [mapa aplikací](../../azure-monitor/app/app-map.md).
- Podívejte se na [datový model](../../azure-monitor/app/data-model.md) pro typy application insights a datový model.
- Nahlaste vlastní [události a metriky](../../azure-monitor/app/api-custom-events-metrics.md) na Application Insights.
- Podívejte se na standardní [konfiguraci](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) pro kolekci vlastností kontextu.
- Zkontrolujte [System.Diagnostics.Activity Uživatelská příručka](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) zobrazíte, jak korelujeme telemetrie.
