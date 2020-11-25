---
title: Jak používat sadu WebJobs SDK
description: Přečtěte si další informace o tom, jak napsat kód pro sadu WebJobs SDK. Vytvářejte úlohy zpracování na pozadí založené na událostech, které přistupují k datům v Azure a službách třetích stran.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: b97ae5d4ba4295ebbb51c960e4cbb76c53dc88a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009670"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Jak použít sadu Azure WebJobs SDK k událostmi řízenému zpracování na pozadí

Tento článek poskytuje pokyny pro práci s Azure WebJobs SDK. Pokud chcete začít s WebJobs hned hned, přečtěte si téma Začínáme [s Azure WEBJOBS SDK pro zpracování na pozadí založené na událostech](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Verze sady WebJobs SDK

Toto jsou klíčové rozdíly mezi verzí 3. *x* a verze 2. *x* sady WebJobs SDK:

* Verze 3. *x* přidává podporu pro .NET Core.
* Ve verzi 3. *x*, musíte explicitně nainstalovat rozšíření vazby úložiště, které vyžaduje Sada WebJobs SDK. Ve verzi 2. *x* byly vazby úložiště součástí sady SDK.
* Nástroje sady Visual Studio pro .NET Core (3.*x*) projekty se liší od nástrojů pro .NET Framework (2.*x*) projektů. Další informace najdete v tématu [vývoj a nasazení WebJobs pomocí sady Visual Studio – Azure App Service](webjobs-dotnet-deploy-vs.md).

Pokud je to možné, jsou k dispozici příklady pro obě verze 3. *x* a verze 2. *x*.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) je postavená na sadě WebJobs SDK a tento článek obsahuje odkazy na dokumentaci k Azure Functions pro některá témata. Všimněte si těchto rozdílů mezi funkcemi a sadou WebJobs SDK:
> * Azure Functions verze 2 *x* odpovídá sadě WebJobs SDK verze 3. *x* a Azure Functions 1. *x* odpovídá sadě WebJobs SDK 2. *x*. Úložiště zdrojového kódu používají číslování sady SDK pro WebJobs.
> * Vzorový kód pro knihovny tříd Azure Functions C# jako kód sady SDK pro WebJobs, s výjimkou, že nepotřebujete `FunctionName` atribut v projektu sady WebJobs SDK.
> * Některé typy vazeb se podporují jenom ve funkcích, jako je HTTP (Webhooky) a Event Grid (založené na HTTP).
>
> Další informace najdete v tématu [porovnání sady WebJobs SDK a Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Hostitel WebJobs

Hostitel je kontejner modulu runtime pro funkce.  Naslouchá triggerům a volání funkcí. Ve verzi 3. *x*, hostitel je implementace `IHost` . Ve verzi 2. *x*, můžete použít `JobHost` objekt. Vytvoříte instanci hostitele v kódu a napíšete kód pro přizpůsobení jeho chování.

Jedná se o klíčový rozdíl mezi použitím sady WebJobs SDK přímo a jejím přímým použitím prostřednictvím Azure Functions. V Azure Functions služba řídí hostitele a nemůžete přizpůsobit hostitele pomocí psaní kódu. Azure Functions umožňuje přizpůsobit chování hostitele prostřednictvím nastavení host.jsv souboru. Tato nastavení jsou řetězce, nikoli kód a tato omezení omezují typy úprav, které lze provést.

### <a name="host-connection-strings"></a>Připojovací řetězce hostitele

Sada WebJobs SDK hledá Azure Storage a Azure Service Bus připojovací řetězce v local.settings.jszapnutém souboru při spuštění místně nebo v prostředí úlohy WebJob při spuštění v Azure. Ve výchozím nastavení se vyžaduje nastavení připojovacího řetězce úložiště s názvem `AzureWebJobsStorage` .  

Verze 2. *x* sady SDK umožňuje používat pro tyto připojovací řetězce vlastní názvy nebo je ukládat jinde. Můžete nastavit názvy v kódu pomocí [`JobHostConfiguration`] , jak je znázorněno zde:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Protože verze 3. *x* používá výchozí rozhraní API pro konfiguraci .NET Core, neexistuje žádné rozhraní API pro změnu názvů připojovacích řetězců.

### <a name="host-development-settings"></a>Nastavení vývoje hostitele

Můžete spustit hostitele v režimu vývoje a zefektivnit tak místní vývoj. Tady jsou některá nastavení, která se změnila při spuštění v režimu pro vývoj:

| Vlastnost | Nastavení vývoje |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` pro maximalizaci výstupu protokolu. |
| `Queues.MaxPollingInterval`  | Nízká hodnota pro zajištění, že se metody fronty spouštějí okamžitě.  |
| `Singleton.ListenerLockPeriod` | 15 sekund na pomoc při rychlém iterativním vývoji. |

Proces povolení režimu vývoje závisí na verzi sady SDK. 

#### <a name="version-3x"></a>Verze 3. *x*

Verze 3. *x* používá standardní rozhraní ASP.NET Core API. Zavolejte [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metodu na [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instanci. Předejte řetězec s názvem `development` , jako v tomto příkladu:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Verze 2. *x*

`JobHostConfiguration`Třída má `UseDevelopmentSettings` metodu, která umožňuje režim vývoje.  Následující příklad ukazuje, jak použít nastavení vývoje. Chcete-li provést `config.IsDevelopment` návrat `true` při místním spuštění, nastavte místní proměnnou prostředí s názvem `AzureWebJobsEnv` s hodnotou `Development` .

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Správa souběžných připojení (verze 2 *) ×*)

Ve verzi 3. *x* se limit připojení nastaví jako nekonečná připojení. Pokud z nějakého důvodu potřebujete tento limit změnit, můžete použít [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) vlastnost [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) třídy.

Ve verzi 2. *x* můžete řídit počet souběžných připojení k hostiteli pomocí rozhraní API [Třída ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . V 2. *x*, před spuštěním hostitele WebJobs byste tuto hodnotu měli zvýšit od výchozí hodnoty 2.

Všechny odchozí požadavky HTTP, které jste provedli z funkce pomocí `HttpClient` toku `ServicePointManager` . Po dosažení hodnoty nastavené v `DefaultConnectionLimit` spustí aplikace `ServicePointManager` před odesláním požadavky do fronty. Předpokládejme, že `DefaultConnectionLimit` je nastavené na 2 a váš kód bude 1 000 požadavků HTTP. Zpočátku jsou do operačního systému povoleny pouze dvě požadavky. Ostatní 998 jsou zařazeny do fronty, dokud pro ně neexistují místnosti. To znamená, že váš `HttpClient` časový limit vypršel, protože se zdá, že byl požadavek proveden, ale požadavek nebyl nikdy odeslán operačním systémem do cílového serveru. Takže se může zobrazit chování, které se jeví jako smysl: požadavek vaší místní `HttpClient` služby trvá 10 sekund, ale služba vrací každý požadavek v 200 ms. 

Výchozí hodnota pro aplikace ASP.NET je, což je `Int32.MaxValue` nejspíš dobře fungovat pro webové úlohy spuštěné v plánu Basic nebo vyšší App Service. U webových úloh se obvykle vyžaduje nastavení Always On a podporuje se jenom plány Basic a vyšší App Service.

Pokud je vaše webová úloha spuštěna v rámci bezplatného nebo sdíleného App Serviceho plánu, aplikace je omezená na App Service izolovaného prostoru (sandbox), která má v současné době [limit připojení 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). U nevázaného limitu připojení v nástroji je `ServicePointManager` pravděpodobnější, že dojde k dosažení prahové hodnoty připojení izolovaného prostoru a lokalita se vypne. V takovém případě je možné, že nastavení `DefaultConnectionLimit` na nižší hodnotu, například 50 nebo 100, může zabránit tomu, aby se to stalo a pořád umožňoval dostatek propustnosti.

Nastavení musí být nakonfigurováno před provedením jakýchkoli požadavků HTTP. Z tohoto důvodu by hostitel WebJobs neměl nastavení automaticky upravit. Může se jednat o požadavky HTTP, ke kterým dojde před spuštěním hostitele, což by mohlo vést k neočekávanému chování. Nejlepším řešením je nastavit hodnotu hned v `Main` metodě před inicializací `JobHost` , jak je znázorněno zde:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Aktivační události

Funkce musí být veřejné metody a musí mít jeden atribut triggeru nebo [`NoAutomaticTrigger`](#manual-triggers) atribut.

### <a name="automatic-triggers"></a>Automatické triggery

Automatické triggery volají funkci v reakci na událost. Vezměte v úvahu tento příklad funkce, která se aktivuje pomocí zprávy přidané do Azure Queue Storage. To reaguje čtením objektu BLOB ze služby Azure Blob Storage:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger`Atribut říká modulu runtime, aby vyvolal funkci pokaždé, když se ve frontě zobrazí zpráva fronty `myqueue-items` . `Blob`Atribut oznamuje modulu runtime, aby používal zprávu queue ke čtení objektu BLOB v kontejneru *Sample-pracovní položky* . Název položky objektu BLOB v `samples-workitems` kontejneru se získá přímo z triggeru fronty jako výraz vazby ( `{queueTrigger}` ).

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Ruční triggery

Chcete-li funkci aktivovat ručně, použijte `NoAutomaticTrigger` atribut, jak je znázorněno zde:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

Proces ruční aktivace funkce závisí na verzi sady SDK.

#### <a name="version-3x"></a>Verze 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Verze 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Vstupní a výstupní vazby

Vstupní vazby poskytují deklarativní způsob, jak vytvářet data z Azure nebo služeb třetích stran, které jsou k dispozici pro váš kód. Výstupní vazby poskytují způsob, jak aktualizovat data. V článku [Začínáme](webjobs-sdk-get-started.md) se zobrazuje příklad každého z nich.

Můžete použít návratovou hodnotu metody pro výstupní vazbu použitím atributu pro návratovou hodnotu metody. Podívejte se na příklad v tématu [použití návratové hodnoty funkce Azure Functions](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Typy vazeb

Proces pro instalaci a správu typů vazeb závisí na tom, zda používáte verzi 3. *x* nebo verze 2. *x* sady SDK. Balíček, který chcete nainstalovat pro konkrétní typ vazby, najdete v části Packages (balíčky) tohoto [článku Azure Functions odkazu](#binding-reference-information)typu vazby. Výjimka je Trigger souborů a vazba (pro místní systém souborů), která není podporována Azure Functions.

#### <a name="version-3x"></a>Verze 3. *x*

Ve verzi 3. *x*, vazby úložiště jsou součástí `Microsoft.Azure.WebJobs.Extensions.Storage` balíčku. Zavolejte `AddAzureStorage` metodu rozšíření v `ConfigureWebJobs` metodě, jak je znázorněno zde:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Chcete-li použít jiné typy triggerů a vazeb, nainstalujte balíček NuGet, který je obsahuje, a zavolejte `Add<binding>` metodu rozšíření implementovanou v rozšíření. Například pokud chcete použít Azure Cosmos DB vazby, nainstalujte `Microsoft.Azure.WebJobs.Extensions.CosmosDB` a zavolejte takto `AddCosmosDB` :

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Chcete-li použít aktivační událost časovače nebo vazby souborů, které jsou součástí základních služeb, zavolejte `AddTimers` `AddFiles` metody rozšíření nebo v uvedeném pořadí.

#### <a name="version-2x"></a>Verze 2. *x*

Tyto triggery a typy vazeb jsou součástí verze 2. *x* `Microsoft.Azure.WebJobs` balíčku:

* Blob Storage
* Queue Storage
* Table Storage

Chcete-li použít jiné typy triggerů a vazeb, nainstalujte balíček NuGet, který je obsahuje, a zavolejte `Use<binding>` metodu `JobHostConfiguration` objektu. Například pokud chcete použít aktivační událost časovače, nainstalujte `Microsoft.Azure.WebJobs.Extensions` a zavolejte `UseTimers` do `Main` metody, jak je znázorněno zde:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Chcete-li použít vazby souborů, nainstalujte `Microsoft.Azure.WebJobs.Extensions` a zavolejte `UseFiles` .

### <a name="executioncontext"></a>ExecutionContext

WebJobs umožňuje vytvořit propojení s [`ExecutionContext`] . Pomocí této vazby můžete [`ExecutionContext`] v podpisu funkce získat přístup k parametru AS. Například následující kód používá kontextový objekt pro přístup k ID vyvolání, které můžete použít ke korelaci všech protokolů vytvořených daným voláním funkce.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

Proces vytvoření vazby [`ExecutionContext`] závisí na vaší verzi sady SDK.

#### <a name="version-3x"></a>Verze 3. *x*

Zavolejte `AddExecutionContextBinding` metodu rozšíření v `ConfigureWebJobs` metodě, jak je znázorněno zde:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Verze 2. *x*

`Microsoft.Azure.WebJobs.Extensions`Výše uvedený balíček také poskytuje speciální typ vazby, který lze zaregistrovat voláním `UseCore` metody. Tato vazba umožňuje definovat [`ExecutionContext`] parametr v signatuře funkce, který je povolený takto:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Konfigurace vazby

Můžete nakonfigurovat chování některých triggerů a vazeb. Proces konfigurace je závislý na verzi sady SDK.

* **Verze 3. *x*:** nastavte konfiguraci při `Add<Binding>` volání metody v `ConfigureWebJobs` .
* **Verze 2. *x*:** nastavte konfiguraci nastavením vlastností v objektu konfigurace, který předáte do `JobHost` .

Tato nastavení specifická pro vazbu jsou ekvivalentní nastavení v [host.jssouboru projektu](../azure-functions/functions-host-json.md) v Azure Functions.

Můžete nakonfigurovat následující vazby:

* [Aktivační událost Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Aktivační událost Event Hubs](#event-hubs-trigger-configuration-version-3x)
* [Trigger služby Queue Storage](#queue-storage-trigger-configuration)
* [Vazba SendGrid](#sendgrid-binding-configuration-version-3x)
* [Aktivační událost Service Bus](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Konfigurace aktivační události Azure CosmosDB (verze 3 *) ×*)

Tento příklad ukazuje, jak nakonfigurovat aktivační událost Azure Cosmos DB:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Další podrobnosti najdete v článku věnovaném [vazbám Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings) .

### <a name="event-hubs-trigger-configuration-version-3x"></a>Konfigurace aktivační události Event Hubs (verze 3 *×*)

Tento příklad ukazuje, jak nakonfigurovat aktivační událost Event Hubs:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Další podrobnosti najdete v článku věnovaném [vazbě Event Hubs](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json) .

### <a name="queue-storage-trigger-configuration"></a>Konfigurace triggeru služby Queue Storage

Tyto příklady ukazují, jak nakonfigurovat aktivační událost fronty pro úložiště:

#### <a name="version-3x"></a>Verze 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Další podrobnosti najdete v článku věnovaném [vazbě úložiště fronty](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties) .

#### <a name="version-2x"></a>Verze 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Další podrobnosti najdete v referenčních informacích o [host.jsv v1. x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Konfigurace vazeb SendGrid (verze 3 *) ×*)

Tento příklad ukazuje, jak nakonfigurovat výstupní vazbu SendGrid:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Další podrobnosti najdete v článku věnovaném [vazbě SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) .

### <a name="service-bus-trigger-configuration-version-3x"></a>Konfigurace aktivační události Service Bus (verze 3 *×*)

Tento příklad ukazuje, jak nakonfigurovat aktivační událost Service Bus:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Další podrobnosti najdete v článku věnovaném [vazbě Service Bus](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings) .

### <a name="configuration-for-other-bindings"></a>Konfigurace pro jiné vazby

Některé typy triggerů a vazeb definují vlastní typy konfigurace. Například Trigger souboru umožňuje zadat kořenovou cestu, která se má monitorovat, jako v těchto příkladech:

#### <a name="version-3x"></a>Verze 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Verze 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Výrazy vazby

V parametrech konstruktoru atributu můžete použít výrazy, které se překládají na hodnoty z různých zdrojů. Například v následujícím kódu cesta pro `BlobTrigger` atribut vytvoří výraz s názvem `filename` . Při použití pro výstupní vazbu se `filename` přeloží na název triggeru objektu BLOB.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Další informace o výrazech vazby naleznete v tématu [výrazy a vzory vazby](../azure-functions/functions-bindings-expressions-patterns.md) v dokumentaci Azure Functions.

### <a name="custom-binding-expressions"></a>Vlastní výrazy vazby

Někdy je třeba zadat název fronty, název objektu BLOB nebo kontejner nebo název tabulky v kódu, nikoli v hardwarovém kódování. Například můžete chtít zadat název fronty pro `QueueTrigger` atribut v konfiguračním souboru nebo v proměnné prostředí.

To lze provést předáním objektu do `NameResolver` `JobHostConfiguration` objektu. Do parametrů triggeru nebo konstruktoru atributu Binding jsou zástupné symboly a váš `NameResolver` kód poskytuje skutečné hodnoty, které budou použity místo těchto zástupných symbolů. Zástupné symboly se identifikují v procentech (%) znaménka, jak je znázorněno zde:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Tento kód umožňuje používat frontu s názvem `logqueuetest` v testovacím prostředí a jednu s názvem `logqueueprod` v produkčním prostředí. Namísto pevně zakódovaného názvu fronty zadáte název položky v `appSettings` kolekci.

`NameResolver`Pokud nezadáte vlastní, projeví se výchozí nastavení. Výchozí hodnota Získá hodnoty z nastavení aplikace nebo proměnných prostředí.

Vaše `NameResolver` Třída získá název fronty z `appSettings` , jak je znázorněno zde:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Verze 3. *x*

Překladač můžete nakonfigurovat pomocí injektáže závislostí. Tyto ukázky vyžadují následující `using` příkaz:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Překladač přidáte voláním [`ConfigureServices`] metody rozšíření na  [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) , jako v tomto příkladu:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Verze 2. *x*

Předejte `NameResolver` třídu do `JobHost` objektu, jak je znázorněno zde:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions implementuje `INameResolver` k získání hodnot z nastavení aplikace, jak je znázorněno v příkladu. Když použijete sadu SDK pro WebJobs přímo, můžete napsat vlastní implementaci, která vrací hodnoty zástupných symbolů z libovolného zdroje, kterému dáváte přednost.

## <a name="binding-at-runtime"></a>Vazba za běhu

Pokud potřebujete udělat nějakou práci ve funkci před použitím atributu vazby `Queue` , jako je, `Blob` nebo `Table` , můžete použít `IBinder` rozhraní.

Následující příklad přebírá zprávu vstupní fronty a vytvoří novou zprávu se stejným obsahem ve výstupní frontě. Název výstupní fronty je nastaven podle kódu v těle funkce.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Další informace najdete v tématu [vázání za běhu](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) v dokumentaci Azure Functions.

## <a name="binding-reference-information"></a>Referenční informace o vazbě

Dokumentace k Azure Functions poskytuje referenční informace o jednotlivých typech vazeb. V každém referenčním článku o vazbách najdete následující informace. (Tento příklad je založen na frontě úložiště.)

* [Balíčky](../azure-functions/functions-bindings-storage-queue.md). Balíček, který je potřeba nainstalovat, aby zahrnoval podporu vazby v projektu sady WebJobs SDK.
* [Příklady](../azure-functions/functions-bindings-storage-queue-trigger.md). Ukázky kódu. Příklad knihovny tříd jazyka C# se vztahuje na sadu WebJobs SDK. Stačí vynechat `FunctionName` atribut.
* [Atributy](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). Atributy, které mají být použity pro typ vazby.
* [Konfigurace](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Vysvětlení vlastností atributů a parametrů konstruktoru.
* [Využití](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Typy, které můžete svázat, a informace o tom, jak vazba funguje. Příklad: algoritmus cyklického dotazování, zpracování fronty poškození.
  
Seznam článků s odkazy na vazby najdete v části "podporované vazby" v článku [triggery a vazby](../azure-functions/functions-triggers-bindings.md#supported-bindings) pro Azure Functions. V tomto seznamu jsou vazby HTTP, webhooks a Event Grid podporovány pouze pomocí Azure Functions, nikoli pomocí sady WebJobs SDK.

## <a name="disable-attribute"></a>Zakázat atribut 

[`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs)Atribut umožňuje řídit, zda lze funkci aktivovat. 

V následujícím příkladu, pokud má nastavení aplikace `Disable_TestJob` hodnotu `1` nebo `True` (bez rozlišení velkých a malých písmen), funkce se nespustí. V takovém případě modul runtime vytvoří funkci zprávy protokolu *Functions. TestJob, která je zakázaná*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Když změníte hodnoty nastavení aplikace v Azure Portal, Webová úloha se restartuje, aby se vybralo nové nastavení.

Atribut lze deklarovat na úrovni parametru, metody nebo třídy. Název nastavení může obsahovat i výrazy vazby.

## <a name="timeout-attribute"></a>Atribut timeout

[`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs)Atribut způsobí zrušení funkce, pokud se nedokončí v určeném časovém intervalu. V následujícím příkladu by se funkce spouštěla po jednom dni bez atributu Timeout. Timeout způsobí, že se funkce zruší po 15 sekundách.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Můžete použít atribut timeout na úrovni třídy nebo metody a můžete zadat globální časový limit pomocí `JobHostConfiguration.FunctionTimeout` . Časové limity na úrovni třídy nebo metody přepisují globální časové limity.

## <a name="singleton-attribute"></a>Singleton – atribut

[`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs)Atribut zajišťuje, že bude spuštěna pouze jedna instance funkce, i když existuje více instancí webové aplikace hostitele. Používá se k tomu [distribuované uzamykání](#viewing-lease-blobs).

V tomto příkladu se v `ProcessImage` daném čase spustí jenom jedna instance funkce:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode. Listener

Některé triggery mají integrovanou podporu pro správu souběžnosti:

* **QueueTrigger**. Nastavte `JobHostConfiguration.Queues.BatchSize` na `1` .
* **ServiceBusTrigger**. Nastavte `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` na `1` .
* **Trigger**. Nastavte `FileProcessor.MaxDegreeOfParallelism` na `1` .

Pomocí těchto nastavení můžete zajistit, aby se vaše funkce spouštěla jako typ singleton v rámci jedné instance. Chcete-li zajistit, aby byla při škálování webové aplikace na více instancí spuštěna pouze jedna instance funkce, použijte pro funkci () zámek singleton na úrovni naslouchacího procesu `[Singleton(Mode = SingletonMode.Listener)]` . Zámky naslouchacího procesu se získávají při spuštění JobHost. Pokud se současně spustí tři instance s horizontálním škálováním, pouze jedna z instancí získá zámek a spustí se pouze jeden naslouchací proces.

> [!NOTE]
> Další informace o tom, jak funguje SingletonMode. Function, najdete v tomto [úložišti GitHub](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonMode.cs) .

### <a name="scope-values"></a>Hodnoty oboru

Pro typ singleton můžete zadat *výraz nebo hodnotu oboru* . Výraz/hodnota zajistí, že všechna spuštění funkce v konkrétním oboru budou serializována. Implementace podrobnějšího zamykání tímto způsobem může způsobit určitou úroveň paralelismu pro vaši funkci při serializaci jiných volání podle vašich požadavků. Například v následujícím kódu se výraz SCOPE váže k `Region` hodnotě příchozí zprávy. Pokud fronta obsahuje tři zprávy v oblasti východ, východní a západní, budou zprávy s oblastí východ spouštěny sériově, zatímco zpráva s oblastí západ se spouští paralelně s uživateli na východě.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope. Host

Výchozím rozsahem zámku je `SingletonScope.Function` , což znamená, že rozsah zámku (cesta k zapůjčení objektu BLOB) je svázán s plně kvalifikovaným názvem funkce. Chcete-li zamknout napříč funkcemi, určete `SingletonScope.Host` a použijte název ID oboru, který je stejný ve všech funkcích, které nechcete spouštět současně. V následujícím příkladu je spuštěna pouze jedna instance nebo v jednom `AddItem` `RemoveItem` okamžiku:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Zobrazení objektů BLOB zapůjčení

Sada WebJobs SDK používá [zapůjčení Azure Blob](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) v rámci pokrývání k implementaci distribuovaného zamykání. Objekty blob zapůjčení používané pomocí typu Singleton najdete v `azure-webjobs-host` kontejneru v `AzureWebJobsStorage` účtu úložiště pod cestou "zámky". Například cesta objektu BLOB zapůjčení pro první `ProcessImage` příklad zobrazená výše může být `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage` . Všechny cesty zahrnují ID JobHost, v tomto případě 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Asynchronní funkce

Informace o způsobu kódování asynchronních funkcí naleznete v [dokumentaci Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokeny zrušení

Informace o tom, jak zpracovávat tokeny zrušení, naleznete v dokumentaci k Azure Functions v tématu o [tokenech zrušení a řádném vypnutí](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Několik instancí

Pokud webová aplikace běží na více instancích, souvislá webová úloha se spustí na každé instanci, bude naslouchat triggerům a volání funkcí. Různé aktivační vazby jsou navržené tak, aby efektivně sdílely společné fungování napříč instancemi, takže škálování na více instancí vám umožní zpracovávat větší zatížení.

Zatímco některé triggery můžou mít za následek dvojí zpracování, fronty a triggery služby Blob Storage automaticky zabraňují funkci ve zpracování zprávy fronty nebo objektu BLOB více než jednou. Další informace najdete v tématu [navrhování pro identický vstup](../azure-functions/functions-idempotent.md) v dokumentaci Azure Functions.

Aktivační událost časovače automaticky zajišťuje, že se spustí jenom jedna instance časovače, takže nebudete mít v daném naplánovaném čase spuštěnou víc než jednu instanci funkce.

Pokud chcete zajistit, aby se jenom jedna instance funkce spouštěla i v případě, že existuje více instancí webové aplikace hostitele, můžete použít [`Singleton`](#singleton-attribute) atribut.

## <a name="filters"></a>Filtry

Filtry funkcí (Preview) poskytují způsob přizpůsobení kanálu spuštění webové úlohy pomocí vlastní logiky. Filtry jsou podobné [filtrům ASP.NET Core](/aspnet/core/mvc/controllers/filters). Můžete je implementovat jako deklarativní atributy, které jsou použity pro vaše funkce nebo třídy. Další informace najdete v tématu [filtry funkcí](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Doporučujeme rozhraní protokolování, které bylo vyvinuto pro ASP.NET. V článku [Začínáme](webjobs-sdk-get-started.md) se dozvíte, jak ho používat. 

### <a name="log-filtering"></a>Filtrování protokolů

Každý protokol vytvořený `ILogger` instancí má přidruženou `Category` a `Level` . [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) je výčet a celočíselný kód označuje relativní důležitost:

|LogLevel    |Kód|
|------------|---|
|Trasování       | 0 |
|Ladění       | 1 |
|Informace | 2 |
|Upozornění     | 3 |
|Chyba       | 4 |
|Kritické    | 5 |
|Žádné        | 6 |

Jednotlivé kategorie můžete filtrovat nezávisle na konkrétním [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) . Můžete například chtít zobrazit všechny protokoly pro zpracování triggerů objektů blob, ale jenom `Error` a vyšší pro všechno ostatní.

#### <a name="version-3x"></a>Verze 3. *x*

Verze 3. *x* sady SDK spoléhá na filtrování integrované do .NET Core. `LogCategories`Třída umožňuje definovat kategorie pro konkrétní funkce, triggery nebo uživatele. Také definuje filtry pro konkrétní stavy hostitele, například `Startup` a `Results` . To vám umožní vyladit výstup protokolování. Pokud se v definovaných kategoriích nenajde žádná shoda, filtr se vrátí k `Default` hodnotě při rozhodování o tom, jestli se má zpráva filtrovat.

`LogCategories` vyžaduje následující příkaz using:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Následující příklad vytvoří filtr, který ve výchozím nastavení filtruje všechny protokoly na `Warning` úrovni. `Function`Kategorie a `results` (ekvivalent `Host.Results` verze 2).*x*) jsou filtrovány na `Error` úrovni. Filtr porovná aktuální kategorii na všechny registrované úrovně v `LogCategories` instanci a zvolí nejdelší shodu. To znamená, že `Debug` úroveň zaregistrovaná pro `Host.Triggers` shody `Host.Triggers.Queue` nebo `Host.Triggers.Blob` . To vám umožní ovládat širší kategorie bez nutnosti přidávat je.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Verze 2. *x*

Ve verzi 2. *x* sady SDK, použijete `LogCategoryFilter` třídu k řízení filtrování. `LogCategoryFilter`Má `Default` vlastnost s počáteční hodnotou `Information` , což znamená, že všechny zprávy na `Information` `Warning` úrovních,, nebo jsou `Error` `Critical` protokolovány, ale všechny zprávy na úrovni `Debug` nebo `Trace` jsou odfiltrovány.

Stejně jako `LogCategories` ve verzi 3.*x*, `CategoryLevels` vlastnost umožňuje zadat úrovně protokolu pro konkrétní kategorie, abyste mohli vyladit výstup protokolování. Pokud se ve slovníku nenajde žádná shoda `CategoryLevels` , filtr se vrátí k `Default` hodnotě při rozhodování o tom, jestli se má zpráva filtrovat.

Následující příklad vytvoří filtr, který ve výchozím nastavení filtruje všechny protokoly na `Warning` úrovni. `Function`Kategorie a `Host.Results` jsou filtrovány na `Error` úrovni. `LogCategoryFilter`Porovná aktuální kategorii se všemi registrovanými `CategoryLevels` a zvolí nejdelší shodu. Takže `Debug` úroveň zaregistrovaná pro `Host.Triggers` bude odpovídat `Host.Triggers.Queue` nebo `Host.Triggers.Blob` . To vám umožní ovládat širší kategorie bez nutnosti přidávat je.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Vlastní telemetrie pro Application Insights

Proces implementace vlastní telemetrie pro [Application Insights](../azure-monitor/app/app-insights-overview.md) závisí na verzi sady SDK. Informace o tom, jak nakonfigurovat Application Insights, najdete v tématu [přidání Application Insights protokolování](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Verze 3. *x*

Protože verze 3. *x* sady WebJobs SDK spoléhá na obecného hostitele .NET Core. vlastní továrna telemetrie už není k dispozici. Můžete ale do kanálu přidat vlastní telemetrii pomocí injektáže závislosti. Příklady v této části vyžadují následující `using` příkazy:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Následující vlastní implementace nástroje [`ITelemetryInitializer`] umožňuje přidat vlastní [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) do výchozího nastavení [`TelemetryConfiguration`] .

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Zavolejte [`ConfigureServices`] v tvůrci a přidejte [`ITelemetryInitializer`] do kanálu vlastní.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Když [`TelemetryConfiguration`] je vytvořen, jsou zahrnuty všechny registrované typy [`ITelemetryInitializer`] . Další informace najdete v tématu [Application Insights API pro vlastní události a metriky](../azure-monitor/app/api-custom-events-metrics.md).

Ve verzi 3. *x*, už nemusíte [`TelemetryClient`] při zastavení hostitele vyprázdnit. Systém injektáže rozhraní .NET Core Dependency vstřiku automaticky odstraní registrovanou `ApplicationInsightsLoggerProvider` , což vyprázdní [`TelemetryClient`] .

#### <a name="version-2x"></a>Verze 2. *x*

Ve verzi 2. *x*, která je [`TelemetryClient`] vytvořena interně poskytovatelem Application Insights pro sadu WebJobs SDK používá [`ServerTelemetryChannel`](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/.publicApi/Microsoft.AI.ServerTelemetryChannel.dll) . Pokud Application Insights koncový bod není k dispozici nebo omezuje příchozí požadavky, tento kanál [uloží požadavky v systému souborů webové aplikace a znovu je odešle](https://apmtips.com/posts/2015-09-03-more-telemetry-channels/).

[`TelemetryClient`]Je vytvořen třídou, která implementuje `ITelemetryClientFactory` . Ve výchozím nastavení je to [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/) .

Chcete-li změnit jakoukoli část kanálu Application Insights, můžete zadat vlastní `ITelemetryClientFactory` a hostitel bude třídu používat k sestavení [`TelemetryClient`] . Například přepsání tohoto kódu `DefaultTelemetryClientFactory` pro úpravu vlastnosti `ServerTelemetryChannel` :

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

`SamplingPercentageEstimatorSettings`Objekt nakonfiguruje [adaptivní vzorkování](../azure-monitor/app/sampling.md). To znamená, že v některých scénářích s vysokým objemem se v aplikacích Application Insights pošle vybraná podmnožina dat telemetrie na server.

Jakmile vytvoříte objekt pro vytváření telemetrie, předáte ho do poskytovatele protokolování Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> Další kroky

V tomto článku jsou uvedené fragmenty kódu, které ukazují, jak zpracovávat běžné scénáře pro práci se sadou WebJobs SDK. Kompletní ukázky najdete v tématu [Azure-WebJobs-SDK-Samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

[ExecutionContext]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemetryInitializer']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
['TelemetryConfiguration']: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostConfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs