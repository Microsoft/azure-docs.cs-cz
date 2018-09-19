---
title: Funkce jazyka C# pro vývojáře referenční informace k Azure
description: Naučte se vyvíjet funkce Azure pomocí jazyka C#.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/12/2018
ms.author: glenga
ms.openlocfilehash: 54ac616f97ba034893721ff62fc6157dd045b5f8
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126816"
---
# <a name="azure-functions-c-developer-reference"></a>Funkce jazyka C# pro vývojáře referenční informace k Azure

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Tento článek je Úvod do vytváření Azure Functions s použitím jazyka C# v knihovnách tříd .NET.

Služba Azure Functions podporuje C# a C# skript programovacích jazyků. Pokud hledáte pokyny [pomocí jazyka C# na webu Azure Portal](functions-create-function-app-portal.md), naleznete v tématu [jazyka C# (.csx) pro vývojáře odkaz na skript](functions-reference-csharp.md).

Tento článek předpokládá, že jste již přečíst následující články:

* [Příručka pro vývojáře Azure Functions](functions-reference.md)
* [Azure Functions nástroje Visual Studio 2017](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Projekt knihovny tříd – funkce

V sadě Visual Studio **Azure Functions** vytvoří šablonu projektu C# projekt knihovny tříd, který obsahuje následující soubory:

* [Host.JSON](functions-host-json.md) – ukládá nastavení konfigurace, které ovlivňují všechny funkce v projektu při spuštění místně nebo v Azure.
* [Local.Settings.JSON](functions-run-local.md#local-settings-file) – ukládá nastavení aplikace a připojovacích řetězců, které se používají při místním spuštění. Tento soubor obsahuje tajných kódů a není publikována do vaší aplikace funkcí v Azure. Místo toho musíte [přidat nastavení aplikace pro vaši aplikaci function app](functions-develop-vs.md#function-app-settings).

Při sestavování projektu strukturu složek, který bude vypadat jako následující vygenerované v sestavení výstupního adresáře:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Tento adresář je, co se nasadí do vaší aplikace funkcí v Azure. Rozšíření vazby vyžaduje [verze 2.x](functions-versions.md) funkce modulu runtime jsou [přidány do projektu jako balíčky NuGet](functions-triggers-bindings.md#c-class-library-with-visual-studio-2017).

> [!IMPORTANT]
> Vytvoří proces sestavení *function.json* souboru pro každou funkci. To *function.json* souboru neměl být upravován přímo. Nelze změnit konfiguraci vazby nebo zakázat funkci úpravou tohoto souboru. Zjistěte, jak zakázat funkce, najdete v článku [zakázání funkce](disable-function.md#functions-2x---c-class-libraries).

## <a name="methods-recognized-as-functions"></a>Metody, které jsou rozpoznány jako funkce

V knihovně tříd, je funkce statická metoda s `FunctionName` a atribut aktivační událost, jak je znázorněno v následujícím příkladu:

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

`FunctionName` Atribut určí metodu jako vstupní bod funkce. Název musí být jedinečný v rámci projektu, začínat písmenem a obsahovat jenom písmena, číslice, `_` a `-`, až 127 znaků. Šablony projektů často vytvořit metodu s názvem `Run`, ale název metody, který může být libovolný platný C# název metody.

Aktivační atribut určuje typ aktivační události a sváže vstupní data pro parametr metody. Aktivuje funkci příklad zprávy fronty a zpráva fronty se předá metodě v `myQueueItem` parametru.

## <a name="method-signature-parameters"></a>Parametry podpisu metody

Podpis metody mohou obsahovat jiné parametry, než jaký se používá s atributem aktivační události. Tady jsou některé další parametry, které můžete zahrnout:

* [Vstupní a výstupní vazby](functions-triggers-bindings.md) jako takové označeny upravení s atributy.  
* `ILogger` Nebo `TraceWriter` parametr pro [protokolování](#logging).
* A `CancellationToken` parametr pro [řádné vypnutí](#cancellation-tokens).
* [Výrazy vazeb](functions-triggers-bindings.md#binding-expressions-and-patterns) aktivovat parametry se mají získat metadata.

Pořadí parametrů v signatuře funkce na nezáleží. Například parametry aktivační události můžete umístit před nebo po jiných vazbách a parametr protokolovacího nástroje můžete umístit před nebo za triggerem nebo vazbou parametry.

### <a name="output-binding-example"></a>Příklad výstupu vazby

Následující příklad upravuje předchozí tak, že přidáte výstupní vazbu fronty. Tato funkce zapíše zpráva fronty, která aktivuje funkci pro nové zprávy fronty v jiné fronty.

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

Články odkaz vazby ([front služby Storage](functions-bindings-storage-queue.md), například) vysvětlují, jaké typy parametrů můžete použít s aktivační událost, vstup nebo výstup atributů vazby.

### <a name="binding-expressions-example"></a>Příklad výrazy vazby

Následující kód získá název fronty k monitorování z nastavení aplikace, a to není čas vytvoření fronty zpráv `insertionTime` parametru.

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

## <a name="autogenerated-functionjson"></a>Automaticky generované function.json

Vytvoří proces sestavení *function.json* souboru ve složce funkci ve složce sestavení. Jak je uvedeno výše, tento soubor není určena k přímo upravovat. Nelze změnit konfiguraci vazby nebo zakázat funkci úpravou tohoto souboru. 

Účelem tohoto souboru je poskytnout informace o škálování řadiče na použití pro [rozhodnutí o škálování na plán consumption](functions-scale.md#how-the-consumption-plan-works). Z tohoto důvodu má soubor jenom informace o aktivační události, není vstupní nebo výstupní vazbu.

Vygenerovaný *function.json* obsahuje soubor `configurationSource` vlastnost, která říká modul runtime pro použití .NET atributy u vazeb, spíše než *function.json* konfigurace. Tady je příklad:

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

*Function.json* generování souboru provádí balíček NuGet [Microsoft\.NET\.Sdk\.funkce](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Stejného balíčku se používá pro verzi 1.x a 2.x modul runtime služby Functions. Cílová architektura, která je, co rozlišuje 1.x projekt z projektu 2.x. Tady jsou v příslušných částech *.csproj* soubory zobrazují různé cílové platformy a stejný `Sdk` balíčku:

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

Mezi `Sdk` závislosti balíčků jsou triggery a vazby. 1.x projekt odkazuje na 1.x triggery a vazby, protože jsou cíleny na rozhraní.NET Framework, zatímco 2.x triggerů a vazeb cílit na .NET Core.

`Sdk` Balíček závisí také na [Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json), nepřímo v [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage). Tyto závislosti Ujistěte se, že váš projekt používá verze tyto balíčky, které pracují s modulem runtime verze funkce, která projekt cílí. Například `Newtonsoft.Json` má verze 11 pro rozhraní .NET Framework 4.6.1, ale modul runtime služby Functions, který cílí na .NET Framework 4.6.1 je kompatibilní jenom s `Newtonsoft.Json` 9.0.1. Takže kódu funkce v daném projektu má také použít `Newtonsoft.Json` 9.0.1.

Zdrojový kód pro `Microsoft.NET.Sdk.Functions` je k dispozici v úložišti Githubu [azure\-funkce\-vs\-sestavení\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Verze modulu runtime

Visual Studio používá [nástrojů Azure Functions Core](functions-run-local.md#install-the-azure-functions-core-tools) pro spouštění projektů funkce. Základní nástroje se rozhraní příkazového řádku pro modul runtime služby Functions.

Pokud nainstalujete pomocí npm základní nástroje, který nemá vliv základní nástroje verze používané v sadě Visual Studio. Funkce modulu runtime verze 1.x, Visual Studio ukládá Core Tools verze v *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* a používá nejnovější verze uložená existuje. Pro funkce 2.x, základní nástroje jsou součástí **Azure Functions and Web Jobs Tools** rozšíření. Pro 1.x a 2.x uvidíte, jaká verze se používá ve výstupu konzoly při spuštění projektu funkce:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Podporované typy pro vazby

Každá vazba má svůj vlastní podporované typy; Například lze atribut aktivační událost objektů blob použít pro parametr řetězec, parametr POCO `CloudBlockBlob` parametr nebo některý další podporované typy. [Článku o vazbě pro vazby objektů blob](functions-bindings-storage-blob.md#trigger---usage) zobrazí všechny podporované typy parametrů. Další informace najdete v tématu [aktivačními událostmi a vazbami](functions-triggers-bindings.md) a [vazby referenční dokumenty pro každý typ vazby](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Vytvoření vazby na návratovou hodnotu metody

Návratová hodnota metody můžete použít pro výstupní vazby, použitím atributu na návratovou hodnotu metody. Příklady najdete v tématu [aktivačními událostmi a vazbami](functions-triggers-bindings.md#using-the-function-return-value). 

Použijte návratovou hodnotu, pouze v případě, že výsledky spuštění úspěšné funkce, který je vždy v návratovou hodnotu pro předání do výstupní vazbu. Jinak použijte `ICollector` nebo `IAsyncCollector`, jak je znázorněno v následující části.

## <a name="writing-multiple-output-values"></a>Zápis více výstupní hodnoty

Zapsat do výstupní vazbu více hodnot nebo pokud volání úspěšné funkce nemusí nic k předání do výstupní vazbu, použijte [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typy. Tyto typy jsou pouze pro zápis kolekce, které jsou zapsány do výstupní vazbu po dokončení metody.

Tento příklad zapíše více front zpráv do stejné frontě pomocí `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Protokolování

Pokud chcete protokolovat výstup do protokolů streamování v C#, zahrňte argument typu `TraceWriter`. Doporučujeme vám, že si vzpomenete `log`. Vyhněte se použití `Console.Write` ve službě Azure Functions. 

`TraceWriter` je definován v [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Úroveň protokolování pro `TraceWriter` se dá nakonfigurovat v [host.json](functions-host-json.md).

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
> Informace o novější protokolovacího rozhraní, které můžete použít namísto `TraceWriter`, naleznete v tématu [zápisu přihlásí funkcí jazyka C#](functions-monitoring.md#write-logs-in-c-functions) v **monitorování Azure Functions** článku.

## <a name="async"></a>asynchronní

Provést funkci [asynchronní](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), použijte `async` – klíčové slovo a vraťte se `Task` objektu.

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

Nemůžete použít `out` parametry v asynchronních funkcí. Výstupní vazby, použijte [návratovou hodnotu funkce](#binding-to-method-return-value) nebo [objekt kolekce](#writing-multiple-output-values) místo.

## <a name="cancellation-tokens"></a>Tokeny zrušení

Funkce může přijmout [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) parametr, který umožňuje operačnímu systému upozornit funkce se chystá ukončit váš kód. Toto oznámení můžete použít k Ujistěte se, že funkce nebude dojde k neočekávanému ukončení tak, aby data ponechá v nekonzistentním stavu.

Následující příklad ukazuje, jak vyhledat brzké ukončení funkce.

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

Chcete-li získat proměnnou prostředí nebo nastavení hodnoty aplikace, použijte `System.Environment.GetEnvironmentVariable`, jak je znázorněno v následujícím příkladu kódu:

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

Nastavení aplikace může číst z proměnných prostředí, při vývoji místně a při spuštění v Azure. Při vývoji místně, pochází z aplikace nastavení `Values` kolekce *local.settings.json* souboru. V obou prostředích místní a Azure, `GetEnvironmentVariable("<app setting name>")` načte hodnotu nastavení s názvem aplikace. Například, když spouštíte místně, "My Server Name" by vrátila Pokud vaše *local.settings.json* soubor obsahuje `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

[System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) vlastnost je alternativní rozhraní API pro získání hodnoty nastavení aplikace, ale doporučujeme, abyste použili `GetEnvironmentVariable` jak je znázorněno zde.

## <a name="binding-at-runtime"></a>Vazbu za běhu

V jazyce C# a jinými jazyky rozhraní .NET, můžete použít [imperativní](https://en.wikipedia.org/wiki/Imperative_programming) vazby modelu, nikoli [ *deklarativní* ](https://en.wikipedia.org/wiki/Declarative_programming) vazby v atributech. Imperativní vazby je užitečné, když parametry vazby musí vypočítat v době běhu spíše než návrhu. V tomto modelu můžete vázat na podporované vstupní a výstupní vazby na průběžné v kódu funkce.

Definování dnešní vazby následujícím způsobem:

- **Ne** zahrnovat atribut v signatuře funkce požadovaného imperativní vazby.
- Předejte vstupní parametr [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) nebo [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- K provedení vazby dat používají následující vzor jazyka C#.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` je atribut .NET, která definuje vazbu, a `T` je vstupní nebo výstupní typ, který podporuje tento typ vazby. `T` nemůže být `out` typ parametru (například `out JObject`). Například v tabulce Mobile Apps Výstupní vazba podporuje [šest výstupní typy](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale můžete použít pouze [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)imperativní vazby.

### <a name="single-attribute-example"></a>Příklad jeden atribut

Následující příklad kódu vytvoří [výstupní vazby úložiště blob](functions-bindings-storage-blob.md#output) s objektem blob cestu, která je definována v době běhu, pak zapíše řetězec do objektu blob.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definuje [úložiště objektů blob](functions-bindings-storage-blob.md) vstupní nebo výstupní vazby, a [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) je typ vazby podporované výstup.

### <a name="multiple-attribute-example"></a>Příklad více atributů

V předchozím příkladu získá nastavení aplikace function App. hlavní připojovací řetězec účtu úložiště (což je `AzureWebJobsStorage`). Můžete zadat nastavení vlastní aplikace, které chcete použít pro účet úložiště tak, že přidáte [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) a předání do pole atributu `BindAsync<T>()`. Použití `Binder` parametr, ne `IBinder`.  Příklad:

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
> [Další informace o aktivační události a vazby](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Další informace o osvědčených postupech pro službu Azure Functions](functions-best-practices.md)
