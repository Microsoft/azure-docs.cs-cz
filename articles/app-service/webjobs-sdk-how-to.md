---
title: Použití sady Azure WebJobs SDK
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
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: 2266f63f9689ec4d22659eb4a7c4876e25fa08b1
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335210"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Použití sady Azure WebJobs SDK pro zpracování na pozadí založený na událostech

Tento článek obsahuje pokyny o tom, jak napsat kód pro [sady Azure WebJobs SDK](webjobs-sdk-get-started.md). V dokumentaci k platí pro verze 2.x a 3.x není uvedeno jinak. Hlavní změny zavedené 3.x je použití .NET Core namísto rozhraní .NET Framework.

>[!NOTE]
> [Služba Azure Functions](../azure-functions/functions-overview.md) je postavené na sadě WebJobs SDK a tento článek obsahuje odkazy na dokumentaci ke službě Azure Functions pro některá témata. Mějte na paměti následující rozdíly mezi službami Functions a WebJobs SDK:
> * Verze služby Azure Functions 1.x odpovídá verzi sady WebJobs SDK 2.x a Azure Functions, WebJobs SDK 2.x odpovídá 3.x. Úložišť zdrojového kódu, postupujte podle číslování sada WebJobs SDK a řada má v2.x větve s hlavní větví momentálně se vyskytl 3.x kódu.
> * Ukázkový kód pro knihovny tříd Azure funkce C# je jako sada WebJobs SDK kódu s tím rozdílem, není nutné `FunctionName` atribut v projektu sady WebJobs SDK.
> * Některé typy vazeb jsou podporovány pouze u funkcí, jako jsou HTTP, webhook a Event Grid (která je založená na protokolu HTTP). 
> 
> Další informace najdete v tématu [porovnání sadou WebJobs SDK a Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste si přečetli [Začínáme se sadou WebJobs SDK](webjobs-sdk-get-started.md).

## <a name="jobhost"></a>JobHost

`JobHost` Objekt je kontejner modulu runtime pro funkce: naslouchá aktivačních událostí a volání funkce. Můžete vytvořit `JobHost` v kódu a napište kód, který přizpůsobit chování aplikace.

Toto je klíčovým rozdílem mezi přímo pomocí sady WebJobs SDK a nepřímo pomocí s využitím Azure Functions. Ve službě Azure Functions, ovládací prvky služby `JobHost`, a nelze ji upravit napsáním kódu. Azure Functions umožňuje přizpůsobit chování hostitele v nastaveních *host.json* souboru. Tato nastavení jsou řetězce, není kód, který omezuje typy vlastního nastavení, které vám pomůžou.

### <a name="jobhost-connection-strings"></a>JobHost připojovacích řetězců

Sada WebJobs SDK hledá připojovací řetězce úložiště a služby Service Bus v *local.settings.json* při spuštění místně nebo v prostředí webové úlohy při spuštění v Azure. Pokud chcete použít vlastní názvy těchto připojovací řetězce, nebo je uložíte jinam, můžete ho nastavit v kódu, jak je znázorněno zde:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>JobHost vývojové nastavení

`JobHostConfiguration` Třída nemá `UseDevelopmentSettings` metodu, která můžete volat a efektivnější místní vývoj. Tady jsou některé z nastavení, která změní takto:

| Vlastnost | Vývojové nastavení |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` pro maximalizaci výstupu protokolu. |
| `Queues.MaxPollingInterval`  | Nízká hodnota zajistit fronty metody se aktivují okamžitě.  |
| `Singleton.ListenerLockPeriod` | 15 sekund pro rychlý iterativní vývoj. |

Následující příklad ukazuje způsob použití vývojového nastavení. Chcete-li `config.IsDevelopment` vrátit `true` při místním spuštění, nastavte proměnnou místní prostředí s názvem `AzureWebJobsEnv` s hodnotou `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Třída JobHost ServicePointManager nastavení

Rozhraní .NET Framework obsahuje rozhraní API volá [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , která určuje počet souběžných připojení k hostiteli. Doporučujeme, abyste zvýšili tuto hodnotu z výchozí hodnoty 2 před spuštěním hostitele WebJobs.

Všechny odchozí požadavky HTTP, které jste provedli z funkce s použitím `HttpClient` procházet skrz `ServicePointManager`. Po kliknutí `DefaultConnectionLimit`, `ServicePointManager` spustí zařazení do fronty žádostí před jejich odesláním. Předpokládejme, že vaše `DefaultConnectionLimit` je nastavena na 2 a váš kód je 1 000 požadavků HTTP. Na začátku pouze 2 jsou ve skutečnosti povoleno požadavků prostřednictvím operačního systému. Další 998 se zařadí do fronty. dokud je volného místa pro ně. To znamená, že vaše `HttpClient` vypršet časový limit, protože ho *domnívá* poskytl požadavek, ale žádost byla odeslána nikdy operačního systému na cílový server. Může se zobrazit chování, které vypadá, že nepodporuje dávat smysl: místní `HttpClient` trvá dokončení požadavku, 10 sekund, ale vaše služba vrací každého požadavku v 200 ms. 

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
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>Vstupní a výstupní vazby

Vstupní vazby poskytují deklarativní způsob zpřístupnění dat z Azure nebo služby třetích stran do vašeho kódu. Výstupní vazby poskytují způsob, jak aktualizovat data. [Get článku](webjobs-sdk-get-started.md) ukazuje příklad každého.

Návratová hodnota metody můžete použít pro výstupní vazby, použitím atributu na návratovou hodnotu metody. Podívejte se na příklad ve službě Azure Functions [aktivačními událostmi a vazbami](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) článku.

## <a name="binding-types"></a>Vazby typů

Následující typy aktivační události a vazby jsou součástí `Microsoft.Azure.WebJobs` balíčku:

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

Můžete najít balíček instalace pro typ konkrétní vazby v **balíčky** část tento typ vazby [článku](#binding-reference-information) pro službu Azure Functions. Výjimkou je aktivační událost soubory a vazbu (pro místního systému souborů), což není podporováno službou Azure Functions. použít soubory vazby, nainstalujte `Microsoft.Azure.WebJobs.Extensions` a volat `UseFiles`.

### <a name="usecore"></a>UseCore

`Microsoft.Azure.WebJobs.Extensions` Balíčku již bylo zmíněno dříve také poskytuje typ speciální vazby, která můžete zaregistrovat pomocí volání `UseCore` metody. Tato vazba umožňuje definovat [kontextu ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) parametrů v signatuře vaší funkce. Objekt kontextu poskytuje přístup k ID vyvolání, které můžete použít ke korelaci všechny protokoly vytvořené metodou volání dané funkce. Tady je příklad:

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

## <a name="binding-configuration"></a>Konfigurace vazby

Mnoho aktivovat a typů vazeb umožňují konfigurovat jejich chování nastavením vlastností v objekt konfigurace, který můžete předat do `JobHost`.

### <a name="queue-trigger-configuration"></a>Konfigurace aktivační událost fronty

Nastavení můžete nakonfigurovat pro aktivační událost fronty úložiště je podrobně popsaný v Azure Functions [referenční materiály k host.json](../azure-functions/functions-host-json.md#queues). Jak je nastavit v projektu sady WebJobs SDK je vidět v následujícím příkladu:

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

Některé typy aktivační události a vazby definovat vlastní typ vlastní konfigurace. Například aktivační událost soubor umožňuje určit kořenovou cestou za účelem monitorování:

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

Tento kód vám umožní používat frontu s názvem logqueuetest v testovacím prostředí a jednu s názvem logqueueprod v produkčním prostředí. Místo názvu fronty pevně zakódované, zadejte název položky v `appSettings` kolekce. 

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
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
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

Můžete zadat **výraz/hodnotu oboru** na jednotlivý prvek, který zajistí, že bude serializována všechny prováděné funkce v daném oboru. Implementace podrobnější uzamčení tímto způsobem můžete povolit pro určitou úroveň paralelizace pro vaše funkce při serializaci další volání podle vašich požadavků. Například v následujícím příkladu výraz scope vytvoří vazbu `Region` hodnotu příchozí zprávy. Pokud tato fronta obsahuje v oblasti "Východ", "Východ" a "Západ" v uvedeném pořadí, zprávy, které mají oblast, kterou "Východ" se spustí sériově při zprávy s oblastí, které "Západní" se spustí paralelně s těmi, 3 zprávy.

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

```charp
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

Pokud chcete zajistit, že pouze jedna instance funkce běží i v případě, že existuje více instancí hostitele webové aplikace, můžete použít [Singleton](#singleton) atribut.
    
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

Aby bylo snazší k určení pravidel filtrování, poskytuje sada WebJobs SDK `LogCategoryFilter` , který může být předán do řady poskytovatelů existující protokolování, včetně Application Insights a konzoly.

`LogCategoryFilter` Má `Default` vlastnost s počáteční hodnotou `Information`, to znamená, že všechny zprávy s úrovní `Information`, `Warning`, `Error`, nebo `Critical` přihlášení, ale všechny zprávy s úrovní `Debug` nebo `Trace` nefiltrují okamžitě.

`CategoryLevels` Vlastnost vám umožní určit úrovně protokolu pro konkrétní kategorie tak mají možnost konfiguraci doladit výstup protokolování. Pokud není nalezena žádná shoda v rámci `CategoryLevels` slovníku. Tím se filtr spadne zpět na `Default` hodnota při rozhodování, jestli se má filtrovat zprávy.

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

Interně `TelemetryClient` vytvořené poskytovateli Application Insights pro použití sady WebJobs SDK [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Pokud koncový bod služby Application Insights je nedostupná nebo omezení příchozích požadavků, v tomto kanálu [uloží žádosti v systému souborů webové aplikace a je později znovu odešle](http://apmtips.com/blog/2015/09/03/more-telemetry-channels).

`TelemetryClient` Vytvoří třídu, která implementuje `ITelemetryClientFactory`. Ve výchozím nastavení je to [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Pokud chcete upravit libovolné části kanálu Application Insights, můžete zadat vlastní `ITelemetryClientFactory`, a Hostitel použije k vytvoření své třídy `TelemetryClient`. Například tento kód přepíše `DefaultTelemetryClientFactory` k úpravě vlastností `ServerTelemetryChannel`:

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