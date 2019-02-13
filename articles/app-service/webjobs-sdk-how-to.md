---
title: Použití sady SDK pro WebJobs – Azure
description: Další informace o tom, jak psát kód pro sadu WebJobs SDK. Vytvoření založený na událostech pozadí úlohy zpracování, které přístup k datům v Azure služby a služby třetích stran.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/19/2019
ms.author: glenga
ms.openlocfilehash: ab502c25a632977065e55d2eeafd684203636b14
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109907"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Použití sady Azure WebJobs SDK pro zpracování na pozadí založený na událostech

Tento článek obsahuje pokyny o tom, jak napsat kód pro [sady Azure WebJobs SDK](webjobs-sdk-get-started.md). V dokumentaci k platí pro obě verze 3.x a 2.x sady SDK pro WebJobs. Pokud existují rozdíly rozhraní API, jsou k dispozici příklady obou. Hlavní změny zavedené ve verzi 3.x je použití .NET Core namísto rozhraní .NET Framework.

>[!NOTE]
> [Služba Azure Functions](../azure-functions/functions-overview.md) je postavené na sadě WebJobs SDK a tento článek obsahuje odkazy na dokumentaci ke službě Azure Functions pro některá témata. Mějte na paměti následující rozdíly mezi službami Functions a WebJobs SDK:
> * Azure Functions verze 2.x odpovídá verzi sady SDK pro WebJobs 3.x a Azure Functions 1.x odpovídá sada WebJobs SDK 2.x. Úložišť zdrojového kódu pomocí sady SDK pro WebJobs číslování.
> * Ukázkový kód pro knihovny tříd Azure funkce C# je jako sada WebJobs SDK kódu s tím rozdílem, není nutné `FunctionName` atribut v projektu sady WebJobs SDK.
> * Některé typy vazeb jsou podporovány pouze u funkcí, jako jsou HTTP, webhook a Event Grid (která je založená na protokolu HTTP).
> 
> Další informace najdete v tématu [porovnání sadou WebJobs SDK a Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá přečetl(a) a dokončených úloh v [Začínáme se sadou WebJobs SDK](webjobs-sdk-get-started.md).

## <a name="webjobs-host"></a>WebJobs hostitele

Hostitel je kontejner modulu runtime pro funkce.  Naslouchá aktivačních událostí a volání funkce. Ve verzi 3.x, hostitel je implementace `IHost`a ve verzi 2.x použijete `JobHost` objektu. Vytvoření instance hostitele ve vašem kódu a psaní kódu můžete přizpůsobit její chování.

Toto je klíčovým rozdílem mezi přímo pomocí sady WebJobs SDK a nepřímo pomocí s využitím Azure Functions. Ve službě Azure Functions služba ovládací prvky hostitele a si nemůžete přizpůsobit napsáním kódu. Azure Functions umožňuje přizpůsobit chování hostitele v nastaveních *host.json* souboru. Tato nastavení jsou řetězce, není kód, který omezuje typy vlastního nastavení, které vám pomůžou.

### <a name="host-connection-strings"></a>Hostitel připojovacích řetězců 

Sada WebJobs SDK hledá připojovacích řetězců Azure Storage a Azure Service Bus v *local.settings.json* souboru při spuštění místně nebo v prostředí webové úlohy při spuštění v Azure. Ve výchozím nastavení, řetězec připojení úložiště nastavení s názvem `AzureWebJobsStorage` je povinný.  

Verze 2.x sady SDK můžete použít vlastní názvy těchto připojovací řetězce, nebo je uložíte jinam. Můžete je nastavit v kódu, jak je znázorněno zde:

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

Protože ho používá výchozí konfigurace .NET Core API, neexistuje žádné rozhraní API ve verzi 3.x změnit názvy připojovací řetězec.

### <a name="host-development-settings"></a>Hostitel vývojové nastavení

Hostitele můžete spustit v režimu pro vývoj pro efektivnější místní vývoj. Tady jsou některé z nastavení, která se mění při spuštění v režimu pro vývoj:

| Vlastnost | Vývojové nastavení |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` pro maximalizaci výstupu protokolu. |
| `Queues.MaxPollingInterval`  | Nízká hodnota zajistit fronty metody se aktivují okamžitě.  |
| `Singleton.ListenerLockPeriod` | 15 sekund pro rychlý iterativní vývoj. |

Způsob povolení režimu pro vývoj závisí na verzi sady SDK. 

#### <a name="version-3x"></a>Verze 3.x

Verze 3.x používá standardní rozhraní API ASP.NET Core. Volání [UseEnvironment](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metodu [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instance. Předat řetězec s názvem `development`, jako v následujícím příkladu:

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

#### <a name="version-2x"></a>Verze 2.x

`JobHostConfiguration` Třída nemá `UseDevelopmentSettings` metodu, která umožňuje vývoj režimu.  Následující příklad ukazuje způsob použití vývojového nastavení. Chcete-li `config.IsDevelopment` vrátit `true` při místním spuštění, nastavte proměnnou místní prostředí s názvem `AzureWebJobsEnv` s hodnotou `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Správa souběžných připojení (v2.x)

Ve verzi 3.x, limitu připojení výchozí hodnota je nekonečná připojení. Pokud z nějakého důvodu potřebujete tento limit změnit, můžete použít [MaxConnectionsPerServer](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) vlastnost [WinHttpHander](/dotnet/api/system.net.http.winhttphandler) třídy.

Pro verzi 2.x, řídí počet souběžných připojení k hostiteli s použitím [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) rozhraní API. V 2.x měli byste zvýšit tuto hodnotu z výchozí hodnoty 2 před spuštěním hostitele WebJobs.

Všechny odchozí požadavky HTTP, které jste provedli z funkce s použitím `HttpClient` procházet skrz `ServicePointManager`. Po kliknutí `DefaultConnectionLimit`, `ServicePointManager` spustí zařazení do fronty žádostí před jejich odesláním. Předpokládejme, že vaše `DefaultConnectionLimit` je nastavena na 2 a váš kód je 1 000 požadavků HTTP. Zpočátku je povoleno pouze dvě požadavků prostřednictvím do operačního systému. Další 998 se zařadí do fronty. dokud je volného místa pro ně. To znamená, že vaše `HttpClient` vypršet časový limit, protože ho *domnívá* poskytl požadavek, ale žádost byla odeslána nikdy operačního systému na cílový server. Může se zobrazit chování, které vypadá, že nepodporuje dávat smysl: místní `HttpClient` trvá dokončení požadavku, 10 sekund, ale vaše služba vrací každého požadavku v 200 ms. 

Výchozí hodnota pro aplikace ASP.NET je `Int32.MaxValue`, a to je pravděpodobně fungovat i pro webové úlohy spuštěné v plánu služby App Service Basic nebo vyšší. WebJobs většinou potřebují nastavení AlwaysOn a je podporovaná jenom v plánech služby App Service Basic nebo vyšší. 

Pokud vaše webová úloha běží v Free nebo sdílený plán služby App Service, vaše aplikace je omezen sandboxu služby App Service, která má v současné době [limitu připojení 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Limit nevázaného připojení je v `ServicePointManager`, je pravděpodobnější, že prahová hodnota připojení izolovaného prostoru bude dostupný a vypněte webu. V takovém případě nastavení `DefaultConnectionLimit` na něco nižší, jako je 50 nebo 100, tomu zabránili a stále umožňují dostatečnou propustnost.

Nastavení musí být nakonfigurované dřív, než všechny požadavky HTTP. Z tohoto důvodu by se neměl pokoušet upravit nastavení hostitele WebJobs automaticky. může existovat požadavky HTTP, ke kterým dochází před spuštění hostitele, a to může vést k neočekávanému chování. Nejlepším řešením je nastavit hodnotu okamžitě ve vaší `Main` metoda před inicializací `JobHost`, jak je znázorněno v následujícím příkladu

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Aktivační události

Funkce musí být veřejné metody a musí obsahovat jeden atribut aktivační události nebo [NoAutomaticTrigger](#manual-trigger) atribut.

### <a name="automatic-trigger"></a>Automatická aktivační událost

Automatických aktivačních procedur volání funkce v reakci na událost. Příklad najdete v tématu aktivační událost fronty v [Get článku](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Ruční aktivační události

Chcete-li aktivovat funkce ručně, použijte `NoAutomaticTrigger` atributu, jak je znázorněno v následujícím příkladu:

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

Způsob, jak ručně aktivujete funkce závisí na verzi sady SDK.

#### <a name="version-3x"></a>Verze 3.x

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

#### <a name="version-2x"></a>Verze 2.x

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Vstupní a výstupní vazby

Vstupní vazby poskytují deklarativní způsob zpřístupnění dat z Azure nebo služby třetích stran do vašeho kódu. Výstupní vazby poskytují způsob, jak aktualizovat data. [Get článku](webjobs-sdk-get-started.md) ukazuje příklad každého.

Návratová hodnota metody můžete použít pro výstupní vazby, použitím atributu na návratovou hodnotu metody. Podívejte se na příklad ve službě Azure Functions [aktivačními událostmi a vazbami](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) článku.

## <a name="binding-types"></a>Vazby typů

Způsobu, jakým jsou nainstalované a spravované typy vazby se liší mezi verzí 3.x a 2.x sady SDK. Můžete najít balíček instalace pro typ konkrétní vazby v **balíčky** část tento typ vazby [článku](#binding-reference-information) pro službu Azure Functions. Výjimkou je aktivační událost soubory a vazbu (pro místního systému souborů), což není podporováno službou Azure Functions.

#### <a name="version-3x"></a>Verze 3.x

Ve verzi 3.x vazby úložiště jsou součástí `Microsoft.Azure.WebJobs.Extensions.Storage` balíčku. Volání `AddAzureStorage` metody rozšíření v `ConfigureWebJobs` způsob, jak je znázorněno v následujícím příkladu:

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

Použít další aktivační události a typy vazeb, nainstalujte balíček NuGet, který je obsahuje a volat `Add<binding>` rozšiřující metoda implementována v rozšíření. Například pokud chcete použít vazby Azure Cosmos DB, nainstalujte `Microsoft.Azure.WebJobs.Extensions.CosmosDB` a volat `AddCosmosDB`, jako v následujícím příkladu:

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

#### <a name="version-2x"></a>Verze 2.x

Následující typy aktivační události a vazby jsou součástí verze 2.x `Microsoft.Azure.WebJobs` balíčku:

* Blob Storage
* Queue Storage
* Úložiště Table

Použít další aktivační události a typy vazeb, nainstalujte balíček NuGet, který je obsahuje a volat `Use<binding>` metodu na `JobHostConfiguration` objektu. Například pokud chcete použít aktivační událost časovače, nainstalujte `Microsoft.Azure.WebJobs.Extensions` a volat `UseTimers` v `Main` metody, jako v následujícím příkladu:

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

Tak, jak můžete svázat [ `ExecutionContext` ] závisí na vaší verzi sady SDK.

#### <a name="version-3x"></a>Verze 3.x

Volání `AddExecutionContextBinding` metody rozšíření v `ConfigureWebJobs` způsob, jak je znázorněno v následujícím příkladu:

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

#### <a name="version-2x"></a>Verze 2.x

`Microsoft.Azure.WebJobs.Extensions` Balíčku již bylo zmíněno dříve také poskytuje typ speciální vazby, která můžete zaregistrovat pomocí volání `UseCore` metody. Tato vazba umožňuje definovat [ `ExecutionContext` ] parametrů v signatuře funkce, která je povolena následujícím způsobem:

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

Některé aktivační událost a vazby umožňují konfigurovat jejich chování. Způsob, jak je nakonfigurovat závisí na verzi sady SDK.

* **Verze 3.x:** Konfigurace je nastavena, když `Add<Binding>` metoda je volána `ConfigureWebJobs`.
* **Verze 2.x:** Nastavením vlastností v objektu konfigurace, který můžete předat do `JobHost`.

### <a name="queue-trigger-configuration"></a>Konfigurace aktivační událost fronty

Nastavení můžete nakonfigurovat pro aktivační událost fronty úložiště je podrobně popsaný v Azure Functions [referenční materiály k host.json](../azure-functions/functions-host-json.md#queues). Následující příklady ukazují, jak je nastavit v konfiguraci:

#### <a name="version-3x"></a>Verze 3.x

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

#### <a name="version-2x"></a>Verze 2.x

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

### <a name="configuration-for-other-bindings"></a>Konfigurace u jiných vazeb

Některé typy aktivační události a vazby definovat vlastní typ vlastní konfigurace. Například aktivační událost soubor umožňuje určit kořenovou cestou za účelem monitorování, stejně jako v následujících příkladech:

#### <a name="version-3x"></a>Verze 3.x

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

#### <a name="version-2x"></a>Verze 2.x

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

Další informace o výrazech vazeb, naleznete v tématu [vazby výrazů a vzory](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) v dokumentaci ke službě Azure Functions.

### <a name="custom-binding-expressions"></a>Výrazy vlastní vazby

Někdy chcete zadat název fronty, název objektu blob nebo kontejneru, nebo tabulka název v kódu namísto pevně zakódovat. Například můžete chtít určit název fronty `QueueTrigger` atribut v konfigurační soubor nebo prostředí proměnnou.

Můžete to udělat předáním `NameResolver` objektu `JobHostConfiguration` objektu. Obsahují zástupné symboly v aktivační události nebo parametry konstruktoru atributu vazby a vaše `NameResolver` kód obsahuje skutečné hodnoty, které mají být zastoupen tyto zástupné symboly. Zástupné symboly jsou označeny obklopením znaky procenta (%), jak je znázorněno v následujícím příkladu:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Tento kód vám umožní použít frontu s názvem `logqueuetest` v testovacím prostředí a jednu s názvem `logqueueprod` v produkčním prostředí. Místo názvu fronty pevně zakódované, zadejte název položky v `appSettings` kolekce.

Neexistuje výchozí NameResolver, který se uplatní, pokud nezadáte vlastní. Výchozí hodnota získá hodnoty z nastavení aplikace nebo proměnné prostředí.

Vaše `NameResolver` třídy získá název fronty z `appSettings` jak je znázorněno v následujícím příkladu:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Verze 3.x

Překladač je nakonfigurovaný pomocí vkládání závislostí. Tyto ukázky vyžadují následující `using` – příkaz:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Překladač je přidána voláním [ `ConfigureServices` ] rozšiřující metody na [HostBuilder](/dotnet/api/microsoft.extensions.hosting.hostbuilder), jako v následujícím příkladu:

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

#### <a name="version-2x"></a>Verze 2.x

Předání vašeho `NameResolver` třídu v `JobHost` jak je znázorněno v následujícím příkladu:

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

Pokud potřebujete udělat nějakou práci ve své funkci před použitím atribut vazby, jako `Queue`, `Blob`, nebo `Table`, můžete použít `IBinder` rozhraní.

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

Referenční informace o jednotlivých typech vazby najdete v dokumentaci ke službě Azure Functions. Jako příklad použijeme fronty úložiště, najdete tyto informace v článku o každé vazby:

* [Balíčky](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) – co balíček nainstalovat, aby zahrnují podporu pro vazbu v projektu sady WebJobs SDK.
* [Příklady](../azure-functions/functions-bindings-storage-queue.md#trigger---example) – příklad knihovny třídy jazyce C# se vztahuje na sadě WebJobs SDK, jednoduše vynechejte `FunctionName` atribut.
* [Atributy](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) – atributy, které mají použít pro typ vazby.
* [Konfigurace](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) – vysvětlení vlastností atributu a parametry konstruktoru.
* [Využití](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) – co typy lze svázat a informace o vazbě funguje. Příklad: dotazování algoritmus, zacházení s nezpracovatelnými zpracování fronty.
  
Seznam vazby článku o příkazech najdete v tématu **nepodporuje vazby** v [aktivačními událostmi a vazbami](../azure-functions/functions-triggers-bindings.md#supported-bindings) článku pro službu Azure Functions. V tomto seznamu vazby protokolu HTTP, webhook a služby Event Grid jsou podporovány pouze funkcí Azure, ne pomocí sady SDK pro WebJobs.

## <a name="disable-attribute"></a>Zakázat atribut 

[Zakázat](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atribut umožňuje řídit, jestli funkce se dá spouštět. 

V následujícím příkladu Pokud nastavení aplikace, které `Disable_TestJob` má hodnotu "1" nebo "PRAVDA" (malá a velká písmena), funkce se nespustí. V takovém případě modul runtime vytvoří zprávu protokolu *zakázána funkce "Functions.TestJob"*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Při změně hodnoty nastavení aplikace na webu Azure Portal, způsobí, že úlohy WebJob k restartování, výběr nového nastavení.

Atribut lze deklarovat na parametr, metody nebo třídy úroveň. Název nastavení může také obsahovat výrazy vazby.

## <a name="timeout-attribute"></a>Atribut časového limitu

[Vypršení časového limitu](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) atribut způsobí, že funkce budou zrušeny, pokud nebude dokončena ve stanoveném čase. V následujícím příkladu spustí funkci za jeden den bez časového limitu. S časovým limitem funkce se zruší po 15 sekundách.

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

Můžete použít atribut časového limitu na úrovni třídy nebo metody a globálního časového limitu můžete určit pomocí `JobHostConfiguration.FunctionTimeout`. Třída nebo metoda úrovně vypršení časového limitu pro přepsání globálního časového limitu.

## <a name="singleton-attribute"></a>Atribut typu singleton

Použití [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) atribut k zajištění tohoto pouze jedna instance funkce běží i při existuje více instancí hostitele webové aplikace. Dělá to tak, že implementace [distribuované uzamčení](#viewing-lease-blobs).

V následujícím příkladu, pouze jednu instanci `ProcessImage` v daném okamžiku spuštění funkce:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Některé aktivační události mají integrovanou podporu řízení souběžnosti:

* **QueueTrigger** – nastavení `JobHostConfiguration.Queues.BatchSize` na hodnotu 1.
* **ServiceBusTrigger** – nastavení `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` na hodnotu 1.
* **FileTrigger** – nastavení `FileProcessor.MaxDegreeOfParallelism` na hodnotu 1.

Tato nastavení můžete použít k zajištění, že funkce běží na jednu instanci jako typ singleton. Aby jenom jednu instanci funkce běží, když webová aplikace škálovat do několika instancí, aplikovat zámek úrovně jednotlivý prvek naslouchacího procesu na funkci (`[Singleton(Mode = SingletonMode.Listener)]`). Při spuštění JobHost jsou získat zámky naslouchacího procesu. Pokud tři instance horizontálním navýšením kapacity všechna spuštění ve stejnou dobu, pouze jedna z instancí získá zámek a pouze jeden naslouchací proces spustí.

### <a name="scope-values"></a>Hodnoty oboru

Můžete zadat **výraz/hodnotu oboru** na jednotlivý prvek, který zajistí, že bude serializována všechny prováděné funkce v daném oboru. Implementace podrobnější uzamčení tímto způsobem můžete povolit pro určitou úroveň paralelizace pro vaše funkce při serializaci další volání podle vašich požadavků. Například v následujícím příkladu výraz scope vytvoří vazbu `Region` hodnotu příchozí zprávy. Pokud fronta obsahuje tři zprávy v oblasti "Východ", "Východ" a "Západ" v uvedeném pořadí, pak zprávy, které mají oblast, kterou "Východ" jsou prováděny sériově při zprávy s oblastí, které "Západní" spouští paralelně s těmi v "– východ.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item
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

Výchozí obor pro zámek je `SingletonScope.Function` znamená rozsah zámku (cesta zapůjčení objektu blob) se váže na funkce plně kvalifikovaného názvu. Zamknout napříč funkcí, zadejte `SingletonScope.Host` a používat název ID oboru, který je stejný pro všechny funkce, které nechcete spustit současně. V následujícím příkladu, pouze jedna instance `AddItem` nebo `RemoveItem` byla najednou spuštěna:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>Objekty BLOB zapůjčení zobrazení

Sada WebJobs SDK používá [zapůjčení objektu blob Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) na pozadí k implementaci distribuovaných uzamčení. Objekty BLOB zapůjčení používané Singleton najdete v `azure-webjobs-host` kontejneru v `AzureWebJobsStorage` účtu úložiště v rámci cesty "zámky". Například cesta objektu blob zapůjčení pro první `ProcessImage` příklad uvedený výše může být `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Všechny programy zahrnují JobHost ID v tomto případu 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Asynchronní funkce

Informace o tom, jak kód asynchronních funkcí najdete v dokumentaci ke službě Azure Functions na [asynchronních funkcí](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokeny zrušení

Informace o tom, jak zpracovávat tokeny zrušení, najdete v dokumentaci ke službě Azure Functions na [tokeny zrušení a řádné vypnutí](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Více instancí

Pokud vaše webová aplikace běží na více instancí, nepřetržitá webová úloha běží na jednotlivých instancích naslouchání pro aktivační události a volání funkcí. Různé vazby aktivační události jsou navrženy pro sdílet práci efektivně spolupracovat napříč instancemi, tak, aby horizontální navýšení kapacity na více instancí umožňuje zpracovávat větší zatížení.

Aktivační události front a objektů blob automaticky zabránit funkci zpracování zpráv fronty nebo objektu blob více než jednou; Funkce nemají být idempotentní.

Trigger časovače automaticky zajistí této pouze jedna instance spuštění časovače, proto nebudete mít více než jednu instanci funkce spuštění v daném okamžiku naplánované.

Pokud chcete zajistit, že pouze jedna instance funkce běží i v případě, že existuje více instancí hostitele webové aplikace, můžete použít [atribut typu Singleton](#singleton-attribute).

## <a name="filters"></a>Filtry

Funkce filtry (preview) poskytují způsob, jak přizpůsobit spouštěcí kanál WebJobs pomocí vlastní logiky. Filtry jsou podobné [ASP.NET Core filtry](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Je možné implementovat jako deklarativních atributů, které se použijí u funkcí nebo tříd. Další informace najdete v tématu [filtry funkce](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Doporučujeme, abyste protokolovacího rozhraní, který byl vyvinut pro technologii ASP.NET a [Začínáme](webjobs-sdk-get-started.md) článek popisuje, jak ji používat. 

### <a name="log-filtering"></a>Filtrování protokolu

Každý protokol vytvořené `ILogger` instance má přiřazený `Category` a `Level`. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) je výčet a kód celé číslo označuje relativní význam:

|LogLevel    |Kód|
|------------|---|
|Trasování       | 0 |
|Ladění       | 1 |
|Informace | 2 |
|Upozornění     | 3 |
|Chyba       | 4 |
|Kritická    | 5 |
|Žádný        | 6 |

Každá kategorie je možné nezávisle na sobě filtrovat na konkrétní [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel). Například můžete chtít zobrazit všechny protokoly pro zpracování objektů blob aktivační událost ale jenom `Error` nebo vyšší pro všechno ostatní.

#### <a name="version-3x"></a>Verze 3.x

Verze 3.x sada SDK spoléhá na filtrování integrované do .NET Core. `LogCategories` Třída umožňuje definovat kategorie pro konkrétní funkce, aktivační události nebo uživatele. Definuje také filtry pro konkrétního hostitele stavy, jako například `Startup` a `Results`. Tímto způsobem lze optimalizovat výstup protokolování. Pokud není nalezena žádná shoda v rámci definovaných kategorií, filtr přejde `Default` hodnota při rozhodování, jestli se má filtrovat zprávy.

`LogCategories` vyžaduje následující příkaz using:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Následující příklad vytvoří filtr, který ve výchozím nastavení vyfiltruje všechny protokoly `Warning` úroveň. Kategorie `Function` nebo `results` (ekvivalentní `Host.Results` verze 2.x) jsou filtrovány na `Error` úroveň. Filtr porovná aktuální kategorii pro všechny úrovně registrované v `LogCategories` instance a zvolí nejdelší shody. To znamená, že `Debug` úroveň zaregistrovali `Host.Triggers` bude odpovídat `Host.Triggers.Queue` nebo `Host.Triggers.Blob`. To umožňuje řídit širší kategorie bez nutnosti přidat každý z nich.

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

#### <a name="version-2x"></a>Verze 2.x

Verze 2.x sady SDK, `LogCategoryFilter` třída se používá k řízení filtrování. `LogCategoryFilter` Má `Default` vlastnost s počáteční hodnotou `Information`, to znamená, že všechny zprávy s úrovní `Information`, `Warning`, `Error`, nebo `Critical` přihlášení, ale všechny zprávy s úrovní `Debug` nebo `Trace` nefiltrují okamžitě.

Stejně jako u `LogCategories` ve verzi 23.x, `CategoryLevels` vlastnost vám umožní určit úrovně protokolu pro konkrétní kategorie tak mají možnost konfiguraci doladit výstup protokolování. Pokud není nalezena žádná shoda v rámci `CategoryLevels` slovníku. Tím se filtr spadne zpět na `Default` hodnota při rozhodování, jestli se má filtrovat zprávy.

Následující příklad vytvoří filtr, který ve výchozím nastavení vyfiltruje všechny protokoly `Warning` úroveň. Kategorie `Function` nebo `Host.Results` jsou filtrovány na `Error` úroveň. `LogCategoryFilter` Porovná aktuální kategorii pro všechny zaregistrované `CategoryLevels` a zvolí nejdelší shody. To znamená, že `Debug` úroveň zaregistrovali `Host.Triggers` bude odpovídat `Host.Triggers.Queue` nebo `Host.Triggers.Blob`. To umožňuje řídit širší kategorie bez nutnosti přidat každý z nich.

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

Tak, jak implementovat vlastní telemetrii pro [Application Insights](../azure-monitor/app/app-insights-overview.md) závisí na verzi sady SDK, kterou používáte. Další informace o konfiguraci Application Insights, najdete v článku [přidat službu Application Insights protokolování](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Verze 3.x

Od verze 3.x sada WebJobs SDK spoléhá na hostiteli obecný .NET Core, už není objekt pro vytváření vlastní telemetrická data za předpokladu. Můžete však přidat vlastní telemetrická data do kanálu pomocí vkládání závislostí. Příklady v této části vyžadují následující `using` příkazy:

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
        // Add Logging Providers
        b.AddConsole();

        // If this key exists in any config, use it to enable App Insights
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

Když [ `TelemetryConfiguration` ] je vytvořen, všechny registrované typy [ `ITelemetryInitializer` ] jsou zahrnuty. Další informace o práci se [API pro Application Insights pro vlastní události a metriky](../azure-monitor/app/api-custom-events-metrics.md).

Ve verzi 3.x, nepotřebujete k vyprázdnění [ `TelemetryClient` ] při zastavení hostitele. Injektor .NET Core závislosti automaticky uvolní zaregistrovanou `ApplicationInsightsLoggerProvider`, které vyprázdnění [ `TelemetryClient` ].

#### <a name="version-2x"></a>Verze 2.x

Ve verzi 2.x, [ `TelemetryClient` ] interně vytvořené poskytovateli Application Insights pro použití sady WebJobs SDK [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Pokud koncový bod služby Application Insights je nedostupná nebo omezení příchozích požadavků, v tomto kanálu [uloží žádosti v systému souborů webové aplikace a je později znovu odešle](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

[ `TelemetryClient` ] Vytvoří třídu, která implementuje `ITelemetryClientFactory`. Ve výchozím nastavení je to [ `DefaultTelemetryClientFactory` ](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Pokud chcete upravit libovolné části kanálu Application Insights, můžete zadat vlastní `ITelemetryClientFactory`, a Hostitel použije k vytvoření své třídy [ `TelemetryClient` ]. Například tento kód přepíše `DefaultTelemetryClientFactory` k úpravě vlastností `ServerTelemetryChannel`:

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

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Konfiguruje objekt SamplingPercentageEstimatorSettings [adaptivního vzorkování](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). To znamená, že v některých scénářích velkého objemu App Insights odešle podmnožinu telemetrická data vybraného serveru.

Po vytvoření telemetrická data factory, předejte ho v poskytovateli protokolování Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Další kroky

Tato příručka poskytuje fragmenty kódu, které ukazují, jak zvládnout běžné scénáře pro práci se sadou WebJobs SDK. Kompletní ukázky najdete v tématu [azure webjobs sdk ukázky](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
