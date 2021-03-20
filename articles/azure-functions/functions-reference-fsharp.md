---
title: 'Referenční informace pro vývojáře v jazyce F # Azure Functions'
description: 'Naučte se vyvíjet Azure Functions pomocí skriptu jazyka F #.'
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: f9b7b92fd21e12f1d86c5d5878e48c6ec6b0e748
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87088015"
---
# <a name="azure-functions-f-developer-reference"></a>Referenční informace pro vývojáře v jazyce F # Azure Functions

F # pro Azure Functions je řešení pro snadné spouštění malých částí kódu nebo "Functions" v cloudu. Data se do funkce F # převedou prostřednictvím argumentů funkce. Názvy argumentů jsou zadány v `function.json` a existují předdefinované názvy pro přístup k takovým účelům, jako je protokolování funkcí a tokeny zrušení. 

>[!IMPORTANT]
>Skript F # (. FSX) je podporován pouze [verzí 1. x](functions-versions.md#creating-1x-apps) modulu runtime Azure Functions. Pokud chcete použít F # s verze 2. x a novějšími verzemi modulu runtime, je nutné použít předkompilovaný projekt knihovny tříd F # (. FS). Pomocí sady Visual Studio vytvoříte, spravujete a publikujete projekt knihovny tříd F # stejně jako [projekt knihovny tříd jazyka C#](functions-dotnet-class-library.md). Další informace o verzích funkcí naleznete v tématu [Azure Functions – přehled verzí modulu runtime](functions-versions.md).

V tomto článku se předpokládá, že už jste si přečetli [Azure Functions referenci pro vývojáře](functions-reference.md).

## <a name="how-fsx-works"></a>Jak funguje. fsx
`.fsx`Soubor je skript jazyka F #. Lze si představit jako projekt F #, který je obsažen v jednom souboru. Soubor obsahuje jak kód pro váš program (v tomto případě Azure Functions), tak direktivy pro správu závislostí.

Použijete-li `.fsx` pro funkci Azure Functions, běžně požadovaná sestavení jsou pro vás automaticky vložena, což vám umožní soustředit se na funkci namísto "často používaného" kódu.

## <a name="folder-structure"></a>Struktura složek

Struktura složky pro projekt skriptu F # vypadá následovně:

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

V souboru je sdílený [host.js](functions-host-json.md) , který se dá použít ke konfiguraci aplikace Function App. Každá funkce má svůj vlastní soubor kódu (. FSX) a konfigurační soubor vazby (function.json).

Rozšíření vazby požadovaná ve [verzi 2. x a novějších verzích](functions-versions.md) modulu runtime Functions jsou v souboru definovány se `extensions.csproj` skutečnými soubory knihoven ve `bin` složce. Při vývoji místně je nutné [zaregistrovat rozšíření vazby](./functions-bindings-register.md#extension-bundles). Při vývoji funkcí v Azure Portal se tato registrace provede za vás.

## <a name="binding-to-arguments"></a>Vazba na argumenty
Každá vazba podporuje určitou sadu argumentů, jak je popsáno v [referenčních Azure Functions triggerech a vývojářích vazeb](functions-triggers-bindings.md). Například jedna z vazeb argumentu, která je triggerem objektu blob, je POCO, který lze vyjádřit pomocí záznamu F #. Například:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Vaše funkce F # Azure provede jeden nebo více argumentů. Když hovoříme o Azure Functionsch argumentech, odkazujeme na *vstupní* argumenty a *výstupní* argumenty. Vstupní argument je přesně to, co zvuk vypadá: vstup do funkce Azure Functions jazyka F #. *Výstupní* argument je proměnlivá data nebo `byref<>` argument, který slouží jako způsob, jak předat data zpět  z vaší funkce.

V předchozím příkladu `blob` je vstupní argument a `output` je výstupní argument. Všimněte si, že jsme použili `byref<>` pro `output` (není nutné přidávat `[<Out>]` anotaci). Použití `byref<>` typu umožňuje vaší funkci změnit, na který záznam nebo objekt odkazuje argument.

Pokud je jako vstupní typ použit záznam F #, definice záznamu musí být označena atributem, `[<CLIMutable>]` aby mohla Azure Functions Framework správně nastavit pole před předáním záznamu do funkce. V digestoři `[<CLIMutable>]` vygeneruje metody setter pro vlastnosti záznamu. Například:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Třídu jazyka F # lze také použít pro argumenty in a out. U třídy vlastnosti budou obvykle potřebovat metody getter a setter. Například:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>protokolování
Pokud chcete protokolovat výstup do [protokolů streamování](../app-service/troubleshoot-diagnostic-logs.md) v F #, funkce by měla převzít argument typu [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). Pro zajištění konzistence doporučujeme tento argument pojmenovaný `log` . Například:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
`async`Pracovní postup lze použít, ale výsledek musí vracet `Task` . To se dá udělat `Async.StartAsTask` například takto:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token zrušení
Pokud vaše funkce potřebuje řádné vypnutí, můžete mu přidělit [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argument. To lze kombinovat `async` například takto:

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
Podobně je možné přidat odkazy na sestavení rozhraní s `#r "AssemblyName"` direktivou.

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

Kromě toho následující sestavení jsou speciální použita a mohou být odkazovány pomocí pole jednoduchý (např. `#r "AssemblyName"` ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Pokud potřebujete odkazovat na soukromé sestavení, můžete nahrát soubor sestavení do `bin` složky relativní vzhledem k vaší funkci a odkazovat na ni pomocí názvu souboru (např.  `#r "MyAssembly.dll"`). Informace o tom, jak nahrát soubory do složky funkcí, najdete v následující části o správě balíčků.

## <a name="editor-prelude"></a>Editor předehru
Editor, který podporuje služby kompilátoru F #, nebude vědět o oborech názvů a sestaveních, která Azure Functions automaticky zahrnuje. V takovém případě může být užitečné zahrnout předehru, který pomáhá editoru najít sestavení, která používáte, a explicitně otevřít obory názvů. Například:

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

Když Azure Functions spustí váš kód, zpracuje zdroj s `COMPILED` definovaný, takže Editor předehru bude ignorován.

<a name="package"></a>

## <a name="package-management"></a>Správa balíčků
Chcete-li použít balíčky NuGet ve funkci jazyka F #, přidejte `project.json` soubor do složky funkce v systému souborů Function App. Tady je příklad `project.json` souboru, který přidá odkaz na balíček NuGet do `Microsoft.ProjectOxford.Face` verze 1.1.0:

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

Je podporována pouze .NET Framework 4,6, proto se ujistěte, že je `project.json` `net46` zde uveden soubor, jak je znázorněno zde.

Když nahrajete `project.json` soubor, modul runtime získá balíčky a automaticky přidá odkazy na sestavení balíčku. Nemusíte přidávat `#r "AssemblyName"` direktivy. Stačí přidat požadované `open` příkazy do `.fsx` souboru.

Můžete chtít umístit automaticky odkazy na sestavení v editoru předehru, aby se zlepšila interakce vašeho editoru s kompilovat službami F #.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Postup přidání `project.json` souboru do funkce Azure Functions
1. Začněte tím, že zajistěte, aby vaše aplikace Function App běžela, a to tak, že v Azure Portal spustíte funkci. Tím také získáte přístup k protokolům streamování, ve kterých se zobrazí výstup instalace balíčku.
2. Pokud chcete nahrát `project.json` soubor, použijte jednu z metod popsaných v tématu [Postup aktualizace souborů aplikace Function App](functions-reference.md#fileupdate). Pokud používáte [průběžné nasazování pro Azure Functions](functions-continuous-deployment.md), můžete `project.json` do pracovní větve přidat soubor, abyste ho mohli před přidáním do větve pro nasazení experimentovat.
3. Po `project.json` Přidání souboru se v protokolu streamování vaší funkce zobrazí výstup podobný následujícímu příkladu:

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
Chcete-li získat proměnnou prostředí nebo hodnotu nastavení aplikace, použijte `System.Environment.GetEnvironmentVariable` například:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Znovu se používá kód. fsx
Pomocí direktivy můžete použít kód z jiných `.fsx` souborů `#load` . Například:

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

Cesty poskytují `#load` direktivám relativním k umístění `.fsx` souboru.

* `#load "logger.fsx"` Načte soubor umístěný ve složce Functions.
* `#load "package\logger.fsx"` Načte soubor umístěný ve `package` složce ve složce Functions.
* `#load "..\shared\mylogger.fsx"` Načte soubor umístěný ve `shared` složce na stejné úrovni jako složka funkce, tj. přímo pod `wwwroot` .

`#load`Direktiva funguje pouze se `.fsx` soubory (F # Script), nikoli se `.fs` soubory.

## <a name="next-steps"></a>Další kroky
Další informace naleznete v následujících zdrojích:

* [F# Guide](/dotnet/articles/fsharp/index)
* [Osvědčené postupy pro Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)
* [Testování Azure Functions](functions-test-a-function.md)
* [Azure Functions škálování](functions-scale.md)
