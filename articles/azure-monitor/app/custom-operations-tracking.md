---
title: Sledování vlastních operací pomocí sady Azure Application Insights .NET SDK
description: Sledování vlastních operací pomocí sady Azure Application Insights .NET SDK
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 42a5318325f9961483465357403089755feb130d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933303"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Sledování vlastních operací pomocí sady Application Insights .NET SDK

Služba Azure Application Insights SDK automaticky sleduje příchozí požadavky HTTP a volá závislé služby, jako jsou požadavky HTTP a dotazy SQL. Sledování a korelace požadavků a závislostí vám umožní zobrazit veškerou odezvu a spolehlivost celé aplikace napříč všemi mikroslužbami, které tuto aplikaci spojují. 

Existuje třída vzorů aplikací, které nelze obecně podporovat. Správné monitorování takových vzorů vyžaduje ruční instrumentaci kódu. Tento článek se věnuje několika vzorům, které mohou vyžadovat ruční instrumentaci, jako je vlastní zpracování fronty a spouštění dlouhotrvajících úloh na pozadí.

Tento dokument poskytuje pokyny k tomu, jak sledovat vlastní operace pomocí sady Application Insights SDK. Tato dokumentace je relevantní pro:

- Application Insights pro .NET (označované také jako základní sada SDK) verze 2.4 +.
- Application Insights pro webové aplikace (se spuštěným ASP.NET) verze 2.4 +.
- Application Insights pro ASP.NET Core verze 2.1 +

## <a name="overview"></a>Přehled
Operace je logická část práce, kterou aplikace spouští. Má název, čas spuštění, dobu trvání, výsledek a kontext spuštění, jako je uživatelské jméno, vlastnosti a výsledek. Pokud operace A byla iniciována operací B, pak je operace B nastavena jako nadřazená pro. Operace může mít pouze jeden nadřazený prvek, ale může mít mnoho podřízených operací. Další informace týkající se operací a korelace telemetrie najdete v tématu [korelace telemetrie Azure Application Insights](correlation.md).

V sadě Application Insights .NET SDK je operace popsána pomocí abstraktní třídy [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) a jejích potomků [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) a [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Sledování příchozích operací 
Application Insights webová sada SDK automaticky shromáždí požadavky HTTP pro aplikace ASP.NET spuštěné v kanálu služby IIS a všech ASP.NET Corech aplikacích. K dispozici jsou řešení podporovaná komunitou pro jiné platformy a rozhraní. Nicméně pokud aplikace není podporovaná žádným z řešení Standard nebo Community, můžete ji instrumentovat ručně.

Dalším příkladem, který vyžaduje vlastní sledování, je pracovní proces, který přijímá položky z fronty. U některých front se volání pro přidání zprávy do této fronty sleduje jako závislost. Operace vysoké úrovně, která popisuje zpracování zprávy, však není automaticky shromažďována.

Pojďme se podívat, jak se tyto operace daly sledovat.

Na nejvyšší úrovni je úkolem vytvořit `RequestTelemetry` a nastavit známé vlastnosti. Po dokončení operace můžete sledovat telemetrii. Následující příklad znázorňuje tuto úlohu.

### <a name="http-request-in-owin-self-hosted-app"></a>Požadavek HTTP v samoobslužné aplikaci Owin
V tomto příkladu je kontext trasování šířený podle [protokolu HTTP pro korelaci](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Měli byste očekávat, že dostanete hlavičky popsané tady.

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

Protokol HTTP pro korelaci také deklaruje `Correlation-Context` hlavičku. Pro zjednodušení je však tady vynecháno.

## <a name="queue-instrumentation"></a>Instrumentace fronty
I když existuje [kontext trasování W3C](https://www.w3.org/TR/trace-context/) a [protokol HTTP, aby korelace](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) vyhověla podrobnostem korelace s požadavkem http, každý protokol fronty musí definovat, jak se budou stejné podrobnosti předávat společně se zprávou fronty. Některé protokoly front (například AMQP) umožňují předat další metadata a jiné (takové Azure Storage fronty) vyžadují, aby byl kontext kódovaný do datové části zprávy.

> [!NOTE]
> * **Pro fronty se ještě nepodporuje trasování mezi komponentami** . Pokud váš producent a zákazník odesílají telemetrii do různých prostředků Application Insights, prostředí pro diagnostiku transakcí a mapa aplikací v případě protokolu HTTP zobrazí kompletní a mapový postup. V případě front to ještě není podporováno. 

### <a name="service-bus-queue"></a>Fronta služby Service Bus
Application Insights sleduje volání zasílání zpráv Service Bus pomocí nového [klienta Microsoft Azure ServiceBus pro rozhraní .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) verze 3.0.0 a vyšší.
Pokud ke zpracování zpráv používáte [vzor obslužné rutiny zpráv](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) , jste hotovi: všechna Service Bus volání prováděná službou jsou automaticky sledována a koreluje s ostatními položkami telemetrie. Pokud ruční zpracování zpráv zpracováváte, přečtěte si téma [Service Bus trasování klienta s Microsoft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) .

Použijete-li balíček [windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) , následující příklady ukazují, jak sledovat (a korelovat) volání Service Bus jako Service Bus fronta používá protokol AMQP a Application Insights automaticky nesleduje operace ve frontě.
Identifikátory korelace jsou předány ve vlastnostech zprávy.

#### <a name="enqueue"></a>Zařazování

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

### <a name="azure-storage-queue"></a>Fronta Azure Storage
Následující příklad ukazuje, jak sledovat operace [Azure Storage fronty](../../storage/queues/storage-dotnet-how-to-use-queues.md) a korelovat telemetrii mezi producentem, spotřebitelem a Azure Storage. 

Fronta úložiště obsahuje rozhraní API HTTP. Všechna volání do fronty jsou sledována Application Insights kolektorem závislostí pro požadavky HTTP.
Ve výchozím nastavení je nakonfigurované v aplikacích ASP.NET a ASP.NET Core s dalšími druhy aplikací, můžete se podívat na [dokumentaci konzolových aplikací](./console.md) .

Můžete taky chtít korelovat ID operace Application Insights s ID žádosti úložiště. Informace o tom, jak nastavit a získat klienta žádosti o úložiště a ID žádosti serveru, najdete v tématu [monitorování, diagnostika a řešení potíží s Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Zařazování
Vzhledem k tomu, že fronty úložiště podporují rozhraní API protokolu HTTP, jsou všechny operace s frontou automaticky sledovány Application Insights. V mnoha případech by měla být tato instrumentace dostatečná. Chcete-li však korelovat trasování na straně spotřebitele pomocí trasování od výrobce, je nutné předat nějaký kontext korelace podobně jako v protokolu HTTP pro korelaci. 

Tento příklad ukazuje, jak sledovat `Enqueue` operaci. Můžete:

 - **Korelace pokusů (pokud existují)**: všichni mají jednu společnou nadřazenou položku, která je `Enqueue` operace. V opačném případě jsou sledovány jako podřízené položky příchozího požadavku. Pokud fronta obsahuje více logických požadavků, může být obtížné zjistit, které volání vedlo k opakovaným pokusům.
 - **Korelace protokolů úložiště (Pokud je potřeba a v případě potřeby)**: jsou ve vztahu k Application Insights telemetrie.

Tato `Enqueue` operace je podřízenou položkou nadřazené operace (například příchozí požadavek HTTP). Volání závislosti protokolu HTTP je podřízeným prvkem `Enqueue` operace a podřízeným objektem příchozího požadavku:

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

Chcete-li snížit množství telemetrie v sestavách aplikace nebo pokud nechcete sledovat `Enqueue` operaci z jiných důvodů, použijte `Activity` rozhraní API přímo:

- Vytvořte (a začněte) novou `Activity` místo spuštění operace Application Insights. *Nemusíte* jim přiřazovat žádné vlastnosti s výjimkou názvu operace.
- Serializovat `yourActivity.Id` do datové části zprávy místo `operation.Telemetry.Id` . Můžete také použít `Activity.Current.Id` .


#### <a name="dequeue"></a>Odstranění z fronty
Podobně platí `Enqueue` , že skutečný požadavek HTTP na frontu úložiště se automaticky sleduje pomocí Application Insights. Operace se však `Enqueue` předpokládá v nadřazeném kontextu, jako je například příchozí kontext požadavku. Application Insights sady SDK automaticky korelují takovou operaci (a její část HTTP) s nadřazeným požadavkem a další telemetrie hlášenou ve stejném oboru.

`Dequeue`Operace je obtížné. Sada SDK pro Application Insights automaticky sleduje požadavky HTTP. Ale neví kontext korelace, dokud se zpráva neanalyzuje. Není možné sladit požadavek protokolu HTTP a získat zprávu se zbytkem telemetrie zvlášť, pokud je přijata více než jedna zpráva.

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

V následujícím příkladu je příchozí zpráva sledována podobným způsobem jako příchozí požadavek HTTP:

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

Podobně je možné instrumentovat další operace s frontou. Operace náhledu by měla být instrumentovaná podobným způsobem jako operace vyřazení z fronty. Operace správy fronty instrumentace není nutná. Application Insights sleduje operace jako HTTP a ve většině případů je to dostatečné.

Při odstraňování zprávy instrumentace se ujistěte, že jste nastavili identifikátory operace (korelace). Alternativně můžete použít `Activity` rozhraní API. Pak nemusíte pro položky telemetrie nastavovat identifikátory operací, protože Application Insights SDK to dělá za vás:

- `Activity`Až budete mít položku z fronty, vytvořte novou.
- Slouží `Activity.SetParentId(message.ParentId)` ke sladění protokolů příjemců a výrobců.
- Spusťte `Activity` .
- Sledujte operace vyřazování z fronty, procesů a odstraňování pomocí `Start/StopOperation` pomocníků. Udělejte to ze stejného asynchronního toku řízení (kontext spuštění). Tímto způsobem jsou správně přihlášeny.
- Zastavte `Activity` .
- Použijte `Start/StopOperation` nebo zavolejte `Track` telemetrii ručně.

### <a name="dependency-types"></a>Typy závislostí

Application Insights používá typ závislosti k přizpůsobení prostředí uživatelského rozhraní. Pro fronty rozpoznává následující typy `DependencyTelemetry` , které zlepšují [prostředí diagnostiky transakcí](./transaction-diagnostics.md):
- `Azure queue` pro Azure Storage fronty
- `Azure Event Hubs` pro Azure Event Hubs
- `Azure Service Bus` pro Azure Service Bus

### <a name="batch-processing"></a>Dávkové zpracování
U některých front můžete vyřadit více zpráv s jednou žádostí. Zpracování takových zpráv je pravděpodobně nezávislé a patří do různých logických operací. Tuto operaci není možné korelovat `Dequeue` s konkrétní zpracovávanou zprávou.

Každá zpráva by měla být zpracována ve vlastním asynchronním toku řízení. Další informace najdete v části [odchozí sledování závislostí](#outgoing-dependencies-tracking) .

## <a name="long-running-background-tasks"></a>Dlouhodobě běžící úlohy na pozadí

Některé aplikace spouštějí dlouhotrvající operace, které mohou být způsobeny požadavky uživatele. Z pohledu trasování/instrumentace se neliší od požadavků a instrumentace závislosti: 

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

V tomto příkladu `telemetryClient.StartOperation` vytvoří `DependencyTelemetry` a vyplní kontext korelace. Řekněme, že máte nadřazenou operaci, která byla vytvořena pomocí příchozích požadavků, které naplánovaly operaci. Pokud se `BackgroundTask` spustí ve stejném asynchronním toku řízení jako příchozí požadavek, je koreluje s touto nadřazenou operací. `BackgroundTask` a všechny vnořené položky telemetrie jsou automaticky korelační s požadavkem, který ji způsobil, i po ukončení žádosti.

Když se úloha spustí z vlákna na pozadí, ke kterému není přidružena žádná operace ( `Activity` ), nemá `BackgroundTask` žádnou nadřazenou položku. Může ale mít vnořené operace. Všechny položky telemetrie hlášené z úlohy jsou ve vztahu k `DependencyTelemetry` vytvořenému v `BackgroundTask` .

## <a name="outgoing-dependencies-tracking"></a>Sledování odchozích závislostí
Můžete sledovat vlastní druh závislosti nebo operaci, kterou Application Insights nepodporuje.

`Enqueue`Metoda ve frontě Service Bus nebo ve frontě úložiště může sloužit jako příklady pro takové vlastní sledování.

Obecným přístupem k vlastnímu sledování závislostí je:

- Zavolejte `TelemetryClient.StartOperation` metodu (Extension), která vyplní `DependencyTelemetry` vlastnosti potřebné pro korelaci a některé další vlastnosti (časové razítko času, doba trvání).
- Nastavte další vlastní vlastnosti na `DependencyTelemetry` , například název a jakýkoli jiný kontext, který potřebujete.
- Proveďte volání závislosti a počkejte na ni.
- Po dokončení operace zastavte `StopOperation` .
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

Operace disposing způsobí zastavení operace, takže ji můžete použít místo volání `StopOperation` .

*Upozornění*: v některých případech [je možné,](/dotnet/csharp/language-reference/keywords/try-finally) že v některých případech `finally` není zavolána výjimka, takže operace nemusí být sledovány.

### <a name="parallel-operations-processing-and-tracking"></a>Paralelní zpracování a sledování operací

`StopOperation` zastaví pouze operaci, která byla spuštěna. Pokud aktuální běžící operace neodpovídá hodnotě, kterou chcete zastavit, `StopOperation` neprovede nic. K této situaci může dojít, Pokud zahájíte paralelní spouštění více operací ve stejném kontextu spuštění:

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

Ujistěte se, že jste vždy volali `StartOperation` a zpracovali operaci ve stejné **asynchronní** metodě pro izolaci operací, které běží paralelně. Pokud je operace synchronní (nebo není asynchronní), zabalte proces a sledujte `Task.Run` :

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

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>Operace ApplicationInsights vs System. Diagnostics. Activity
`System.Diagnostics.Activity` představuje kontext distribuovaného trasování a je používán architekturami a knihovnami k vytvoření a rozšíření kontextu uvnitř a vně procesu a korelaci položek telemetrie. Aktivita spolupracuje s `System.Diagnostics.DiagnosticSource` mechanismem oznámení mezi architekturou/knihovnou a oznamuje o zajímavých událostech (příchozí nebo odchozí požadavky, výjimky atd.).

Aktivity jsou občany první třídy v Application Insights a Automatická závislost a shromažďování žádostí se na ně spoléhá i s `DiagnosticSource` událostmi. Pokud ve své aplikaci vytvoříte aktivitu – nebude to mít za následek vytvoření telemetrie Application Insights. Application Insights musí přijímat události DiagnosticSource a znát názvy událostí a datové části pro překlad aktivity do telemetrie.

Každá operace Application Insights (požadavek nebo závislost) zahrnuje `Activity` – při `StartOperation` volání funkce se vytvoří aktivita pod ní. `StartOperation` je doporučeným způsobem, jak ručně sledovat telemetrií požadavků nebo závislostí, a zajistit, aby vše bylo korelujé.

## <a name="next-steps"></a>Další kroky

- Seznamte se se základy [korelace telemetrie](correlation.md) v Application Insights.
- Podívejte se, jak [prostředí pro diagnostiku transakcí](./transaction-diagnostics.md) a datovou [mapu aplikace](./app-map.md)jsou v relaci.
- Seznamte se s [datovým modelem](./data-model.md) pro Application Insights typy a datový model.
- Vykázat vlastní [události a metriky](./api-custom-events-metrics.md) na Application Insights.
- Podívejte se na standardní [konfiguraci](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) pro kolekci vlastností kontextu.
- Podívejte se na [uživatelskou příručku System. Diagnostics. Activity](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) , kde zjistíte, jak korelace telemetrie.

