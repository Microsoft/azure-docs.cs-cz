---
title: Azure funkcí jazyka C# referenční informace pro vývojáře
description: Pochopit, jak vyvíjet Azure Functions pomocí jazyka C#.
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
ms.openlocfilehash: e521ef29a338d0c7d80493f92acff4758a091359
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261283"
---
# <a name="azure-functions-c-developer-reference"></a>Azure funkcí jazyka C# referenční informace pro vývojáře

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Tento článek je úvodem do Azure Functions vývoj s použitím jazyka C# v knihovny tříd rozhraní .NET.

Azure Functions podporuje C# a C# skript programovacích jazyků. Pokud hledáte pokyny [pomocí jazyka C# na portálu Azure](functions-create-function-app-portal.md), najdete v části [C# skript (.csx) referenční informace pro vývojáře](functions-reference-csharp.md).

Tento článek předpokládá, že jste si přečetli již v následujících článcích:

* [Příručka pro vývojáře Azure funkce](functions-reference.md)
* [Nástroje sady Visual Studio 2017 funkcí Azure](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Funkce projektu knihovny tříd

V sadě Visual Studio **Azure Functions** vytvoří šablona projektu C# projektu knihovny tříd obsahující následující soubory:

* [Host.JSON](functions-host-json.md) – ukládá nastavení konfigurace, které ovlivní všechny funkce v projektu, když běží místně nebo v Azure.
* [Local.Settings.JSON](functions-run-local.md#local-settings-file) – ukládá nastavení aplikace a připojovacích řetězců, které se používají při místním spuštění.

> [!IMPORTANT]
> Vytvoří procesu sestavení *function.json* soubor pro každou funkci. To *function.json* soubor není určen k upravovat přímo. Nelze změnit konfiguraci vazby nebo zakázat funkci úpravou tohoto souboru. Zakázat funkci, použijte [zakázat](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atribut. Například přidat nastavení MY_TIMER_DISABLED logickou aplikaci a použít `[Disable("MY_TIMER_DISABLED")]` na funkce. Potom můžete povolit a zakázat tak, že změníte nastavení aplikace.

## <a name="methods-recognized-as-functions"></a>Metody rozpoznán jako funkce

Knihovny tříd, funkce je statickou metodu s `FunctionName` a aktivační události atributu, jak je znázorněno v následujícím příkladu:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

`FunctionName` Atribut určí metodu jako vstupní bod funkce. Název musí být jedinečný v rámci projektu, začínat písmenem a obsahovat pouze písmena, číslice, `_` a `-`, až 127 znaků. Šablony projektů často vytvořte metodu s názvem `Run`, ale název metody může být jakýkoli platný C# metoda název.

Aktivační událost atribut určuje typ aktivační události a sváže vstupní data parametru metody. Příklad funkce se aktivuje zprávu fronty a zprávy ve frontě, je předaná metodě v `myQueueItem` parametr.

## <a name="method-signature-parameters"></a>Parametry metody podpis

Podpis metody mohou obsahovat parametry než používaný s atributem aktivační události. Tady jsou některé další parametry, které můžou zahrnovat:

* [Vstup a výstup vazby](functions-triggers-bindings.md) označeny jako takový je architekturu s atributy.  
* `ILogger` Nebo `TraceWriter` parametr pro [protokolování](#logging).
* A `CancellationToken` parametr pro [řádné vypnutí](#cancellation-tokens).
* [Vazba výrazy](functions-triggers-bindings.md#binding-expressions-and-patterns) parametry získat aktivovat metadat.

Pořadí parametrů v podpis funkce nezáleží. Například před nebo po další vazby, které můžete vložit aktivační parametry a před nebo po aktivační události nebo vazby parametrů, které můžete vložit parametr protokolovacího nástroje.

### <a name="output-binding-example"></a>Příklad výstupu vazby

Následující příklad změní předchozí jeden přidáním vazbu výstupní fronty. Funkce zapíše zprávy ve frontě, která aktivuje funkci pro nové zprávy fronty v jiné fronty.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Vazba odkaz články ([fronty úložiště](functions-bindings-storage-queue.md), například) vysvětlují, jaké typy parametrů můžete použít s aktivační událost, vstup nebo výstup vazby atributy.

### <a name="binding-expressions-example"></a>Příklad výrazy vazby

Následující kód získá název fronty k monitorování z nastavení aplikace a získá čas vytvoření fronty zpráv `insertionTime` parametr.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        TraceWriter log)
    {
        log.Info($"Message content: {myQueueItem}");
        log.Info($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automaticky generovaný function.json

Vytvoří procesu sestavení *function.json* souboru ve složce funkce ve složce sestavení. Jak již bylo uvedeno dříve, tento soubor není určen k upravovat přímo. Nelze změnit konfiguraci vazby nebo zakázat funkci úpravou tohoto souboru. 

Účelem tohoto souboru je poskytují informace, které řadič škálování pro [škálování rozhodnutí plánu spotřeby](functions-scale.md#how-the-consumption-plan-works). Z tohoto důvodu soubor má jenom informace o aktivační událost není vstupem nebo výstupem vazby.

Generovaný objekt *function.json* soubor obsahuje `configurationSource` vlastnost, která sděluje modulu runtime .NET atributy použít u vazeb, místo *function.json* konfigurace. Tady je příklad:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

*Function.json* generování souboru se provádí pomocí balíčku NuGet [Microsoft\.NET\.Sdk\.funkce](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Stejného balíčku se používá pro obě verze 1.x a 2.x funkce modulu runtime. Cílovém Frameworku, který je co odlišuje 1.x projekt z projektu 2.x. Tady jsou v příslušných částech *.csproj* soubory, zobrazuje různé cílové architektury a stejné `Sdk` balíčku:

**Funkce 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Funkce 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netstandard2.0</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Mezi `Sdk` závislosti balíčků jsou triggerů a vazeb. 1.x projekt odkazuje na 1.x triggerů a vazeb, protože ty cílové rozhraní .NET Framework, zatímco 2.x triggerů a vazeb cílí na .NET Core.

`Sdk` Balíček závisí také na [Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json)a nepřímo na [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage). Ujistěte se, že projektu používá verzích tyto balíčky, které pracují s verzi modulu runtime funkce tyto závislosti, cíle projektu. Například `Newtonsoft.Json` má verze 11 pro rozhraní .NET Framework 4.6.1, ale funkce runtime, která cílí na rozhraní .NET Framework 4.6.1 je kompatibilní jen s `Newtonsoft.Json` 9.0.1. Aby funkce kódu v tomto projektu má také používat `Newtonsoft.Json` 9.0.1.

Zdrojový kód pro `Microsoft.NET.Sdk.Functions` je k dispozici v úložišti GitHub [azure\-funkce\-vs\-sestavení\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Verze modulu runtime

Visual Studio použije [nástroje základní funkce Azure](functions-run-local.md#install-the-azure-functions-core-tools) pro spouštění projektů funkce. Základní nástroje je rozhraní příkazového řádku pro modul runtime funkce.

Pokud nainstalujete nástroje základní pomocí npm, který nemá vliv nástroje základní verzi, kterou Visual Studio. Verze runtime funkce 1.x, Visual Studio ukládá základní nástroje verze v *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* a používá nejnovější verze uložená existuje. Pro funkce 2.x, základní nástroje jsou součástí **Azure Functions a webové úlohy nástroje** rozšíření. Pro 1.x a 2.x můžete zjistit, jaká verze se používá v výstup konzoly, při spuštění projektu funkce:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Podporované typy u vazeb

Každé vazby má svou vlastní podporované typy; pro instanci atribut aktivační události objektu blob lze použít na řetězcový parametr, parametr objektů POCO, `CloudBlockBlob` parametr, nebo libovolná z některé další podporované typy. [Článku vazby u vazeb objektů blob](functions-bindings-storage-blob.md#trigger---usage) zobrazí všechny podporované typy parametrů. Další informace najdete v tématu [triggerů a vazeb](functions-triggers-bindings.md) a [vazby referenční dokumentace pro každý typ vazby](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Vytvoření vazby na návratovou hodnotu – Metoda

Můžete návratovou hodnotu metoda pro vazbu výstup použitím atribut metoda návratovou hodnotu. Příklady najdete v tématu [triggerů a vazeb](functions-triggers-bindings.md#using-the-function-return-value).

## <a name="writing-multiple-output-values"></a>Zápis více hodnot výstup

K vytvoření více hodnot pro vazbu výstup, použijte [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typy. Tyto typy jsou jen pro zápis kolekce, které se zapisují do výstupu vazby po dokončení metody.

Tento příklad zapisuje více fronty zpráv do fronty ke stejné pomocí `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 1: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Protokolování

Do protokolu výstup váš datový proud protokolů v jazyce C#, zahrnují argument typu `TraceWriter`. Doporučujeme, abyste pojmenujte ji `log`. Nepoužívejte `Console.Write` v Azure Functions. 

`TraceWriter` je definována v [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Úroveň protokolu pro `TraceWriter` se dá nakonfigurovat v [host.json](functions-host-json.md).

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

> [!NOTE]
> Informace o novější rozhraní protokolování, který můžete použít místo `TraceWriter`, najdete v části [zápisu protokolů v C# funkce](functions-monitoring.md#write-logs-in-c-functions) v **monitorování Azure Functions** článku.

## <a name="async"></a>Asynchronní

Chcete-li funkci [asynchronní](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), použijte `async` – klíčové slovo a vraťte se `Task` objektu.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        TraceWriter log)
    {
        log.Info($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="cancellation-tokens"></a>Zrušení tokenů

Funkce může přijmout [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) parametr, který umožňuje operačního systému a funkcí je možné ukončit upozorňovaly kódu. Toto oznámení můžete Ujistěte se, že funkce nepodporuje neočekávaně ukončí tak, aby data ponechá v nekonzistentním stavu.

Následující příklad ukazuje, jak zkontrolovat pro předcházení ukončení funkce.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
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
}
```

## <a name="environment-variables"></a>Proměnné prostředí

Proměnné prostředí nebo nastavení hodnoty aplikace, použijte `System.Environment.GetEnvironmentVariable`, jak ukazuje následující příklad kódu:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
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
}
```

Nastavení aplikace lze číst z proměnné prostředí při vývoji místně i při spuštění v Azure. Při vývoji místně, nastavení aplikace pocházet z `Values` kolekce *local.settings.json* souboru. V obou prostředích místní a Azure, `GetEnvironmentVariable("<app setting name>")` načte hodnotu nastavení s názvem aplikace. Například když spouštíte místně, "My název lokality" by vrácena v případě vaší *local.settings.json* soubor obsahuje `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

[System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/en-us/dotnet/api/system.configuration.configurationmanager.appsettings) vlastnost je alternativní rozhraní API pro získání hodnoty nastavení aplikace, ale doporučujeme vám, že používáte `GetEnvironmentVariable` jak je vidět tady.

## <a name="binding-at-runtime"></a>Vazba za běhu

V jazyce C# a jinými jazyky rozhraní .NET, můžete použít [imperativní](https://en.wikipedia.org/wiki/Imperative_programming) vazby vzor, nikoli [ *deklarativní* ](https://en.wikipedia.org/wiki/Declarative_programming) vazby v atributech. Imperativní vazba je užitečné, když vázané parametry muset počítaný v době běhu spíše než návrhu. Tento vzor je možné svázat podporované vstup a výstup vazby na průběžně ve vašem kódu funkce.

Definujte imperativní vazby následujícím způsobem:

- **Nechcete** zahrnout atribut podpis funkce pro vaše požadované imperativní vazby.
- Předejte jí vstupní parametr [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) nebo [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- K provedení vazby dat použijte následující vzor C#.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` je atribut rozhraní .NET, která definuje vazbu, a `T` je vstupní nebo výstupní typ, který podporuje tento typ vazby. `T` nelze `out` typ parametru (například `out JObject`). Například v tabulce Mobile Apps výstupu vazba podporuje [šest výstup typy](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale můžete použít pouze [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)imperativní vazbou.

### <a name="single-attribute-example"></a>Příklad jeden atribut

Následující příklad kódu vytvoří [objektu blob Storage výstup vazby](functions-bindings-storage-blob.md#output) s objektem blob cestu, která je definována v době běhu, pak zapíše řetězec do objektu blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        TraceWriter log)
    {
        log.Info($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definuje [objektu blob Storage](functions-bindings-storage-blob.md) vstupem nebo výstupem vazby a [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) je typ vazby podporované výstup.

### <a name="multiple-attribute-example"></a>Příklad více atributů

V předchozím příkladu získá nastavení aplikace pro aplikaci funkce hlavní účet připojovacího řetězce úložiště (což je `AzureWebJobsStorage`). Můžete zadat nastavení vlastní aplikace použít pro účet úložiště tak, že přidáte [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) a předání atribut pole do `BindAsync<T>()`. Použití `Binder` parametr není `IBinder`.  Příklad:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            TraceWriter log)
    {
        log.Info($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Triggery a vazby 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o triggerů a vazeb](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Další informace o osvědčených postupech pro Azure Functions](functions-best-practices.md)
