---
title: Ladění funkcí PowerShellu Azure místně
description: Zjistěte, jak vyvíjet funkce pomocí prostředí PowerShell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163756"
---
# <a name="debug-powershell-azure-functions-locally"></a>Ladění funkcí PowerShellu Azure místně

Funkce Azure vám umožní rozvíjet funkce jako skripty Prostředí PowerShell.

Funkce prostředí PowerShell můžete ladit místně stejně jako všechny skripty prostředí PowerShell pomocí následujících standardních vývojových nástrojů:

* [Visual Studio Code:](https://code.visualstudio.com/)Bezplatný, lehký a open source textový editor společnosti Microsoft s rozšířením Prostředí PowerShell, který nabízí úplné prostředí PowerShell.
* Konzola Prostředí PowerShell: Ladění pomocí stejných příkazů, které byste použili k ladění jakéhokoli jiného procesu prostředí PowerShell.

[Nástroje Azure Functions Core Tools](functions-run-local.md) podporují místní ladění funkcí Azure, včetně funkcí PowerShellu.

## <a name="example-function-app"></a>Ukázková aplikace funkcí

Aplikace funkce použitá v tomto článku má jednu funkci aktivovanou protokolem HTTP a má následující soubory:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Tato aplikace funkce je podobná té, kterou získáte po dokončení [rychlého startu prostředí PowerShell](functions-create-first-function-powershell.md).

Kód funkce `run.ps1` v aplikaci vypadá jako následující skript:

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

Chcete-li ladit všechny funkce prostředí PowerShell, funkce musí zastavit ladicí program, který má být připojen. Rutina `Wait-Debugger` zastaví provádění a čeká na ladicí program.

Vše, co musíte udělat, je `Wait-Debugger` přidat volání do `if` rutiny těsně nad příkazem, a to následovně:

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

Ladění začíná na `if` příkaz. 

S `Wait-Debugger` na místě, můžete nyní ladit funkce pomocí visual studio kód nebo konzoly PowerShell.

## <a name="debug-in-visual-studio-code"></a>Ladění v kódu sady Visual Studio

Chcete-li ladit funkce prostředí PowerShell v kódu sady Visual Studio, musíte mít nainstalované následující:

* [Rozšíření Prostředí PowerShell pro kód Sady Visual Studio](/powershell/scripting/components/vscode/using-vscode)
* [Rozšíření Azure Functions pro Visual Studio Code](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 nebo vyšší](/powershell/scripting/install/installing-powershell-core-on-windows)

Po instalaci těchto závislostí načtěte existující projekt PowerShell Functions nebo [vytvořte svůj první projekt Funkce prostředí PowerShell](functions-create-first-function-powershell.md).

>[!NOTE]
> Pokud projekt nemá potřebné konfigurační soubory, budete vyzváni k jejich přidání.

### <a name="set-the-powershell-version"></a>Nastavení verze PowerShellu

PowerShell Core se instaluje vedle sebe s prostředím Windows PowerShell. Nastavte Jádro Prostředí PowerShell jako verzi PowerShellu pro použití s rozšířením PowerShellu pro kód Visual Studia.

1. Stisknutím klávesy F1 zobrazte příkazovou paletu a vyhledejte . `Session`

1. Zvolte **PowerShell: Zobrazit nabídku relace**.

1. Pokud vaše **aktuální relace** není **PowerShell Core 6**, zvolte **Přepnout na: PowerShell Core 6**.

Když máte otevřený soubor PowerShellu, uvidíte verzi zobrazenou zeleně v pravém dolním rohu okna. Výběrem tohoto textu se také zobrazí nabídka relace. Další informace najdete v tématu [Výběr verze PowerShellu, která se má používat s rozšířením](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Spuštění aplikace funkce

Ověřte, `Wait-Debugger` že je nastavena ve funkci, kam chcete připojit ladicí program.  S `Wait-Debugger` přidáním můžete ladit funkci aplikace pomocí kódu Sady Visual Studio.

Zvolte podokno **Ladění** a potom **připojte k funkci PowerShellu**.

![Ladicí program](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Můžete také stisknout klávesu F5 a spustit ladění.

Spuštění ladění operace provádí následující úkoly:

* Spustí `func extensions install` v terminálu nainstalovat všechna rozšíření Azure Funkce vyžadovaná vaší aplikace funkce.
* Spustí `func host start` v terminálu spustit aplikaci funkce v hostiteli funkce.
* Připojte ladicí program prostředí PowerShell k runspace prostředí PowerShell v rámci běhu functions.

>[!NOTE]
> Je třeba zajistit PSWorkerInProcConcurrencyUpperBound je nastavena na 1 zajistit správné ladění prostředí v kódu sady Visual Studio. Toto nastavení je výchozí.

Když je spuštěná funkční aplikace, potřebujete k volání funkce aktivované http samostatnou konzolu PowerShellu.

V tomto případě konzole PowerShell je klientem. Slouží `Invoke-RestMethod` ke spuštění funkce.

V konzole PowerShell spusťte následující příkaz:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Všimněte si, že odpověď není okamžitě vrácena. To proto, `Wait-Debugger` že má připojené ladicí program a powershell spuštění přešel do režimu přerušení, jakmile to bylo možné. Důvodem je [koncept BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), který je vysvětlen později. Po stisknutí `continue` tlačítka ladicí program nyní přeruší `Wait-Debugger`na řádku hned po .

V tomto okamžiku je připojen ladicí program a můžete provést všechny normální operace ladicího programu. Další informace o použití ladicího programu v kódu sady Visual Studio naleznete v [oficiální dokumentaci](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Poté, co budete pokračovat a plně vyvolat skript, všimnete si, že:

* Konzola prostředí PowerShell, která provedla, `Invoke-RestMethod` vrátila výsledek
* Integrovaný konzolový konzola prostředí PowerShell v kódu sady Visual Studio čeká na spuštění skriptu

Později při vyvolání stejné funkce se ladicí program `Wait-Debugger`v rozšíření prostředí PowerShell přeruší hned za .

## <a name="debugging-in-a-powershell-console"></a>Ladění v konzole PowerShell

>[!NOTE]
> Tato část předpokládá, že jste si přečetli [dokumenty nástroje Azure Functions Core Tools](functions-run-local.md) a víte, jak pomocí příkazu `func host start` spustit aplikaci funkcí.

Otevřete konzolu `cd` do adresáře aplikace funkcí a spusťte následující příkaz:

```sh
func host start
```

S běžící aplikací funkcí `Wait-Debugger` a na místě se můžete připojit k procesu. Potřebujete další dvě konzoly PowerShellu.

Jedna z konzolí funguje jako klient. Z tohoto volání `Invoke-RestMethod` spustit funkci. Můžete například spustit následující příkaz:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Všimněte si, že nevrátí odpověď, která je výsledkem `Wait-Debugger`. Prostředí PowerShell runspace nyní čeká na připojení ladicího programu. Připoutáme to.

V jiné konzoli PowerShell spusťte následující příkaz:

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

Poznamenejte `ProcessId` si položku v `ProcessName` tabulce `dotnet`pomocí písmene as. Tento proces je vaše funkce aplikace.

Dále spusťte následující úryvek:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Po spuštění se ladicí program rozbije a zobrazí něco jako následující výstup:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

V tomto okamžiku jste zastaveni na zarážku v [ladicím programu prostředí PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). Odtud můžete provést všechny obvyklé operace ladění, krok přes, krok do, pokračovat, ukončit a další. Chcete-li zobrazit úplnou sadu ladicích příkazů `h` dostupných v konzole, spusťte příkazy nebo. `?`

Můžete také nastavit zarážky na `Set-PSBreakpoint` této úrovni s rutinou.

Jakmile budete pokračovat a plně vyvolat skript, všimnete si, že:

* Konzola prostředí PowerShell, `Invoke-RestMethod` ve které jste provedli, nyní vrátila výsledek.
* Konzola Prostředí PowerShell, `Debug-Runspace` ve které jste provedli, čeká na spuštění skriptu.

Můžete vyvolat stejnou funkci znovu `Invoke-RestMethod` (například pomocí) a ladicí `Wait-Debugger` program se rozdělí hned za příkazem.

## <a name="considerations-for-debugging"></a>Důležité informace pro ladění

Mějte na paměti následující problémy při ladění kódu funkce.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`může způsobit, že se ladicí program přeruší na neočekávaném místě

Rozšíření Prostředí PowerShell používá `Debug-Runspace`, což zase závisí `BreakAll` na funkci prostředí PowerShell. Tato funkce říká PowerShell zastavit na první příkaz, který je spuštěn. Toto chování umožňuje nastavit zarážky v rámci laděné runspace.

Runtime Azure Functions spustí několik příkazů, než se `run.ps1` skutečně vyvoláte skript, takže je možné, že `Microsoft.Azure.Functions.PowerShellWorker.psm1` `Microsoft.Azure.Functions.PowerShellWorker.psd1`ladicí program skončí rozdělení v rámci nebo .

Pokud by k této `continue` `c` přestávce došlo, spusťte příkaz nebo, abyste tuto zarážku přeskočili. Potom zastavit na očekávané zarážky.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji funkcí pomocí PowerShellu najdete v [tématu Průvodce vývojáři Prostředí Azure Functions PowerShell](functions-reference-powershell.md).
