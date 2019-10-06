---
title: Místní ladění Azure Functions PowerShellu
description: Naučte se vyvíjet funkce pomocí prostředí PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 5b396ef6b00d53a313ed4fb426685c12e2c1549d
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981850"
---
# <a name="debug-powershell-azure-functions-locally"></a>Místní ladění Azure Functions PowerShellu

Azure Functions umožňuje vyvíjet funkce jako skripty prostředí PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Funkce PowerShellu můžete ladit místně stejně jako všechny skripty PowerShellu pomocí následujících standardních vývojářských nástrojů:

* [Visual Studio Code](https://code.visualstudio.com/): bezplatný, odlehčený a open source textový editor s rozšířením PowerShellu, který nabízí kompletní vývojové prostředí PowerShellu.
* Konzola PowerShellu: ladění pomocí stejných příkazů, které byste použili pro ladění jakýchkoli jiných procesů prostředí PowerShell.

[Azure Functions Core Tools](functions-run-local.md) podporuje místní ladění Azure Functions, včetně funkcí prostředí PowerShell.

## <a name="example-function-app"></a>Příklad aplikace Function App

Aplikace Function App použitá v tomto článku obsahuje jednu funkci aktivovanou protokolem HTTP a má následující soubory:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Tato aplikace Function App je podobná té, kterou dostanete po dokončení [rychlého startu PowerShellu](functions-create-first-function-powershell.md).

Kód funkce v `run.ps1` vypadá jako tento skript:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Nastavení bodu připojení

Chcete-li ladit jakoukoli funkci prostředí PowerShell, funkce musí zastavit, aby byl ladicí program připojen. Rutina `Wait-Debugger` zastaví provádění a počká na ladicí program.

Vše, co potřebujete udělat, je přidat volání rutiny `Wait-Debugger` těsně nad příkaz `if`, a to takto:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Ladění začíná příkazem `if`. 

Když `Wait-Debugger`, můžete nyní ladit funkce pomocí Visual Studio Code nebo konzoly PowerShellu.

## <a name="debug-in-visual-studio-code"></a>Ladění v Visual Studio Code

Chcete-li ladit funkce prostředí PowerShell v Visual Studio Code, je nutné mít nainstalované následující:

* [Rozšíření PowerShellu pro Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Rozšíření Azure Functions pro Visual Studio Code](functions-create-first-function-vs-code.md)
* [PowerShell Core 6,2 nebo vyšší](/powershell/scripting/install/installing-powershell-core-on-windows)

Po instalaci těchto závislostí Načtěte existující projekt funkcí PowerShellu nebo [vytvořte svůj první projekt funkcí PowerShellu](functions-create-first-function-powershell.md).

>[!NOTE]
> Pokud váš projekt nemá potřebné konfigurační soubory, budete vyzváni k jeho přidání.

### <a name="set-the-powershell-version"></a>Nastavení verze prostředí PowerShell

PowerShell Core se nainstaluje souběžně s Windows PowerShellem. Nastavte PowerShell Core jako verzi prostředí PowerShell pro použití s rozšířením PowerShell pro Visual Studio Code.

1. Stisknutím klávesy F1 zobrazte paletu příkazů a potom vyhledejte `Session`.

1. Vyberte **PowerShell: Zobrazit nabídku relace**.

1. Pokud **aktuální relace** není **PowerShell Core 6**, vyberte **Přepnout na: PowerShell Core 6**.

Když máte otevřený soubor PowerShellu, zobrazí se verze zobrazená zeleně v pravém dolním rohu okna. Výběrem tohoto textu se zobrazí také nabídka relace. Další informace najdete v tématu [Výběr verze prostředí PowerShell, která se má použít s příponou](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Spuštění aplikace Function App

Ověřte, zda je ve funkci, do které chcete ladicí program připojit, nastavena hodnota `Wait-Debugger`.  Když jste přidali `Wait-Debugger`, můžete aplikaci Function App ladit pomocí Visual Studio Code.

Zvolte podokno **ladění** a potom **se připojte k funkci PowerShellu**.

![ladění](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Můžete také stisknout klávesu F5 a spustit ladění.

Operace spustit ladění provádí následující úlohy:

* Spustí `func extensions install` v terminálu, aby se nainstalovala rozšíření Azure Functions, která vyžaduje aplikace Function App.
* Spustí aplikaci Function App na hostiteli Functions `func host start` v terminálu.
* Připojte ladicí program PowerShellu k prostředí PowerShell PowerShellu v rámci modulu runtime Functions.

>[!NOTE]
> Je nutné zajistit, aby byl PSWorkerInProcConcurrencyUpperBound nastaven na hodnotu 1, aby bylo zajištěno správné prostředí ladění v Visual Studio Code. Toto je výchozí nastavení.

Když máte spuštěnou aplikaci Function App, potřebujete samostatnou konzolu PowerShellu pro volání funkce aktivované protokolem HTTP.

V takovém případě je konzola prostředí PowerShell klientem. K aktivaci funkce se používá `Invoke-RestMethod`.

V konzole PowerShellu spusťte následující příkaz:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Všimnete si, že odpověď není hned vrácena. Důvodem je to, že `Wait-Debugger` připojil ladicí program a spuštění prostředí PowerShell přešlo do režimu přerušení, jakmile to bude možné. Důvodem je [BreakAll koncept](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), který je vysvětlen později. Po stisknutí tlačítka `continue` se ladicí program teď na řádku po `Wait-Debugger` zalomí.

V tomto okamžiku je připojen ladicí program a můžete provádět všechny běžné operace ladicího programu. Další informace o používání ladicího programu v Visual Studio Code najdete v [oficiální dokumentaci](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Po pokračování a plném vyvolání skriptu si všimněte, že:

* Konzola prostředí PowerShell, která byla `Invoke-RestMethod`, vrátila výsledek.
* Integrovaná konzola PowerShellu v Visual Studio Code čeká na provedení skriptu.

Později při vyvolání stejné funkce se ladicí program v rozšíření PowerShellu přeruší hned po `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Ladění v konzole PowerShellu

>[!NOTE]
> V této části se předpokládá, že jste si přečetli [dokumentaci Azure Functions Core Tools](functions-run-local.md) a víte, jak pomocí příkazu `func host start` spustit aplikaci Function App.

Otevřete konzolu, `cd` do adresáře aplikace Function App a spusťte následující příkaz:

```sh
func host start
```

Se spuštěnou aplikací Function App a `Wait-Debugger` se můžete připojit k procesu. Potřebujete ještě dvě další konzoly PowerShellu.

Jedna z konzol funguje jako klient. Z toho zavoláte `Invoke-RestMethod` pro aktivaci funkce. Můžete například spustit následující příkaz:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Všimnete si, že nevrátí odpověď, což je výsledek `Wait-Debugger`. Prostředí runspace pro PowerShell nyní čeká na připojení ladicího programu. Pojďme se připojit.

V jiné konzole prostředí PowerShell spusťte následující příkaz:

```powershell
Get-PSHostProcessInfo
```

Tato rutina vrátí tabulku, která vypadá jako následující výstup:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Poznamenejte si `ProcessId` pro položku v tabulce s `ProcessName` jako `dotnet`. Tento proces je vaší aplikací Function App.

Dále spusťte následující fragment kódu:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Po spuštění ladicí program ukončí a zobrazí něco podobného následujícímu výstupu:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

V tuto chvíli jste v [ladicím programu PowerShellu](/powershell/module/microsoft.powershell.core/about/about_debuggers)zastavili zarážku. Odtud můžete provádět všechny běžné operace ladění, Krokovat s vnořením, Krokovat s vnořením, pokračováním, ukončením a dalšími uživateli. Chcete-li zobrazit úplnou sadu příkazů ladění, které jsou k dispozici v konzole nástroje, spusťte příkazy `h` nebo `?`.

Zarážky na této úrovni můžete také nastavit pomocí rutiny `Set-PSBreakpoint`.

Jakmile budete pokračovat a plně vyvolali váš skript, všimnete si, že:

* Konzola prostředí PowerShell, ve které jste provedli `Invoke-RestMethod`, teď vrátila výsledek.
* Konzola prostředí PowerShell, ve které jste provedli `Debug-Runspace` čeká na provedení skriptu.

Stejnou funkci můžete vyvolat znovu (například pomocí `Invoke-RestMethod`) a ladicí program se naplní hned po příkazu `Wait-Debugger`.

## <a name="considerations-for-debugging"></a>Pokyny pro ladění

Při ladění kódu vašich funkcí Pamatujte na následující problémy.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` může způsobit přerušení ladicího programu na neočekávaném místě.

Rozšíření prostředí PowerShell používá `Debug-Runspace`, která se následně spoléhá na funkci `BreakAll` prostředí PowerShell. Tato funkce oznamuje, že prostředí PowerShell se zastaví na prvním příkazu, který je spuštěn. Toto chování vám dává možnost nastavit zarážky v laděném prostředí runspace.

Modul runtime Azure Functions spouští několik příkazů před samotným vyvoláním skriptu `run.ps1`, takže je možné, že ladicí program skončí v rámci `Microsoft.Azure.Functions.PowerShellWorker.psm1` nebo `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Pokud k tomuto přerušení dojde, spusťte příkaz `continue` nebo `c`, který přeskočí tuto zarážku. Pak se zastaví na očekávané zarážce.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji funkcí pomocí prostředí PowerShell najdete v tématu [Azure Functions příručka pro vývojáře PowerShellu](functions-reference-powershell.md).
