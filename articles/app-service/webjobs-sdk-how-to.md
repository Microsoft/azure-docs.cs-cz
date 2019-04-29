---
title: Použití sady SDK pro WebJobs – Azure
description: Další informace o tom, jak psát kód pro sadu WebJobs SDK. Vytvoření založený na událostech pozadí úlohy zpracování, které přístup k datům v Azure služby a služby třetích stran.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 38d8bdfcba48d2080b434ebec192b41f3663ae6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831788"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Použití sady Azure WebJobs SDK pro zpracování na pozadí založený na událostech

Tento článek obsahuje pokyny o tom, jak pracovat se sadou Azure WebJobs SDK. Pomocí WebJobs rovnou začít, najdete v článku [Začínáme se sadou Azure WebJobs SDK pro zpracování na pozadí založený na událostech](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Verze sady WebJobs SDK

Toto jsou hlavní rozdíly mezi verze 3. *x* a verze 2. *x* sady WebJobs SDK:

* Verze 3. *x* přidává podporu pro .NET Core.
* Ve verzi 3. *x*, je potřeba explicitně nainstalovat rozšíření vazby úložiště vyžadované sada WebJobs SDK. Ve verzi 2. *x*, vazby úložiště byly zahrnuty v sadě SDK.
* Nástroje pro .NET Core sady Visual Studio (3. *x*) projektů se liší od nástroje pro rozhraní .NET Framework (2. *x*) projekty. Další informace najdete v tématu [vývoj a nasazení WebJobs pomocí sady Visual Studio – služby Azure App Service](webjobs-dotnet-deploy-vs.md).

Pokud je to možné, jsou k dispozici příklady pro obě verze 3. *x* a verze 2. *x*.

> [!NOTE]
> [Služba Azure Functions](../azure-functions/functions-overview.md) je postavená na sadě WebJobs SDK, a tento článek obsahuje odkazy na dokumentaci ke službě Azure Functions pro některá témata. Všimněte si těchto rozdílů mezi službami Functions a WebJobs SDK:
> * Azure Functions verze 2. *x* odpovídá sada WebJobs SDK verze 3. *x*a Azure Functions 1. *x* odpovídá WebJobs SDK 2. *x*. Úložišť zdrojového kódu pomocí sady SDK pro WebJobs číslování.
> * Ukázkový kód pro službu Azure Functions C# knihovny tříd je stejně jako kód sada WebJobs SDK, s tím rozdílem, není nutné `FunctionName` atribut v projektu sady WebJobs SDK.
> * Některé typy vazeb jsou podporovány pouze v funkce, jako jsou HTTP (Webhooky) a služby Event Grid (která je založená na protokolu HTTP).
>
> Další informace najdete v tématu [porovnání sadou WebJobs SDK a Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>WebJobs hostitele

Hostitel je kontejner modulu runtime pro funkce.  Naslouchá aktivačních událostí a volání funkce. Ve verzi 3. *x*, hostitel je implementace `IHost`. Ve verzi 2. *x*, je použít `JobHost` objektu. Vytvoření instance hostitele ve vašem kódu a psaní kódu můžete přizpůsobit její chování.

Toto je klíčovým rozdílem mezi přímo pomocí sady WebJobs SDK a používat jej nepřímo prostřednictvím Azure Functions. Ve službě Azure Functions služba ovládací prvky hostitele a hostitele nelze přizpůsobit napsáním kódu. Azure Functions umožňuje přizpůsobit chování hostitele pomocí nastavení v souboru host.json. Tato nastavení jsou řetězce, nikoli kódu, a to omezuje typy vlastního nastavení, které vám pomůžou.

### <a name="host-connection-strings"></a>Hostitel připojovacích řetězců

Sada WebJobs SDK hledá služby Azure Storage a Azure Service Bus připojovací řetězce v souboru local.settings.json při spuštění místně nebo v prostředí webové úlohy při spuštění v Azure. Ve výchozím nastavení, řetězec připojení úložiště nastavení s názvem `AzureWebJobsStorage` je povinný.  

Verze 2. *x* sady SDK můžete použít vlastní názvy pro tyto řetězce připojení nebo je uložíte jinam. Můžete nastavit názvy v kódu pomocí [ `JobHostConfiguration` ], jak je znázorněno zde:

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

Protože verze 3. *x* používá výchozí konfiguraci .NET Core API, neexistuje žádné rozhraní API, chcete-li změnit připojovací řetězec názvy.

### <a name="host-development-settings"></a>Hostitel vývojové nastavení

Hostitele můžete spustit v režimu pro vývoj pro efektivnější místní vývoj. Tady jsou některé z nastavení, která se mění při spuštění v režimu pro vývoj:

| Vlastnost | Vývojové nastavení |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` pro maximalizaci výstupu protokolu. |
| `Queues.MaxPollingInterval`  | Nízká hodnota zajistit fronty metody se aktivují okamžitě.  |
| `Singleton.ListenerLockPeriod` | 15 sekund pro rychlý iterativní vývoj. |

Postup pro povolení režimu pro vývoj závisí na verzi sady SDK. 

#### <a name="version-3x"></a>Verze 3. *x*

Verze 3. *x* používá standardní rozhraní API ASP.NET Core. Volání [ `UseEnvironment` ](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metodu [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instance. Předat řetězec s názvem `development`, jako v tomto příkladu:

```cs
static void Main()
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
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Verze 2. *x*

`JobHostConfiguration` Třída nemá `UseDevelopmentSettings` metodu, která umožňuje vývoj režimu.  Následující příklad ukazuje způsob použití vývojového nastavení. Chcete-li `config.IsDevelopment` vrátit `true` při spuštění místně, nastavte proměnnou prostředí místní `AzureWebJobsEnv` s hodnotou `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Správa souběžných připojení (verze 2. *x*)

Ve verzi 3. *x*, limitu připojení výchozí hodnota je nekonečná připojení. Pokud z nějakého důvodu potřebujete tento limit změnit, můžete použít [ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) vlastnost [ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler) třídy.

Ve verzi 2. *x*, řídí počet souběžných připojení k hostiteli s použitím [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) rozhraní API. Ve verzi 2. *x*, měli byste zvýšit tuto hodnotu z výchozí hodnoty 2 před spuštěním hostitele WebJobs.

Všechny odchozí požadavky HTTP, které jste provedli z funkce s použitím `HttpClient` procházet skrz `ServicePointManager`. Po dosažení hodnotu nastavenou v `DefaultConnectionLimit`, `ServicePointManager` spustí zařazení do fronty žádostí před jejich odesláním. Předpokládejme, že vaše `DefaultConnectionLimit` je nastavena na 2 a váš kód je 1 000 požadavků HTTP. Zpočátku je povoleno pouze dvě požadavků prostřednictvím do operačního systému. Další 998 se zařadí do fronty. dokud je volného místa pro ně. To znamená, že vaše `HttpClient` může být vypršení časového limitu, protože zdá se, že jste provedli požadavek, ale žádost byla odeslána nikdy operačního systému na cílový server. Může se zobrazit chování, které vypadá, že nepodporuje dávat smysl: místní `HttpClient` trvá dokončení požadavku, 10 sekund, ale vaše služba vrací každého požadavku v 200 ms. 

Výchozí hodnota pro aplikace ASP.NET je `Int32.MaxValue`, a to je pravděpodobně fungovat i pro webové úlohy spuštěné v Basic nebo vyšší. plán služby App Service. WebJobs většinou potřebují nastavení AlwaysOn a, který je podporován pouze základní a vyšší plány služby App Service.

Pokud vaše webová úloha běží v Free nebo sdílený plán služby App Service, vaše aplikace je omezen sandboxu služby App Service, která má v současné době [limitu připojení 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Limit nevázaného připojení je v `ServicePointManager`, je pravděpodobnější, že prahová hodnota připojení izolovaného prostoru bude dostupný a webu se vypne. V takovém případě nastavení `DefaultConnectionLimit` na něco nižší, jako je 50 nebo 100, tomu zabránili a stále umožňují dostatečnou propustnost.

Nastavení musí být nakonfigurované dřív, než všechny požadavky HTTP. Z tohoto důvodu nesmí hostitele webových úloh upravit nastavení automaticky. Je možné požadavky HTTP, ke kterým dochází před spuštěním hostitele, které by mohly vést k neočekávanému chování. Nejlepším řešením je nastavit hodnotu okamžitě ve vaší `Main` metoda před inicializací `JobHost`, jak je znázorněno zde:

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

Funkce musí být veřejné metody a musí obsahovat jeden atribut aktivační události nebo [ `NoAutomaticTrigger` ](#manual-triggers) atribut.

### <a name="automatic-triggers"></a>Automatických aktivačních procedur

Automatických aktivačních procedur volání funkce v reakci na událost. Podívejte se například funkce, která se aktivuje do úložiště Azure Queue storage přidá zpráva. Odpovědí ve čtení objektu blob z úložiště objektů Blob v Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger` Atribut oznamuje modul runtime pro volání funkce pokaždé, když se zobrazí zpráva fronty v `myqueue-items` fronty. `Blob` Atribut oznamuje modul runtime pro použití fronty zpráv ke čtení objektů blob v *ukázka workitems* kontejneru. Obsah zprávy fronty předaného do funkce `myQueueItem` parametr, je název objektu blob.


### <a name="manual-triggers"></a>Ruční aktivační události

Chcete-li aktivovat funkce ručně, použijte `NoAutomaticTrigger` atributu, jak je znázorněno zde:

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

Proces pro ruční aktivací funkce závisí na verzi sady SDK.

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

Vstupní vazby poskytují deklarativní způsob zpřístupnění dat z Azure nebo služby třetích stran do vašeho kódu. Výstupní vazby poskytují způsob, jak aktualizovat data. [Začínáme](webjobs-sdk-get-started.md) článek ukazuje příklad každého.

Návratová hodnota metody můžete použít pro výstupní vazbu použitím atribut návratovou hodnotu metody. Podívejte se na příklad v [pomocí funkce Azure Functions vrátí hodnotu](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Vazby typů

Proces pro instalaci a správu typů vazeb závisí na, jestli používáte verzi 3. *x* nebo verze 2. *x* sady SDK. Můžete najít balíček k instalaci pro typ konkrétní vazbu v části "Packages" Tento typ vazby Azure Functions [článku](#binding-reference-information). Výjimkou je aktivační událost soubory a vazbu (pro místního systému souborů), což není podporováno službou Azure Functions.

#### <a name="version-3x"></a>Verze 3. *x*

Ve verzi 3. *x*, vazby úložiště jsou součástí `Microsoft.Azure.WebJobs.Extensions.Storage` balíčku. Volání `AddAzureStorage` metody rozšíření v `ConfigureWebJobs` způsob, jak je znázorněno zde:

```cs
static void Main()
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
        host.Run();
    }
}
```

Použít další aktivační události a typy vazeb, nainstalujte balíček NuGet, který je obsahuje a volat `Add<binding>` rozšiřující metoda implementována v rozšíření. Například pokud chcete použít vazby Azure Cosmos DB, nainstalujte `Microsoft.Azure.WebJobs.Extensions.CosmosDB` a volat `AddCosmosDB`, tímto způsobem:

```cs
static void Main()
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
        host.Run();
    }
}
```

Pomocí aktivační událost časovače nebo soubory vazby, které jsou součástí základní služby, zavolejte `AddTimers` nebo `AddFiles` rozšiřující metody, v uvedeném pořadí.

#### <a name="version-2x"></a>Verze 2. *x*

Tyto typy aktivační události a vazby jsou zahrnuty ve verzi 2. *x* z `Microsoft.Azure.WebJobs` balíčku:

* Blob Storage
* Queue Storage
* Úložiště Table

Použít další aktivační události a typy vazeb, nainstalujte balíček NuGet, který je obsahuje a volat `Use<binding>` metodu na `JobHostConfiguration` objektu. Například pokud chcete použít aktivační událost časovače, nainstalujte `Microsoft.Azure.WebJobs.Extensions` a volat `UseTimers` v `Main` způsob, jak je znázorněno zde:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

použít soubory vazby, nainstalujte `Microsoft.Azure.WebJobs.Extensions` a volat `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

WebJobs umožňuje svázat [ `ExecutionContext` ]. S touto vazbou se dostanete [ `ExecutionContext` ] jako parametr v podpisu funkce. Například následující kód používá objekt kontextu pro přístup k ID vyvolání, které můžete použít ke korelaci všechny protokoly vytvořené metodou volání dané funkce.  

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

Proces pro vazbu [ `ExecutionContext` ] závisí na vaší verzi sady SDK.

#### <a name="version-3x"></a>Verze 3. *x*

Volání `AddExecutionContextBinding` metody rozšíření v `ConfigureWebJobs` způsob, jak je znázorněno zde:

```cs
static void Main()
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
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Verze 2. *x*

`Microsoft.Azure.WebJobs.Extensions` Balíčku již bylo zmíněno dříve také poskytuje typ speciální vazby, která můžete zaregistrovat pomocí volání `UseCore` metody. Tato vazba umožňuje definovat [ `ExecutionContext` ] parametrů v signatuře funkce, která je povolena takto:

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

Můžete provádět konfiguraci chování některé triggerů a vazeb. Postup pro jejich konfiguraci závisí na verzi sady SDK.

* **Verze 3. *x*:** Konfigurace sady při `Add<Binding>` metoda je volána `ConfigureWebJobs`.
* **Verze 2. *x*:** Konfigurace sady nastavením vlastností v objekt konfigurace, který můžete předat do `JobHost`.

Tato nastavení specifické pro vazby jsou ekvivalentní nastavení v [soubor projektu host.json](../azure-functions/functions-host-json.md) ve službě Azure Functions.

Můžete nakonfigurovat následující vazby:

* [Aktivační událost Azure cosmos DB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Aktivace služby Event Hubs](#event-hubs-trigger-configuration-version-3x)
* Aktivační událost fronty úložiště
* [Vazby služby SendGrid](#sendgrid-binding-configuration-version-3x)
* [Aktivační události služby Service Bus](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Konfigurace aktivační událost Azure cosmos DB (verze 3. *x*)

Tento příklad ukazuje postup při konfiguraci triggeru služby Azure Cosmos DB:

```cs
static void Main()
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

        host.Run();
    }
}
```

Další podrobnosti najdete v tématu [vazby služby Azure cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) článku.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Konfigurace aktivace služby Event Hubs (verze 3. *x*)

Tento příklad ukazuje postup při konfiguraci triggeru služby Event Hubs:

```cs
static void Main()
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

        host.Run();
    }
}
```

Další podrobnosti najdete v tématu [vazby služby Event Hubs](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) článku.

### <a name="queue-storage-trigger-configuration"></a>Konfigurace aktivační událost fronty úložiště

Tyto příklady ukazují, jak nakonfigurovat aktivační událost fronty úložiště:

#### <a name="version-3x"></a>Verze 3. *x*

```cs
static void Main()
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

        host.Run();
    }
}
```

Další podrobnosti najdete v tématu [fronty úložiště vazby](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) článku.

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

Další podrobnosti najdete v tématu [referenční materiály k host.json v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Konfigurace vazby služby SendGrid (verze 3. *x*)

Tento příklad ukazuje, jak nakonfigurovat SendGrid výstupní vazby:

```cs
static void Main()
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

        host.Run();
    }
}
```

Další podrobnosti najdete v tématu [vazby služby SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) článku.

### <a name="service-bus-trigger-configuration-version-3x"></a>Konfigurace aktivační události služby Service Bus (verze 3. *x*)

Tento příklad ukazuje postup při konfiguraci aktivační události služby Service Bus:

```cs
static void Main()
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

        host.Run();
    }
}
```

Další podrobnosti najdete v tématu [vazby služby Service Bus](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) článku.

### <a name="configuration-for-other-bindings"></a>Konfigurace u jiných vazeb

Některé typy aktivační události a vazby definovat jejich vlastní typy vlastní konfigurace. Například aktivační událost soubor umožňuje určit kořenovou cestou za účelem monitorování, jako v těchto příkladech:

#### <a name="version-3x"></a>Verze 3. *x*

```cs
static void Main()
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

        host.Run();
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

V parametrech konstruktoru atributu můžete použít výrazy, které odkazují na hodnoty z různých zdrojů. Například v následujícím kódu, cesty pro `BlobTrigger` atribut vytváří výraz s názvem `filename`. Když se použije pro výstupní vazbu `filename` přeloží název spouštěcí objekt blob.

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

Další informace o výrazech vazeb, naleznete v tématu [vazby výrazů a vzory](../azure-functions/functions-bindings-expressions-patterns.md) v dokumentaci ke službě Azure Functions.

### <a name="custom-binding-expressions"></a>Výrazy vlastní vazby

Někdy budete chtít zadat název fronty, název objektu blob nebo kontejner nebo název tabulky v kódu namísto pevně kódováno pomocí ho. Například můžete chtít určit název fronty `QueueTrigger` atribut v konfigurační soubor nebo prostředí proměnnou.

Můžete to udělat pomocí předání `NameResolver` objektu v `JobHostConfiguration` objektu. Obsahují zástupné symboly v aktivační události nebo parametry konstruktoru atributu vazby a vaše `NameResolver` kód obsahuje skutečné hodnoty, které mají být zastoupen tyto zástupné symboly. Identifikujte zástupné symboly obklopením je (%) příznaky, jak je znázorněno zde:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Tento kód vám umožní použít frontu s názvem `logqueuetest` v testovacím prostředí a jednu s názvem `logqueueprod` v produkčním prostředí. Místo názvu fronty pevně zakódované, zadejte název položky v `appSettings` kolekce.

Je výchozí `NameResolver` , která má vliv, pokud nezadáte vlastní. Výchozí hodnota získá hodnoty z nastavení aplikace nebo proměnné prostředí.

Vaše `NameResolver` třídy získá název fronty z `appSettings`, jak je znázorněno zde:

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

Překladač nakonfigurujete pomocí vkládání závislostí. Tyto ukázky vyžadují následující `using` – příkaz:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Přidat překladač voláním [ `ConfigureServices` ] rozšiřující metody na [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder), jako v tomto příkladu:

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

Předání vašeho `NameResolver` třídu v `JobHost` objektu, jak je znázorněno zde:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure implementuje funkce `INameResolver` získat hodnoty z aplikace nastavení, jak je znázorněno v příkladu. Při použití sady WebJobs SDK přímo, můžete napsat vlastní implementace, která získá zástupný symbol náhradní hodnoty z jakéhokoli zdroje dáváte přednost.

## <a name="binding-at-runtime"></a>Vazbu za běhu

Pokud potřebujete provést nějakou práci ve své funkci předtím, než použijete atribut vazby, jako je `Queue`, `Blob`, nebo `Table`, můžete použít `IBinder` rozhraní.

V následujícím příkladu přebírá zprávu vstupní fronty a vytvoří novou zprávu s stejný obsah ve výstupní frontě. Název fronty výstupu nastavený prostřednictvím kódu v těle funkce.

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

Další informace najdete v tématu [vazby za běhu](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) v dokumentaci ke službě Azure Functions.

## <a name="binding-reference-information"></a>Referenční informace o vazbě

Dokumentace ke službě Azure Functions poskytuje referenční informace o jednotlivých typech vazby. Tyto informace najdete v článku o každé vazby. (V tomto příkladu je založená na frontu úložiště.)

* [Balíčky](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). Balíček, který je potřeba nainstalovat zahrnující podporu pro vazbu v projektu sady WebJobs SDK.
* [Příklady](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Ukázky kódu. C# Třídy knihovny příklad se týká sada WebJobs SDK. Jednoduše vynechejte `FunctionName` atribut.
* [Atributy](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Atributy pro typ vazby.
* [Konfigurace](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Vysvětlení vlastností atributu a parametry konstruktoru.
* [Využití](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Typy, které lze svázat a informace o tom, jak funguje vazby. Příklad: dotazování algoritmus, zacházení s nezpracovatelnými zpracování fronty.
  
Seznam vazby článku o příkazech naleznete v části "Podporované vazby" v [aktivačními událostmi a vazbami](../azure-functions/functions-triggers-bindings.md#supported-bindings) článku pro službu Azure Functions. V tomto seznamu vazby protokolu HTTP, Webhooky a služby Event Grid jsou podporovány pouze funkcí Azure, ne pomocí sady SDK pro WebJobs.

## <a name="disable-attribute"></a>Zakázat atribut 

[ `Disable` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) Atribut umožňuje řídit, jestli funkce se dá spouštět. 

V následujícím příkladu Pokud nastavení aplikace, které `Disable_TestJob` má hodnotu `1` nebo `True` (malá a velká písmena), funkce se nespustí. V takovém případě modul runtime vytvoří zprávu protokolu *zakázána funkce "Functions.TestJob"*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Při změně hodnoty nastavení aplikace na webu Azure Portal, aby se získaly nové nastavení restartuje webová úloha.

Atribut lze deklarovat na parametr, metody nebo třídy úroveň. Název nastavení může také obsahovat výrazy vazby.

## <a name="timeout-attribute"></a>Atribut časového limitu

[ `Timeout` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) Atribut způsobí, že funkce budou zrušeny, pokud není dokončena ve stanoveném čase. V následujícím příkladu spustí funkci za jeden den bez atributu časového limitu. Časový limit způsobí, že funkce zrušení po 15 sekundách.

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

Můžete použít atribut časového limitu na úrovni třídy nebo metody a globálního časového limitu můžete určit pomocí `JobHostConfiguration.FunctionTimeout`. Vypršení časového limitu pro úrovni třídy nebo metody přepsat globální vypršení časového limitu.

## <a name="singleton-attribute"></a>Atribut typu singleton

[ `Singleton` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) Atribut zajišťuje, že pouze jedna instance funkce spuštění, i když existuje více instancí hostitele webové aplikace. Dělá to pomocí [distribuované uzamčení](#viewing-lease-blobs).

V tomto příkladu pouze jednu instanci `ProcessImage` v daném okamžiku spuštění funkce:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Některé aktivační události mají integrovanou podporu řízení souběžnosti:

* **QueueTrigger**. Nastavte `JobHostConfiguration.Queues.BatchSize` k `1`.
* **ServiceBusTrigger**. Nastavte `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` k `1`.
* **FileTrigger**. Nastavte `FileProcessor.MaxDegreeOfParallelism` k `1`.

Tato nastavení můžete použít k zajištění, že funkce běží na jednu instanci jako typ singleton. Aby bylo zajištěno, že jenom jednu instanci funkce běží, když webová aplikace horizontálně navýší kapacitu na několik instancí, použít singleton naslouchací proces – úroveň zámku na funkci (`[Singleton(Mode = SingletonMode.Listener)]`). Při spuštění JobHost pořízené zámky naslouchacího procesu. Pokud tři instance horizontálním navýšením kapacity všechna spuštění ve stejnou dobu, pouze jedna z instancí získá zámek a pouze jeden naslouchací proces spustí.

### <a name="scope-values"></a>Hodnoty oboru

Můžete zadat *výraz/hodnotu oboru* na jednotlivý prvek. Výraz a hodnota zajistí, že bude serializována všechny prováděné funkci v konkrétním oboru. Implementace podrobnější uzamčení tímto způsobem můžete povolit pro některé stupeň paralelismu funkce při serializaci další volání podle vašich požadavků. Například v následujícím kódu, výraz scope vytvoří vazbu `Region` hodnotu příchozí zprávy. Když tato fronta obsahuje tři zprávy v oblastech východ – východ a v uvedeném pořadí – Západ, zprávy, které mají oblast východ spouštějí sériově while zprávy s oblastí, že západní běží paralelně s aliasy ve – východ.

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

Výchozí obor pro zámek je `SingletonScope.Function`, což znamená rozsah zámku (cesta zapůjčení objektu blob) se váže na funkce plně kvalifikovaného názvu. Zamknout napříč funkcí, zadejte `SingletonScope.Host` a používat název ID oboru, které jsou stejné všechny funkce, které nechcete spustit současně. V následujícím příkladu, pouze jedna instance `AddItem` nebo `RemoveItem` byla najednou spuštěna:

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

### <a name="viewing-lease-blobs"></a>Objekty BLOB zapůjčení zobrazení

Sada WebJobs SDK používá [zapůjčení objektu blob Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) na pozadí k implementaci distribuovaných uzamčení. Objekty BLOB zapůjčení používané Singleton najdete v `azure-webjobs-host` kontejneru v `AzureWebJobsStorage` účtu úložiště v rámci cesty "zámky". Například cesta objektu blob zapůjčení pro první `ProcessImage` příklad uvedený výše může být `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Všechny programy zahrnují JobHost ID v tomto případu 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Asynchronní funkce

Informace o tom, jak kód asynchronních funkcí najdete v tématu [dokumentaci ke službě Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokeny zrušení

Informace o tom, jak zpracovávat tokeny zrušení, najdete v dokumentaci ke službě Azure Functions na [tokeny zrušení a řádné vypnutí](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Několik instancí

Pokud vaše webová aplikace běží na více instancí, nepřetržitá webová úloha běží na jednotlivých instancích naslouchání pro aktivační události a volání funkcí. Různé vazby aktivační události jsou navrženy pro sdílet práci efektivně spolupracovat napříč instancemi, tak, aby horizontální navýšení kapacity na více instancí umožňuje zpracovávat větší zatížení.

Aktivační události front a objektů blob automaticky zabránit funkci zpracování zpráv fronty nebo objektu blob více než jednou; funkce nemusí být idempotentní.

Trigger časovače automaticky zajistí této pouze jedna instance spuštění časovače, proto nebudete mít více než jednu instanci funkce spuštění v daném okamžiku naplánované.

Pokud chcete zajistit, že pouze jedna instance funkce běží i v případě, že existuje více instancí hostitele webové aplikace, můžete použít [ `Singleton` ](#singleton-attribute) atribut.

## <a name="filters"></a>Filtry

Funkce filtry (preview) poskytují způsob, jak přizpůsobit spouštěcí kanál WebJobs pomocí vlastní logiky. Filtry jsou podobné [filtry pro ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Lze je implementovat jako deklarativních atributů, které se použijí u funkcí nebo tříd. Další informace najdete v tématu [filtry funkce](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Doporučujeme, abyste protokolovacího rozhraní, který byl vyvinut pro technologii ASP.NET. [Začínáme](webjobs-sdk-get-started.md) článek popisuje, jak ji používat. 

### <a name="log-filtering"></a>Filtrování protokolu

Každý protokol vytvořené `ILogger` instance má přiřazený `Category` a `Level`. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) výčet a kód celé číslo označuje relativní význam:

|LogLevel    |Kód|
|------------|---|
|Trasování       | 0 |
|Ladění       | 1 |
|Informace | 2 |
|Upozornění     | 3 |
|Chyba       | 4 |
|Kritická    | 5 |
|Žádný        | 6 |

Nezávisle na sobě můžete filtrovat každá kategorie pro určitý [ `LogLevel` ](/dotnet/api/microsoft.extensions.logging.loglevel). Například můžete chtít zobrazit všechny protokoly pro zpracování objektů blob aktivační událost ale jenom `Error` nebo vyšší pro všechno ostatní.

#### <a name="version-3x"></a>Verze 3. *x*

Verze 3. *x* sady SDK spoléhá na filtrování integrované do .NET Core. `LogCategories` Třída umožňuje definovat kategorie pro konkrétní funkce, aktivační události nebo uživatele. Definuje také filtry pro konkrétního hostitele stavy, jako je třeba `Startup` a `Results`. To umožňuje optimalizovat výstup protokolování. Pokud není nalezena žádná shoda v rámci definovaných kategorií, filtr přejde `Default` hodnota při rozhodování, jestli se má filtrovat zprávy.

`LogCategories` vyžaduje následující příkaz using:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Následující příklad vytvoří filtr, který ve výchozím nastavení, vyfiltruje všechny protokoly `Warning` úroveň. `Function` a `results` kategorie (ekvivalentní `Host.Results` ve verzi 2. *x*) jsou filtrovány na `Error` úroveň. Filtr porovná aktuální kategorii pro všechny úrovně registrované v `LogCategories` instance a zvolí nejdelší shody. To znamená, že `Debug` úroveň zaregistrovali `Host.Triggers` odpovídá `Host.Triggers.Queue` nebo `Host.Triggers.Blob`. To umožňuje řídit širší kategorie bez nutnosti přidat každý z nich.

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

Ve verzi 2. *x* sady SDK, můžete použít `LogCategoryFilter` třídy řídit filtrování. `LogCategoryFilter` Má `Default` vlastnost s počáteční hodnotou `Information`, to znamená, že všechny zprávy v `Information`, `Warning`, `Error`, nebo `Critical` jsou protokolovány úrovně, ale všechny zprávy v `Debug` nebo `Trace` úrovně jsou filtrovány okamžitě.

Stejně jako u `LogCategories` ve verzi 3. *x*, `CategoryLevels` vlastnost vám umožní určit úrovně protokolu pro konkrétní kategorie tak mají možnost konfiguraci doladit výstup protokolování. Pokud není nalezena žádná shoda v rámci `CategoryLevels` slovníku. Tím se filtr spadne zpět na `Default` hodnota při rozhodování, jestli se má filtrovat zprávy.

Následující příklad vytvoří filtr, který ve výchozím nastavení vyfiltruje všechny protokoly `Warning` úroveň. `Function` a `Host.Results` kategorií jsou filtrovány na `Error` úroveň. `LogCategoryFilter` Porovná aktuální kategorii pro všechny zaregistrované `CategoryLevels` a zvolí nejdelší shody. Proto `Debug` úroveň zaregistrovali `Host.Triggers` bude odpovídat `Host.Triggers.Queue` nebo `Host.Triggers.Blob`. To umožňuje řídit širší kategorie bez nutnosti přidat každý z nich.

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

### <a name="custom-telemetry-for-application-insights"></a>Vlastní telemetrii pro službu Application Insights

Proces pro implementaci vlastní telemetrii pro [Application Insights](../azure-monitor/app/app-insights-overview.md) závisí na verzi sady SDK. Další informace o konfiguraci Application Insights, najdete v článku [přidat službu Application Insights protokolování](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Verze 3. *x*

Protože verze 3. *x* sady WebJobs SDK spoléhá na .NET Core, už je k dispozici Obecný hostitelský objekt pro vytváření vlastní telemetrii. Ale můžete přidat vlastní telemetrická data do kanálu pomocí vkládání závislostí. Příklady v této části vyžadují následující `using` příkazy:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Následující vlastní implementace [ `ITelemetryInitializer` ] umožňuje přidat vlastní [ `ITelemetry` ](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) na výchozí hodnotu [ `TelemetryConfiguration` ].

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Volání [ `ConfigureServices` ] v Tvůrci přidáte vlastní [ `ITelemetryInitializer` ] do kanálu.

```cs
static void Main()
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

        host.Run();
    }
}
```

Když [ `TelemetryConfiguration` ] je vytvořen, všechny registrované typy [ `ITelemetryInitializer` ] jsou zahrnuty. Další informace najdete v tématu [API pro Application Insights pro vlastní události a metriky](../azure-monitor/app/api-custom-events-metrics.md).

Ve verzi 3. *x*, máte už se nezdařil [ `TelemetryClient` ] při zastavení hostitele. Injektor .NET Core závislosti automaticky uvolní zaregistrovanou `ApplicationInsightsLoggerProvider`, které vyprázdnění [ `TelemetryClient` ].

#### <a name="version-2x"></a>Verze 2. *x*

Ve verzi 2. *x*, [ `TelemetryClient` ] interně vytvořené poskytovateli Application Insights pro použití sady WebJobs SDK [ `ServerTelemetryChannel` ](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Pokud koncový bod služby Application Insights je nedostupná nebo omezení příchozích požadavků, v tomto kanálu [uloží žádosti v systému souborů webové aplikace a je později znovu odešle](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

[ `TelemetryClient` ] Vytvoří třídu, která implementuje `ITelemetryClientFactory`. Ve výchozím nastavení je to [ `DefaultTelemetryClientFactory` ](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Pokud chcete upravit libovolné části kanálu Application Insights, můžete zadat vlastní `ITelemetryClientFactory`, a Hostitel použije k vytvoření své třídy [ `TelemetryClient` ]. Například tento kód přepíše `DefaultTelemetryClientFactory` upravit vlastnost `ServerTelemetryChannel`:

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

`SamplingPercentageEstimatorSettings` Konfiguruje objekt [adaptivního vzorkování](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). To znamená, že v některých scénářích velkého objemu aplikace Insights odesílá vybranou podmnožinu telemetrická data na serveru.

Jakmile vytvoříte objekt pro vytváření telemetrická data, předejte ji v k poskytovateli protokolování Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Další kroky

Tento článek poskytuje fragmenty kódu, které ukazují, jak zvládnout běžné scénáře pro práci se sadou WebJobs SDK. Kompletní ukázky najdete v tématu [azure webjobs sdk ukázky](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
