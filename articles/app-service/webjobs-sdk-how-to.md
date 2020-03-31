---
title: Použití sady WebJobs SDK
description: Přečtěte si další informace o psaní kódu sady WebJobs SDK. Vytvořte úlohy zpracování na pozadí řízené událostmi, které přistupují k datům ve službách Azure a třetích stran.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: c606f6e60b1c906a0d5c29992287d126aaa37b7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602936"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Jak použít sadu Azure WebJobs SDK k událostmi řízenému zpracování na pozadí

Tento článek obsahuje pokyny, jak pracovat s sadou Azure WebJobs SDK. Pokud chcete začít s WebJobs hned, [najdete v tématu Začínáme s Azure WebJobs SDK pro zpracování na pozadí založené na událostech](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Verze sady WebJobs SDK

To jsou hlavní rozdíly mezi verzí 3. *x* a verze 2. *x* sady WebJobs SDK:

* Verze 3. *x* přidává podporu pro .NET Core.
* Ve verzi 3. *x*, je třeba explicitně nainstalovat rozšíření vazby úložiště vyžadované sadou WebJobs SDK. Ve verzi 2. *x*, storage vazby byly zahrnuty do sady SDK.
* Visual Studio nástroje pro .NET Core (3.* x*) projekty se liší od nástrojů pro rozhraní .NET Framework (2.* x*) projekty. Další informace najdete v [tématu Vývoj a nasazení webových úloh pomocí Visual Studia – Azure App Service](webjobs-dotnet-deploy-vs.md).

Pokud je to možné, jsou uvedeny příklady pro obě verze 3. *x* a verze 2. *x*.

> [!NOTE]
> [Funkce Azure](../azure-functions/functions-overview.md) je postavenna WebJobs SDK a tento článek obsahuje odkazy na azure funkce dokumentace pro některá témata. Všimněte si těchto rozdílů mezi funkcemi a sadou WebJobs SDK:
> * Funkce Azure verze 2. *x* odpovídá sady WebJobs SDK verze 3. *x*a funkce Azure 1. *x* odpovídá sada WebJobs SDK 2. *x*. Úložiště zdrojového kódu používají číslování sady WebJobs SDK.
> * Ukázkový kód pro knihovny tříd Azure Functions C# je jako kód Sady `FunctionName` WebJobs SDK, s výjimkou, že v projektu sady WebJobs SDK nepotřebujete atribut.
> * Některé typy vazeb jsou podporovány pouze ve funkcích, jako je HTTP (Webhooks) a Event Grid (který je založen na PROTOKOLU HTTP).
>
> Další informace naleznete [v tématu Porovnání sady WebJobs SDK a funkcí Azure](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Hostitel webových úloh

Hostitel je runtime kontejner pro funkce.  Naslouchá spouštěčům a volá funkce. Ve verzi 3. *x*, hostitel je `IHost`implementace . Ve verzi 2. *x*, použijete `JobHost` objekt. Vytvoříte instanci hostitele v kódu a napíšete kód pro přizpůsobení jeho chování.

To je klíčový rozdíl mezi přímým použitím sady WebJobs SDK a jeho nepřímým používáním prostřednictvím funkcí Azure. Ve službě Azure Functions služba řídí hostitele a nemůžete přizpůsobit hostitele psaním kódu. Funkce Azure umožňuje přizpůsobit chování hostitele prostřednictvím nastavení v souboru host.json. Tato nastavení jsou řetězce, nikoli kód, a to omezuje druhy přizpůsobení, které můžete provést.

### <a name="host-connection-strings"></a>Připojovací řetězce hostitele

Sada WebJobs SDK hledá připojovací řetězce Azure Storage a Azure Service Bus v souboru local.settings.json při místním spuštění nebo v prostředí webjobové při spuštění v Azure. Ve výchozím nastavení je vyžadováno nastavení připojovacího řetězce úložiště s názvem. `AzureWebJobsStorage`  

Verze 2. *x* sady SDK umožňuje používat vlastní názvy pro tyto připojovací řetězce nebo je uložit jinde. Názvy můžete nastavit v [`JobHostConfiguration`]kódu pomocí , jak je znázorněno zde:

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

Protože verze 3. *x* používá výchozí rozhraní API konfigurace .NET Core, neexistuje žádné rozhraní API pro změnu názvů připojovacích řetězců.

### <a name="host-development-settings"></a>Nastavení vývoje hostitele

Můžete spustit hostitele v režimu vývoje, aby byl místní vývoj efektivnější. Zde jsou některá nastavení, která se změní při spuštění v režimu vývoje:

| Vlastnost | Nastavení vývoje |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`pro maximalizaci výstupu protokolu. |
| `Queues.MaxPollingInterval`  | Nízká hodnota, která zajišťuje okamžité spuštění metod fronty.  |
| `Singleton.ListenerLockPeriod` | 15 sekund na pomoc v rychlém iterativním vývoji. |

Proces povolení režimu vývoje závisí na verzi sady SDK. 

#### <a name="version-3x"></a>Verze 3. *x*

Verze 3. *x* používá standardní ASP.NET základní api. Volání [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metody na [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instanci. Předajte `development`řetězec s názvem , jako v tomto příkladu:

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

Třída `JobHostConfiguration` má `UseDevelopmentSettings` metodu, která umožňuje režim vývoje.  Následující příklad ukazuje, jak používat nastavení vývoje. `config.IsDevelopment` Chcete-li `true` vrátit při spuštění místně, nastavte `AzureWebJobsEnv` proměnnou `Development`místního prostředí s názvem s hodnotou .

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

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Správa souběžných připojení (verze 2.* x*)

Ve verzi 3. *x*, výchozí limit připojení je nekonečný na nekonečná připojení. Pokud z nějakého důvodu je třeba změnit [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) tento limit, můžete použít vlastnost třídy. [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler)

Ve verzi 2. *x*můžete řídit počet souběžných připojení k hostiteli pomocí rozhraní [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. Ve 2. *x*, měli byste tuto hodnotu zvýšit z výchozí hodnoty 2 před spuštěním hostitele WebJobs.

Všechny odchozí požadavky HTTP, které provedete `HttpClient` z `ServicePointManager`funkce pomocí toku přes . Po dosažení hodnoty nastavené `ServicePointManager` v aplikace `DefaultConnectionLimit`začne požadavky před odesláním zařadit do fronty. Předpokládejme, že je `DefaultConnectionLimit` nastavena na 2 a váš kód vytvoří 1 000 požadavků HTTP. Zpočátku jsou do operačního su povoleny pouze dva požadavky. Zbylých 998 je ve frontě, dokud pro ně nebude místo. To znamená, že váš `HttpClient` časový časový odpočitavý časový čas, protože se zdá, že žádost podala, ale žádost nebyla nikdy odeslána os na cílový server. Takže se může zobrazit chování, které se nezdá `HttpClient` smysl: místní trvá 10 sekund k dokončení požadavku, ale vaše služba vrací každý požadavek v 200 ms. 

Výchozí hodnota pro ASP.NET `Int32.MaxValue`aplikace je a to bude pravděpodobně fungovat dobře pro WebJobs spuštěné v základní nebo vyšší plán služby App Service. WebJobs obvykle potřebují vždy na nastavení a to je podporováno pouze základní a vyšší plány služby App Service.

Pokud vaše webová úloha běží v plánu služby Free nebo Shared App Service, je vaše aplikace omezena sandboxem služby App Service, který má aktuálně [limit připojení 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). S neomezeným limitem `ServicePointManager`připojení v aplikaci je pravděpodobnější, že bude dosaženo prahové hodnoty připojení izolovaného prostoru a lokalita bude ukončena. V takovém případě `DefaultConnectionLimit` nastavení na něco nižší, jako je 50 nebo 100, můžete zabránit tomu, aby se to stalo a stále umožňují dostatečnou propustnost.

Nastavení musí být nakonfigurováno před vytvořením všech požadavků HTTP. Z tohoto důvodu by hostitel WebJobs neměl nastavení automaticky upravovat. Mohou existovat požadavky HTTP, ke kterým dojde před spuštěním hostitele, což může vést k neočekávanému chování. Nejlepším přístupem je nastavit hodnotu `Main` okamžitě ve `JobHost`vaší metodě před inicializací , jak je znázorněno zde:

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

Funkce musí být veřejné metody a musí [`NoAutomaticTrigger`](#manual-triggers) mít jeden atribut aktivační události nebo atribut.

### <a name="automatic-triggers"></a>Automatické spouštěče

Automatické aktivační události volají funkci v reakci na událost. Vezměme si tento příklad funkce, která se aktivuje zpráva přidána do úložiště Fronty Azure. Reaguje čtením objektu blob z úložiště objektů Blob Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Atribut `QueueTrigger` říká, že za běhu chcete volat funkci `myqueue-items` vždy, když se ve frontě objeví zpráva fronty. Atribut `Blob` říká, že za běhu chcete použít zprávu fronty ke čtení objektu blob v kontejneru *ukázkových pracovních položek.* Obsah zprávy fronty, předaný funkci v `myQueueItem` parametru, je název objektu blob.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Ruční spouštěče

Chcete-li spustit funkci ručně, použijte `NoAutomaticTrigger` atribut, jak je znázorněno zde:

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

Proces ručního spuštění funkce závisí na verzi sady SDK.

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

Vstupní vazby poskytují deklarativní způsob, jak zpřístupnit data z Azure nebo služeb třetích stran pro váš kód. Výstupní vazby poskytují způsob aktualizace dat. Článek [Začínáme](webjobs-sdk-get-started.md) ukazuje příklad každého z nich.

Můžete použít vrácenou hodnotu metody pro výstupní vazbu použitím atributu na vrácenou hodnotu metody. Podívejte se na příklad [v použití vrácené hodnoty funkce Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Typy vazeb

Proces instalace a správy typů vazeb závisí na tom, zda používáte verzi 3. *x* nebo verze 2. *x* sady SDK. Balíček k instalaci pro konkrétní typ vazby najdete v části "Balíčky" [v referenčním článku azure](#binding-reference-information)functions tohoto typu vazby . Výjimkou je aktivační událost a vazba souborů (pro místní systém souborů), která není podporována funkcemi Azure.

#### <a name="version-3x"></a>Verze 3. *x*

Ve verzi 3. *x*, jsou v `Microsoft.Azure.WebJobs.Extensions.Storage` balíčku zahrnuty vazby úložiště. Volání `AddAzureStorage` metody rozšíření `ConfigureWebJobs` v metodě, jak je znázorněno zde:

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

Chcete-li použít jiné typy aktivační události a vazby, `Add<binding>` nainstalujte balíček NuGet, který je obsahuje, a volejte metodu rozšíření implementovanou v rozšíření. Například pokud chcete použít vazby Azure Cosmos `Microsoft.Azure.WebJobs.Extensions.CosmosDB` DB, nainstalujte a zavolejte `AddCosmosDB`, takto:

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

Chcete-li použít aktivační událost Timer nebo vazby Files, `AddTimers` `AddFiles` které jsou součástí základních služeb, volejte metody nebo rozšíření.

#### <a name="version-2x"></a>Verze 2. *x*

Tyto aktivační události a vazby typy jsou zahrnuty ve verzi 2. *x* `Microsoft.Azure.WebJobs` balení:

* Blob Storage
* Queue Storage
* Úložiště Table

Chcete-li použít jiné typy aktivační události a vazby, `Use<binding>` nainstalujte `JobHostConfiguration` balíček NuGet, který je obsahuje, a volání metody na objektu. Chcete-li například použít aktivační událost `Microsoft.Azure.WebJobs.Extensions` Timer, `UseTimers` nainstalujte a zavolejte metodu, `Main` jak je znázorněno zde:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Chcete-li použít vazbu Soubory, nainstalujte `Microsoft.Azure.WebJobs.Extensions` a zavolejte `UseFiles`.

### <a name="executioncontext"></a>Executioncontext

WebJobs umožňuje vytvořit vazbu na [`ExecutionContext`]. Pomocí této vazby můžete [`ExecutionContext`] přistupovat jako parametr v podpisu funkce. Například následující kód používá objekt kontextu pro přístup k ID vyvolání, které můžete použít ke korelaci všech protokolů vytvořených danou funkcí vyvolání.  

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

Proces pro vazbu [`ExecutionContext`] na závisí na verzi sady SDK.

#### <a name="version-3x"></a>Verze 3. *x*

Volání `AddExecutionContextBinding` metody rozšíření `ConfigureWebJobs` v metodě, jak je znázorněno zde:

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

Balíček `Microsoft.Azure.WebJobs.Extensions` je uvedeno výše také poskytuje speciální typ vazby, které můžete zaregistrovat voláním `UseCore` metody. Tato vazba umožňuje [`ExecutionContext`] definovat parametr v podpisu funkce, který je povolen takto:

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

Můžete nakonfigurovat chování některých aktivačních událostí a vazeb. Proces jejich konfigurace závisí na verzi sady SDK.

* **Verze 3. *x*:** Nastavte `Add<Binding>` konfiguraci při `ConfigureWebJobs`volání metody .
* **Verze 2. *x*:** Nastavte konfiguraci nastavením vlastností v `JobHost`objektu konfigurace, do kterého se předáváte .

Tato nastavení specifická pro vazbu jsou ekvivalentní nastavení v [souboru projektu host.json](../azure-functions/functions-host-json.md) v Azure Functions.

Můžete nakonfigurovat následující vazby:

* [Aktivační událost Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Aktivační událost Centra](#event-hubs-trigger-configuration-version-3x)
* [Trigger služby Queue Storage](#queue-storage-trigger-configuration)
* [Vazba SendGrid](#sendgrid-binding-configuration-version-3x)
* [Aktivační událost sběrnice](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Konfigurace aktivační události Azure CosmosDB (verze 3.* x*)

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

Další podrobnosti najdete v článku [vazby Azure CosmosDB.](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

### <a name="event-hubs-trigger-configuration-version-3x"></a>Konfigurace event hubů (verze 3.* x*)

Tento příklad ukazuje, jak nakonfigurovat aktivační událost Centra událostí:

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

Další podrobnosti najdete v článku [vazby Centra událostí.](../azure-functions/functions-bindings-event-hubs-output.md#hostjson-settings)

### <a name="queue-storage-trigger-configuration"></a>Konfigurace aktivační události úložiště fronty

Tyto příklady ukazují, jak nakonfigurovat aktivační událost úložiště fronty:

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

Další podrobnosti naleznete v článku [vazby úložiště fronty.](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)

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

Další podrobnosti naleznete v [odkazu host.json v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Konfigurace vazby SendGrid (verze 3.* x*)

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

Další podrobnosti naleznete v článku [vazby SendGrid.](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)

### <a name="service-bus-trigger-configuration-version-3x"></a>Konfigurace aktivační události service bus (verze 3.* x*)

Tento příklad ukazuje, jak nakonfigurovat aktivační událost služby Service Bus:

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

Další podrobnosti naleznete v článku [vazby service bus.](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings)

### <a name="configuration-for-other-bindings"></a>Konfigurace pro ostatní vazby

Některé typy aktivačních událostí a vazeb definují vlastní typy konfigurace. Aktivační událost Soubor například umožňuje určit kořenovou cestu ke sledování, jako v těchto příkladech:

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

## <a name="binding-expressions"></a>Vazby výrazy

V parametrech konstruktoru atributů můžete použít výrazy, které se přemisí na hodnoty z různých zdrojů. Například v následujícím kódu cesta pro `BlobTrigger` atribut vytvoří `filename`výraz s názvem . Při použití pro výstupní `filename` vazby, překládá na název aktivační objekt u objektu blob.

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

Další informace o výrazy vazby naleznete v [tématu vazby výrazy a vzory](../azure-functions/functions-bindings-expressions-patterns.md) v dokumentaci funkce Azure.

### <a name="custom-binding-expressions"></a>Vlastní výrazy vazby

Někdy chcete zadat název fronty, název objektu blob nebo kontejner nebo název tabulky v kódu, nikoli pevný kód. Můžete například zadat název fronty pro `QueueTrigger` atribut v konfiguračním souboru nebo proměnné prostředí.

Můžete to udělat předáním `NameResolver` objektu `JobHostConfiguration` do objektu. Zástupné symboly zahrnete do parametrů konstruktoru atributů aktivační události nebo vazby a váš `NameResolver` kód poskytuje skutečné hodnoty, které mají být použity místo těchto zástupných symbolů. Zástupné symboly identifikujete tak, že je obklopíte procenty (%) značky, jak je znázorněno zde:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Tento kód umožňuje použít frontu pojmenovanou `logqueuetest` v `logqueueprod` testovacím prostředí a pojmenovanou v produkčním prostředí. Místo pevně zakódovaného názvu fronty zadáte název položky `appSettings` v kolekci.

Pokud nezadáte `NameResolver` vlastní, projeví se výchozí nastavení. Výchozí získá hodnoty z nastavení aplikace nebo proměnné prostředí.

Název `NameResolver` fronty z vás `appSettings`získá název fronty, jak je znázorněno zde:

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

Překládání nakonfigurujete pomocí vkládání závislostí. Tyto vzorky `using` vyžadují následující prohlášení:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Překládání přidáte voláním [`ConfigureServices`] metody [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)rozšíření na , jako v tomto příkladu:

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

Předejte `NameResolver` třídu `JobHost` objektu, jak je znázorněno zde:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions `INameResolver` implementuje získat hodnoty z nastavení aplikace, jak je znázorněno v příkladu. Při použití sady WebJobs SDK přímo, můžete napsat vlastní implementaci, která získá zástupný náhradní hodnoty z libovolného zdroje, který dáváte přednost.

## <a name="binding-at-runtime"></a>Vazba za běhu

Pokud potřebujete provést nějakou práci ve funkci před použitím `Queue` `Blob`atributu `Table`vazby, `IBinder` jako je , , nebo , můžete použít rozhraní.

Následující příklad přebírá zprávu vstupní fronty a vytvoří novou zprávu se stejným obsahem ve výstupní frontě. Název výstupní fronty je nastaven kódem v těle funkce.

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

Další informace najdete [v tématu vazby za běhu](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) v dokumentaci k funkcím Azure.

## <a name="binding-reference-information"></a>Referenční informace o vazbě

Dokumentace k funkcím Azure poskytuje referenční informace o jednotlivých typech vazby. Následující informace naleznete v každém článku odkazu na vazbu. (Tento příklad je založen na frontě úložiště.)

* [Balíčky](../azure-functions/functions-bindings-storage-queue.md). Balíček, který je třeba nainstalovat, aby zahrnoval podporu pro vazbu v projektu Sady WebJobs SDK.
* [Příklady](../azure-functions/functions-bindings-storage-queue-trigger.md). Ukázky kódu. Příklad knihovny tříd jazyka C# se vztahuje na webjobs SDK. Jen vynechat `FunctionName` atribut.
* [Atributy](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). Atributy, které mají být pro typ vazby.
* [Konfigurace](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Vysvětlení vlastností atributů a parametrů konstruktoru.
* [Využití](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Typy, které můžete vytvořit vazbu a informace o tom, jak vazba funguje. Například: algoritmus dotazování, zpracování fronty poison.
  
Seznam odkazů na vazby najdete v tématu "Podporované vazby" v [aktivační události a vazby](../azure-functions/functions-triggers-bindings.md#supported-bindings) článku pro funkce Azure. V tomto seznamu jsou vazby HTTP, Webhooks a Event Grid podporovány jenom funkcemi Azure, nikoli sadou WebJobs SDK.

## <a name="disable-attribute"></a>Zakázat atribut 

Atribut [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) umožňuje řídit, zda lze spustit funkci. 

V následujícím příkladu pokud `Disable_TestJob` má nastavení `1` aplikace `True` hodnotu nebo (malá a velká písmena), funkce se nespustí. V takovém případě runtime vytvoří zprávu protokolu *Funkce Functions.TestJob je zakázána*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Když změníte hodnoty nastavení aplikace na webu Azure Portal, webová úloha se restartuje a vyzvedne nové nastavení.

Atribut lze deklarovat na úrovni parametru, metody nebo třídy. Název nastavení může také obsahovat výrazy vazby.

## <a name="timeout-attribute"></a>Atribut časového opojení

Atribut [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) způsobí, že funkce bude zrušena, pokud není dokončena v zadaném čase. V následujícím příkladu by funkce spustit pro jeden den bez timeout atribut. Časový čas způsobí, že funkce bude zrušena po 15 sekundách.

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

Atribut Timeout můžete použít na úrovni třídy nebo metody a pomocí `JobHostConfiguration.FunctionTimeout`aplikace můžete určit globální časový čas . Časové výčasové výtky na úrovni třídy nebo metody přepíší globální časové přeběhy.

## <a name="singleton-attribute"></a>Atribut Singleton

Atribut [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) zajišťuje, že se spustí pouze jedna instance funkce, i když existuje více instancí hostitelské webové aplikace. Je to pomocí [distribuované hořajení](#viewing-lease-blobs).

V tomto příkladu je spuštěna pouze jedna instance `ProcessImage` funkce v daném okamžiku:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Některé aktivační události mají integrovanou podporu pro správu souběžnosti:

* **Fronttrigger**. `JobHostConfiguration.Queues.BatchSize` Nastaveno `1`na .
* **ServiceBusTrigger**. `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` Nastaveno `1`na .
* **FileTrigger**. `FileProcessor.MaxDegreeOfParallelism` Nastaveno `1`na .

Tato nastavení můžete použít k zajištění, že vaše funkce běží jako singleton na jedné instanci. Chcete-li zajistit, že je spuštěna pouze jedna instance funkce, když se webová aplikace škáluje na`[Singleton(Mode = SingletonMode.Listener)]`více instancí, použijte na funkci zámek singleton na úrovni posluchače ( ). Naslouchací proces zámky jsou získány při spuštění JobHost. Pokud tři škálované instance začínají současně, pouze jedna z instancí získá zámek a spustí se pouze jeden naslouchací proces.

### <a name="scope-values"></a>Hodnoty oboru

*Výraz/hodnotu oboru* můžete zadat na singletonu. Výraz/hodnota zajišťuje, že všechny spuštění funkce v určitém oboru budou serializovány. Implementace podrobnější zamykání tímto způsobem může umožnit určitou úroveň paralelismu pro vaši funkci při serializaci jiných vyvolání podle vašich požadavků. Například v následujícím kódu se výraz oboru `Region` váže na hodnotu příchozí zprávy. Pokud fronta obsahuje tři zprávy v oblastech východ, východ a západ, zprávy, které mají oblast východ jsou spuštěny sériově, zatímco zpráva s region West je spuštěna paralelně s těmi na východ.

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

### <a name="singletonscopehost"></a>SingletonScope.Host

Výchozí obor zámku `SingletonScope.Function`je , což znamená, že obor zámku (cesta zapůjčení objektu blob) je svázaný s plně kvalifikovanýnázev funkce. Chcete-li zamknout `SingletonScope.Host` mezi funkcemi, zadejte a použijte název ID oboru, který je stejný ve všech funkcích, které nechcete spustit současně. V následujícím příkladu pouze `AddItem` jedna `RemoveItem` instance nebo spustí současně:

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

Sada WebJobs SDK používá [zapůjčení objektů blob Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) pod kryty k implementaci distribuovaného uzamčení. Objekty BLOB zapůjčení používané Singleton `azure-webjobs-host` lze nalézt `AzureWebJobsStorage` v kontejneru v účtu úložiště pod cestou "zámky". Například cesta k blob zapůjčení pro `ProcessImage` první `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`příklad zobrazendříve může být . Všechny cesty zahrnují JobHost ID, v tomto případě 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Asynchronní funkce

Informace o tom, jak kódovat asynchronní funkce, najdete v [dokumentaci k funkcím Azure](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokeny zrušení

Informace o tom, jak zpracovat tokeny zrušení, najdete v dokumentaci k funkcím Azure na [tokeny zrušení a řádné vypnutí](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Několik instancí

Pokud vaše webová aplikace běží na více instancích, běží souvislá webová úloha na každé instanci, naslouchání aktivačním událostem a volajícím funkcím. Různé aktivační události vazby jsou navrženy tak, aby efektivně sdílet práci ve spolupráci napříč instancemi, takže škálování na více instancí umožňuje zpracovat větší zatížení.

Zatímco některé aktivační události může mít za následek dvojité zpracování, fronty a aktivační události úložiště objektů blob automaticky zabránit funkci ze zpracování zprávy fronty nebo objekt blob více než jednou. Další informace najdete v [tématu navrhování identické vstup](../azure-functions/functions-idempotent.md) v dokumentaci Funkce Azure.

Aktivační událost časovače automaticky zajišťuje, že se spustí pouze jedna instance časovače, takže nezískáte více než jednu instanci funkce spuštěnou v daném naplánovaném čase.

Pokud chcete zajistit, že pouze jedna instance funkce spustí i v případě, že existuje [`Singleton`](#singleton-attribute) více instancí hostitelské webové aplikace, můžete použít atribut.

## <a name="filters"></a>Filtry

Filtry funkcí (náhled) poskytují způsob, jak přizpůsobit kanál spuštění webjobs s vlastní logikou. Filtry jsou podobné [ASP.NET filtry Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Můžete je implementovat jako deklarativní atributy, které jsou použity pro vaše funkce nebo třídy. Další informace naleznete v tématu [Filtry funkcí](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Doporučujeme rámec protokolování, který byl vyvinut pro ASP.NET. Článek [Začínáme](webjobs-sdk-get-started.md) ukazuje, jak ho používat. 

### <a name="log-filtering"></a>Filtrování protokolů

Každý protokol vytvořený `ILogger` instancí `Category` `Level`má přidružený a . [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)je výčt a etičervený kód označuje relativní důležitost:

|LogLevel    |kód|
|------------|---|
|Trasování       | 0 |
|Ladění       | 1 |
|Informace | 2 |
|Upozornění     | 3 |
|Chyba       | 4 |
|Kritická    | 5 |
|Žádný        | 6 |

Můžete nezávisle filtrovat každou kategorii [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)na konkrétní . Například můžete chtít zobrazit všechny protokoly pro zpracování `Error` aktivační události objektu blob, ale pouze a vyšší pro všechno ostatní.

#### <a name="version-3x"></a>Verze 3. *x*

Verze 3. *x* sady SDK závisí na filtrování integrovaném do jádra .NET Core. Třída `LogCategories` umožňuje definovat kategorie pro konkrétní funkce, aktivační události nebo uživatele. Definuje také filtry pro konkrétní `Startup` hostitelské `Results`stavy, jako a . To umožňuje doladit výstup protokolování. Pokud v definovaných kategoriích není nalezena žádná `Default` shoda, filtr se při rozhodování, zda má zpráva filtrovat, vrátí zpět na hodnotu.

`LogCategories`vyžaduje následující using prohlášení:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Následující příklad vytvoří filtr, který ve výchozím nastavení filtruje všechny protokoly na `Warning` úrovni. A `Function` `results` kategorie (ekvivalentní `Host.Results` ve verzi 2.* x*) jsou filtrovány na `Error` úrovni. Filtr porovná aktuální kategorii se všemi registrovanými úrovněmi v `LogCategories` instanci a vybere nejdelší shodu. To znamená, `Debug` že `Host.Triggers` úroveň `Host.Triggers.Queue` `Host.Triggers.Blob`registrovaná pro zápasy nebo . To umožňuje ovládat širší kategorie bez nutnosti přidávat každou z nich.

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

Ve verzi 2. *x* sady SDK, použijte `LogCategoryFilter` třídu k řízení filtrování. Má `LogCategoryFilter` `Default` `Information`vlastnost s počáteční hodnotou , což znamená, že všechny zprávy na úrovni `Information`, `Warning`, `Trace` `Error`nebo `Critical` jsou protokolovány, ale všechny zprávy na úrovních `Debug` nebo jsou odfiltrovány.

Stejně `LogCategories` jako ve verzi 3. *x*, `CategoryLevels` vlastnost umožňuje zadat úrovně protokolu pro určité kategorie, takže můžete doladit výstup protokolování. Pokud není nalezena `CategoryLevels` žádná shoda ve slovníku, `Default` filtr přejde zpět na hodnotu při rozhodování, zda chcete filtrovat zprávu.

Následující příklad vytvoří filtr, který ve výchozím nastavení `Warning` filtruje všechny protokoly na úrovni. Kategorie `Function` `Host.Results` a jsou filtrovány `Error` na úrovni. Porovná `LogCategoryFilter` aktuální kategorii se všemi `CategoryLevels` registrovanými a vybere nejdelší zápas. Takže `Debug` úroveň registrovaná pro `Host.Triggers` bude odpovídat `Host.Triggers.Queue` nebo `Host.Triggers.Blob`. To umožňuje ovládat širší kategorie bez nutnosti přidávat každou z nich.

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

### <a name="custom-telemetry-for-application-insights"></a>Vlastní telemetrie pro přehledy aplikací

Proces implementace vlastní telemetrie pro [Application Insights](../azure-monitor/app/app-insights-overview.md) závisí na verzi sady SDK. Informace o konfiguraci přehledů aplikací najdete v [tématu Přidání protokolování přehledů aplikací](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Verze 3. *x*

Protože verze 3. *x* sady WebJobs SDK závisí na obecném hostiteli .NET Core, vlastní telemetrická továrna již není k dispozici. Ale můžete přidat vlastní telemetrie do kanálu pomocí vkládání závislostí. Příklady v této části `using` vyžadují následující příkazy:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Následující vlastní implementace [`ITelemetryInitializer`] umožňuje přidat vlastní [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) výchozí [`TelemetryConfiguration`].

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Zavolejte [`ConfigureServices`] tvůrce a přidejte své vlastní [`ITelemetryInitializer`] do kanálu.

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

Při [`TelemetryConfiguration`] sestavení jsou zahrnuty všechny [`ITelemetryInitializer`] registrované typy. Další informace najdete v [tématu Application Insights API pro vlastní události a metriky](../azure-monitor/app/api-custom-events-metrics.md).

Ve verzi 3. *x*, již není třeba [`TelemetryClient`] vyprázdnit, když se hostitel zastaví. Systém vkládání závislostí .NET Core automaticky `ApplicationInsightsLoggerProvider`vykládá registrované [`TelemetryClient`], který vyprázdní .

#### <a name="version-2x"></a>Verze 2. *x*

Ve verzi 2. *x*, [`TelemetryClient`] které je vytvořeno interně poskytovatelem Application Insights [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)pro webjobs SDK používá . Pokud koncový bod Application Insights není k dispozici nebo omezení příchozích požadavků, tento kanál [uloží požadavky v systému souborů webové aplikace a znovu je odešle později](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

Je [`TelemetryClient`] vytvořen třídou, která `ITelemetryClientFactory`implementuje . Ve výchozím nastavení [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)se jedná o soubor .

Pokud chcete upravit libovolnou část kanálu Application Insights, `ITelemetryClientFactory`můžete zadat vlastní a hostitel použije [`TelemetryClient`]vaši třídu k vytvoření . Tento kód například `DefaultTelemetryClientFactory` přepíše upravit `ServerTelemetryChannel`vlastnost :

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

Objekt `SamplingPercentageEstimatorSettings` konfiguruje [adaptivní vzorkování](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). To znamená, že v některých scénářích s velkým objemem aplikace Insights odešle vybranou podmnožinu telemetrických dat na server.

Po vytvoření továrny telemetrie ji předáte poskytovateli protokolování Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a>Další kroky

Tento článek obsahuje fragmenty kódu, které ukazují, jak zpracovat běžné scénáře pro práci s sadou WebJobs SDK. Úplné ukázky naleznete [v tématu azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

['ExecutionContext']: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
["TelemetrieClient"]: /dotnet/api/microsoft.applicationinsights.telemetryclient
['ConfigureServices']: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
["ITelemetrieInitializer"]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
["Telemetrická konfigurace"]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostKonfigurace']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
