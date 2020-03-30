---
title: Odkaz na vývojáře skriptů Azure Functions C#
description: Pochopit, jak vyvíjet funkce Azure pomocí skriptu C#.
author: craigshoemaker
ms.topic: reference
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 76af1f51c83e9554a51e6c17266fac739e6bd6b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276813"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions C# odkaz na vývojáře (.csx)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Tento článek je úvod do vývoje funkcí Azure pomocí skriptu C# (*.csx*).

Funkce Azure podporuje programovací jazyky skriptů C# a C#. Pokud hledáte pokyny k [použití jazyka C# v projektu knihovny tříd sady Visual Studio](functions-develop-vs.md), přečtěte si odkaz na [vývojáře jazyka C#](functions-dotnet-class-library.md).

Tento článek předpokládá, že jste si už [přečetli průvodce vývojáři Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Jak .csx funguje

Prostředí skriptu C# pro funkce Azure je založené na [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Data proudí do funkce C# prostřednictvím argumentů metody. Názvy argumentů `function.json` jsou zadány v souboru a existují předdefinované názvy pro přístup k věcem, jako je tokeny funkce a zrušení.

Formát *.csx* umožňuje psát méně "často používaný text" a zaměřit se na psaní pouze c# funkce. Namísto obtékání vše v oboru `Run` názvů a třídy, stačí definovat metodu. Zahrnout všechny odkazy sestavení a obory názvů na začátku souboru jako obvykle.

Soubory *.csx* aplikace funkce jsou zkompilovány při inicializování instance. Tento krok kompilace znamená, že věci, jako je studený start může trvat déle pro funkce skriptu Jazyka C# ve srovnání s knihovnami tříd jazyka C#. Tento krok kompilace je také důvod, proč c# skript funkce jsou upravitelné na webu Azure Portal, zatímco knihovny tříd C# nejsou.

## <a name="folder-structure"></a>Struktura složek

Struktura složek pro projekt skriptu jazyka C# vypadá takto:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

K dispozici je sdílený soubor [host.json,](functions-host-json.md) který lze použít ke konfiguraci aplikace funkce. Každá funkce má svůj vlastní soubor kódu (.csx) a konfigurační soubor vazby (function.json).

V `extensions.csproj` souboru jsou definovány rozšíření vazby vyžadované ve [verzi 2.x a novějších verzích](functions-versions.md) runtime Funkce se skutečnými soubory knihovny ve `bin` složce. Při vývoji místně, musíte [zaregistrovat rozšíření vazby](./functions-bindings-register.md#extension-bundles). Při vývoji funkcí na webu Azure Portal se tato registrace provádí za vás.

## <a name="binding-to-arguments"></a>Vazba na argumenty

Vstupní nebo výstupní data jsou vázána na `name` parametr funkce skriptu jazyka C# prostřednictvím vlastnosti v konfiguračním souboru *function.json.* Následující příklad ukazuje soubor *function.json* a *soubor run.csx* pro funkci aktivovanou frontou. Parametr, který přijímá data ze zprávy `myQueueItem` fronty, je pojmenován, `name` protože se jedná o hodnotu vlastnosti.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

Prohlášení `#r` je vysvětleno [dále v tomto článku](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Podporované typy pro vazby

Každá vazba má své vlastní podporované typy; například aktivační událost objektu blob lze použít s parametrem `CloudBlockBlob` řetězce, parametrem POCO, parametrem nebo některou z několika dalších podporovaných typů. Odkaz [na vazbu pro vazby objektů blob](functions-bindings-storage-blob-trigger.md#usage) uvádí všechny podporované typy parametrů pro aktivační události objektů blob. Další informace naleznete v [tématu Aktivační události a vazby](functions-triggers-bindings.md) a [vazby referenční dokumenty pro každý typ vazby](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Odkazování na vlastní třídy

Pokud potřebujete použít vlastní třídu Prostý starý objekt CLR (POCO), můžete zahrnout definici třídy do stejného souboru nebo ji umístit do samostatného souboru.

Následující příklad ukazuje příklad *run.csx,* který obsahuje definici třídy POCO.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Třída POCO musí mít pro každou vlastnost definováno getter a setter.

## <a name="reusing-csx-code"></a>Opětovné použití kódu .csx

Třídy a metody definované v jiných souborech *CSX* můžete použít v souboru *run.csx.* Chcete-li to `#load` provést, použijte direktivy v souboru *run.csx.* V následujícím příkladu je `MyLogger` rutina protokolování s názvem sdílena v *souboru myLogger.csx* a načtena do *souboru run.csx* pomocí direktivy: `#load`

Příklad *run.csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Příklad *mylogger.csx*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Použití sdíleného souboru *.csx* je běžný vzor, pokud chcete silně zadávat data předávaná mezi funkcemi pomocí objektu POCO. V následujícím zjednodušeném příkladu aktivační událost HTTP a `Order` aktivační událost fronty sdílejí objekt POCO s názvem silně zadejte data objednávky:

Příklad *spuštění souboru CSX* pro aktivační událost HTTP:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Příklad *spuštění.csx* pro aktivační událost fronty:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Příklad *order.csx*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +
                  "\n\tcustAddress : " + custAddress +
                  "\n\tcustEmail : " + custEmail +
                  "\n\tcartId : " + cartId + "\n}";
    }
}
```

Relativní cestu můžete použít `#load` se směrnicí:

* `#load "mylogger.csx"`načte soubor umístěný ve složce funkcí.
* `#load "loadedfiles\mylogger.csx"`načte soubor umístěný ve složce ve složce funkcí.
* `#load "..\shared\mylogger.csx"`načte soubor umístěný ve složce na stejné úrovni jako složka funkcí, tedy přímo pod *wwwroot*.

Směrnice `#load` funguje pouze se soubory *.csx,* nikoli se soubory *.cs.*

## <a name="binding-to-method-return-value"></a>Vazba na vrácenou hodnotu metody

Vrácená hodnota metody pro výstupní vazbu můžete `$return` použít pomocí názvu v *souboru function.json*. Příklady naleznete v tématu [Aktivační události a vazby](./functions-bindings-return-value.md).

Vrácenou hodnotu použijte pouze v případě, že úspěšné spuštění funkce vždy vede k návratové hodnotě, která má být předávána výstupní vazbě. V opačném `ICollector` `IAsyncCollector`případě použijte nebo , jak je znázorněno v následující části.

## <a name="writing-multiple-output-values"></a>Zápis více výstupních hodnot

Chcete-li zapsat více hodnot do výstupní vazby nebo pokud úspěšné vyvolání funkce nemusí [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) mít za následek nic předat výstupní vazbě, použijte typy nebo. Tyto typy jsou kolekce pouze pro zápis, které jsou zapsány do výstupní vazby po dokončení metody.

Tento příklad zapisuje více `ICollector`zpráv fronty do stejné fronty pomocí :

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>protokolování

Chcete-li protokolovat výstup do protokolů datových proudů v c#, zahrňte argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Doporučujeme, abyste `log`jej pojmenovali . Nepoužívejte `Console.Write` v Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Informace o novější rozhraní protokolování, které `TraceWriter`můžete použít místo , naleznete v tématu [Zápis protokolů ve funkcích jazyka C#](functions-monitoring.md#write-logs-in-c-functions) v článku **Monitorování funkcí Azure.**

## <a name="async"></a>Async

Chcete-li funkci [asynchronní](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), `async` použijte klíčové `Task` slovo a vraťte objekt.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Parametry nelze `out` použít v asynchronních funkcích. Pro výstupní vazby použijte [vrácenou hodnotu funkce](#binding-to-method-return-value) nebo [objekt kolektoru.](#writing-multiple-output-values)

## <a name="cancellation-tokens"></a>Tokeny zrušení

Funkce může přijmout [CancellationToken](/dotnet/api/system.threading.cancellationtoken) parametr, který umožňuje operačnímu systému upozornit váš kód, když se má funkce ukončit. Toto oznámení můžete použít k ujistěte se, že funkce neukončí neočekávaně způsobem, který ponechává data v nekonzistentním stavu.

Následující příklad ukazuje, jak zkontrolovat blížící se ukončení funkce.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>Import oborů názvů

Pokud potřebujete importovat obory názvů, můžete tak `using` učinit jako obvykle, s klauzulí.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Následující obory názvů jsou automaticky importovány, a proto jsou volitelné:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Odkazování na externí sestavení

Pro rozhraní sestavení, přidejte odkazy `#r "AssemblyName"` pomocí směrnice.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Hostitelské prostředí Azure Functions automaticky přidává následující sestavení:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Následující sestavení mohou být odkazována jednoduchým názvem `#r "AssemblyName"`(například ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Odkazování na vlastní sestavení

Chcete-li odkazovat na vlastní sestavení, můžete použít sdílené *sestavení* nebo *soukromé* sestavení:

* Sdílená sestavení jsou sdílena ve všech funkcích v rámci aplikace funkce. Chcete-li odkazovat na vlastní sestavení, `bin` nahrajte sestavení do složky pojmenované v [kořenové složce aplikace funkce](functions-reference.md#folder-structure) (wwwroot).

* Privátní sestavení jsou součástí kontextu dané funkce a podporují boční načítání různých verzí. Soukromá sestavení by měla `bin` být odeslána do složky v adresáři funkcí. Odkazna sestavení pomocí názvu souboru, například `#r "MyAssembly.dll"`.

Informace o nahrávání souborů do složky funkcí naleznete v části [Správa balíčků](#using-nuget-packages).

### <a name="watched-directories"></a>Sledované adresáře

Adresář, který obsahuje soubor skriptu funkce, je automaticky sledován pro změny v sestaveních. Chcete-li sledovat změny sestavení v jiných `watchDirectories` adresářích, přidejte je do seznamu v [souboru host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Použití balíčků NuGet
Chcete-li použít balíčky NuGet ve funkci 2.x a novější C#, nahrajte soubor *function.proj* do složky funkce v systému souborů aplikace funkce. Zde je příklad souboru *function.proj,* který přidává odkaz na *Microsoft.ProjectOxford.Face* verze *1.1.0*:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Chcete-li použít vlastní informační kanál NuGet, zadejte informační kanál v souboru *Nuget.Config* v kořenovém adresáři aplikace funkce. Další informace naleznete [v tématu Configuring NuGet behavior](/nuget/consume-packages/configuring-nuget-behavior).

> [!NOTE]
> Ve funkcích 1.x C# jsou balíčky NuGet odkazovány souborem *project.json* namísto souboru *function.proj.*

Pro funkce 1.x použijte soubor *project.json.* Zde je příklad souboru *project.json:*

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Použití souboru function.proj

1. Otevřete funkci na webu Azure Portal. Na kartě protokoly se zobrazí výstup instalace balíčku.
2. Chcete-li nahrát soubor *function.proj,* použijte jednu z metod popsaných v tématu [Jak aktualizovat soubory aplikací funkcí](functions-reference.md#fileupdate) v tématu odkazu pro vývojáře Azure Functions.
3. Po nahrání souboru *function.proj* se výstup jako následující příklad zobrazí v protokolu streamování funkce:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Proměnné prostředí

Chcete-li získat proměnnou prostředí nebo `System.Environment.GetEnvironmentVariable`hodnotu nastavení aplikace, použijte , jak je znázorněno v následujícím příkladu kódu:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a>

## <a name="binding-at-runtime"></a>Vazba za běhu

V jazycích C# a dalších jazycích .NET můžete použít [imperativní](https://en.wikipedia.org/wiki/Imperative_programming) vzor vazby, na rozdíl od [*deklarativních*](https://en.wikipedia.org/wiki/Declarative_programming) vazeb v *souboru function.json*. Imperativní vazba je užitečná, když je třeba vypočítat parametry vazby za běhu, nikoli za čas návrhu. Pomocí tohoto vzoru můžete vázat na podporované vstupní a výstupní vazby průběžně v kódu funkce.

Definujte imperativní vazbu takto:

- **Do not** Nezahrnujte položku v *function.json* pro požadované imperativní vazby.
- Předat vstupní parametr [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)nebo .
- K provedení datové vazby použijte následující vzor jazyka C#.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute`je atribut .NET, který definuje `T` vazby a je vstupní nebo výstupní typ, který je podporován tímto typem vazby. `T`nemůže být `out` typ parametru `out JObject`(například ). Například vazby výstupu tabulky mobilní aplikace podporuje [šest typů výstupu](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale můžete použít pouze [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) pro `T`.

### <a name="single-attribute-example"></a>Příklad jednoho atributu

Následující ukázkový kód vytvoří [výstupní vazbu objektu blob úložiště](functions-bindings-storage-blob-output.md) s cestou objektu blob, která je definována za běhu, a pak zapíše řetězec do objektu blob.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[Objekt BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definuje vstupní nebo výstupní vazbu [objektu blob úložiště](functions-bindings-storage-blob.md) a [TextWriter](/dotnet/api/system.io.textwriter) je podporovaný typ vazby výstupu.

### <a name="multiple-attribute-example"></a>Příklad více atributů

V předchozím příkladu získá nastavení aplikace pro hlavní aplikace pro připojení `AzureWebJobsStorage`účtu úložiště řetězce funkce (což je). Můžete zadat vlastní nastavení aplikace, které se má použít pro účet úložiště `BindAsync<T>()`přidáním [atributu StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) a předáním pole atributů do aplikace . Použijte `Binder` parametr, `IBinder`nikoli .  Například:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

V následující tabulce jsou uvedeny atributy .NET pro každý typ vazby a balíčky, ve kterých jsou definovány.

> [!div class="mx-codeBreakAll"]
> | Vazba | Atribut | Přidání odkazu |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Fronta úložiště | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Objekt blob úložiště | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tabulka úložiště | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Další informace o doporučených postupech pro funkce Azure](functions-best-practices.md)
