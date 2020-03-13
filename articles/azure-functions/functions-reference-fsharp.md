---
title: Referenční F# informace pro vývojáře Azure Functions
description: Naučte se vyvíjet Azure Functions pomocí F# skriptu.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276761"
---
# <a name="azure-functions-f-developer-reference"></a>Referenční F# informace pro vývojáře Azure Functions

F#pro Azure Functions je řešení pro snadné spouštění malých částí kódu nebo "Functions" v cloudu. Data přetoků do F# funkce prostřednictvím argumentů funkce. Názvy argumentů jsou zadány v `function.json`a existují předdefinované názvy pro přístup k takovým účelům, jako je protokolování funkcí a tokeny zrušení. 

>[!IMPORTANT]
>F#skript (. FSX) je podporován pouze pomocí [verze 1. x](functions-versions.md#creating-1x-apps) Azure Functions modulu runtime. Pokud chcete použít F# s verzí 2. x a novějšími verzemi modulu runtime, je nutné použít předkompilovaný F# projekt knihovny tříd (. FS). Pomocí sady F# [ C# ](functions-dotnet-class-library.md)Visual Studio můžete vytvořit, spravovat a publikovat projekt knihovny tříd jako projekt knihovny tříd. Další informace o verzích funkcí naleznete v tématu [Azure Functions – přehled verzí modulu runtime](functions-versions.md).

V tomto článku se předpokládá, že už jste si přečetli [Azure Functions referenci pro vývojáře](functions-reference.md).

## <a name="how-fsx-works"></a>Jak funguje. fsx
`.fsx` soubor je F# skript. Lze si představit jako F# projekt, který je obsažen v jednom souboru. Soubor obsahuje jak kód pro váš program (v tomto případě Azure Functions), tak direktivy pro správu závislostí.

Když použijete `.fsx` pro funkci Azure, běžně se pro vás přiřadí požadovaná sestavení, což vám umožní soustředit se na funkci namísto "často používaného" kódu.

## <a name="folder-structure"></a>Struktura složek

Struktura složek pro projekt F# skriptu vypadá následovně:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Existuje soubor Shared [Host. JSON](functions-host-json.md) , který se dá použít ke konfiguraci aplikace Function App. Každá funkce má svůj vlastní soubor kódu (. FSX) a konfigurační soubor vazby (Function. JSON).

Rozšíření vazby požadovaná ve [verzi 2. x a novějších verzích](functions-versions.md) modulu runtime funkcí jsou definována v souboru `extensions.csproj` se skutečnými soubory knihovny ve složce `bin`. Při vývoji místně je nutné [zaregistrovat rozšíření vazby](./functions-bindings-register.md#extension-bundles). Při vývoji funkcí v Azure Portal se tato registrace provede za vás.

## <a name="binding-to-arguments"></a>Vazba na argumenty
Každá vazba podporuje určitou sadu argumentů, jak je popsáno v [referenčních Azure Functions triggerech a vývojářích vazeb](functions-triggers-bindings.md). Například jedna z vazeb argumentu, která je triggerem objektu blob, je POCO, který lze vyjádřit pomocí F# záznamu. Příklad:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Vaše F# funkce Azure bude přebírat jeden nebo více argumentů. Když hovoříme o Azure Functionsch argumentech, odkazujeme na *vstupní* argumenty a *výstupní* argumenty. Vstupní argument je přesně to, co zvuk vypadá: vstup do funkce F# Azure Functions. *Výstupní* argument je proměnlivá data nebo `byref<>` argument, který slouží jako způsob, jak předat *data zpět z* vaší funkce.

V předchozím příkladu je `blob` vstupním argumentem a `output` je výstupní argument. Všimněte si, že jsme použili `byref<>` pro `output` (není nutné přidávat `[<Out>]` anotace). Použití `byref<>`ho typu umožňuje vaší funkci změnit, na který záznam nebo objekt odkazuje argument.

Pokud je F# záznam použit jako vstupní typ, definice záznamu musí být označena atributem `[<CLIMutable>]`, aby Azure Functions Framework umožnila odpovídající nastavení polí před předáním záznamu do funkce. V digestoři `[<CLIMutable>]` generuje metody setter pro vlastnosti záznamu. Příklad:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

F# Třídu lze také použít pro argumenty in a out. U třídy vlastnosti budou obvykle potřebovat metody getter a setter. Příklad:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Protokolování
Pokud chcete protokolovat výstup do [protokolů streamování](../app-service/troubleshoot-diagnostic-logs.md) v F#, funkce by měla převzít argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Pro zajištění konzistence doporučujeme tento argument pojmenovaný `log`. Příklad:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
Je možné použít pracovní postup `async`, ale výsledek musí vracet `Task`. To se dá udělat `Async.StartAsTask`například:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token zrušení
Pokud vaše funkce potřebuje řádně vypínat, můžete jí dát [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argument. To může být kombinované s `async`, například:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Import oborů názvů
Obory názvů lze otevřít obvyklým způsobem:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Automaticky se otevřou tyto obory názvů:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Odkazování na externí sestavení
Podobně je možné přidat odkazy na sestavení rozhraní s direktivou `#r "AssemblyName"`.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Následující sestavení jsou automaticky přidána pomocí hostitelského prostředí Azure Functions:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Kromě toho následující sestavení jsou speciální použita a mohou být odkazovány pomocí pole jednoduchým způsobem (například `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Pokud potřebujete odkazovat na soukromé sestavení, můžete nahrát soubor sestavení do složky `bin` relativní vzhledem k vaší funkci a odkazovat na ni pomocí názvu souboru (např.  `#r "MyAssembly.dll"`). Informace o tom, jak nahrát soubory do složky funkcí, najdete v následující části o správě balíčků.

## <a name="editor-prelude"></a>Editor předehru
Editor, který podporuje F# služby kompilátoru, nebude vědět o oborech názvů a sestaveních, která Azure Functions automaticky zahrnuje. V takovém případě může být užitečné zahrnout předehru, který pomáhá editoru najít sestavení, která používáte, a explicitně otevřít obory názvů. Příklad:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Když Azure Functions spustí váš kód, zpracuje zdroj s definovaným `COMPILED`, takže Editor předehru se bude ignorovat.

<a name="package"></a>

## <a name="package-management"></a>Správa balíčků
Pokud chcete ve F# funkci použít balíčky NuGet, přidejte `project.json` soubor do složky funkce v systému souborů Function App. Tady je příklad `project.json` souboru, který přidá odkaz na balíček NuGet do `Microsoft.ProjectOxford.Face` verze 1.1.0:

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

Je podporována pouze .NET Framework 4,6, takže se ujistěte, že `project.json` soubor určuje `net46`, jak je znázorněno zde.

Když nahrajete soubor `project.json`, modul runtime získá balíčky a automaticky přidá odkazy na sestavení balíčku. Nemusíte přidávat direktivy `#r "AssemblyName"`. Stačí přidat požadované příkazy `open` do souboru `.fsx`.

Můžete chtít umístit automaticky odkazy na sestavení v editoru předehru, aby se zlepšila interakce editoru s F# kompilovat služby.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Postup přidání souboru `project.json` do funkce Azure Functions
1. Začněte tím, že zajistěte, aby vaše aplikace Function App běžela, a to tak, že v Azure Portal spustíte funkci. Tím také získáte přístup k protokolům streamování, ve kterých se zobrazí výstup instalace balíčku.
2. Pokud chcete nahrát soubor `project.json`, použijte jednu z metod popsaných v tématu [Postup aktualizace souborů aplikace Function App](functions-reference.md#fileupdate). Pokud používáte [průběžné nasazování pro Azure Functions](functions-continuous-deployment.md), můžete do pracovní větve přidat soubor `project.json`, abyste ho mohli před přidáním do větve pro nasazení experimentovat.
3. Po přidání souboru `project.json` se v protokolu streamování vaší funkce zobrazí výstup podobný následujícímu příkladu:

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
Chcete-li získat proměnnou prostředí nebo hodnotu nastavení aplikace, použijte `System.Environment.GetEnvironmentVariable`například:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Znovu se používá kód. fsx
Pomocí direktivy `#load` můžete použít kód z jiných `.fsx` souborů. Příklad:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Cesty poskytují direktivu `#load` jsou relativní vzhledem k umístění vašeho souboru `.fsx`.

* `#load "logger.fsx"` načte soubor umístěný ve složce Functions.
* `#load "package\logger.fsx"` načte soubor umístěný ve složce `package` ve složce Functions.
* `#load "..\shared\mylogger.fsx"` načte soubor umístěný ve složce `shared` na stejné úrovni jako složka funkce, to znamená přímo pod `wwwroot`.

Direktiva `#load` pracuje pouze se soubory `.fsx`F# (skriptu), nikoli se soubory `.fs`.

## <a name="next-steps"></a>Další kroky
Další informace naleznete v následujících zdrojích:

* [F#Program](/dotnet/articles/fsharp/index)
* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)
* [Testování Azure Functions](functions-test-a-function.md)
* [Azure Functions škálování](functions-scale.md)

