---
title: Místní ladění funkce Azure Powershellu
description: Naučte se vyvíjet funkce s použitím prostředí PowerShell.
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
ms.openlocfilehash: fc30a2efb21d5b7f3168d9229ec5baf9a7f05eb1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706411"
---
# <a name="debug-powershell-azure-functions-locally"></a>Místní ladění funkce Azure Powershellu

Azure Functions umožňuje vývoj vašich funkcí jako skripty prostředí PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Vaše funkce Powershellu můžete ladit místně, stejně jako všechny skripty prostředí PowerShell pomocí následující standardní vývojové nástroje:

* [Visual Studio Code](https://code.visualstudio.com/): Bezplatné, jednoduchý a open source textový editor společnosti Microsoft s příponou prostředí PowerShell, který nabízí úplné vývojové prostředí PowerShell.
* Konzolu Powershellu: Ladění pomocí stejné příkazy, které můžete použít k ladění jakýkoli jiný proces, prostředí PowerShell.

[Nástroje Azure Functions Core](functions-run-local.md) podporuje místní ladění funkce Azure, včetně funkcí Powershellu.

## <a name="example-function-app"></a>Příklad aplikace function app

Aplikace function app, na které se používají v tomto článku má jednu funkci aktivovanou protokolem HTTP a má následující soubory:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Tuto aplikaci function app je podobná získáte po dokončení [rychlý start pro PowerShell](functions-create-first-function-powershell.md).

Kód funkce v `run.ps1` vypadá podobně jako následující skript:

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

Chcete-li ladit všechny funkce Powershellu, funkce musí zastavit ladicí program připojit. `Wait-Debugger` Rutina zastaví provádění a čeká na ladicí program.

Všechno, co potřebujete udělat, je přidání volání `Wait-Debugger` rutina hned nad `if` příkaz následujícím způsobem:

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

Ladění začíná `if` příkazu. 

S `Wait-Debugger` na místě, teď můžete ladit funkce pomocí Visual Studio Code nebo konzolu Powershellu.

## <a name="debug-in-visual-studio-code"></a>Ladění ve Visual Studio Code

Chcete-li ladit vaše funkce Powershellu ve Visual Studio Code, musíte mít nainstalované tyto položky:

* [Rozšíření prostředí PowerShell pro Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Rozšíření Azure Functions pro Visual Studio Code](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 nebo novější](/powershell/scripting/install/installing-powershell-core-on-windows)

Po instalaci těchto závislostí, načtěte existující projekt funkce Powershellu, nebo [vytvořit svůj první projekt funkce Powershellu](functions-create-first-function-powershell.md).

>[!NOTE]
> Váš projekt, by neměly mít potřebných konfiguračních souborů, zobrazí se výzva je přidat.

### <a name="set-the-powershell-version"></a>Nastavit verzi prostředí PowerShell

PowerShell Core se nainstaluje vedle prostředí Windows PowerShell. PowerShell Core nastavte jako verze prostředí PowerShell pomocí prostředí PowerShell rozšíření pro Visual Studio Code.

1. Po stisknutí klávesy F1 zobrazení palet příkaz a potom najděte `Session`.

1. Zvolte **prostředí PowerShell: Zobrazit nabídku relace**.

1. Pokud vaše **aktuální relace** není **PowerShell Core 6**, zvolte **přepnout na: PowerShell Core 6**.

Až budete mít otevřený soubor prostředí PowerShell, se zobrazí verze označený zelenou v pravém dolním rohu okna. Vyberete tento text se zobrazí také nabídky relace. Další informace najdete v tématu [zvolíte verzi prostředí PowerShell pro použití s příponou](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Spuštění aplikace function app

Ověřte, že `Wait-Debugger` nastavit ve funkci, ve které chcete připojit ladicí program.  S `Wait-Debugger` přidali, můžete ladit vaši aplikaci function app pomocí nástroje Visual Studio Code.

Zvolte **ladění** podokně a pak **připojit k prostředí PowerShell funkce**.

![Ladicí program](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Můžete také stisknout klávesu F5 pro spuštění ladění.

Spusťte ladění operace provede následující úlohy:

* Spuštění `func extensions install` v terminálu nainstalujte všechna rozšíření Azure Functions vyžaduje vaši aplikaci function app.
* Spuštění `func host start` v terminálu spusťte aplikaci function app na hostiteli funkce.
* Připojte ladicí program prostředí PowerShell k prostředí runspace prostředí PowerShell v rámci modul runtime služby Functions.

Spuštění aplikace function app bude nutné samostatné konzoly Powershellu volat funkci aktivovanou protokolem HTTP.

Konzola Powershellu v tomto případě je klient. `Invoke-RestMethod` Se používá k aktivaci funkce.

V konzole Powershellu spusťte následující příkaz:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Můžete si všimnout, že odpověď nevrátí okamžitě. Důvodem je, že `Wait-Debugger` je připojen ladicí program a prostředí PowerShell spuštění byly přidány do režimu přerušení, jakmile ho může. Je to z důvodu [BreakAll koncept](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), který je vysvětlen později. Po stisknutí klávesy `continue` tlačítko, ladicí program nyní konce řádku hned po `Wait-Debugger`.

V tomto okamžiku je připojen ladicí program a všechny normální funkce ladicího programu můžete provést operace. Další informace o používání ladicího programu ve Visual Studio Code najdete v tématu [oficiální dokumentaci](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Poté, co dál a plně vyvolání skriptu, uvidíte, že:

* Konzole Powershellu, který nebyl `Invoke-RestMethod` vrátil výsledek
* Integrovaná konzola Powershellu ve Visual Studio Code čeká na skript, který se spustí

Později při vyvolání stejnou funkci, ladicí program v prostředí PowerShell rozšíření přeruší hned po `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Ladění v konzole prostředí PowerShell

>[!NOTE]
> V této části se předpokládá jste si přečetli [dokumentace nástrojů Azure Functions Core](functions-run-local.md) a vědět, jak používat `func host start` příkaz ke spuštění aplikace function app.

Otevřete konzolu, `cd` do adresáře aplikace function app a spusťte následující příkaz:

```sh
func host start
```

S aplikaci funkcí spuštěnou a `Wait-Debugger` na místě, můžete připojit k procesu. Budete potřebovat dvě další konzoly Powershellu.

Mezi konzolí slouží jako klient. Z tohoto volání `Invoke-RestMethod` k aktivaci funkce. Můžete například spustit následující příkaz:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Všimněte si, že nevrací odpověď, která je výsledkem z `Wait-Debugger`. Prostředí runspace prostředí PowerShell je teď čekání na ladicí program mohl. Pojďme si připojený.

V jiné konzole Powershellu spusťte následující příkaz:

```powershell
Get-PSHostProcessInfo
```

Tato rutina vrací tabulku, která vypadá podobně jako následující výstup:

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

Poznamenejte si `ProcessId` položky v tabulce s `ProcessName` jako `dotnet`. Tento proces je vaše aplikace function app.

Potom spusťte následující fragment kódu:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Po zahájení ladicí program přeruší a ukazuje něco jako následující výstup:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

V tomto okamžiku jste zastavení na zarážce v [ladicího programu Powershellu](/powershell/module/microsoft.powershell.core/about/about_debuggers). Tady můžete provádět všechny operace obvyklé ladění, Krokovat přes, Krokovat s vnořením, pokračovat, ukončete a další. Pokud chcete zobrazit kompletní sadu příkazy ladění k dispozici v konzole, spusťte `h` nebo `?` příkazy.

Můžete také nastavit zarážky na této úrovni s `Set-PSBreakpoint` rutiny.

Jakmile pokračovat a plně vyvolání skriptu, uvidíte, že:

* Konzole Powershellu, ve kterém jste spustili `Invoke-RestMethod` má nyní vrátilo výsledek.
* Konzole Powershellu, ve kterém jste spustili `Debug-Runspace` čeká na skript, který se spustí.

Stejnou funkci lze vyvolat akci (pomocí `Invoke-RestMethod` třeba) a ladicí program přeruší v hned za `Wait-Debugger` příkazu.

## <a name="considerations-for-debugging"></a>Důležité informace pro ladění

Vzít v úvahu následující problémy při ladění kódu funkce.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` může způsobit, že vaše ladicí program na přerušení na neočekávané místě

Použití rozšíření prostředí PowerShell `Debug-Runspace`, která zase spoléhá na Powershellu `BreakAll` funkce. Tato funkce se říká Powershellu k zastavení v prvním příkazu, který se spouští. Toto chování vám dává příležitost k nastavení zarážek v rámci ladicího prostředí runspace.

Modul runtime služby Azure Functions spuštění několika příkazů před vyvoláním skutečně vaše `run.ps1` skriptu, takže je možné, že ladicí program končí slov v rámci `Microsoft.Azure.Functions.PowerShellWorker.psm1` nebo `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Se stane toto přerušení, spusťte `continue` nebo `c` příkazu Přeskočit tuto zarážku. Potom zastavení na zarážce očekávané.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji funkcí pomocí Powershellu najdete v tématu [– Příručka pro vývojáře Azure Functions Powershellu](functions-reference-powershell.md).
