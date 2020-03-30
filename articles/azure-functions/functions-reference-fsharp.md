---
title: Odkaz na vývojáře Azure Functions F#
description: Zjistěte, jak vyvíjet funkce Azure pomocí skriptu F#.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276761"
---
# <a name="azure-functions-f-developer-reference"></a>Referenční příručka pro vývojáře funkcí Azure F#

F# pro Funkce Azure je řešení pro snadné spuštění malé části kódu nebo "funkce", v cloudu. Data proudí do funkce F# prostřednictvím argumentů funkce. Názvy argumentů `function.json`jsou určeny v aplikaci a existují předdefinované názvy pro přístup k položkám, jako je protokolovací nástroj funkce a tokeny zrušení. 

>[!IMPORTANT]
>Skript Jazyka F# (.fsx) je podporován pouze [verzí 1.x](functions-versions.md#creating-1x-apps) modulu runtime Azure Functions. Pokud chcete použít F# s verzí 2.x a novější mise runtime, musíte použít předkompilovaný projekt knihovny tříd F# (.fs). Projekt knihovny tříd F# můžete vytvořit, spravovat a publikovat pomocí sady Visual Studio stejně jako [projekt knihovny tříd jazyka C#](functions-dotnet-class-library.md). Další informace o verzích functions najdete v [tématu Přehled verzí runtime Azure Functions](functions-versions.md).

Tento článek předpokládá, že jste si už přečetli [odkaz na vývojáře Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Jak přípravek .fsx funguje
Soubor `.fsx` je skript F#. To si lze myslet jako projekt F#, který je obsažen v jednom souboru. Soubor obsahuje kód pro váš program (v tomto případě funkce Azure) a direktivy pro správu závislostí.

Při použití `.fsx` funkce Azure, běžně požadované sestavení jsou automaticky zahrnuty pro vás, což vám umožní zaměřit se na funkci, nikoli "často používaný" kód.

## <a name="folder-structure"></a>Struktura složek

Struktura složek pro projekt skriptu F# vypadá takto:

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

K dispozici je sdílený soubor [host.json,](functions-host-json.md) který lze použít ke konfiguraci aplikace funkce. Každá funkce má svůj vlastní soubor kódu (.fsx) a konfigurační soubor vazby (function.json).

V `extensions.csproj` souboru jsou definovány rozšíření vazby vyžadované ve [verzi 2.x a novějších verzích](functions-versions.md) runtime Funkce se skutečnými soubory knihovny ve `bin` složce. Při vývoji místně, musíte [zaregistrovat rozšíření vazby](./functions-bindings-register.md#extension-bundles). Při vývoji funkcí na webu Azure Portal se tato registrace provádí za vás.

## <a name="binding-to-arguments"></a>Vazba na argumenty
Každá vazba podporuje některé sady argumentů, jak je podrobně popsáno v [Azure Functions aktivační události a odkazy na vývojáře](functions-triggers-bindings.md). Například jeden z vazby argumentů aktivační události objektu blob podporuje je POCO, které lze vyjádřit pomocí záznamu F#. Například:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Funkce F# Azure bude mít jeden nebo více argumentů. Když mluvíme o azure funkce argumenty, odkazujeme na *vstupní* argumenty a *výstupní* argumenty. Vstupní argument je přesně to, co zní jako: vstup do funkce F# Azure. *Výstupní* argument je proměnlivá `byref<>` data nebo argument, který slouží jako způsob, jak předat data zpět *z* vaší funkce.

Ve výše uvedeném příkladu `blob` je `output` vstupní argument a je výstupní argument. Všimněte si, že jsme použili `byref<>` pro `output` (není třeba přidávat poznámky). `[<Out>]` Použití `byref<>` typu umožňuje funkci změnit, na který záznam nebo objekt argument odkazuje.

Při použití záznamu F# jako vstupní typ, definice záznamu `[<CLIMutable>]` musí být označeny s chcete-li povolit azure functions rozhraní nastavit pole správně před předáním záznamu do funkce. Pod kapotou `[<CLIMutable>]` generuje settery pro vlastnosti záznamu. Například:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

F# třídy lze také použít pro argumenty in i out. Pro třídu vlastnosti obvykle potřebují getters a setters. Například:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>protokolování
Chcete-li protokolovat výstup do [protokolů datových proudů](../app-service/troubleshoot-diagnostic-logs.md) v f#, vaše funkce by měla trvat argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Konzistence doporučujeme tento argument `log`je pojmenován . Například:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
Pracovní `async` postup lze použít, ale výsledek `Task`musí vrátit . To lze provést `Async.StartAsTask`pomocí například:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token zrušení
Pokud vaše funkce potřebuje řádně zpracovat vypnutí, [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) můžete mu dát argument. To lze kombinovat `async`s , například:

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

Automaticky se otevírají následující obory názvů:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Odkazování na externí sestavení
Podobně odkazy na sestavení rámce lze `#r "AssemblyName"` přidat se směrnicí.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Hostitelské prostředí Azure Functions automaticky přidává následující sestavení:

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

Kromě toho jsou následující sestavy zvláštní a mohou být odkazovány jednoduchým `#r "AssemblyName"`názvem (např. ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Pokud potřebujete odkazovat na soukromé sestavení, můžete nahrát soubor sestavení do `bin` složky vzhledem k vaší funkci a odkazovat na něj pomocí názvu souboru (např.  `#r "MyAssembly.dll"`). Informace o tom, jak nahrát soubory do složky funkcí, naleznete v následující části o správě balíčků.

## <a name="editor-prelude"></a>Editor Prelude
Editor, který podporuje F# Compiler Services nebude znát obory názvů a sestavení, které funkce Azure automaticky zahrnuje. Jako takové může být užitečné zahrnout předehru, která pomáhá editoru najít sestavení, která používáte, a explicitně otevřít obory názvů. Například:

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

Když Azure Functions spustí váš kód, `COMPILED` zpracuje zdroj s definovaným, takže předehra editoru bude ignorována.

<a name="package"></a>

## <a name="package-management"></a>Správa balíčků
Chcete-li použít balíčky NuGet ve `project.json` funkci F#, přidejte soubor do složky funkce v systému souborů aplikace funkce. Zde je `project.json` ukázkový soubor, který přidá `Microsoft.ProjectOxford.Face` odkaz na balíček NuGet na verzi 1.1.0:

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

Podporována je pouze rozhraní .NET Framework 4.6, proto se ujistěte, že soubor `project.json` určuje, `net46` jak je znázorněno zde.

Když nahrajete `project.json` soubor, runtime získá balíčky a automaticky přidá odkazy na sestavení balíčků. Není nutné přidávat `#r "AssemblyName"` direktivy. Stačí do `open` `.fsx` souboru přidat požadované příkazy.

Můžete chtít umístit automaticky odkazuje sestavení v předehře editoru, ke zlepšení interakce editoru s F# Kompilace services.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Jak přidat `project.json` soubor do funkce Azure
1. Začněte tím, že se ujistíte, že je spuštěná aplikace pro funkce, což můžete udělat otevřením funkce na webu Azure Portal. To také poskytuje přístup k protokolům streamování, kde se zobrazí výstup instalace balíčku.
2. Chcete-li `project.json` nahrát soubor, použijte jednu z metod popsaných v [tom, jak aktualizovat soubory aplikací funkcí](functions-reference.md#fileupdate). Pokud používáte [průběžné nasazování pro funkce Azure](functions-continuous-deployment.md), můžete přidat `project.json` soubor do pracovní větve, abyste s ní mohli experimentovat před přidáním do větve nasazení.
3. Po `project.json` přidání souboru se výstup podobný následujícímu příkladu zobrazí v protokolu datových proudů funkce:

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
Chcete-li získat proměnnou prostředí nebo `System.Environment.GetEnvironmentVariable`hodnotu nastavení aplikace, použijte například:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Opětovné použití kódu .fsx
Kód z jiných `.fsx` souborů můžete `#load` použít pomocí směrnice. Například:

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

Cesty poskytuje `#load` směrnice jsou relativní k umístění `.fsx` souboru.

* `#load "logger.fsx"`načte soubor umístěný ve složce funkcí.
* `#load "package\logger.fsx"`načte soubor umístěný `package` ve složce ve složce funkcí.
* `#load "..\shared\mylogger.fsx"`načte soubor umístěný `shared` ve složce na stejné úrovni jako složka `wwwroot`funkcí, tj.

Směrnice `#load` pracuje pouze `.fsx` se soubory (Skript F# `.fs` ) a nikoli se soubory.

## <a name="next-steps"></a>Další kroky
Další informace najdete v následujících materiálech:

* [Průvodce F#](/dotnet/articles/fsharp/index)
* [Doporučené postupy pro funkce Azure](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure Funkce aktivační události a vazby](functions-triggers-bindings.md)
* [Testování funkcí Azure](functions-test-a-function.md)
* [Škálování funkcí Azure](functions-scale.md)

