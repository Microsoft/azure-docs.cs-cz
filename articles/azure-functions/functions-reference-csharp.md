---
title: Azure funkcí jazyka C# skript referenční informace pro vývojáře
description: Pochopit, jak vyvíjet Azure Functions pomocí skriptu jazyka C#.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: tdykstra
ms.openlocfilehash: 91e85fb25f9324f9b8d83884fe34a014f280de0b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure funkcí jazyka C# skript (.csx) referenční informace pro vývojáře

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Tento článek je úvodem do vývoj Azure Functions pomocí skriptu jazyka C# (*.csx*).

Azure Functions podporuje C# a C# skript programovacích jazyků. Pokud hledáte pokyny [pomocí jazyka C# v sadě Visual Studio projektu knihovny tříd](functions-develop-vs.md), najdete v části [C# referenční informace pro vývojáře](functions-dotnet-class-library.md).

Tento článek předpokládá, že jste si již přečetli [Příručka pro vývojáře Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Jak funguje .csx

Je na základě zkušeností skriptu jazyka C# pro Azure Functions [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Toky dat do funkce jazyka C# prostřednictvím metoda argumenty. Argument názvy jsou určené v `function.json` souborů a jsou předdefinovány názvy pro přístup k věci jako funkce protokolovacího nástroje a zrušení tokenů.

*.Csx* formátu umožňuje zapsat menší "standardní" a zaměřit se na psaní právě C# funkce. Místo zabalení všechno, co v oboru názvů a třídy, pouze definuje `Run` metoda. Zahrnout všechny odkazy na sestavení a obory názvů na začátku souboru jako obvykle.

Aplikaci funkce *.csx* soubory kompilované při inicializaci instance. Tento krok kompilace znamená akce, jako je studený start může trvat delší dobu, C# skript funkce ve srovnání s knihovny tříd jazyka C#. Tento krok kompilace je taky, proč se skript funkce jazyka C# upravovat na portálu Azure při nejsou knihovny tříd jazyka C#.

## <a name="binding-to-arguments"></a>Vytvoření vazby na argumenty

Vstupních nebo výstupních dat je vázána na C# skript parametr funkce prostřednictvím `name` vlastnost *function.json* konfigurační soubor. Následující příklad ukazuje *function.json* souboru a *run.csx* soubor pro funkci aktivovaného fronty. Parametr, který přijímá data ze zprávy ve frontě jmenuje `myQueueItem` , protože hodnota `name` vlastnost.

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

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

`#r` Údajů je vysvětleno [dále v tomto článku](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Podporované typy u vazeb

Každé vazby má svou vlastní podporované typy; aktivační události objektu blob pro instanci lze použít s řetězcový parametr, parametr objektů POCO, `CloudBlockBlob` parametr, nebo libovolná z některé další podporované typy. [Článku vazby u vazeb objektů blob](functions-bindings-storage-blob.md#trigger---usage) zobrazí všechny podporované typy parametrů pro aktivační události objektu blob. Další informace najdete v tématu [triggerů a vazeb](functions-triggers-bindings.md) a [vazby referenční dokumentace pro každý typ vazby](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Odkazování na vlastní třídy

Pokud budete muset použít vlastní třídu prostý staré CLR objektů POCO (), můžete zahrnout definice třídy ve stejném souboru nebo ji umístit do samostatného souboru.

Následující příklad ukazuje *run.csx* příklad, který zahrnuje definice objektů POCO třídy.

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

Třídu objektů POCO musí mít metody getter a setter definované pro každou vlastnost.

## <a name="reusing-csx-code"></a>Opětovné použití kódu .csx

Můžete použít třídy a metody definované v jiných *.csx* soubory ve vaší *run.csx* souboru. Chcete-li provést, použijte `#load` direktivy ve vaší *run.csx* souboru. V následujícím příkladu rutiny protokolování s názvem `MyLogger` je sdílena v *myLogger.csx* a načtena do *run.csx* pomocí `#load` – direktiva:

Příklad *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Příklad *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext);
}
```

Pomocí sdílenou *.csx* souboru je běžný vzor, pokud chcete na typově data předávají mezi funkcí podle pomocí objektů POCO objektu. V následujícím příkladu zjednodušené služby triggeru protokolu HTTP a aktivační události fronty sdílet objektů POCO objekt s názvem `Order` na typově pořadí dat:

Příklad *run.csx* pro triggeru protokolu HTTP:

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

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

Příklad *run.csx* frontě aktivační události:

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

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

Můžete použít relativní cestu `#load` – direktiva:

* `#load "mylogger.csx"` načte soubor umístěný ve složce funkce.
* `#load "loadedfiles\mylogger.csx"` načte soubor umístěný ve složce ve složce funkce.
* `#load "..\shared\mylogger.csx"` načte soubor umístěný ve složce na stejné úrovni jako složka funkce, který je přímo pod *wwwroot*.

`#load` – Direktiva pracuje pouze s *.csx* soubory, ne při *.cs* soubory.

## <a name="binding-to-method-return-value"></a>Vytvoření vazby na návratovou hodnotu – Metoda

Můžete použít návratovou hodnotu metoda pro vazbu výstup pomocí názvu `$return` v *function.json*. Příklady najdete v tématu [triggerů a vazeb](functions-triggers-bindings.md#using-the-function-return-value).

## <a name="writing-multiple-output-values"></a>Zápis více hodnot výstup

K vytvoření více hodnot pro vazbu výstup, použijte [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typy. Tyto typy jsou jen pro zápis kolekce, které se zapisují do výstupu vazby po dokončení metody.

Tento příklad zapisuje více fronty zpráv do fronty ke stejné pomocí `ICollector`:

```csharp
public static void Run(ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Hello");
    myQueueItem.Add("World!");
}
```

## <a name="logging"></a>Protokolování

Do protokolu výstup váš datový proud protokolů v jazyce C#, zahrnují argument typu `TraceWriter`. Doporučujeme, abyste pojmenujte ji `log`. Nepoužívejte `Console.Write` v Azure Functions. 

`TraceWriter` je definována v [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Úroveň protokolu pro `TraceWriter` se dá nakonfigurovat v [host.json](functions-host-json.md).

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Informace o novější rozhraní protokolování, který můžete použít místo `TraceWriter`, najdete v části [zápisu protokolů v C# funkce](functions-monitoring.md#write-logs-in-c-functions) v **monitorování Azure Functions** článku.

## <a name="async"></a>Asynchronní

Chcete-li funkci [asynchronní](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), použijte `async` – klíčové slovo a vraťte se `Task` objektu.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

## <a name="cancellation-tokens"></a>Zrušení tokenů

Funkce může přijmout [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) parametr, který umožňuje operačního systému a funkcí je možné ukončit upozorňovaly kódu. Toto oznámení můžete Ujistěte se, že funkce nepodporuje neočekávaně ukončí tak, aby data ponechá v nekonzistentním stavu.

Následující příklad ukazuje, jak zkontrolovat pro předcházení ukončení funkce.

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

## <a name="importing-namespaces"></a>Import obory názvů

Pokud potřebujete importovat obory názvů, můžete to udělat tak jako obvyklé, se `using` klauzule.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Následující obory názvů jsou automaticky importuje a jsou proto volitelné:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Odkazování na externí sestavení

Pro sestavení architektury, přidání odkazů pomocí `#r "AssemblyName"` – direktiva.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Následující sestavení jsou automaticky přidány Azure Functions hostování prostředí:

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

Následující sestavení může být odkazován jednoduchý název (například `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Odkazování na vlastní sestavení

Chcete-li vlastního sestavení, můžete použít buď *sdílené* sestavení nebo *privátní* sestavení:
- Sdílená sestavení jsou sdíleny ve všech funkcí v rámci funkce aplikace. Chcete-li vlastního sestavení, nahrajte sestavení do funkce aplikace, například v `bin` složku v kořenu aplikace funkce. 
- Soukromá sestavení jsou součástí kontextu danou funkci a podporovat zkušební načtení různých verzí. Soukromá sestavení musí být nahrán v `bin` složky v adresáři funkce. Odkaz sestavení pomocí názvu souboru, jako třeba `#r "MyAssembly.dll"`. 

Informace o tom, jak odeslat soubory do složky funkce, najdete v části na [balíček správy](#using-nuget-packages).

### <a name="watched-directories"></a>Sledované adresáře

Adresář, který obsahuje soubor skriptu funkce je automaticky sledovaná změny sestavení. Podívejte se na sestavení změny v ostatních adresářů, přidejte je do `watchDirectories` seznamu v [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Pomocí balíčků NuGet

Použití balíčků NuGet ve funkci jazyka C#, nahrajte *project.json* soubor do složky funkce v systému souborů aplikaci funkce. Tady je příklad *project.json* soubor, který přidá odkaz na Microsoft.ProjectOxford.Face verze 1.1.0:

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

V Azure funguje 1.x, je podporován pouze rozhraní .NET Framework 4.6, tak zkontrolujte, zda vaše *project.json* soubor Určuje `net46` jak je vidět tady.

Když nahrajete *project.json* soubor modulu runtime získá balíčky a automaticky přidá reference na sestavení balíčku. Nemusíte přidávat `#r "AssemblyName"` direktivy. Chcete-li použít typy definované v balíčky NuGet; Stačí přidat požadované `using` příkazů do vaší *run.csx* souboru. 

V modulu runtime funkce obnovení NuGet funguje tak, že porovnáte `project.json` a `project.lock.json`. Pokud data a časových razítek souborů **nepodporují** běží shodu, obnovení NuGet a aktualizovat balíčky NuGet stahování. Ale pokud razítka data a času souborů **provést** shodu, NuGet nebude provádět obnovení. Proto `project.lock.json` by neměl být nasazen, protože způsobuje, že chcete přeskočit obnovení balíčku NuGet. Abyste se vyhnuli nasazení souboru zámku, přidejte `project.lock.json` k `.gitignore` souboru.

Pokud chcete používat vlastní NuGet kanálu, zadejte datového kanálu v *Nuget.Config* soubor v kořenu aplikace funkce. Další informace najdete v tématu [konfigurace NuGet chování](/nuget/consume-packages/configuring-nuget-behavior).

### <a name="using-a-projectjson-file"></a>Pomocí souboru project.json.

1. Otevřete funkce na portálu Azure. Na kartě protokoly zobrazí výstup instalace balíčku.
2. Nahrát soubor project.json, použijte jednu z metod popsaných v [jak aktualizovat soubory aplikace funkce](functions-reference.md#fileupdate) v referenčním tématu pro vývojáře Azure Functions.
3. Po *project.json* soubor odešle, uvidíte výstup podobný následujícímu příkladu funkce je streamování protokolu:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Proměnné prostředí

Proměnné prostředí nebo nastavení hodnoty aplikace, použijte `System.Environment.GetEnvironmentVariable`, jak ukazuje následující příklad kódu:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>Vazba za běhu

V jazyce C# a jinými jazyky rozhraní .NET, můžete použít [imperativní](https://en.wikipedia.org/wiki/Imperative_programming) vazby vzor, nikoli [ *deklarativní* ](https://en.wikipedia.org/wiki/Declarative_programming) vazeb v *function.json*. Imperativní vazba je užitečné, když vázané parametry muset počítaný v době běhu spíše než návrhu. Tento vzor je možné svázat podporované vstup a výstup vazby na průběžně ve vašem kódu funkce.

Definujte imperativní vazby následujícím způsobem:

- **Nechcete** zahrnují položku v *function.json* pro vaše požadované imperativní vazby.
- Předejte jí vstupní parametr [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) nebo [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- K provedení vazby dat použijte následující vzor C#.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` je atribut rozhraní .NET, který definuje váš vazby a `T` je vstupní nebo výstupní typ, který podporuje tento typ vazby. `T` nelze `out` typ parametru (například `out JObject`). Například v tabulce Mobile Apps výstupu vazba podporuje [šest výstup typy](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale můžete použít pouze [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)pro `T`.

### <a name="single-attribute-example"></a>Příklad jeden atribut

Následující příklad kódu vytvoří [objektu blob Storage výstup vazby](functions-bindings-storage-blob.md#output) s objektem blob cestu, která je definována v době běhu, pak zapíše řetězec do objektu blob.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definuje [objektu blob Storage](functions-bindings-storage-blob.md) vstupem nebo výstupem vazby a [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) je typ vazby podporované výstup.

### <a name="multiple-attribute-example"></a>Příklad více atributů

V předchozím příkladu získá nastavení aplikace pro aplikaci funkce hlavní účet připojovacího řetězce úložiště (což je `AzureWebJobsStorage`). Můžete zadat nastavení vlastní aplikace použít pro účet úložiště tak, že přidáte [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) a předání atribut pole do `BindAsync<T>()`. Použití `Binder` parametr není `IBinder`.  Příklad:

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

Následující tabulka obsahuje seznam atributy .NET pro každý typ vazby a balíčky, ve kterých jsou definovány.

> [!div class="mx-codeBreakAll"]
| Vazba | Atribut | Přidání odkazu |
|------|------|------|
| Databáze Cosmos | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Fronta úložiště | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Objekt blob úložiště | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Tabulka úložiště | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o triggerů a vazeb](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Další informace o osvědčených postupech pro Azure Functions](functions-best-practices.md)
