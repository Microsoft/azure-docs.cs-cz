---
title: Odkaz na vývojáře Azure Functions C#
description: Zjistěte, jak vyvíjet funkce Azure pomocí jazyka C#.
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: cfa53fe2defca768196af595c1d088d41bc60f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277060"
---
# <a name="azure-functions-c-developer-reference"></a>Odkaz na vývojáře Azure Functions C#

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Tento článek je úvod do vývoje funkcí Azure pomocí Jazyka C# v knihovnách tříd .NET.

Funkce Azure podporuje programovací jazyky skriptů C# a C#. Pokud hledáte pokyny k [používání C# na webu Azure Portal](functions-create-function-app-portal.md), podívejte se na odkaz pro [vývojáře skriptu C# (.csx).](functions-reference-csharp.md)

Tento článek předpokládá, že jste si již přečetli následující články:

* [Průvodce vývojáři funkcí Azure](functions-reference.md)
* [Nástroje Visual Studia 2019 azure funkce](functions-develop-vs.md)

## <a name="supported-versions"></a>Podporované verze

Verze funkce za běhu pracují s konkrétními verzemi rozhraní .NET. V následující tabulce je uvedena nejvyšší úroveň rozhraní .NET Core a .NET Framework a .NET Core, kterou lze použít s konkrétní verzí funkcí v projektu. 

| Funkční verze runtime | Max .NET verze |
| ---- | ---- |
| Funkce 3.x | .NET Jádro 3.1 |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET Framework 4.6 |

Další informace najdete v tématu [Přehled verzí runtime Azure Functions](functions-versions.md)

## <a name="functions-class-library-project"></a>Projekt knihovny tříd Functions

V sadě Visual Studio vytvoří šablona projektu **Azure Functions** projekt c# projekt knihovny třídy, který obsahuje následující soubory:

* [host.json](functions-host-json.md) – ukládá nastavení konfigurace, které ovlivňují všechny funkce v projektu při spuštění místně nebo v Azure.
* [local.settings.json](functions-run-local.md#local-settings-file) - ukládá nastavení aplikací a připojovací řetězce, které se používají při místním spuštění. Tento soubor obsahuje tajné klíče a není publikován do aplikace funkce v Azure. Místo toho [přidejte nastavení aplikace do aplikace funkce](functions-develop-vs.md#function-app-settings).

Při vytváření projektu je ve výstupním adresáři sestavení generována struktura složek, která vypadá jako následující příklad:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Tento adresář je to, co se nasadí do vaší aplikace funkce v Azure. Rozšíření vazby požadované ve [verzi 2.x](functions-versions.md) functions runtime jsou [přidány do projektu jako balíčky NuGet](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Proces sestavení vytvoří soubor *function.json* pro každou funkci. Tento soubor *function.json* není určen k přímé úpravě. Úpravou tohoto souboru nelze změnit konfiguraci vazby nebo zakázat funkci. Informace o zakázání funkce naleznete v tématu [Jak zakázat funkce](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Metody rozpoznané jako funkce

V knihovně tříd je funkce statickou `FunctionName` metodou s atributem a a a trigger, jak je znázorněno v následujícím příkladu:

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

Atribut `FunctionName` označí metodu jako vstupní bod funkce. Název musí být jedinečný v rámci projektu, musí začínat `_`písmenem a obsahovat pouze písmena, čísla a `-`písma o délce až 127 znaků. Šablony projektu často vytvářejí `Run`metodu s názvem , ale název metody může být libovolný platný název metody Jazyka C#.

Atribut aktivační události určuje typ aktivační události a váže vstupní data na parametr metody. Ukázková funkce je spuštěna zprávou fronty a zpráva fronty `myQueueItem` je předána metodě v parametru.

## <a name="method-signature-parameters"></a>Parametry podpisu metody

Podpis metody může obsahovat jiné parametry než ty, které byly použity s atributem aktivační události. Zde jsou některé z dalších parametrů, které můžete zahrnout:

* [Vstupní a výstupní vazby](functions-triggers-bindings.md) označené jako takové zdobení je s atributy.  
* Parametr `ILogger` `TraceWriter` nebo ([verze 1.x pouze](functions-versions.md#creating-1x-apps)) pro [protokolování](#logging).
* Parametr `CancellationToken` pro [řádné vypnutí](#cancellation-tokens).
* [Parametry výrazů vazby](./functions-bindings-expressions-patterns.md) pro získání metadat aktivační události.

Nezáleží na pořadí parametrů v podpisu funkce. Můžete například umístit parametry aktivační události před nebo za jiné vazby a můžete umístit parametr protokolování před nebo za parametry aktivační události nebo vazby.

### <a name="output-binding-example"></a>Příklad vazby výstupu

Následující příklad upraví předchozí přidáním vazby výstupní fronty. Funkce zapíše zprávu fronty, která spustí funkci na novou zprávu fronty v jiné frontě.

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

Odkazy na vazby (například[fronty úložiště)](functions-bindings-storage-queue.md)vysvětlují, které typy parametrů můžete použít s atributy aktivační události, vstupu nebo výstupní vazby.

### <a name="binding-expressions-example"></a>Příklad vazebné výrazy

Následující kód získá název fronty ke sledování z nastavení aplikace a získá čas `insertionTime` vytvoření zprávy fronty v parametru.

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

## <a name="autogenerated-functionjson"></a>Automaticky generovaná funkce.json

Proces sestavení vytvoří soubor *function.json* ve složce funkcí ve složce sestavení. Jak již bylo uvedeno dříve, tento soubor není určen k přímé úpravě. Úpravou tohoto souboru nelze změnit konfiguraci vazby nebo zakázat funkci. 

Účelem tohoto souboru je poskytnout informace řadiči váhy, který se použije pro [změnu měřítka rozhodnutí o plánu spotřeby](functions-scale.md#how-the-consumption-and-premium-plans-work). Z tohoto důvodu soubor má pouze informace o aktivační události, nikoli vstupní nebo výstupní vazby.

Generovaný soubor *function.json* obsahuje `configurationSource` vlastnost, která říká, že má být runtime používán atributy .NET pro vazby, nikoli konfigurace *function.json.* Tady je příklad:

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

## <a name="microsoftnetsdkfunctions"></a>Funkce sady Microsoft.NET.Sdk

Generování souboru *function.json* provádí balíček NuGet [Microsoft\.NET\.\.Sdk Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Stejný balíček se používá pro verzi 1.x a 2.x funkce runtime. Cílová architektura je to, co odlišuje projekt 1.x od projektu 2.x. Zde jsou příslušné části *souborů .csproj,* které zobrazují `Sdk` různé cílové architektury a stejný balíček:

**Functions 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Mezi `Sdk` závislosti balíčku jsou aktivační události a vazby. Projekt 1.x odkazuje na aktivační události a vazby 1.x, protože tyto aktivační události a vazby cílí na rozhraní .NET Framework, zatímco 2.x aktivační události a vazby cíl .NET Core.

Balíček `Sdk` také závisí na [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json)a nepřímo na [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Tyto závislosti ujistěte se, že váš projekt používá verze těchto balíčků, které pracují s functions runtime verze, která cíle projektu. Například `Newtonsoft.Json` má verzi 11 pro rozhraní .NET Framework 4.6.1, ale za běhu Functions, který `Newtonsoft.Json` se zaměřuje na rozhraní .NET Framework 4.6.1, je kompatibilní pouze s rozhraním 9.0.1. Takže váš funkční kód v tomto `Newtonsoft.Json` projektu musí také použít 9.0.1.

Zdrojový kód `Microsoft.NET.Sdk.Functions` pro je k dispozici v GitHub repo [azure\-funkce\-vs\-sestavení\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Runtime verze

Visual Studio používá [základní nástroje Azure functions](functions-run-local.md#install-the-azure-functions-core-tools) ke spuštění projektů Functions. Základní nástroje je rozhraní příkazového řádku pro modul runtime Functions.

Pokud nainstalujete základní nástroje pomocí npm, to nemá vliv na verzi nástroje Core používané visual studio. Pro funkci Runtime functions verze 1.x ukládá Visual Studio verze základních nástrojů do *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* a používá nejnovější verzi, která je v něm uložena. Pro funkce 2.x základní nástroje jsou zahrnuty v rozšíření **Azure Functions and Web Jobs Tools.** Pro oba 1.x a 2.x, můžete vidět, jaká verze se používá ve výstupu konzoly při spuštění projektu Funkce:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Podporované typy pro vazby

Každá vazba má své vlastní podporované typy; například atribut aktivační události objektu blob lze použít na parametr `CloudBlockBlob` řetězce, parametr POCO, parametr nebo některý z několika dalších podporovaných typů. Odkaz [na vazbu pro vazby objektů blob](functions-bindings-storage-blob-trigger.md#usage) uvádí všechny podporované typy parametrů. Další informace naleznete v [tématu Aktivační události a vazby](functions-triggers-bindings.md) a [vazby referenční dokumenty pro každý typ vazby](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Vazba na vrácenou hodnotu metody

Můžete použít vrácenou hodnotu metody pro výstupní vazbu použitím atributu na vrácenou hodnotu metody. Příklady naleznete v tématu [Aktivační události a vazby](./functions-bindings-return-value.md). 

Vrácenou hodnotu použijte pouze v případě, že úspěšné spuštění funkce vždy vede k návratové hodnotě, která má být předávána výstupní vazbě. V opačném `ICollector` `IAsyncCollector`případě použijte nebo , jak je znázorněno v následující části.

## <a name="writing-multiple-output-values"></a>Zápis více výstupních hodnot

Chcete-li zapsat více hodnot do výstupní vazby nebo pokud úspěšné vyvolání funkce nemusí [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) mít za následek nic předat výstupní vazbě, použijte typy nebo. Tyto typy jsou kolekce pouze pro zápis, které jsou zapsány do výstupní vazby po dokončení metody.

Tento příklad zapisuje více `ICollector`zpráv fronty do stejné fronty pomocí :

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

Chcete-li protokolovat výstup do protokolů datových proudů v c#, zahrňte argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Doporučujeme jej pojmenovat `log`, jako v následujícím příkladu:  

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

Nepoužívejte `Console.Write` v Azure Functions. Další informace najdete [v tématu Zápis protokolů ve funkcích jazyka C#](functions-monitoring.md#write-logs-in-c-functions) v článku **Monitorování funkcí Azure.**

## <a name="async"></a>Async

Chcete-li funkci [asynchronní](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), `async` použijte klíčové `Task` slovo a vraťte objekt.

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

Parametry nelze `out` použít v asynchronních funkcích. Pro výstupní vazby použijte [vrácenou hodnotu funkce](#binding-to-method-return-value) nebo [objekt kolektoru.](#writing-multiple-output-values)

## <a name="cancellation-tokens"></a>Tokeny zrušení

Funkce může přijmout [CancellationToken](/dotnet/api/system.threading.cancellationtoken) parametr, který umožňuje operačnímu systému upozornit váš kód, když se má funkce ukončit. Toto oznámení můžete použít k ujistěte se, že funkce neukončí neočekávaně způsobem, který ponechává data v nekonzistentním stavu.

Následující příklad ukazuje, jak zkontrolovat blížící se ukončení funkce.

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

Chcete-li získat proměnnou prostředí nebo `System.Environment.GetEnvironmentVariable`hodnotu nastavení aplikace, použijte , jak je znázorněno v následujícím příkladu kódu:

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

Nastavení aplikací lze číst z proměnných prostředí při vývoji místně i při spuštění v Azure. Při vývoji místně, nastavení `Values` aplikace pocházejí z kolekce v *souboru local.settings.json.* V obou prostředích, místní `GetEnvironmentVariable("<app setting name>")` a Azure, načte hodnotu nastavení pojmenované aplikace. Pokud například používáte místní, bude vráceno "Můj název webu", pokud soubor *local.settings.json* obsahuje `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

Vlastnost [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) je alternativní rozhraní API pro získání hodnot nastavení `GetEnvironmentVariable` aplikace, ale doporučujeme použít, jak je znázorněno zde.

## <a name="binding-at-runtime"></a>Vazba za běhu

V jazycích C# a dalších jazycích .NET můžete použít [imperativní](https://en.wikipedia.org/wiki/Imperative_programming) vzor vazby, na rozdíl od [*deklarativnívazby*](https://en.wikipedia.org/wiki/Declarative_programming) v atributech. Imperativní vazba je užitečná, když je třeba vypočítat parametry vazby za běhu, nikoli za čas návrhu. Pomocí tohoto vzoru můžete vázat na podporované vstupní a výstupní vazby průběžně v kódu funkce.

Definujte imperativní vazbu takto:

- **Do not** Nezahrnujte atribut v podpisu funkce pro požadované imperativní vazby.
- Předat vstupní parametr [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)nebo .
- K provedení datové vazby použijte následující vzor jazyka C#.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`je atribut .NET, který definuje `T` vazby a je vstupní nebo výstupní typ, který je podporován tímto typem vazby. `T`nemůže být `out` typ parametru `out JObject`(například ). Například vazby výstupu tabulky mobilních aplikací podporuje [šest typů výstupu](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale můžete použít pouze [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) s imperativní vazbou.

### <a name="single-attribute-example"></a>Příklad jednoho atributu

Následující ukázkový kód vytvoří [výstupní vazbu objektu blob úložiště](functions-bindings-storage-blob-output.md) s cestou objektu blob, která je definována za běhu, a pak zapíše řetězec do objektu blob.

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

[Objekt BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definuje vstupní nebo výstupní vazbu [objektu blob úložiště](functions-bindings-storage-blob.md) a [TextWriter](/dotnet/api/system.io.textwriter) je podporovaný typ vazby výstupu.

### <a name="multiple-attribute-example"></a>Příklad více atributů

V předchozím příkladu získá nastavení aplikace pro hlavní aplikace pro připojení `AzureWebJobsStorage`účtu úložiště řetězce funkce (což je). Můžete zadat vlastní nastavení aplikace, které se má použít pro účet úložiště `BindAsync<T>()`přidáním [atributu StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) a předáním pole atributů do aplikace . Použijte `Binder` parametr, `IBinder`nikoli .  Například:

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
> [Další informace o doporučených postupech pro funkce Azure](functions-best-practices.md)
