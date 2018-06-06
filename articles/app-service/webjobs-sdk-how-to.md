---
title: Postup použití ke zpracování na pozadí událostmi řízené - Azure WebJobs SDK
description: Další informace o tom, jak napsat kód pro WebJobs SDK. Událostmi řízené pozadí vytvořte úlohy zpracování, které přístup k datům v Azure služby a služby třetích stran.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 18b47014e6fe3e489f783f675a3498c58981b99f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725526"
---
# <a name="how-to-use-the-webjobs-sdk-for-event-driven-background-processing"></a>Jak používat sadu SDK webové úlohy pro zpracování na pozadí založeného na událostech

Tento článek obsahuje pokyny o tom, jak napsat kód pro [WebJobs SDK](webjobs-sdk-get-started.md). V dokumentaci platí pro verze 2.x a 3.x, pokud není uvedeno jinak jinak. Hlavní změna zaváděné 3.x je použití .NET Core místo rozhraní .NET Framework.

>[!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) je založený na WebJobs SDK a tento článek obsahuje odkazy na Azure Functions dokumentaci pro některá témata. Vezměte na vědomí následující rozdíly mezi funkcemi a WebJobs SDK:
> * Verze Azure funkce 1.x odpovídá WebJobs SDK verze 2.x a Azure Functions 2.x odpovídá WebJobs SDK 3.x. Úložišť zdrojového kódu podle WebJobs SDK číslování a mnoho má v2.x větví s hlavní větve aktuálně s 3.x kódu.
> * Ukázkový kód pro knihovny tříd Azure funkcí jazyka C# je jako WebJobs SDK kódu s výjimkou nepotřebujete `FunctionName` atribut v projektu WebJobs SDK.
> * Některé typy vazeb jsou podporovány pouze v funkce, jako je například HTTP, webhooku a událostí mřížky (která je založena na protokolu HTTP). 
> 
> Další informace najdete v tématu [porovnat WebJobs SDK a Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste četli [začít pracovat s WebJobs SDK](webjobs-sdk-get-started.md).

## <a name="jobhost"></a>JobHost

`JobHost` Objektu je kontejner modulu runtime pro funkce: naslouchá aktivačních událostí a volání funkce. Vytvoříte `JobHost` v kódu a kódu zápisu přizpůsobit své chování.

Toto je zásadní rozdíl mezi pomocí WebJobs SDK přímo a pomocí nepřímo pomocí Azure Functions. V Azure Functions, ovládací prvky služby `JobHost`, a nelze ji upravit tak, že psaní kódu. Azure Functions umožňuje přizpůsobit chování hostitele pomocí nastavení v *host.json* souboru. Tato nastavení jsou řetězce, není kód, který omezuje druhy přizpůsobení, které může provádět.

### <a name="jobhost-connection-strings"></a>JobHost připojovací řetězce

Sada WebJobs SDK hledá úložiště a Service Bus připojovací řetězce v *local.settings.json* při spuštění místně nebo v prostředí webové úlohy při spuštění v Azure. Pokud chcete použít vlastní názvy těchto připojovací řetězce, nebo jejich uložení jinde, můžete ho nastavit v kódu, jak je vidět tady:

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

### <a name="jobhost-development-settings"></a>Nastavení pro vývoj JobHost

`JobHostConfiguration` Třída má `UseDevelopmentSettings` metoda, kterou můžete volat k efektivní místní vývoj. Tady jsou některé nastavení, která tuto metodu změny:

| Vlastnost | Vývoj pro nastavení |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` Chcete-li maximalizovat výstup protokolu. |
| `Queues.MaxPollingInterval`  | Nízkou hodnotu zajistit fronty metody se aktivují okamžitě.  |
| `Singleton.ListenerLockPeriod` | 15 sekund a usnadňuje rychlý iterativní vývoj. |

Následující příklad ukazuje, jak používat nastavení pro vývoj. Chcete-li `config.IsDevelopment` vrátit `true` při místním spuštění nastavit místní prostředí proměnné s názvem `AzureWebJobsEnv` s hodnotou `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>ServicePointManager JobHost – nastavení

Rozhraní .NET Framework obsahuje rozhraní API volat [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , řídí počet souběžných připojení k hostiteli. Doporučujeme zvýšit tuto hodnotu z výchozí hodnotu 2 před zahájením váš hostitel webové úlohy.

Všechny odchozí požadavky HTTP, které provedete z funkce pomocí `HttpClient` procházet skrz `ServicePointManager`. Po kliknutí `DefaultConnectionLimit`, `ServicePointManager` spustí queueing požadavky před jejich odesláním. Předpokládejme, že vaše `DefaultConnectionLimit` nastavena na 2 a váš kód díky 1 000 žádostí HTTP. Na začátku pouze 2 jsou ve skutečnosti povoleno požadavků prostřednictvím operačního systému. Další 998 jsou zařazeny do fronty až do je prostor pro ně. To znamená, vaše `HttpClient` vypršet časový limit, protože ho *rootu předpokládá, že* provedl žádost, ale žádost nikdy odeslal operačního systému na cílový server. Tak může zobrazit chování, které ještě nezačala smysl: na místní `HttpClient` trvá 10 sekund. k dokončení požadavku, ale vrací všechny žádosti o služby v 200 ms. 

Výchozí hodnota pro aplikace ASP.NET je `Int32.MaxValue`, a které by mohlo fungovat i pro webové úlohy spuštěné v plánu služby App Service základní nebo vyšší. Webové úlohy obvykle potřebovat nastavení Always On a který je podporován pouze plány služby App Service úrovně Basic a vyšší. 

Pokud vaše webová úloha běží v volné nebo sdílený plán služby App Service, vaše aplikace je omezené na základě izolovaného prostoru služby App Service, který má aktuálně [limitu připojení 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Limit nevázaný připojení v `ServicePointManager`, je vyšší pravděpodobnost, že prahová hodnota připojení izolovaného prostoru bude dostupný a webu vypnout. V takovém případě nastavení `DefaultConnectionLimit` na jinou hodnotu nižší, jako je 50 nebo 100, tomu zabránili a stále umožňují dostatečná propustnost.

Nastavení musí být nakonfigurovaná, než jsou vytvářeny všechny požadavky HTTP. Z tohoto důvodu se hostitel webové úlohy by se neměl pokoušet upravit nastavení automaticky. může být požadavky HTTP, které udělat předtím, než spustí hostitele, a to může vést k neočekávanému chování. Nejlepším postupem se nastavit hodnotu okamžitě ve vaší `Main` metody před inicializace `JobHost`, jak je znázorněno v následujícím příkladu

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Triggery

Funkce musí být veřejné metody a musí obsahovat jeden atribut aktivační události nebo [NoAutomaticTrigger](#manual-trigger) atribut.

### <a name="automatic-trigger"></a>S automatickou aktivací

Automatické aktivace volání funkce v reakci na událost. Příklad, naleznete v části aktivační událost fronty v [Get Začínáme článku](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Ruční aktivační události

Chcete-li aktivovat funkci ručně, použijte `NoAutomaticTrigger` atributu, jak je znázorněno v následujícím příkladu:

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

## <a name="input-and-output-bindings"></a>Vstup a výstup vazby

Vstupní vazby zadejte deklarativní způsob, jak zpřístupnit data z Azure nebo služby třetích stran, do vašeho kódu. Výstup vazby poskytují způsob, jak aktualizovat data. [Začínáme článku Get](webjobs-sdk-get-started.md) ukazuje příklad jednotlivých.

Můžete návratovou hodnotu metoda pro vazbu výstup použitím atribut metoda návratovou hodnotu. Podívejte se na příklad v Azure Functions [triggerů a vazeb](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) článku.

## <a name="binding-types"></a>Vazby typů

Následující typy aktivační události a vazby jsou součástí `Microsoft.Azure.WebJobs` balíčku:

* Blob Storage
* Queue Storage
* Úložiště Table

Pokud chcete použít jiné aktivační události a typ vazby, nainstalujte balíček NuGet, který je obsahuje a volání `Use<binding>` metodu `JobHostConfiguration` objektu. Například pokud chcete použít aktivaci časovačem, nainstalujte `Microsoft.Azure.WebJobs.Extensions` a volání `UseTimers` v `Main` metoda jako v následujícím příkladu:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Můžete najít balíček k instalaci pro konkrétní vazbu typ v **balíčky** části Typ vazby, že [článku](#binding-reference-information) pro Azure Functions. Výjimkou je aktivační událost soubory a vazbu (pro místního systému souborů), který nepodporuje Azure Functions. použít soubory vazby, nainstalujte `Microsoft.Azure.WebJobs.Extensions` a volání `UseFiles`.

### <a name="usecore"></a>UseCore

`Microsoft.Azure.WebJobs.Extensions` Balíček již bylo zmíněno dříve také poskytuje speciální vazby typ, který můžete zaregistrovat pomocí volání `UseCore` metoda. Tato vazba umožňuje definovat [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) parametr ve vašem podpis funkce. Objekt context umožňuje přístup k ID vyvolání, které můžete použít ke korelaci všechny protokoly vyprodukované volání dané funkce. Tady je příklad:

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

## <a name="binding-configuration"></a>Konfigurace vazeb

Mnoho aktivovat a vytvoření vazby typy umožňují nakonfigurovat jejich chování nastavením vlastnosti v objektu konfigurace, kterou předáte do `JobHost`.

### <a name="queue-trigger-configuration"></a>Konfigurace aktivace fronty

Nastavení můžete nakonfigurovat pro aktivační událost fronty úložiště je podrobně popsaný Azure Functions [host.json odkaz](../azure-functions/functions-host-json.md#queues). Postup nastavení je v projektu WebJobs SDK je znázorněno v následujícím příkladu:

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

### <a name="configuration-for-other-bindings"></a>Konfigurace pro jiné vazby

Některé typy aktivační události a vazba definovat vlastní typ vlastní konfigurace. Například soubor aktivační událost umožňuje určit kořenovou cestou za účelem monitorování:

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

V atributu konstruktoru parametry můžete použít výrazy, které odkazují na hodnoty z různých zdrojů. Například v následujícím kódu, cesta k `BlobTrigger` atribut vytvoří výraz s názvem `filename`. Pokud se používá pro vazbu výstup `filename` překládá název spouštěcí objekt blob.
 
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

Další informace o výrazech vazby najdete v tématu [vazby výrazy a vzory](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) v dokumentaci k Azure Functions.

### <a name="custom-binding-expressions"></a>Výrazy vlastní vazby

Někdy chcete zadat název fronty, název objektu blob nebo kontejner, nebo tabulku název v kódu než pevný kódu. Například můžete chtít zadejte název fronty `QueueTrigger` atribut v konfiguraci souboru nebo prostředí proměnné.

Můžete to udělat pomocí předávání `NameResolver` do objektu `JobHostConfiguration` objektu. Obsahují zástupné symboly v aktivační události nebo vazby atributu konstruktoru parametrů a `NameResolver` kód obsahuje skutečné hodnoty má být použit místo tyto zástupné symboly. Zástupné symboly jsou identifikovány tím, že je s znaky procenta (%), jak je znázorněno v následujícím příkladu:
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Tento kód vám umožní používat frontu s názvem logqueuetest v testovacím prostředí a jednu s názvem logqueueprod v produkčním prostředí. Místo pevně fronty název, zadejte název záznamu v `appSettings` kolekce. 

Je výchozí NameResolver, která přijímá vliv, pokud nezadáte vlastní. Výchozí hodnota získá hodnoty z nastavení aplikace nebo proměnné prostředí.

Vaše `NameResolver` třída získá název fronty z `appSettings` jak je znázorněno v následujícím příkladu:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Předat vaše `NameResolver` třídy v do `JobHost` objektu, jak je znázorněno v následujícím příkladu:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure implementuje funkce `INameResolver` k získání hodnoty z nastavení aplikace, jak je znázorněno v příkladu. Použijete-li WebJobs SDK přímo, můžete napsat vlastní implementaci, který získá zástupný symbol nahrazení hodnoty z jakéhokoli zdroje dáváte přednost. 

## <a name="binding-at-runtime"></a>Vazba za běhu

Pokud potřebujete nějakou práci ve vašem funkci před použitím atribut vazby, jako `Queue`, `Blob`, nebo `Table`, můžete použít `IBinder` rozhraní.

Následující příklad přebírá zprávu vstupní fronty a vytvoří novou zprávu se stejným obsahem v výstupní fronty. Název fronty výstupu je nastavena podle kódu v těle funkce.

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

Další informace najdete v tématu [vazby za běhu](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) v dokumentaci k Azure Functions.

## <a name="binding-reference-information"></a>Vazba referenční informace

Referenční informace o jednotlivých typech vazby najdete v dokumentaci k Azure Functions. Jako příklad použijeme fronty úložiště, najdete následující informace v článku o každé vazby:

* [Balíčky](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) -co balíček pro instalaci tak, aby zahrnovaly podporu pro vazbu v projektu WebJobs SDK.
* [Příklady](../azure-functions/functions-bindings-storage-queue.md#trigger---example) – C# – třída knihovny příklad se týká WebJobs SDK; právě vynechejte `FunctionName` atribut.
* [Atributy](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) – atributy, které se pro typ vazby použít.
* [Konfigurace](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) -vysvětlení konstruktor parametrů a vlastností atributu.
* [Využití](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) – co typy, které můžete vázat na a informace, jak funguje vazby. Příklad: dotazování algoritmus, škodlivých zpracování fronty.
  
Seznam vazby odkaz na články, naleznete v části **podporované vazby** v [triggerů a vazeb](../azure-functions/functions-triggers-bindings.md#supported-bindings) Azure Functions najdete v článku. V tomto seznamu vazby HTTP, webhooku a mřížky události jsou podporovány pouze funkce Azure, ne podle WebJobs SDK.

## <a name="disable-attribute"></a>Zakázat atribut 

[Zakázat](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atribut vám umožní určit, zda funkce můžete spustit. 

V následujícím příkladu Pokud nastavení aplikace `Disable_TestJob` má hodnotu "1" nebo "PRAVDA" (malá a velká písmena), funkce se nespustí. V takovém případě modul runtime vytvoří zprávu protokolu *je zakázána funkce 'Functions.TestJob'*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Pokud můžete změnit hodnoty nastavení aplikace v portálu Azure, budou úlohu restartovat, výběr se nové nastavení.

Atribut lze deklarovat na parametr, metoda nebo úrovni třídy. Název nastavení může také obsahovat výrazy vazby.

## <a name="timeout-attribute"></a>Časový limit atribut

[Časový limit](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) atributu způsobí, že funkce, která se zruší, pokud se nemá dokončit během zadaného časového intervalu. V následujícím příkladu spustí funkce pro jeden den bez časového limitu. S časovým limitem funkce zrušeny po 15 sekund.

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

Můžete použít na úrovni třída nebo metoda atributu časového limitu, a zadáte globálního časového limitu pomocí `JobHostConfiguration.FunctionTimeout`. Třída nebo metoda úrovně časové limity přepsat globálního časového limitu.

## <a name="singleton-attribute"></a>Atribut typu singleton

Použití [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) atribut zajistit, že pouze jedna instance funkce spustí, i když je spuštěno více instancí hostitele webové aplikace. Dělá to pomocí implementace [distribuované zamykání](#viewing-lease-blobs).

V následujícím příkladu, pouze jednu instanci `ProcessImage` funkce spuštěná v každém okamžiku:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Některé aktivační události mít integrovanou podporu pro správu souběžnosti:

* **QueueTrigger** – nastavte `JobHostConfiguration.Queues.BatchSize` na 1.
* **ServiceBusTrigger** – nastavte `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` na 1.
* **FileTrigger** – nastavte `FileProcessor.MaxDegreeOfParallelism` na 1.

Tato nastavení můžete použít k zajištění, že funkce běží jako typ singleton do jedné instance. Aby webové aplikace horizontálně navýší kapacitu na více instancí je spuštěný pouze jedna instance funkce, použije Singleton zámek úrovně naslouchací proces na funkce (`[Singleton(Mode = SingletonMode.Listener)]`). Při spuštění JobHost jsou získat zámky naslouchací proces. Pokud ve stejnou dobu tři instance upraveným všechny, spusťte jenom jedna z instancí získá zámek a spustí jenom jeden naslouchací proces.

### <a name="scope-values"></a>Hodnoty rozsahu

Můžete zadat **obor nebo hodnota výrazu** na Singleton, která zajistí, že všechny spuštěních funkce v tomto oboru budou serializována. Implementace podrobnější zamykání tímto způsobem můžete povolit pro určité úrovně paralelismus pro funkce, při serializaci další volání, jak je stanoveno podle požadavků. Například v následujícím příkladu výrazu oboru váže `Region` hodnotu příchozí zprávy. Pokud fronty obsahuje 3 zprávy v oblasti "Východ", "Východ" a "Západ" v uvedeném pořadí zprávy, které mají oblast, kterou "Východ" budou prováděny sériově při zprávě s oblast, kterou paralelně s těmi, která bude proveden "Západní".

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

Výchozí obor na zámek `SingletonScope.Function` znamená obor zámků (cesta zapůjčení objektů blob) je vázaný na funkce plně kvalifikovaný název. Zamknout mezi funkce, zadejte `SingletonScope.Host` a používat název ID oboru, který je stejný pro všechny funkce, které nechcete spustit současně. V následujícím příkladu, pouze jedna instance `AddItem` nebo `RemoveItem` najednou spuštěna:

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

Využívá sadu WebJobs SDK [objektů blob v Azure zapůjčení](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) skrytě k implementaci distribuovaného uzamčení. Objekty BLOB zapůjčení používané Singleton najdete v `azure-webjobs-host` kontejneru `AzureWebJobsStorage` účet úložiště v cestě "zamknout". Například cesta blobu zapůjčení pro první `ProcessImage` příkladu dříve může být `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Všechny cesty JobHost ID, zahrnout do této případu 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Asynchronní funkce

Informace o tom, jak funkce asynchronní kódu, naleznete v dokumentaci k Azure Functions na [funkce asynchronní](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Zrušení tokenů

Informace o způsobu zpracování zrušení tokenů, naleznete v dokumentaci k Azure Functions na [zrušení tokenů a řádné vypnutí](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Více instancí

Pokud vaše webová aplikace běží na několik instancí, nepřetržitá webová úloha běží na každou instanci naslouchá pro aktivační události a volání funkcí. Různé vazby aktivační události jsou navrženy pro efektivní sdílet práci společně napříč instancemi, tak, aby škálování na více instancí umožňuje zpracovávat další zátěž.

Aktivace fronty a objektů blob automaticky zabránilo ve zpracování zprávy fronty funkce nebo objektu blob více než jednou; funkce nemusí být idempotent.

Aktivační událost časovače automaticky zajistí že pouze jedna instance spustí časovač, takže neobdržíte více než jedna instance funkce spuštěn v okamžiku naplánované.

Pokud chcete zajistit, že pouze jedna instance funkce spustí i v případě, že existuje více instancí hostitele webové aplikace, můžete použít [Singleton](#singleton) atribut.
    
## <a name="filters"></a>Filtry 

Funkce filtry (preview) poskytují způsob, jak přizpůsobit spouštěcí kanál webové úlohy s vlastní logikou. Filtry jsou podobné [ASP.NET Core filtry](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Mohou být implementovány jako deklarativní atributy, které se používají pro funkce nebo třídy. Další informace najdete v tématu [filtry funkce](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Doporučujeme, abyste rozhraní protokolování, která byla vyvinuta pro technologii ASP.NET a [Začínáme](webjobs-sdk-get-started.md) článek ukazuje, jak ji používat. 

### <a name="log-filtering"></a>Filtrování protokolu

Každý protokol vytvořený `ILogger` má přidruženou instanci `Category` a `Level`. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) je výčet a kód celé číslo označuje relativní důležitost:

|LogLevel    |Kód|
|------------|---|
|Trasování       | 0 |
|Ladění       | 1 |
|Informace | 2 |
|Upozornění     | 3 |
|Chyba       | 4 |
|Důležité    | 5 |
|Žádný        | 6 |

Každou kategorii lze filtrovat nezávisle na konkrétní [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel). Například můžete chtít zobrazit všechny protokoly pro zpracování objektů blob aktivační událost ale jenom `Error` a vyšší všem ostatním.

Aby bylo snazší k určení pravidel filtrování, poskytuje WebJobs SDK `LogCategoryFilter` , mohou být předány do řadu existující poskytovatelů protokolování, včetně Application Insights a konzoly.

`LogCategoryFilter` Má `Default` vlastnost s počáteční hodnotou `Information`, znamená to, že všechny zprávy s úrovní `Information`, `Warning`, `Error`, nebo `Critical` přihlášení, ale všechny zprávy s úrovní `Debug` nebo `Trace` jsou filtrovány rychle.

`CategoryLevels` Vlastnost umožňuje určit úrovních protokolování pro specifických kategorií, můžete upřesnit výstup protokolování. Pokud není nalezena žádná shoda v rámci `CategoryLevels` slovník filtr spadne zpět na `Default` hodnota při rozhodování, jestli se k filtrování zprávy.

Následující příklad vytvoří filtr, který ve výchozím nastavení všechny protokoly na filtry `Warning` úroveň. Kategorie `Function` nebo `Host.Results` jsou filtrovány na `Error` úroveň. `LogCategoryFilter` Porovná aktuální kategorii, kterou chcete všechny registrované `CategoryLevels` a vybere nejdelší shody. To znamená, že `Debug` úroveň zaregistrovat pro `Host.Triggers` bude shodovat s `Host.Triggers.Queue` nebo `Host.Triggers.Blob`. To umožňuje řídit širší kategorie bez nutnosti přidání každé z nich.

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

### <a name="custom-telemetry-for-application-insights"></a>Vlastní telemetrii Application Insights

Interně `TelemetryClient` vytvořené poskytovateli Application Insights pro využívá sadu WebJobs SDK [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Když koncový bod služby Application Insights je nedostupná nebo omezení příchozí požadavky a tento kanál [uloží požadavky v systému souborů webovou aplikaci a znovu později odešle](http://apmtips.com/blog/2015/09/03/more-telemetry-channels).

`TelemetryClient` Vytvoří třídu, která implementuje `ITelemetryClientFactory`. Ve výchozím nastavení je to [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Pokud chcete upravit libovolná součást kanálu Application Insights, můžete zadat vlastní `ITelemetryClientFactory`, a Hostitel použije k vytvoření vlastní třídy `TelemetryClient`. Například tento kód přepíše `DefaultTelemetryClientFactory` k úpravě vlastností `ServerTelemetryChannel`:

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

Nakonfiguruje objekt SamplingPercentageEstimatorSettings [adaptivního vzorkování](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). To znamená, že v některých scénářích vysoký počet aplikace Insights odesílá vybranou podmnožinu telemetrická data na server.

Po vytvoření objektu pro vytváření telemetrie, předejte ji v poskytovateli protokolování Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Další kroky

Tato příručka poskytl fragmenty kódu, které ukazují, jak ke zpracování běžných scénářů pro práci s WebJobs SDK. Kompletní ukázky naleznete v části [azure webjobs sdk ukázky](https://github.com/Azure/azure-webjobs-sdk-samples).