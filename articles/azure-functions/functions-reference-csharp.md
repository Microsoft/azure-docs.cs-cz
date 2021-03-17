---
title: Referenční dokumentace pro vývojáře skriptu v jazyce C# Azure Functions
description: Naučte se vyvíjet Azure Functions pomocí skriptu jazyka C#.
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 48614640660da6d85face5ea416d267fa9f59515
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164835"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions referenční příručka pro vývojáře skriptu jazyka C# (. csx)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Tento článek představuje úvod k vývoji Azure Functions pomocí skriptu jazyka C# (*. csx*).

Azure Functions podporuje programovací jazyky C# a C#. Pokud hledáte pokyny k [používání jazyka C# v projektu knihovny tříd sady Visual Studio](functions-develop-vs.md), přečtěte si téma [referenční informace pro vývojáře v jazyce c#](functions-dotnet-class-library.md).

V tomto článku se předpokládá, že už jste si přečetli [příručku pro vývojáře Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Jak funguje. csx

Prostředí skriptu C# pro Azure Functions je založené na sadě [SDK pro Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Data toků do funkce jazyka C# prostřednictvím argumentů metody. V souboru jsou zadány názvy argumentů `function.json` a existují předdefinované názvy pro přístup k objektům, jako je protokolování funkcí a tokeny zrušení.

Formát *. csx* umožňuje napsat méně "často používaný" text a soustředit se na zápis pouze do funkce jazyka C#. Místo zabalení všeho v oboru názvů a třídě stačí definovat `Run` metodu. Zahrňte na začátek souboru odkazy na sestavení a obory názvů jako obvykle.

Soubory *. csx* aplikace Function App jsou kompilovány při inicializaci instance. Tento krok kompilace znamená, že u funkcí skriptu jazyka C#, které jsou ve srovnání s knihovnami tříd C#, může trvat déle jako studený start. Tento krok kompilace také znamená, že funkce skriptu jazyka C# jsou v Azure Portal upravovat, zatímco knihovny tříd jazyka C# nejsou.

## <a name="folder-structure"></a>Struktura složek

Struktura složek pro projekt skriptu jazyka C# vypadá následovně:

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

V souboru je sdílený [host.js](functions-host-json.md) , který se dá použít ke konfiguraci aplikace Function App. Každá funkce má svůj vlastní soubor kódu (. csx) a konfigurační soubor vazby (function.json).

Rozšíření vazby požadovaná ve [verzi 2. x a novějších verzích](functions-versions.md) modulu runtime Functions jsou v souboru definovány se `extensions.csproj` skutečnými soubory knihoven ve `bin` složce. Při vývoji místně je nutné [zaregistrovat rozšíření vazby](./functions-bindings-register.md#extension-bundles). Při vývoji funkcí v Azure Portal se tato registrace provede za vás.

## <a name="binding-to-arguments"></a>Vazba na argumenty

Vstupní nebo výstupní data jsou svázána s parametrem funkce skriptu jazyka C# prostřednictvím `name` vlastnosti v *function.js* v konfiguračním souboru. Následující příklad ukazuje *function.js* souboru a *spuštění souboru. csx* pro funkci aktivovanou frontou. Parametr, který přijímá data ze zprávy ve frontě, je pojmenován `myQueueItem` , protože to je hodnota `name` Vlastnosti.

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

`#r`Prohlášení je vysvětleno [dále v tomto článku](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Podporované typy pro vazby

Každá vazba má své vlastní podporované typy; například Trigger objektu BLOB lze použít s parametrem řetězce, parametrem POCO, `CloudBlockBlob` parametrem nebo jakýmkoli jiným podporovaným typem. [Článek odkazu vazby pro vazby objektů BLOB](functions-bindings-storage-blob-trigger.md#usage) obsahuje seznam všech podporovaných typů parametrů pro triggery objektů BLOB. Další informace najdete v tématech [triggery a vazby](functions-triggers-bindings.md) a [Referenční dokumentace k vazbě pro každý typ vazby](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Odkazování na vlastní třídy

Pokud potřebujete použít vlastní třídu typu CLR (POCO), můžete zahrnout definici třídy do stejného souboru nebo ji umístit do samostatného souboru.

Následující příklad ukazuje příklad *Run. csx* , který obsahuje definici třídy POCO.

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

Třída POCO musí mít pro každou vlastnost definovanou metodu getter a setter.

## <a name="reusing-csx-code"></a>Znovu se používá kód. csx

Můžete použít třídy a metody definované v jiných souborech *. csx* v souboru *Run. csx* . K tomu použijte `#load` direktivy v souboru *Run. csx* . V následujícím příkladu je rutina s názvem `MyLogger` sdílena v *myLogger. csx* a načtena do *Run. csx* pomocí `#load` direktivy:

Příklad *spuštění. csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Příklad *MyLogger. csx*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Použití sdíleného souboru *. csx* je běžný vzor, pokud chcete silného typu dat předávaných mezi funkcemi pomocí objektu POCO. V následujícím zjednodušeném příkladu aktivační událost HTTP a Trigger fronty sdílí objekt POCO s názvem `Order` pro silné zadání dat objednávky:

Příklad *spuštění. csx* pro Trigger http:

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

Příklad *spuštění. csx* pro aktivační událost fronty:

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

Příklad *Order. csx*:

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

Můžete použít relativní cestu k této `#load` direktivě:

* `#load "mylogger.csx"` Načte soubor umístěný ve složce Functions.
* `#load "loadedfiles\mylogger.csx"` Načte soubor umístěný ve složce ve složce Functions.
* `#load "..\shared\mylogger.csx"` Načte soubor umístěný do složky na stejné úrovni jako složka funkcí, která je přímo pod uzlem *wwwroot*.

`#load`Direktiva funguje pouze se soubory *. csx* , nikoli s *příponou. cs* .

## <a name="binding-to-method-return-value"></a>Vazba na návratovou hodnotu metody

Můžete použít návratovou hodnotu metody pro výstupní vazbu pomocí názvu `$return` v *function.js*. Příklady najdete v tématu [triggery a vazby](./functions-bindings-return-value.md).

Návratovou hodnotu použijte pouze v případě, že úspěšné spuštění funkce vždy způsobí návratovou hodnotu, která bude předána výstupní vazbě. V opačném případě použijte `ICollector` nebo `IAsyncCollector` , jak je znázorněno v následující části.

## <a name="writing-multiple-output-values"></a>Zápis více výstupních hodnot

Chcete-li zapsat více hodnot do výstupní vazby nebo pokud úspěšné vyvolání volání funkce nevede k žádnému předání do výstupní vazby, použijte [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typy nebo. Tyto typy jsou kolekce pouze pro zápis, které jsou zapsány do výstupní vazby po dokončení metody.

Tento příklad zapisuje do stejné fronty více zpráv fronty pomocí `ICollector` :

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>protokolování

Chcete-li protokolovat výstup do protokolů streamování v jazyce C#, zahrňte argument typu [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). Doporučujeme vám ho pojmenovat `log` . Nepoužívejte `Console.Write` v Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Informace o novějším rozhraní protokolování, které lze použít místo `TraceWriter` , najdete v dokumentaci k [ILogger](functions-dotnet-class-library.md#ilogger) v příručce pro vývojáře knihovny tříd .NET.

### <a name="custom-metrics-logging"></a>Protokolování vlastních metrik

`LogMetric`Metodu rozšíření pro můžete použít `ILogger` k vytvoření vlastních metrik v Application Insights. Zde je příklad volání metody:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Tento kód je alternativou pro volání `TrackMetric` pomocí rozhraní Application Insights API pro rozhraní .NET.

## <a name="async"></a>Async

Chcete-li provést [asynchronní](/dotnet/csharp/programming-guide/concepts/async/)funkci, použijte `async` klíčové slovo a vraťte `Task` objekt.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

`out`V asynchronních funkcích nemůžete použít parametry. Pro výstupní vazby použijte místo toho [vrácenou hodnotu funkce](#binding-to-method-return-value) nebo [objekt sběrače](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Tokeny zrušení

Funkce může přijmout parametr [CancellationToken](/dotnet/api/system.threading.cancellationtoken) , který umožňuje operačnímu systému upozornit váš kód, když bude funkce ukončena. Toto oznámení můžete použít k tomu, abyste se ujistili, že funkce nekončí nečekaně způsobem, který opustí data v nekonzistentním stavu.

Následující příklad ukazuje, jak kontrolovat blížící se ukončení funkce.

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

Pokud potřebujete importovat obory názvů, můžete to udělat jako obvykle s `using` klauzulí.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Následující obory názvů se naimportují automaticky a jsou proto volitelné:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Odkazování na externí sestavení

Pro sestavení rozhraní přidejte odkazy pomocí `#r "AssemblyName"` direktivy.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Následující sestavení jsou automaticky přidána pomocí hostitelského prostředí Azure Functions:

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

Na následující sestavení může být odkazováno pomocí jednoduchého názvu (například `#r "AssemblyName"` ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Odkazování na vlastní sestavení

Chcete-li odkazovat na vlastní sestavení, můžete použít buď *sdílené* sestavení, nebo *soukromé* sestavení:

* Sdílená sestavení se sdílejí napříč všemi funkcemi v rámci aplikace Function App. Chcete-li odkazovat na vlastní sestavení, nahrajte sestavení do složky s názvem `bin` v [kořenové složce aplikace Function App](functions-reference.md#folder-structure) (wwwroot).

* Soukromá sestavení jsou součástí kontextu dané funkce a podporují vedlejší načítání různých verzí. Soukromá sestavení by se měla nahrát do `bin` složky v adresáři Functions. Odkazujte na sestavení pomocí názvu souboru, například `#r "MyAssembly.dll"` .

Informace o tom, jak nahrát soubory do složky funkcí, najdete v části [Správa balíčků](#using-nuget-packages).

### <a name="watched-directories"></a>Sledované adresáře

Adresář, který obsahuje soubor skriptu funkce, je automaticky sledován pro změny sestavení. Chcete-li sledovat změny sestavení v jiných adresářích, přidejte je do `watchDirectories` seznamu v [host.js](functions-host-json.md).

## <a name="using-nuget-packages"></a>Používání balíčků NuGet
Chcete-li použít balíčky NuGet ve funkci 2. x a novější C#, nahrajte soubor *Function. proj* do složky funkce v systému souborů aplikace Function App. Tady je příklad souboru *Function. proj* , který přidá odkaz na *Microsoft. ProjectOxford. Face* verze *1.1.0*:

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

Pokud chcete použít vlastní kanál NuGet, zadejte informační kanál v souboru *Nuget.Config* v kořenu Function App. Další informace najdete v tématu [Konfigurace chování NuGet](/nuget/consume-packages/configuring-nuget-behavior).

> [!NOTE]
> Ve funkcích 1. x C# jsou balíčky NuGet odkazovány pomocí *project.jsv* souboru místo souboru *Function. proj* .

Pro funkce 1. x použijte místo toho *project.jsv* souboru. Tady je příklad *project.js* souboru:

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

### <a name="using-a-functionproj-file"></a>Použití souboru Function. proj

1. Otevřete funkci v Azure Portal. Na kartě protokoly se zobrazí výstup instalace balíčku.
2. Pokud chcete nahrát soubor *Function. proj* , použijte jednu z metod popsaných v tématu [Postup aktualizace souborů aplikace Function App](functions-reference.md#fileupdate) v tématu referenční informace pro vývojáře Azure Functions.
3. Po nahrání souboru *Function. proj* uvidíte výstup podobný následujícímu příkladu v protokolu streamování vaší funkce:

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

Chcete-li získat proměnnou prostředí nebo hodnotu nastavení aplikace, použijte `System.Environment.GetEnvironmentVariable` , jak je znázorněno v následujícím příkladu kódu:

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

V jazyce C# a dalších jazycích .NET můžete použít [imperativní](https://en.wikipedia.org/wiki/Imperative_programming) vzor vazby, a to na rozdíl od [*deklarativních*](https://en.wikipedia.org/wiki/Declarative_programming) vazeb v *function.js*. Imperativní vazba je užitečná v případě, že parametry vazby je třeba vypočítat za běhu, nikoli jako dobu návrhu. S tímto modelem můžete vytvořit vazbu na podporované vstupní a výstupní vazby průběžně v kódu funkce.

Definujte imperativní vazbu následujícím způsobem:

- **Nezahrnovat položku** v *function.js* pro pro požadované imperativní vazby.
- Předejte vstupní parametr [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) nebo [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- K provedení datové vazby použijte následující vzor C#.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` je atribut rozhraní .NET, který definuje vaši vazbu a `T` je vstupní nebo výstupní typ podporovaný tímto typem vazby. `T` nelze `out` zadat typ parametru (například `out JObject` ). Například výstupní vazba Mobile Apps tabulky podporuje [šest výstupních typů](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale můžete použít pouze [ICollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) pro `T` .

### <a name="single-attribute-example"></a>Příklad jednoduchého atributu

Následující příklad kódu vytvoří [výstupní vazbu objektu BLOB úložiště](functions-bindings-storage-blob-output.md) s cestou objektu blob, která je definovaná v době běhu, a pak zapíše řetězec do objektu BLOB.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definuje vstupní nebo výstupní vazbu [objektu BLOB úložiště](functions-bindings-storage-blob.md) a [TextWriter](/dotnet/api/system.io.textwriter) je podporovaný výstupní typ vazby.

### <a name="multiple-attribute-example"></a>Příklad více atributů

Předchozí příklad získá nastavení aplikace pro připojovací řetězec hlavního účtu úložiště aplikace Function App (což je `AzureWebJobsStorage` ). Můžete zadat vlastní nastavení aplikace, které se má použít pro účet úložiště, a to přidáním [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) a předáním pole atributu do `BindAsync<T>()` . Použijte `Binder` parametr, ne `IBinder` .  Například:

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

V následující tabulce jsou uvedeny atributy rozhraní .NET pro každý typ vazby a balíčky, ve kterých jsou definovány.

> [!div class="mx-codeBreakAll"]
> | Vazba | Atribut | Přidání odkazu |
> |------|------|------|
> | Databáze Cosmos | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAccountTests.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAttributeTests.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAccountTests.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Fronta úložiště | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Objekt blob úložiště | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tabulka úložiště | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Další informace o osvědčených postupech pro Azure Functions](functions-best-practices.md)
