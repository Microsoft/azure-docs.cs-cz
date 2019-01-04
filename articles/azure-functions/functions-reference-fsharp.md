---
title: Služba Azure Functions F# referenční informace pro vývojáře | Dokumentace Microsoftu
description: Naučte se vyvíjet funkce Azure pomocí F# skriptu.
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
keywords: Azure funkce, funkce, zpracování událostí, webhooky, dynamické výpočty, architektura bez serveruF#
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: azure-functions
ms.devlang: fsharp
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 112a986efc11822f6c847511a33be6206b1f00da
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717480"
---
# <a name="azure-functions-f-developer-reference"></a>Služba Azure Functions F# referenční informace pro vývojáře

F#pro službu Azure Functions je řešení umožňující snadno spouštět malé části kódu, nebo "funkce" v cloudu. Data budou téci do vaší F# funkce prostřednictvím argumentů funkce. Argument názvy jsou určené v `function.json`, a jsou předdefinované názvy pro přístup k věci, jako je funkce protokolovací nástroj a zrušení tokenů. 

>[!IMPORTANT]
>F#skript (.fsx) je podporovaná jenom rozhraním [verzi 1.x](functions-versions.md#creating-1x-apps) modulu runtime Azure Functions. Pokud chcete použít F# s modulem runtime verze 2.x, je nutné použít předkompilované F# projekt knihovny tříd (.fs). Vytvoření, správě a publikování F# projekt knihovny tříd pomocí sady Visual Studio, stejně jako [ C# projekt knihovny tříd](functions-dotnet-class-library.md). Další informace o verzích funkce, najdete v části [přehled verze modulu runtime Azure Functions](functions-versions.md).

Tento článek předpokládá, že jste už čtete [referenční informace pro vývojáře Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Jak funguje .fsx
`.fsx` Soubor je F# skriptu. To můžete představit jako F# projekt, který je obsažen v jednom souboru. Soubor obsahuje kód pro váš program (v tomto případě vaši funkci Azure Functions) a direktivy pro správu závislostí.

Při použití `.fsx` pro funkce Azure Functions, běžně vyžaduje sestavení budou zahrnuty automaticky za vás, abyste mohli zaměřit na samotný kód funkce, nikoli "standardní".

## <a name="folder-structure"></a>struktura složek

Struktura složek F# skript projekt vypadá takto:

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

Existuje sdílený [host.json](functions-host-json.md) soubor, který můžete použít ke konfiguraci aplikace function app. Každá funkce má svůj vlastní soubor s kódem (.fsx) a vazbu konfigurační soubor (function.json).

Rozšíření vazby vyžaduje [verze 2.x](functions-versions.md) funkce modulu runtime jsou definovány v `extensions.csproj` souboru se soubory knihovny v `bin` složky. Při vývoji místně, musíte [registraci rozšíření vazby](functions-triggers-bindings.md#local-development-azure-functions-core-tools). Při vytváření funkcí na webu Azure Portal, je tato registrace provede za vás.

## <a name="binding-to-arguments"></a>Vytvoření vazby na argumenty
Každá vazba podporuje některé sadu argumentů, jak je uvedeno v [referenční informace pro vývojáře Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md). Jedna z vazeb argument podporuje aktivační událost objektů blob je třeba POCO, které lze vyjádřit pomocí F# záznamu. Příklad:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Vaše F# funkce Azure Functions bude trvat jeden nebo více argumentů. Když mluvíme o Azure Functions argumenty, označujeme *vstupní* argumenty a *výstup* argumenty. Vstupní argument je přesně to vypadá jako: vstup do vaší F# funkce Azure functions. *Výstup* argument je proměnlivé datové nebo `byref<>` argument, který slouží jako způsob, jak předání dat zpět *si* vaší funkce.

V příkladu výše `blob` je vstupní argument a `output` je výstupní argument. Všimněte si, že jsme použili `byref<>` pro `output` (není nutné přidat `[<Out>]` poznámky). Použití `byref<>` typ umožňuje změnit, který záznam nebo argument odkazuje na objekt funkce.

Když F# záznam se používá jako vstupní typ, musí být označené záznam definice `[<CLIMutable>]` aby rozhraní Azure Functions k odpovídajícím způsobem nastavit pole před předáním funkci záznam. Pod pokličkou `[<CLIMutable>]` generuje setter vlastnosti záznamu. Příklad:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

F# Třídy lze také použít pro vstupní ani výstupní argumenty. Pro třídy vlastnosti obvykle potřebovat metody getter a setter. Příklad:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Protokolování
Protokolovat výstup do vaší [streamování protokolů](../app-service/troubleshoot-diagnostic-logs.md) v F#, funkce by měla trvat argumentu typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Pro zajištění konzistence, doporučujeme tento argument je s názvem `log`. Příklad:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>asynchronní
`async` Postup se dá použít, ale výsledek musí vracet `Task`. To lze provést pomocí `Async.StartAsTask`, například:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token zrušení
Pokud vaše funkce elegantně zpracovat vypnutí, můžete jí přiřadit [ `CancellationToken` ](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argument. To je možné kombinovat s `async`, například:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Import obory názvů
Obory názvů lze otevřít obvyklým způsobem:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Jsou automaticky otevřeny následující obory názvů:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Odkazování na externí sestavení
Obdobně lze přidat odkazy na sestavení rozhraní s `#r "AssemblyName"` směrnice.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Následující sestavení jsou automaticky přidány pomocí Azure Functions, který je hostitelem prostředí:

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

Navíc následující sestavení jsou speciální notaci a mohou být odkazovány simplename (třeba `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Pokud potřebujete odkazovat na soukromé sestavení, můžete nahrát soubor sestavení do `bin` složce relativní k vaší funkce a odkaz, ji pomocí souboru (např. název  `#r "MyAssembly.dll"`). Informace o tom, jak nahrávat soubory do složky funkce naleznete v tématu v následující části týkající se správy balíčků.

## <a name="editor-prelude"></a>Editor Prelude
Editor, který podporuje F# kompilátoru služby nebude vědět o obory názvů a sestavení, která se automaticky zahrne Azure Functions. V důsledku toho může být užitečné zahrnout prelude, usnadňující editor najít sestavení, které používáte a explicitně otevřít obory názvů. Příklad:

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

Jakmile Azure Functions spustí váš kód, zpracuje zdroje s `COMPILED` definovány, proto prelude editoru se bude ignorovat.

<a name="package"></a>

## <a name="package-management"></a>Správa balíčků
Použití balíčků NuGet v F# funkci, přidejte `project.json` soubor do složky funkce v systému souborů aplikace function app. Tady je příklad `project.json` soubor, který přidá odkaz na balíček NuGet do `Microsoft.ProjectOxford.Face` verze 1.1.0:

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

Rozhraní .NET Framework 4.6 je podporováno, proto se ujistěte, že vaše `project.json` soubor Určuje `net46` jak je znázorněno zde.

Po odeslání `project.json` souboru, modul runtime získá balíčky a automaticky přidá odkazy na sestavení balíčku. Není nutné přidat `#r "AssemblyName"` direktivy. Stačí přidat požadované `open` příkazy pro vaše `.fsx` souboru.

Možná budete chtít změnit automaticky odkazy na sestavení na váš editor prelude zlepšit váš editor interakci s F# kompilaci služby.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Postup přidání `project.json` soubor pro vaši funkci Azure functions
1. Začátek řešíme tak, aby vaše aplikace function app běží, což lze provést tak, že otevřete vaši funkci na portálu Azure portal. To také poskytuje přístup do protokolů streamování ve kterém se zobrazí výstup instalace balíčku.
2. K nahrání `project.json` souboru, použijte jednu z metod popsaných v [aktualizace souborů aplikace funkce](functions-reference.md#fileupdate). Pokud používáte [průběžné nasazování pro službu Azure Functions](functions-continuous-deployment.md), můžete přidat `project.json` soubor k vaší pracovní větve, pokud chcete experimentovat s ním před přidáním do vaší větve nasazení.
3. Po `project.json` při přidání souboru, se zobrazí výstup jako v následujícím příkladu ve své funkci je streamování protokolů:

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
Chcete-li získat proměnnou prostředí nebo nastavení hodnoty aplikace, použijte `System.Environment.GetEnvironmentVariable`, například:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Opětovné použití kódu .fsx
Můžete použít kód z jiných `.fsx` souborů s využitím `#load` směrnice. Příklad:

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

Poskytuje cesty `#load` směrnice jsou relativní k umístění vašeho `.fsx` souboru.

* `#load "logger.fsx"` načte soubor umístěný ve složce funkce.
* `#load "package\logger.fsx"` načte soubor umístěný ve `package` složky ve složce funkce.
* `#load "..\shared\mylogger.fsx"` načte soubor umístěný ve `shared` složky na stejné úrovni jako funkce složky tedy přímo pod `wwwroot`.

`#load` – Direktiva funguje pouze s `.fsx` (F# skript) soubory a ne s `.fs` soubory.

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících materiálech:

* [F#Průvodce](/dotnet/articles/fsharp/index)
* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)
* [Testování Azure Functions](functions-test-a-function.md)
* [Škálování Azure Functions](functions-scale.md)

