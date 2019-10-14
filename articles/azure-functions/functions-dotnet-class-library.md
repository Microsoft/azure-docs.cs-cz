---
title: Referenční C# informace pro vývojáře Azure Functions
description: Naučte se vyvíjet Azure Functions pomocí C#.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: služby Azure Functions, Functions, zpracování událostí, Webhooky, dynamická výpočetní funkce a architektura bez serveru
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/12/2018
ms.author: glenga
ms.openlocfilehash: c3c13b7e28ef7c17fd45682d828f318de5326542
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293865"
---
# <a name="azure-functions-c-developer-reference"></a>Referenční C# informace pro vývojáře Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Tento článek představuje úvod k vývoji Azure Functions pomocí C# knihovny tříd .NET.

Azure Functions podporuje C# a C# skriptovací programovací jazyky. Pokud hledáte pokyny k [používání C# v Azure Portal](functions-create-function-app-portal.md), přečtěte si téma [ C# referenční informace pro vývojáře skriptu (. csx)](functions-reference-csharp.md).

V tomto článku se předpokládá, že už jste si přečetli následující články:

* [Příručka pro vývojáře Azure Functions](functions-reference.md)
* [Azure Functions nástroje sady Visual Studio 2019](functions-develop-vs.md)

## <a name="supported-versions"></a>Podporované verze

Modul runtime Azure Functions 2. x používá .NET Core 2,2. Kód funkce může používat rozhraní .NET Core 2,2 API aktualizací nastavení projektu sady Visual Studio. Šablony funkcí nejsou ve výchozím nastavení pro .NET Core 2,2, aby nedošlo k nepříznivým dopadům na zákazníky, kteří nemají nainstalované rozhraní .NET Core 2,2.

## <a name="functions-class-library-project"></a>Projekt knihovny tříd Functions

V aplikaci Visual Studio šablona projektu **Azure Functions** vytvoří projekt knihovny C# tříd, který obsahuje následující soubory:

* [Host. JSON](functions-host-json.md) – ukládá nastavení konfigurace, která mají vliv na všechny funkce v projektu při spuštění místně nebo v Azure.
* [Local. Settings. JSON](functions-run-local.md#local-settings-file) – uloží nastavení aplikace a připojovací řetězce, které se používají při místním spuštění. Tento soubor obsahuje tajné klíče a není publikovaný do aplikace Function App v Azure. Místo toho [do aplikace Function App přidejte nastavení aplikace](functions-develop-vs.md#function-app-settings).

Při sestavování projektu se ve výstupním adresáři sestavení vygeneruje struktura složek, která vypadá jako v následujícím příkladu:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Tento adresář se nasadí do vaší aplikace Function App v Azure. Rozšíření vazby požadovaná ve [verzi 2. x](functions-versions.md) modulu runtime Functions jsou [přidána do projektu jako balíčky NuGet](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Proces sestavení vytvoří soubor *Function. JSON* pro každou funkci. Tento soubor *Function. JSON* není určen k přímému upravování. Konfiguraci vazby nemůžete změnit ani tuto funkci můžete zakázat úpravou tohoto souboru. Informace o tom, jak funkci zakázat, najdete v tématu [Jak zakázat funkce](disable-function.md#functions-2x---c-class-libraries).


## <a name="methods-recognized-as-functions"></a>Metody rozpoznané jako funkce

V knihovně tříd je funkce statická metoda s `FunctionName` a aktivačním atributem, jak je znázorněno v následujícím příkladu:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Atribut `FunctionName` označuje metodu jako vstupní bod funkce. Název musí být jedinečný v rámci projektu, začínat písmenem a obsahovat jenom písmena, číslice, `_` a `-`, až 127 znaků. Šablony projektu často vytvářejí metodu s názvem `Run`, ale název metody může být libovolný platný C# název metody.

Atribut Trigger určuje typ triggeru a váže vstupní data k parametru metody. Ukázková funkce je aktivována zprávou fronty a zpráva fronty je předána metodě v parametru `myQueueItem`.

## <a name="method-signature-parameters"></a>Parametry signatury metody

Signatura metody může obsahovat parametry jiné než ta, která se používá s atributem triggeru. Tady je několik dalších parametrů, které můžete zahrnout:

* [Vstupní a výstupní vazby](functions-triggers-bindings.md) označeny jako upravení s atributy.  
* Parametr `ILogger` nebo `TraceWriter` ([pouze verze 1. x-Only](functions-versions.md#creating-1x-apps)) pro [protokolování](#logging).
* Parametr `CancellationToken` pro [řádné vypnutí](#cancellation-tokens).
* Parametry [výrazů vazby](./functions-bindings-expressions-patterns.md) pro získání metadat triggeru.

Pořadí parametrů v signatuře funkce nezáleží. Můžete například vložit parametry triggeru před nebo za jiné vazby a parametr protokolovacího nástroje můžete vložit před nebo po Trigger nebo parametry vazby.

### <a name="output-binding-example"></a>Příklad výstupní vazby

Následující příklad upravuje předchozí rozhraní přidáním vazby výstupní fronty. Tato funkce zapíše zprávu fronty, která aktivuje funkci do nové zprávy fronty v jiné frontě.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Články s odkazy na vazby (například[fronty úložiště](functions-bindings-storage-queue.md)) vysvětlují typy parametrů, které můžete použít s atributy triggeru, vstupu a výstupu.

### <a name="binding-expressions-example"></a>Příklad výrazů vazby

Následující kód Získá název fronty, která se má monitorovat, z nastavení aplikace a získá čas vytvoření zprávy fronty v parametru `insertionTime`.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automaticky vygenerované funkce Function. JSON

Proces sestavení vytvoří soubor *Function. JSON* ve složce Functions ve složce Build. Jak bylo uvedeno dříve, tento soubor není určen k úpravám přímo. Konfiguraci vazby nemůžete změnit ani tuto funkci můžete zakázat úpravou tohoto souboru. 

Účelem tohoto souboru je poskytnout informace pro kontroler škálování pro použití při [rozhodování o škálování podle plánu spotřeby](functions-scale.md#how-the-consumption-and-premium-plans-work). Z tohoto důvodu má soubor pouze aktivační událost, nikoli vstupní nebo výstupní vazby.

Vygenerovaný soubor *Function. JSON* obsahuje vlastnost `configurationSource`, která instruuje modul runtime, aby místo konfigurace *Function. JSON* používal atributy .NET pro vazby. Tady je příklad:

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

## <a name="microsoftnetsdkfunctions"></a>Microsoft. NET. SDK. Functions

Generování souboru *Function. JSON* provádí balíček NuGet [Microsoft @ no__t-2NET @ no__t-3Sdk @ no__t-4Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Stejný balíček se používá pro obě verze 1. x a 2. x modulu runtime Functions. Cílová architektura je tím, že rozlišuje projekt 1. x z projektu 2. x. Tady jsou relevantní části souborů *. csproj* , které zobrazují různá cílová rozhraní a stejný balíček `Sdk`:

**Funkce 1. x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2. x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Mezi závislostmi balíčku `Sdk` jsou triggery a vazby. Projekt 1. x odkazuje na události triggerů a vazeb 1. x, protože tyto triggery a vazby cílí na .NET Framework, zatímco 2. x Triggers a Bindings Target .NET Core.

Balíček `Sdk` závisí také na [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json)a nepřímo na [windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Tyto závislosti zajistí, že projekt používá verze těchto balíčků, které pracují s verzí modulu runtime Functions, který je cílem projektu. Například `Newtonsoft.Json` má verzi 11 pro .NET Framework 4.6.1, ale modul runtime Functions, který cílí na .NET Framework 4.6.1, je kompatibilní pouze s `Newtonsoft.Json` 9.0.1. Takže váš kód funkce v tomto projektu musí také používat `Newtonsoft.Json` 9.0.1.

Zdrojový kód pro `Microsoft.NET.Sdk.Functions` je k dispozici v úložišti GitHub [Azure @ no__t-2functions @ no__t-3vs @ no__t-4build @ no__t-5sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Verze modulu runtime

Visual Studio používá [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) ke spouštění projektů funkcí. Základní nástroje jsou rozhraní příkazového řádku pro modul runtime Functions.

Pokud nainstalujete základní nástroje pomocí NPM, neovlivní to základní verze nástrojů používané v rámci sady Visual Studio. Pro modul runtime Functions verze 1. x aplikace Visual Studio ukládá verze základních nástrojů v *%USERPROFILE%\AppData\Local\Azure.Functions.CLI* a používá nejnovější uloženou verzi. V případě funkcí 2. x jsou základní nástroje součástí rozšíření **Azure functions a nástroje webové úlohy** . Pro 1. x a 2. x uvidíte, jaká verze se používá v výstupu konzoly při spuštění projektu Functions:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Podporované typy pro vazby

Každá vazba má své vlastní podporované typy; atribut triggeru objektu BLOB lze například použít pro parametr typu řetězec, parametr POCO, parametr `CloudBlockBlob` nebo některý z několika dalších podporovaných typů. [Článek odkazu vazby pro vazby objektů BLOB](functions-bindings-storage-blob.md#trigger---usage) obsahuje seznam všech podporovaných typů parametrů. Další informace najdete v tématech [triggery a vazby](functions-triggers-bindings.md) a [Referenční dokumentace k vazbě pro každý typ vazby](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Vazba na návratovou hodnotu metody

Můžete použít návratovou hodnotu metody pro výstupní vazbu použitím atributu na návratovou hodnotu metody. Příklady najdete v tématu [triggery a vazby](./functions-bindings-return-value.md). 

Návratovou hodnotu použijte pouze v případě, že úspěšné spuštění funkce vždy způsobí návratovou hodnotu, která bude předána výstupní vazbě. V opačném případě použijte `ICollector` nebo `IAsyncCollector`, jak je znázorněno v následující části.

## <a name="writing-multiple-output-values"></a>Zápis více výstupních hodnot

Chcete-li zapsat více hodnot do výstupní vazby, nebo pokud úspěšné vyvolání volání funkce nemusí být výsledkem cokoli k předání výstupní vazbě, použijte typy [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) . Tyto typy jsou kolekce pouze pro zápis, které jsou zapsány do výstupní vazby po dokončení metody.

Tento příklad zapisuje do stejné fronty více zpráv Queue pomocí `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>protokolování

Pokud chcete protokolovat výstup do protokolů streamování v C#, zahrňte argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Doporučujeme, abyste si pojmenovat `log`, jako v následujícím příkladu:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Vyhněte se použití `Console.Write` v Azure Functions. Další informace najdete v článku o [zápisu protokolů C# ve funkcích](functions-monitoring.md#write-logs-in-c-functions) v článku **monitorování Azure Functions** .

## <a name="async"></a>Async

Chcete-li provést [asynchronní](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)funkci, použijte klíčové slovo `async` a vraťte objekt `Task`.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

V asynchronních funkcích nelze použít parametry `out`. Pro výstupní vazby použijte místo toho [vrácenou hodnotu funkce](#binding-to-method-return-value) nebo [objekt sběrače](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Tokeny zrušení

Funkce může přijmout parametr [CancellationToken](/dotnet/api/system.threading.cancellationtoken) , který umožňuje operačnímu systému upozornit váš kód, když bude funkce ukončena. Toto oznámení můžete použít k tomu, abyste se ujistili, že funkce nekončí nečekaně způsobem, který opustí data v nekonzistentním stavu.

Následující příklad ukazuje, jak kontrolovat blížící se ukončení funkce.

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

Chcete-li získat proměnnou prostředí nebo hodnotu nastavení aplikace, použijte `System.Environment.GetEnvironmentVariable`, jak je znázorněno v následujícím příkladu kódu:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
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
}
```

Nastavení aplikace je možné číst z proměnných prostředí při vývoji místně a při spuštění v Azure. Při místním vývoji nastavení aplikace pochází z kolekce `Values` v souboru *Local. Settings. JSON* . V obou prostředích místní a Azure `GetEnvironmentVariable("<app setting name>")` načte hodnotu nastavení pojmenované aplikace. Například když pracujete místně, vrátí se název "můj web", pokud soubor *Local. Settings. JSON* obsahuje `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

Vlastnost [System. Configuration. ConfigurationManager. appSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) je alternativní rozhraní API pro získání hodnot nastavení aplikace, ale doporučujeme použít `GetEnvironmentVariable`, jak je znázorněno zde.

## <a name="binding-at-runtime"></a>Vazba za běhu

V C# a dalších jazycích .NET můžete použít [imperativní](https://en.wikipedia.org/wiki/Imperative_programming) vzor vazby, a to na rozdíl od [*deklarativních*](https://en.wikipedia.org/wiki/Declarative_programming) vazeb v atributech. Imperativní vazba je užitečná v případě, že parametry vazby je třeba vypočítat za běhu, nikoli jako dobu návrhu. S tímto modelem můžete vytvořit vazbu na podporované vstupní a výstupní vazby průběžně v kódu funkce.

Definujte imperativní vazbu následujícím způsobem:

- **Nezahrnujte atribut** do signatury funkce pro požadované imperativní vazby.
- Předejte vstupní parametr [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) nebo [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- K provedení datové C# vazby použijte následující vzor.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` je atribut rozhraní .NET, který definuje vaši vazbu, a `T` je vstupní nebo výstupní typ podporovaný tímto typem vazby. `T` nemůže být typ parametru `out` (například `out JObject`). Například výstupní vazba Mobile Apps tabulky podporuje [šest výstupních typů](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale můžete použít pouze [ICollector @ no__t-2T >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [IAsyncCollector @ no__t-4T >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) s imperativní vazbou.

### <a name="single-attribute-example"></a>Příklad jednoduchého atributu

Následující příklad kódu vytvoří [výstupní vazbu objektu BLOB úložiště](functions-bindings-storage-blob.md#output) s cestou objektu blob, která je definovaná v době běhu, a pak zapíše řetězec do objektu BLOB.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definuje vstupní nebo výstupní vazbu [objektu BLOB úložiště](functions-bindings-storage-blob.md) a [TextWriter](/dotnet/api/system.io.textwriter) je podporovaný výstupní typ vazby.

### <a name="multiple-attribute-example"></a>Příklad více atributů

Předchozí příklad získá nastavení aplikace pro připojovací řetězec hlavního účtu úložiště aplikace Function App (což je `AzureWebJobsStorage`). Můžete zadat vlastní nastavení aplikace, které se má použít pro účet úložiště, a to přidáním [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) a předáním pole atributu do `BindAsync<T>()`. Použijte parametr `Binder`, ne `IBinder`.  Příklad:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Další informace o osvědčených postupech pro Azure Functions](functions-best-practices.md)
