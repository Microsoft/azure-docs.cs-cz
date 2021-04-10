---
title: Vývoj funkcí knihovny tříd C# pomocí Azure Functions
description: Naučte se používat jazyk C# k vývoji a publikování kódu jako knihoven tříd, které se spouštějí v procesu pomocí modulu runtime Azure Functions.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: c7d14599ec1ebbcb94e0c0f3985a3b857f9353dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563876"
---
# <a name="develop-c-class-library-functions-using-azure-functions"></a>Vývoj funkcí knihovny tříd C# pomocí Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Tento článek je Úvod k vývoji Azure Functions pomocí jazyka C# v knihovnách tříd .NET.

>[!IMPORTANT]
>Tento článek podporuje funkce knihovny tříd .NET, které běží v procesu s modulem runtime. Funkce také podporují rozhraní .NET 5. x spuštěním funkcí C# mimo proces a izolací z modulu runtime. Další informace najdete v tématu [funkce izolovaného procesu .NET](dotnet-isolated-process-guide.md).

Jako vývojář v jazyce C# může být také zajímat některé z následujících článků:

| Začínáme | Koncepty| Učení s asistencí/ukázky |
|--| -- |--| 
| <ul><li>[Pomocí sady Visual Studio](functions-create-your-first-function-visual-studio.md)</li><li>[Používání nástroje Visual Studio Code](create-first-function-vs-code-csharp.md)</li><li>[Používání nástrojů příkazového řádku](create-first-function-cli-csharp.md)</li></ul> | <ul><li>[Možnosti hostování](functions-scale.md)</li><li>[Požadavky na výkon &nbsp;](functions-best-practices.md)</li><li>[Vývoj sady Visual Studio](functions-develop-vs.md)</li><li>[Injektáž závislostí](functions-dotnet-dependency-injection.md)</li></ul> | <ul><li>[Vytváření bezserverových aplikací](/learn/paths/create-serverless-applications/)</li><li>[Ukázky C#](/samples/browse/?products=azure-functions&languages=csharp)</li></ul> |

Azure Functions podporuje programovací jazyky C# a C#. Pokud hledáte pokyny k [používání jazyka C# v Azure Portal](functions-create-function-app-portal.md), přečtěte si téma [referenční informace pro vývojáře skriptu jazyka c# (. csx)](functions-reference-csharp.md).

## <a name="supported-versions"></a>Podporované verze

Verze běhových funkcí fungují s konkrétními verzemi .NET. Další informace o verzích funkcí najdete v tématu [Přehled verzí Azure Functions runtime](functions-versions.md) .

Následující tabulka ukazuje nejvyšší úroveň rozhraní .NET Core nebo .NET Framework, kterou lze použít s určitou verzí funkcí. 

| Verze modulu runtime Functions | Maximální verze .NET |
| ---- | ---- |
| Funkce 3. x | .NET Core 3.1<br/>.NET 5,0<sup>1</sup> |
| Functions 2.x | .NET Core 2,2<sup>2</sup> |
| Functions 1.x |  .NET Framework 4.7 |

<sup>1</sup> musí běžet [mimo proces](dotnet-isolated-process-guide.md).  
<sup>2</sup> podrobnosti najdete v tématu [informace o funkcích v2. x](#functions-v2x-considerations).   

Nejnovější novinky o Azure Functions verzích, včetně odebrání určitých starších podverzí, najdete v části [Azure App Service oznámení](https://github.com/Azure/app-service-announcements/issues).

### <a name="functions-v2x-considerations"></a>Pokyny k funkcím v2. x

Aplikace Function App, které cílí na nejnovější verzi 2. x ( `~2` ), se automaticky upgradují tak, aby běžely v .NET Core 3,1. Vzhledem k zásadním změnám mezi verzemi .NET Core, ne všechny aplikace vyvíjené a zkompilované pro .NET Core 2,2, se dají bezpečně upgradovat na .NET Core 3,1. Tento upgrade můžete odhlásit připnutím aplikace Function App `~2.0` . Funkce také zjišťují nekompatibilní rozhraní API a můžou aplikaci připnout, aby `~2.0` nedocházelo k nesprávnému provádění .NET Core 3,1. 

>[!NOTE]
>Pokud je vaše aplikace Functions připnutá `~2.0` a změníte cílovou verzi na `~2` , vaše aplikace Function App může poškodit. Pokud nasadíte pomocí šablon ARM, Projděte si verzi v šablonách. Pokud k tomu dojde, změňte verzi zpátky na cílovou `~2.0` a opravte problémy s kompatibilitou. 

Aplikace Function App, které cílí na `~2.0` běh i nadále pracují v .NET Core 2,2. Tato verze .NET Core už nepřijímá zabezpečení a další aktualizace údržby. Další informace najdete na [této stránce oznámení](https://github.com/Azure/app-service-announcements/issues/266). 

Měli byste pracovat, aby byly funkce kompatibilní s .NET Core 3,1 co nejdříve. Po vyřešení těchto problémů změňte verzi na `~2` nebo upgradujte na `~3` . Další informace o cílení verzí modulu runtime Functions najdete v tématu [jak cílit na verze Azure Functions runtime](set-runtime-version.md).

Při spuštění v systému Linux v plánu Premium nebo vyhrazený (App Service) připnete místo toho svou verzi tím, že nastavíte nastavení `linuxFxVersion` Konfigurace lokality tak `DOCKER|mcr.microsoft.com/azure-functions/dotnet:2.0.14786-appservice` , aby se zjistilo, jak je nastavit `linuxFxVersion` , v tématu [Ruční aktualizace verzí na Linux](set-runtime-version.md#manual-version-updates-on-linux).

## <a name="functions-class-library-project"></a>Projekt knihovny tříd Functions

V aplikaci Visual Studio šablona projektu **Azure Functions** vytvoří projekt knihovny tříd jazyka C#, který obsahuje následující soubory:

* [host.jsv systému](functions-host-json.md) ukládá nastavení konfigurace, která ovlivňují všechny funkce v projektu při spuštění místně nebo v Azure.
* [local.settings.jsv](functions-run-local.md#local-settings-file) aplikaci ukládá nastavení aplikace a připojovací řetězce, které se používají při místním spuštění. Tento soubor obsahuje tajné klíče a není publikovaný do aplikace Function App v Azure. Místo toho [do aplikace Function App přidejte nastavení aplikace](functions-develop-vs.md#function-app-settings).

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
> Proces sestavení vytvořífunction.jspro každou funkci *v* souboru. Tato *function.jsv* souboru není určena k přímému upravování. Konfiguraci vazby nemůžete změnit ani tuto funkci můžete zakázat úpravou tohoto souboru. Informace o tom, jak funkci zakázat, najdete v tématu [Jak zakázat funkce](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Metody rozpoznané jako funkce

V knihovně tříd je funkce statická metoda s `FunctionName` atributem a triggerem, jak je znázorněno v následujícím příkladu:

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

`FunctionName`Atribut označuje metodu jako vstupní bod funkce. Název musí být v rámci projektu jedinečný, musí začínat písmenem a obsahovat jenom písmena, číslice, `_` a `-` až 127 znaků. Šablony projektu často vytvářejí metodu s názvem `Run` , ale název metody může být libovolný platný název metody jazyka C#.

Atribut Trigger určuje typ triggeru a váže vstupní data k parametru metody. Ukázková funkce je aktivována zprávou fronty a zpráva fronty je předána metodě v `myQueueItem` parametru.

## <a name="method-signature-parameters"></a>Parametry signatury metody

Signatura metody může obsahovat parametry jiné než ta, která se používá s atributem triggeru. Tady jsou některé z dalších parametrů, které můžete použít:

* [Vstupní a výstupní vazby](functions-triggers-bindings.md) označeny jako upravení s atributy.  
* `ILogger`Parametr nebo `TraceWriter` ([pouze verze 1. x](functions-versions.md#creating-1x-apps)) pro [protokolování](#logging).
* `CancellationToken`Parametr pro [řádné vypnutí](#cancellation-tokens).
* Parametry [výrazů vazby](./functions-bindings-expressions-patterns.md) pro získání metadat triggeru.

Pořadí parametrů v signatuře funkce nezáleží. Můžete například vložit parametry triggeru před nebo za jiné vazby a parametr protokolovacího nástroje můžete vložit před nebo po Trigger nebo parametry vazby.

### <a name="output-bindings"></a>Výstupní vazby

Funkce může mít nula nebo jednu výstupní vazbu, která je definována pomocí parametrů Output. 

Následující příklad upravuje předchozí rozhraní přidáním vazby výstupní fronty s názvem `myQueueItemCopy` . Funkce zapíše obsah zprávy, která aktivuje funkci, do nové zprávy v jiné frontě.

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

Hodnoty přiřazené výstupním vazbám jsou zapsány při ukončení funkce. Můžete použít více než jednu výstupní vazbu ve funkci pouhým přiřazením hodnot k více výstupním parametrům. 

Články s odkazy na vazby (například[fronty úložiště](functions-bindings-storage-queue.md)) vysvětlují typy parametrů, které můžete použít s atributy triggeru, vstupu a výstupu.

### <a name="binding-expressions-example"></a>Příklad výrazů vazby

Následující kód Získá název fronty, která se má monitorovat, z nastavení aplikace a v parametru Získá čas vytvoření zprávy fronty `insertionTime` .

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

## <a name="autogenerated-functionjson"></a>Automaticky vygenerované function.js

Proces sestavení vytvoří *function.js* v souboru ve složce funkce ve složce sestavení. Jak bylo uvedeno dříve, tento soubor není určen k úpravám přímo. Konfiguraci vazby nemůžete změnit ani tuto funkci můžete zakázat úpravou tohoto souboru. 

Účelem tohoto souboru je poskytnout informace pro kontroler škálování pro použití při [rozhodování o škálování podle plánu spotřeby](event-driven-scaling.md). Z tohoto důvodu soubor má pouze aktivační údaje, nikoli vstupní/výstupní vazby.

Vygenerovaná *function.jsv* souboru obsahuje `configurationSource` vlastnost, která určuje, že modul runtime bude používat pro vazby atributy .NET namísto *function.jsv* konfiguraci. Tady je příklad:

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

*function.jspři* generování souboru provádí balíček NuGet [ \. funkce Microsoft .NET \. SDK \.](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Stejný balíček se používá pro obě verze 1. x a 2. x modulu runtime Functions. Cílová architektura je tím, že rozlišuje projekt 1. x z projektu 2. x. Tady jsou relevantní části souborů *. csproj* , které zobrazují různá cílová rozhraní se stejným `Sdk` balíčkem:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

# <a name="v1x"></a>[V1. x](#tab/v1)

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```
---


Mezi `Sdk` závislostmi balíčku jsou triggery a vazby. Projekt 1. x odkazuje na události triggerů a vazeb 1. x, protože tyto triggery a vazby cílí na .NET Framework, zatímco 2. x Triggers a Bindings Target .NET Core.

`Sdk`Balíček také závisí na [Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json)a nepřímo na [windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Tyto závislosti zajistí, že projekt používá verze těchto balíčků, které pracují s verzí modulu runtime Functions, který je cílem projektu. Například `Newtonsoft.Json` má verze 11 pro .NET Framework 4.6.1, ale modul runtime Functions, který cílí na .NET Framework 4.6.1, je kompatibilní pouze s `Newtonsoft.Json` 9.0.1. Takže váš kód funkce v tomto projektu musí také používat `Newtonsoft.Json` 9.0.1.

Zdrojový kód pro `Microsoft.NET.Sdk.Functions` je k dispozici v úložišti GitHub [Azure \- Functions \- vs \- \- SDK Build](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Verze modulu runtime

Visual Studio používá [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) ke spouštění projektů funkcí. Základní nástroje jsou rozhraní příkazového řádku pro modul runtime Functions.

Pokud nainstalujete základní nástroje pomocí NPM, neovlivní to základní verze nástrojů používané v rámci sady Visual Studio. Pro modul runtime Functions verze 1. x aplikace Visual Studio ukládá verze základních nástrojů v *%USERPROFILE%\AppData\Local\Azure.Functions.CLI* a používá nejnovější uloženou verzi. V případě funkcí 2. x jsou základní nástroje součástí rozšíření **Azure functions a nástroje webové úlohy** . Pro 1. x a 2. x uvidíte, jaká verze se používá v výstupu konzoly při spuštění projektu Functions:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

Aplikaci Function App můžete zkompilovat jako [binární soubory ReadyToRun](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images). ReadyToRun je forma průběžné kompilace, která může zlepšit výkon při spuštění, aby se snížil dopad [studeného](event-driven-scaling.md#cold-start) startu při spuštění v [plánu spotřeby](consumption-plan.md).

ReadyToRun je k dispozici v rozhraní .NET 3,0 a vyžaduje [verzi 3,0 modulu runtime Azure Functions](functions-versions.md).

Chcete-li zkompilovat projekt jako ReadyToRun, aktualizujte soubor projektu přidáním `<PublishReadyToRun>` prvků a `<RuntimeIdentifier>` . Následuje konfigurace pro publikování do aplikace Function App v systému Windows 32.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> ReadyToRun aktuálně nepodporuje křížovou kompilaci. Aplikaci musíte sestavit na stejné platformě jako cíl nasazení. Věnujte pozornost také "bitová verze", který je nakonfigurovaný ve vaší aplikaci Function App. Pokud je vaše aplikace Function App v Azure například Windows 64-bit, musíte aplikaci zkompilovat ve Windows s `win-x64` [identifikátorem modulu runtime](/dotnet/core/rid-catalog).

Můžete také vytvořit aplikaci pomocí ReadyToRun z příkazového řádku. Další informace naleznete `-p:PublishReadyToRun=true` v možnosti v tématu [`dotnet publish`](/dotnet/core/tools/dotnet-publish) .

## <a name="supported-types-for-bindings"></a>Podporované typy pro vazby

Každá vazba má své vlastní podporované typy; atribut triggeru objektu BLOB lze například použít na řetězcový parametr, parametr POCO, `CloudBlockBlob` parametr nebo některý z několika dalších podporovaných typů. [Článek odkazu vazby pro vazby objektů BLOB](functions-bindings-storage-blob-trigger.md#usage) obsahuje seznam všech podporovaných typů parametrů. Další informace najdete v tématech [triggery a vazby](functions-triggers-bindings.md) a [Referenční dokumentace k vazbě pro každý typ vazby](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Vazba na návratovou hodnotu metody

Můžete použít návratovou hodnotu metody pro výstupní vazbu použitím atributu na návratovou hodnotu metody. Příklady najdete v tématu [triggery a vazby](./functions-bindings-return-value.md). 

Návratovou hodnotu použijte pouze v případě, že úspěšné spuštění funkce vždy způsobí návratovou hodnotu, která bude předána výstupní vazbě. V opačném případě použijte `ICollector` nebo `IAsyncCollector` , jak je znázorněno v následující části.

## <a name="writing-multiple-output-values"></a>Zápis více výstupních hodnot

Chcete-li zapsat více hodnot do výstupní vazby nebo pokud úspěšné vyvolání volání funkce nevede k žádnému předání do výstupní vazby, použijte [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typy nebo. Tyto typy jsou kolekce pouze pro zápis, které jsou zapsány do výstupní vazby po dokončení metody.

Tento příklad zapisuje do stejné fronty více zpráv fronty pomocí `ICollector` :

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

## <a name="async"></a>Async

Chcete-li provést [asynchronní](/dotnet/csharp/programming-guide/concepts/async/)funkci, použijte `async` klíčové slovo a vraťte `Task` objekt.

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

`out`V asynchronních funkcích nemůžete použít parametry. Pro výstupní vazby použijte místo toho [vrácenou hodnotu funkce](#binding-to-method-return-value) nebo [objekt sběrače](#writing-multiple-output-values) .

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

## <a name="logging"></a>protokolování

V kódu funkce můžete napsat výstup do protokolů, které se zobrazí jako trasování v Application Insights. Doporučeným způsobem, jak zapisovat do protokolů, je zahrnout parametr typu [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger), který se obvykle nazývá `log` . Verze 1. x používaného modulu runtime Functions `TraceWriter` , který také zapisuje do Application Insights, ale nepodporuje strukturované protokolování. Nepoužívejte `Console.Write` k zápisu protokolů, protože tato data nejsou zachycena Application Insights. 

### <a name="ilogger"></a>ILogger

Do definice funkce zadejte parametr [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) , který podporuje [strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

S `ILogger` objektem zavoláte `Log<level>` [metody rozšíření v ILogger](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) k vytváření protokolů. Následující kód zapisuje `Information` protokoly do kategorie `Function.<YOUR_FUNCTION_NAME>.User.` :

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

Další informace o tom, jak funkce implementují `ILogger` , najdete v tématu [shromažďování dat telemetrie](functions-monitoring.md#collecting-telemetry-data). Kategorie s předponou `Function` předpokládá, že používáte `ILogger` instanci. Pokud se rozhodnete místo toho použít `ILogger<T>` , název kategorie může být založen na `T` .  

### <a name="structured-logging"></a>Strukturované protokolování

Pořadí zástupných symbolů, nikoli jejich názvů, určuje, které parametry se použijí ve zprávě protokolu. Předpokládejme, že máte následující kód:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Pokud zachováte stejný řetězec zprávy a obrátíte pořadí parametrů, výsledný text zprávy by měl mít hodnoty na nesprávných místech.

Zástupné symboly jsou zpracovávány tímto způsobem, aby bylo možné provádět strukturované protokolování. Application Insights ukládá páry parametr název-hodnota a řetězec zprávy. Výsledkem je, že se argumenty zprávy stanou poli, se kterými se můžete dotazovat.

Pokud vaše volání metody protokolovacího nástroje vypadá jako v předchozím příkladu, můžete zadat dotaz na pole `customDimensions.prop__rowKey` . `prop__`Je přidána předpona, aby se zajistilo, že mezi poli, které modul runtime přidává, nedochází k žádné kolizi, které přidávají kód funkce.

Můžete také zadat dotaz na původní řetězec zprávy odkazem na pole `customDimensions.prop__{OriginalFormat}` .  

Tady je ukázková reprezentace dat ve formátu JSON `customDimensions` :

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="log-custom-telemetry"></a><a name="log-custom-telemetry-in-c-functions"></a>Vlastní telemetrie protokolu

Verze Application Insights SDK specifická pro konkrétní funkce, kterou můžete použít k posílání vlastních dat telemetrie z vašich funkcí do Application Insights: [Microsoft. Azure. WebJobs. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). K instalaci tohoto balíčku použijte následující příkaz z příkazového řádku:

# <a name="command"></a>[Příkaz](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

V tomto příkazu nahraďte `<VERSION>` verzi tohoto balíčku, která podporuje vaši nainstalovanou verzi [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

Následující příklady jazyka C# používají [vlastní rozhraní API telemetrie](../azure-monitor/app/api-custom-events-metrics.md). Příklad je pro knihovnu tříd .NET, ale kód Application Insights je stejný pro skript jazyka C#.

# <a name="v2x"></a>[v2. x +](#tab/v2)

Verze 2. x a novější verze modulu runtime používají v Application Insights novějších funkcí k automatickému korelaci telemetrie s aktuální operací. Nemusíte ručně nastavit operaci `Id` , `ParentId` nebo `Name` pole.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Write an event to the customEvents table.
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Generate a custom metric, in this case let's use ContentLength.
            this.telemetryClient.GetMetric("contentLength").TrackValue(req.ContentLength);

            // Log a custom dependency in the dependencies table.
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

V tomto příkladu se vlastní data metriky agreguje hostitelem před odesláním do tabulky customMetrics. Další informace najdete v dokumentaci [getmetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) v tématu Application Insights. 

Při místním spuštění je nutné přidat `APPINSIGHTS_INSTRUMENTATIONKEY` nastavení s klíčem Application Insights do [local.settings.jsv](functions-run-local.md#local-settings-file) souboru.


# <a name="v1x"></a>[V1. x](#tab/v1)

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```
---

Nevolejte `TrackRequest` nebo `StartOperation<RequestTelemetry>` , protože se zobrazí duplicitní požadavky na vyvolání funkce.  Modul runtime Functions automaticky sleduje požadavky.

Nenastaveno `telemetryClient.Context.Operation.Id` . Toto globální nastavení způsobuje nesprávnou korelaci, pokud mnoho funkcí běží současně. Místo toho vytvořte novou instanci telemetrie ( `DependencyTelemetry` , `EventTelemetry` ) a upravte její `Context` vlastnost. Pak předejte instanci telemetrie do odpovídající `Track` metody v `TelemetryClient` ( `TrackDependency()` , `TrackEvent()` , `TrackMetric()` ). Tato metoda zajišťuje, že telemetrie má správné korelační údaje pro aktuální vyvolání funkce.


## <a name="environment-variables"></a>Proměnné prostředí

Chcete-li získat proměnnou prostředí nebo hodnotu nastavení aplikace, použijte `System.Environment.GetEnvironmentVariable` , jak je znázorněno v následujícím příkladu kódu:

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

    private static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Nastavení aplikace je možné číst z proměnných prostředí při vývoji místně a při spuštění v Azure. Při místním vývoji se nastavení aplikace podávají z `Values` kolekce v *local.settings.js* souboru. V obou prostředích místní a Azure `GetEnvironmentVariable("<app setting name>")` načítá hodnotu nastavení pojmenované aplikace. Například když spouštíte místně, vrátí se název "můj web", pokud *local.settings.js* soubor obsahuje `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` .

Vlastnost [System.Configuration.ConfigurationManager. appSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) je alternativní rozhraní API pro získání hodnot nastavení aplikace, ale doporučujeme, abyste používali, `GetEnvironmentVariable` jak je znázorněno zde.

## <a name="binding-at-runtime"></a>Vazba za běhu

V jazyce C# a dalších jazycích .NET můžete použít [imperativní](https://en.wikipedia.org/wiki/Imperative_programming) vzor vazby, a to na rozdíl od [*deklarativních*](https://en.wikipedia.org/wiki/Declarative_programming) vazeb v atributech. Imperativní vazba je užitečná v případě, že parametry vazby je třeba vypočítat za běhu, nikoli jako dobu návrhu. S tímto modelem můžete vytvořit vazbu na podporované vstupní a výstupní vazby průběžně v kódu funkce.

Definujte imperativní vazbu následujícím způsobem:

- **Nezahrnujte atribut** do signatury funkce pro požadované imperativní vazby.
- Předejte vstupní parametr [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) nebo [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- K provedení datové vazby použijte následující vzor C#.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` je atribut rozhraní .NET, který definuje vaši vazbu, a `T` je vstupní nebo výstupní typ, který je podporován typem vazby. `T` nelze `out` zadat typ parametru (například `out JObject` ). Například výstupní vazba Mobile Apps tabulky podporuje [šest výstupních typů](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale můžete použít pouze [ \<T> ICollector](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) nebo [ \<T> IAsyncCollector](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) s imperativní vazbou.

### <a name="single-attribute-example"></a>Příklad jednoduchého atributu

Následující příklad kódu vytvoří [výstupní vazbu objektu BLOB úložiště](functions-bindings-storage-blob-output.md) s cestou objektu blob, která je definovaná v době běhu, a pak zapíše řetězec do objektu BLOB.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definuje vstupní nebo výstupní vazbu [objektu BLOB úložiště](functions-bindings-storage-blob.md) a [TextWriter](/dotnet/api/system.io.textwriter) je podporovaný výstupní typ vazby.

### <a name="multiple-attributes-example"></a>Příklad více atributů

Předchozí příklad získá nastavení aplikace pro připojovací řetězec hlavního účtu úložiště aplikace Function App (což je `AzureWebJobsStorage` ). Můžete zadat vlastní nastavení aplikace, které se má použít pro účet úložiště, a to přidáním [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) a předáním pole atributu do `BindAsync<T>()` . Použijte `Binder` parametr, ne `IBinder` .  Například:

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
