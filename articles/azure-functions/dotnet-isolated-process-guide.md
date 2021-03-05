---
title: Průvodce izolovaným procesem pro .NET 5,0 v Azure Functions
description: Naučte se používat izolované procesy .NET ke spouštění funkcí C# na platformě .NET 5,0 mimo procesy v Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: b13c19aea3c3d36bd1cb4237278e5f0edbb8ed54
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181716"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Průvodce pro spouštění funkcí v .NET 5,0 v Azure

_Podpora .NET 5,0 je aktuálně ve verzi Preview._

Tento článek je Úvod k použití jazyka C# k vývoji funkcí izolovaného procesu .NET, které se spouštějí mimo proces v Azure Functions. Spuštění mimo proces umožňuje oddělit kód vaší funkce od Azure Functions modulu runtime. Poskytuje také způsob, jak vytvořit a spustit funkce, které cílí na aktuální verzi rozhraní .NET 5,0. 

Pokud nepotřebujete podporovat .NET 5,0 nebo spouštět funkce mimo proces, možná budete chtít místo toho [vyvinout funkce knihovny tříd jazyka C#](functions-dotnet-class-library.md).

## <a name="why-net-isolated-process"></a>Proč se jedná o izolovaný proces .NET?

Dříve Azure Functions podporoval pouze úzce integrovaný režim pro funkce .NET, které se spouští [jako knihovna tříd](functions-dotnet-class-library.md) ve stejném procesu jako hostitel. Tento režim zajišťuje rozsáhlou integraci mezi hostitelským procesem a funkcemi. Například funkce knihovny tříd .NET mohou sdílet vazby rozhraní API a typy. Tato integrace ale také vyžaduje užší propojení mezi hostitelským procesem a funkcí .NET. Například funkce .NET spuštěné v procesu jsou požadovány ke spuštění na stejné verzi rozhraní .NET jako modul runtime Functions. Pokud chcete povolit spouštění mimo tato omezení, můžete se teď rozhodnout spustit v izolovaném procesu. Tato izolace procesu také umožňuje vyvíjet funkce, které používají aktuální verze rozhraní .NET (například .NET 5,0), nikoli nativně podporované modulem runtime Functions.

Vzhledem k tomu, že tyto funkce jsou spouštěny v samostatném procesu, existují některé [rozdíly funkcí a funkcí](#differences-with-net-class-library-functions) mezi aplikacemi pro izolované funkce .NET a aplikacemi funkcí knihoven tříd .NET.

### <a name="benefits-of-running-out-of-process"></a>Výhody spuštění mimo proces

Při spuštění mimo proces můžou vaše funkce .NET využít výhod následujících výhod: 

+ Méně konfliktů: vzhledem k tomu, že se funkce spouštějí v samostatném procesu, nebudou sestavení použitá ve vaší aplikaci v konfliktu s jinou verzí stejného sestavení, kterou používá hostitelský proces.  
+ Úplné řízení procesu: řídíte spuštění aplikace a můžete řídit používané konfigurace a spuštění middlewaru.
+ Vkládání závislostí: vzhledem k tomu, že máte úplnou kontrolu nad procesem, můžete použít aktuální chování rozhraní .NET pro vkládání závislostí a zahrnutí middlewaru do aplikace Function App. 

## <a name="supported-versions"></a>Podporované verze

Jediná verze rozhraní .NET, která je aktuálně podporována pro spuštění mimo proces, je .NET 5,0.

## <a name="net-isolated-project"></a>Izolovaný projekt .NET

Projekt izolované funkce .NET je v podstatě projekt konzolové aplikace .NET, který cílí na .NET 5,0. Níže jsou uvedené základní soubory vyžadované v jakémkoli projektu izolovaném na rozhraní .NET:

+ [host.jsv](functions-host-json.md) souboru.
+ [local.settings.jsv](functions-run-local.md#local-settings-file) souboru.
+ Soubor projektu C# (. csproj), který definuje projekt a závislosti.
+ Program.cs soubor, který je vstupním bodem aplikace.

## <a name="package-references"></a>Odkazy na balíčky

Při spuštění mimo proces používá váš projekt .NET jedinečnou sadu balíčků, které implementují základní funkce i rozšíření vazby. 

### <a name="core-packages"></a>Základní balíčky 

Následující balíčky jsou požadovány ke spuštění funkcí .NET v izolovaném procesu:

+ [Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft. Azure. Functions. Worker. SDK](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Balíčky rozšíření

Vzhledem k tomu, že funkce, které jsou spuštěny v izolovaném procesu .NET, používají jiné typy vazeb, vyžadují jedinečnou sadu balíčků rozšíření vazby. 

Tyto balíčky rozšíření najdete v části [Microsoft. Azure. Functions. Worker. Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions).
 
## <a name="start-up-and-configuration"></a>Spuštění a konfigurace 

Při použití izolovaných funkcí .NET máte přístup ke spuštění aplikace Function App, která je obvykle v Program.cs. Zodpovídáte za vytvoření a spuštění vlastní instance hostitele. V takovém případě máte také přímý přístup ke konfiguračnímu kanálu vaší aplikace. Můžete mnohem snadněji vložit závislosti a spustit middleware při spuštění mimo proces. 

Následující kód ukazuje příklad `HostBuilder` kanálu:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="20-33":::

`HostBuilder`Používá se k sestavení a vrácení plně inicializované `IHost` instance, která se spouští asynchronně, aby se spustila aplikace Function App. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="35":::

### <a name="configuration"></a>Konfigurace

Přístup k kanálu tvůrce hostitele znamená, že při inicializaci můžete nastavit všechny konfigurace specifické pro danou aplikaci. Tyto konfigurace se vztahují na vaši aplikaci Function App spuštěnou v samostatném procesu. Chcete-li provést změny v konfiguraci hostitele nebo triggeru funkcí a vazeb, je stále nutné použít [host.jsv souboru](functions-host-json.md).      

Následující příklad ukazuje, jak přidat konfiguraci `args` , která je načítána jako argumenty příkazového řádku: 
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="21-24" :::

`ConfigureAppConfiguration`Metoda se používá ke konfiguraci zbytku procesu sestavení a aplikace. V tomto příkladu se používá také [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder?view=dotnet-plat-ext-5.0&preserve-view=true), což usnadňuje přidávání více položek konfigurace. Vzhledem `ConfigureAppConfiguration` k tomu, že vrací stejnou instanci [`IConfiguration `](/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true) , můžete ji také jednoduše volat několikrát, chcete-li přidat více položek konfigurace. Můžete získat přístup k úplné sadě konfigurací z i [`HostBuilderContext.Configuration`](/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration?view=dotnet-plat-ext-5.0&preserve-view=true) [`IHost.Services`](/dotnet/api/microsoft.extensions.hosting.ihost.services?view=dotnet-plat-ext-5.0&preserve-view=true) .

Další informace o konfiguraci najdete v tématu [konfigurace v ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

### <a name="dependency-injection"></a>Injektáž závislostí

Vkládání závislostí je zjednodušené, v porovnání s knihovnami tříd .NET. Místo toho, abyste museli vytvářet spouštěcí třídu pro registraci služeb, stačí zavolat `ConfigureServices` na tvůrce hostitele a použít rozšiřující metody pro [`IServiceCollection`](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true) Vložení konkrétních služeb. 

Následující příklad vloží závislost služby typu Singleton:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="29-32" :::

Další informace najdete v tématu [vkládání závislostí v ASP.NET Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Middleware

Rozhraní .NET izolované také podporuje registraci middlewaru pomocí modelu podobného tomu, co existuje v ASP.NET. Tento model poskytuje možnost vložit logiku do kanálu volání a před a po spuštění funkcí.

I když plná registrační sada rozhraní API pro middleware ještě není vystavená, podporuje se registrace middlewaru a do této ukázkové aplikace jsme přidali příklad do složky middlewaru.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="25-28" :::

## <a name="execution-context"></a>Kontext spuštění

Rozhraní .NET izolované předá `FunctionContext` objekt vašim metodám funkce. Tento objekt umožňuje získat [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) instanci pro zápis do protokolů voláním `GetLogger` metody a zadáním `categoryName` řetězce. Další informace najdete v tématu [protokolování](#logging). 

## <a name="bindings"></a>Vazby 

Vazby jsou definovány pomocí atributů v metodách, parametrech a návratových typech. Metoda Function je metoda s `Function` atributem a triggerem použitým pro vstupní parametr, jak je znázorněno v následujícím příkladu:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" range="11-14" :::

Atribut Trigger určuje typ triggeru a váže vstupní data k parametru metody. Předchozí ukázková funkce je aktivována zprávou fronty a zpráva fronty je předána metodě v `myQueueItem` parametru.

`Function`Atribut označuje metodu jako vstupní bod funkce. Název musí být v rámci projektu jedinečný, musí začínat písmenem a obsahovat jenom písmena, číslice, `_` a `-` až 127 znaků. Šablony projektu často vytvářejí metodu s názvem `Run` , ale název metody může být libovolný platný název metody jazyka C#.

Vzhledem k tomu, že projekty izolované .NET běží v samostatném pracovním procesu, vazby nemůžou využívat bohatou vazbu tříd, jako jsou `ICollector<T>` , `IAsyncCollector<T>` a `CloudBlockBlob` . K dispozici není žádná přímá podpora pro typy děděné ze základních sad SDK služby, jako jsou [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Namísto toho vazby spoléhají na řetězce, pole a Serializovatelné typy, jako jsou například obyčejné staré objekty třídy (POCOs). 

Pro aktivační události HTTP je nutné použít `HttpRequestData` a `HttpResponseData` pro přístup k datům žádosti a odpovědi. Důvodem je to, že nemáte přístup k původním objektům požadavků HTTP a odpovědí při spuštění mimo proces. 

### <a name="input-bindings"></a>Vstupní vazby

Funkce může mít nula nebo více vstupních vazeb, které mohou předat data funkci. Podobně jako triggery jsou vstupní vazby definovány použitím atributu vazby na vstupní parametr. Když se funkce spustí, modul runtime se pokusí získat data zadaná ve vazbě. Požadovaná data jsou často závislá na informacích poskytnutých triggerem pomocí parametrů vazby.  

### <a name="output-bindings"></a>Výstupní vazby

Chcete-li zapisovat do výstupní vazby, je nutné použít výstupní vazbu atributu na metodu funkce, která definuje, jak zapisovat do vázané služby. Hodnota vrácená metodou je zapsána do výstupní vazby. Například následující příklad zapíše řetězcovou hodnotu do fronty zpráv s názvem `functiontesting2` pomocí výstupní vazby:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" range="11-21" :::

### <a name="multiple-output-bindings"></a>Více výstupních vazeb

Data zapsaná do výstupní vazby jsou vždy návratovou hodnotou funkce. Pokud potřebujete zapisovat do více než jedné výstupní vazby, je nutné vytvořit vlastní návratový typ. Tento návratový typ musí mít atribut výstupní vazby použit pro jednu nebo více vlastností třídy. Následující příklad zapisuje do odpovědi HTTP a výstupní vazby fronty:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Function1/Function1.cs" range="14-33":::

### <a name="http-trigger"></a>HTTP trigger

Aktivační události protokolu HTTP přeloží příchozí zprávu požadavku HTTP do `HttpRequestData` objektu, který je předán funkci. Tento objekt poskytuje data z požadavku, včetně `Headers` ,, `Cookies` , `Identities` `URL` a volitelné zprávy `Body` . Tento objekt je reprezentace objektu požadavku HTTP a nikoli samotného požadavku. 

Podobně funkce vrátí `HttpReponseData` objekt, který poskytuje data použitá k vytvoření odpovědi HTTP, včetně zprávy `StatusCode` , `Headers` a volitelně zprávy `Body` .  

Následující kód je Trigger HTTP. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" range="13-27" :::

## <a name="logging"></a>protokolování

V izolovaném rozhraní .NET můžete zapisovat do protokolů pomocí [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) instance získané z `FunctionContext` objektu předaného do funkce. Zavolejte `GetLogger` metodu a předejte řetězcovou hodnotu, která je název pro kategorii, do které jsou protokoly zapisovány. Kategorie je obvykle název konkrétní funkce, ze které se zapisují protokoly. Další informace o kategoriích najdete v [článku monitorování](functions-monitoring.md#log-levels-and-categories). 

Následující příklad ukazuje, jak získat `ILogger` protokoly a zapsat je uvnitř funkce:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" range="17-18" ::: 

Použijte různé metody `ILogger` pro zápis různých úrovní protokolu, například `LogWarning` nebo `LogError` . Další informace o úrovních protokolu naleznete v [článku monitorování](functions-monitoring.md#log-levels-and-categories).

K [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) dispozici je také při použití [Injektáže závislosti](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Rozdíly s funkcemi knihovny tříd .NET

Tato část popisuje aktuální stav rozdílů funkčního a chování, které běží na platformě .NET 5,0, v porovnání s funkcemi knihovny tříd .NET spuštěné v procesu:

| Funkce/chování |  Vnitroprocesové v procesu (.NET Core 3,1) | Mimo proces (.NET 5,0) |
| ---- | ---- | ---- |
| Verze rozhraní .NET | LTS (.NET Core 3,1) | Aktuální (.NET 5,0) |
| Základní balíčky | [Microsoft. NET. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Balíčky rozšíření vazby | [`Microsoft.Azure.WebJobs.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Položk [`Microsoft.Azure.Functions.Worker.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| protokolování | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) předáno do funkce | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) získáno z `FunctionContext` |
| Tokeny zrušení | [Podporováno](functions-dotnet-class-library.md#cancellation-tokens) | Nepodporováno |
| Výstupní vazby | Výstupní parametry | Vrácené hodnoty |
| Typy výstupních vazeb |  `IAsyncCollector`, [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet&preserve-view=true), [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true)a jiné typy specifické pro klienta | Jednoduché typy, Serializovatelné typy JSON a pole. |
| Více výstupních vazeb | Podporováno | [Podporováno](#multiple-output-bindings) |
| HTTP trigger | [`HttpRequest`](/dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true)/[`ObjectResult`](/dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true) | `HttpRequestData`/`HttpResponseData` |
| Odolná služba Functions | [Podporováno](durable/durable-functions-overview.md) | Nepodporováno | 
| Imperativní vazby | [Podporováno](functions-dotnet-class-library.md#binding-at-runtime) | Nepodporováno |
| function.jsartefaktu | Dojde | Negenerováno |
| Konfigurace | [host.jsna](functions-host-json.md) | [host.js](functions-host-json.md) a [vlastní inicializace](#configuration) |
| Injektáž závislostí | [Podporováno](functions-dotnet-dependency-injection.md)  | [Podporováno](#dependency-injection) |
| Middleware | Nepodporováno | [Podporováno](#middleware) |
| Časy studeného startu | Typické | Delší dobu jako při spuštění. Spustit na platformě Linux místo systému Windows, aby se snížila možná zpoždění. |
| ReadyToRun | [Podporováno](functions-dotnet-class-library.md#readytorun) | _TBD_ |


## <a name="next-steps"></a>Další kroky

+ [Další informace o aktivačních událostech a vazbách](functions-triggers-bindings.md)
+ [Další informace o osvědčených postupech pro Azure Functions](functions-best-practices.md)
