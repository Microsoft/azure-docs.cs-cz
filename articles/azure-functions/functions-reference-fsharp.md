---
title: 'Azure funkce F # referenční informace pro vývojáře | Microsoft Docs'
description: 'Pochopit, jak vyvíjet Azure Functions pomocí F #.'
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
editor: ''
tags: ''
keywords: 'Azure funkce, funkce, událostí zpracování, webhooků, dynamické výpočetní, bez serveru architekturu, F #'
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: functions
ms.devlang: fsharp
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/09/2016
ms.author: syclebsc
ms.openlocfilehash: 2c84de3f38a49bc97fda04a7a4eb449a1f7d14bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31515590"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions referenční informace pro vývojáře F #

F # pro Azure Functions je řešení umožňující snadno spouštět malé části kódu, nebo "funkce" v cloudu. Toky dat do funkce F # prostřednictvím argumenty funkce. Argument názvy jsou určené v `function.json`, a jsou předdefinované názvy pro přístup k takové věci, jako funkce protokolovacího nástroje a zrušení tokenů.

Tento článek předpokládá, že jste si již přečetli [referenční informace pro vývojáře Azure Functions](functions-reference.md).

## <a name="how-fsx-works"></a>Jak funguje .fsx
`.fsx` Soubor je skript F #. Ho můžete představit jako projekt F #, který je obsažen v jednom souboru. Soubor obsahuje kód k aplikaci (v tomto případě funkce Azure) a direktivy pro správu závislosti.

Když použijete `.fsx` pro funkce Azure, běžně vyžaduje sestavení jsou pro vás, což umožňuje zaměřit se na funkce, nikoli "standardní" kód automaticky zahrnuty.

## <a name="binding-to-arguments"></a>Vytvoření vazby na argumenty
Každou vazbu podporuje někteří sadu argumentů, podle popisu v [referenční vývojáře triggerů a vazeb Azure Functions](functions-triggers-bindings.md). Například jedna z vazeb argument, který podporuje aktivační události objektu blob je objektů POCO, které lze vyjádřit pomocí záznamu F #. Příklad:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Funkce Azure F # bude trvat jeden nebo více argumentů. Když mluvíme o argumenty funkce Azure, označujeme *vstupní* argumenty a *výstup* argumenty. Vstupní argument je přesně vypadá jako: vstup do funkce Azure F #. *Výstup* argument je měnitelný datový nebo `byref<>` argument, který slouží jako způsob, jak předat data zpět *out* vaší funkce.

V příkladu nahoře `blob` je vstupní argument, a `output` je argument výstup. Všimněte si, že jsme použili `byref<>` pro `output` (je nutné přidat `[<Out>]` poznámky). Použití `byref<>` typ umožňuje funkce změnit které záznam nebo argument odkazuje na objekt.

Pokud záznam F # je použita jako vstupní typ, musí být označen definici záznam s `[<CLIMutable>]` Chcete-li povolit rozhraní Azure Functions správně nastavit pole před předáním záznam funkce. Pod pokličkou `[<CLIMutable>]` generuje setter vlastnosti záznamu. Příklad:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Třída F # lze také pro obě vstup a výstup argumenty. Pro třídy vlastnosti obvykle potřebovat mechanismy získání a nastavení. Příklad:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Protokolování
Protokolovat výstup do vaší [protokoly streamování](../app-service/web-sites-enable-diagnostic-log.md) v F # funkce zabere argument typu `TraceWriter`. Konzistence, doporučujeme, abyste tento argument je s názvem `log`. Příklad:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Asynchronní
`async` Pracovní postup můžete použít, ale musí vrátit výsledek `Task`. To lze provést pomocí `Async.StartAsTask`, například:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token zrušení
Pokud funkce potřebuje pro pohodlné zpracování vypnutí, můžete jí [ `CancellationToken` ](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argument. To je možné kombinovat s `async`, například:

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

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Automaticky se otevře následujících oborů názvů:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Odkazování na externí sestavení
Podobně lze přidat framework odkazy na sestavení s `#r "AssemblyName"` – direktiva.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Následující sestavení jsou automaticky přidány Azure Functions hostování prostředí:

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

Kromě toho jsou speciální následující sestavení použita a může být odkazováno simplename (například `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Pokud potřebujete odkazovat na privátní sestavení, můžete nahrát soubor sestavení do `bin` složky vzhledem ke vaší ho pomocí souboru (např. název funkce a referenční dokumentace  `#r "MyAssembly.dll"`). Informace o tom, jak odeslat soubory do složky funkce najdete v následující části na správy balíčků.

## <a name="editor-prelude"></a>Editor Prelude
Editor, který podporuje služby kompilátoru F # nebude vědět, obory názvů a sestavení, které automaticky zahrne Azure Functions. Jako takový může být užitečné zahrnout prelude, která pomáhá editoru najít sestavení, který používáte a explicitně otevřete obory názvů. Příklad:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Když Azure Functions provede kódu, zpracovává zdroji s `COMPILED` definované editor prelude bude proto ignorován.

<a name="package"></a>

## <a name="package-management"></a>Správy balíčků
Použití balíčků NuGet v funkci F #, přidejte `project.json` soubor do složky funkce v systému souborů aplikaci funkce. Tady je příklad `project.json` soubor, který přidá odkaz na balíček NuGet `Microsoft.ProjectOxford.Face` verze 1.1.0:

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

Je podporováno pouze rozhraní .NET Framework 4.6, tak zkontrolujte, zda vaše `project.json` soubor Určuje `net46` jak je vidět tady.

Když nahrajete `project.json` soubor modulu runtime získá balíčky a automaticky přidá reference na sestavení balíčku. Nemusíte přidávat `#r "AssemblyName"` direktivy. Stačí přidat požadované `open` příkazů do vaší `.fsx` souboru.

Můžete uvést automaticky odkazy na sestavení do vaší prelude editor, ke zlepšení vaší editor interakci s zkompilovat služby F #.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Postup přidání `project.json` soubor funkce Azure
1. Začněte zajišťuje funkce aplikace běží, což lze provést otevřením funkce na portálu Azure. To také umožňuje přístup do protokolů streamování kde se zobrazí výstup instalace balíčku.
2. Nahrát `project.json` souboru, použijte jednu z metod popsaných v [jak aktualizovat soubory aplikace funkce](functions-reference.md#fileupdate). Pokud používáte [průběžné nasazování pro Azure Functions](functions-continuous-deployment.md), můžete přidat `project.json` souboru do svojí pracovní větve, aby bylo možné experimentovat před přidáním do svojí větve nasazení.
3. Po `project.json` se přidá soubor, zobrazí se výstup podobný následujícímu příkladu funkce je streamování protokolu:

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
Proměnné prostředí nebo nastavení hodnoty aplikace, použijte `System.Environment.GetEnvironmentVariable`, například:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Opětovné použití kódu .fsx
Můžete použít kód z jiných `.fsx` soubory pomocí `#load` – direktiva. Příklad:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Poskytuje cesty `#load` direktivy jsou relativní vzhledem k umístění vaší `.fsx` souboru.

* `#load "logger.fsx"` načte soubor umístěný ve složce funkce.
* `#load "package\logger.fsx"` načte soubor umístěný ve `package` složky ve složce funkce.
* `#load "..\shared\mylogger.fsx"` načte soubor umístěný ve `shared` složky na stejné úrovni jako složka funkce, který je přímo pod `wwwroot`.

`#load` – Direktiva pracuje pouze s `.fsx` soubory (F # skript) a nikoli s `.fs` soubory.

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících materiálech:

* [Průvodce F #](/dotnet/articles/fsharp/index)
* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
* [Azure funkce triggerů a vazeb](functions-triggers-bindings.md)
* [Azure Functions testování](functions-test-a-function.md)
* [Škálování Azure Functions](functions-scale.md)

