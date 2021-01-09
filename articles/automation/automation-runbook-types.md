---
title: Azure Automation typy runbooků
description: Tento článek popisuje typy sad Runbook, které lze použít v Azure Automation a požadavky na určení typu, který se má použít.
services: automation
ms.subservice: process-automation
ms.date: 01/08/2021
ms.topic: conceptual
ms.openlocfilehash: c1398d25b6d2540abea0012acd69555e5e53e25c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050965"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation typy runbooků

Funkce automatizace procesu Azure Automation podporuje několik typů sad Runbook, jak je definováno v následující tabulce. Další informace o prostředí automatizace procesu naleznete v tématu [Spuštění Runbooku v Azure Automation](automation-runbook-execution.md).

| Typ | Popis |
|:--- |:--- |
| [Grafický](#graphical-runbooks)|Grafická sada Runbook založená na prostředí Windows PowerShell a vytvořena a upravována zcela v grafickém editoru v Azure Portal. |
| [Grafický pracovní postup PowerShellu](#graphical-runbooks)|Grafická sada Runbook založená na pracovním postupu prostředí Windows PowerShell a vytvořená a upravená úplně v grafickém editoru v Azure Portal. |
| [PowerShell](#powershell-runbooks) |Textový Runbook založený na skriptování Windows PowerShellu |
| [Pracovní postup PowerShellu](#powershell-workflow-runbooks)|Textový Runbook založený na skriptování pracovního postupu prostředí Windows PowerShell. |
| [Python](#python-runbooks) |Textový Runbook založený na skriptování Pythonu |

Při určování typu, který se má použít pro konkrétní Runbook, vezměte v úvahu následující skutečnosti.

* Runbooky nemůžete převést z grafického na textový typ nebo jiným způsobem.
* Existují omezení při použití sad Runbook různých typů jako podřízených runbooků. Další informace naleznete v tématu [podřízené Runbooky v Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Grafické Runbooky

Můžete vytvářet a upravovat grafické a grafické Runbooky pracovních postupů PowerShellu pomocí grafického editoru v Azure Portal. Tento typ sady Runbook však nelze vytvořit ani upravit pomocí jiného nástroje. Hlavní funkce grafických runbooků:

* Exportovány do souborů v účtu Automation a pak se naimportují do jiného účtu Automation.
* Vygeneruje kód PowerShellu.
* Převede se na grafické Runbooky pracovních postupů PowerShellu během importu nebo z nich.

### <a name="advantages"></a>Výhody

* Použijte vizuální model vytváření odkazů – konfigurace.
* Zaměřte se na to, jak tok dat zpracovává.
* Vizuálně reprezentují procesy správy.
* Zahrnout další Runbooky jako podřízené Runbooky pro vytváření pracovních postupů vysoké úrovně.
* Podpora modulárního programování.

### <a name="limitations"></a>Omezení

* Nelze vytvořit ani upravit mimo Azure Portal.
* Může vyžadovat aktivitu kódu obsahující kód prostředí PowerShell ke spuštění komplexní logiky.
* Nelze převést na jeden z [formátů textu](automation-runbook-types.md), ani nelze převést textový Runbook do grafického formátu. 
* Nelze zobrazit nebo přímo upravovat kód prostředí PowerShell, který vytváří grafický pracovní postup. Kód, který vytvoříte, můžete zobrazit v aktivitách kódu.
* Nelze spouštět Runbooky na Hybrid Runbook Worker Linux. Informace najdete [v tématu Automatizace prostředků ve vašem datovém centru nebo cloudu pomocí Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShellové Runbooky

Runbooky PowerShellu jsou založené na Windows PowerShellu. Kód sady Runbook můžete přímo upravovat pomocí textového editoru v Azure Portal.  Můžete také použít libovolný textový editor offline a [importovat Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte všechny komplexní logiky pomocí kódu prostředí PowerShell bez dalších složitosti pracovního postupu PowerShellu.
* Začněte rychleji než Runbooky pracovních postupů PowerShellu, protože je před spuštěním nemusíte kompilovat.
* Spouštějte v Azure a na hybridních pracovních procesech Runbooku pro Windows i Linux.

### <a name="limitations"></a>Omezení

* Je nutné znát prostředí PowerShell Scripting.
* Runbooky nemůžou použít [paralelní zpracování](automation-powershell-workflow.md#use-parallel-processing) k paralelnímu provádění více akcí.
* Runbooky nemůžou použít [kontrolní body](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) k obnovení Runbooku, pokud dojde k chybě.
* Pomocí rutiny [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) můžete zahrnout jenom Runbooky pracovního postupu PowerShellu a grafické Runbooky jako podřízené Runbooky. tím se vytvoří nová úloha.

### <a name="known-issues"></a>Známé problémy

Tady jsou uvedené aktuální známé problémy s Runbooky PowerShellu:

* Runbooky PowerShellu nemůžou načíst nešifrovaný [variabilní prostředek](./shared-resources/variables.md) s hodnotou null.
* Runbooky PowerShellu nemůžou v názvu načíst variabilní prostředek `*~*` .
* Operace [GET-Process](/powershell/module/microsoft.powershell.management/get-process) ve smyčce v Runbooku PowerShellu může po přibližně 80 iterací selhat.
* PowerShellový Runbook může selhat, pokud se pokusí zapisovat velké množství dat do výstupního datového proudu najednou. Tento problém můžete obvykle obejít tak, že výstup Runbooku vydáte jenom informace potřebné pro práci s velkými objekty. Například namísto použití `Get-Process` bez omezení můžete mít výstup rutiny pouze požadované parametry, jako v `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>Runbooky pracovních postupů PowerShellu

Runbooky pracovních postupů PowerShellu jsou textové Runbooky založené na [pracovním postupu prostředí Windows PowerShell](automation-powershell-workflow.md). Kód sady Runbook můžete přímo upravovat pomocí textového editoru v Azure Portal. Můžete také použít libovolný textový editor offline a [importovat Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte všechny komplexní logiky pomocí kódu pracovního postupu PowerShellu.
* Pokud dojde k chybě, můžete pokračovat v operaci pomocí [kontrolních bodů](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) .
* Paralelní [zpracování](automation-powershell-workflow.md#use-parallel-processing) slouží k paralelnímu provádění více akcí.
* Může zahrnovat další grafické Runbooky a runbooky pracovních postupů PowerShellu jako podřízené Runbooky pro vytváření pracovních postupů vysoké úrovně.

### <a name="limitations"></a>Omezení

* Musíte být obeznámeni s pracovním postupem PowerShellu.
* Runbooky se musí vypořádat s dalšími složitostmi pracovního postupu PowerShellu, jako jsou [deserializovatelné objekty](automation-powershell-workflow.md#deserialized-objects).
* Spouštění Runbooků trvá déle než Runbooky PowerShellu, protože je nutné je před spuštěním zkompilovat.
* PowerShellové Runbooky můžete zahrnout jenom jako podřízené Runbooky pomocí `Start-AzAutomationRunbook` rutiny.
* Runbooky nelze spustit na Hybrid Runbook Worker pro Linux.

## <a name="python-runbooks"></a>Runbooky Python

Runbooky Pythonu jsou kompilovány v Pythonu 2 a Python 3. Sady Python 3 Runbooky jsou momentálně ve verzi Preview. Můžete přímo upravit kód sady Runbook pomocí textového editoru v Azure Portal. Můžete také použít textový editor offline a [importovat Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Používejte robustní knihovny Pythonu.
* Může běžet v Azure nebo na hybridních pracovních procesech Runbooku.
* V Pythonu 2 jsou pracovní procesy Windows Hybrid Runbook Worker podporovány s nainstalovanou [Python 2,7](https://www.python.org/downloads/release/latest/python2) .
* V případě cloudových úloh Python 3 je podporována verze Python 3,8. Skripty a balíčky z jakékoli 3. x verze mohou fungovat, pokud je kód kompatibilní napříč různými verzemi.  
* V případě hybridních úloh Python 3 na počítačích s Windows můžete zvolit instalaci libovolné verze 3. x, kterou možná budete chtít použít.  
* U hybridních úloh Pythonu 3 v počítačích se systémem Linux závisí na verzi Pythonu 3 nainstalované v počítači, aby se spouštěla OMSConfiga DSC a Linux Hybrid Worker. Doporučujeme nainstalovat 3,6 na počítače se systémem Linux. Různé verze by však měly fungovat i v případě, že se nevyskytnou žádné zásadní změny signatury metody nebo kontraktů mezi verzemi Pythonu 3.

### <a name="limitations"></a>Omezení

* Musíte být obeznámeni se skriptováním v Pythonu.
* Chcete-li použít knihovny třetích stran, je nutné [importovat balíčky](python-packages.md) do účtu Automation.
*    Spuštění sady Runbook (verze Preview) pomocí rutiny Start-AutomationRunbook v pracovním postupu PowerShell/PowerShell nefunguje. Chcete-li toto omezení vyřešit, můžete použít rutinu **Start-AzAutomationRunbook** z modulu AZ. Automation Module nebo rutinu **Start-AzureRmAutomationRunbook** z modulu AzureRm. Automation.  
* Sady Runbook (verze Preview) Python 3 nefungují s prostředím PowerShell.
* Azure Automation nepodporuje **Sys. stderr**.

### <a name="known-issues"></a>Známé problémy

V některých úlohách Python 3 dojde k chybě, zpráva výjimky je *neplatná cesta ke spustitelnému souboru interpretu*. Tato výjimka se může zobrazit, pokud je úloha zpožděna a začíná více než 10 minut nebo se pomocí **Start-AutomationRunbook** spustí sady Python 3 pro Runbooky. Pokud je úloha zpožděna, měla by být sada Runbook restartem dostačující.

## <a name="next-steps"></a>Další kroky

* Další informace o runbooků PowerShellu najdete v tématu [kurz: vytvoření Runbooku PowerShellu](learn/automation-tutorial-runbook-textual-powershell.md).
* Další informace o Runbooky pracovních postupů PowerShellu najdete v tématu [kurz: vytvoření Runbooku pracovního postupu PowerShellu](learn/automation-tutorial-runbook-textual.md).
* Další informace o grafických sadách Runbook najdete v tématu [kurz: Vytvoření grafického Runbooku](learn/automation-tutorial-runbook-graphical.md).
* Další informace o runbooků Pythonu najdete v tématu [kurz: vytvoření Runbooku v Pythonu](learn/automation-tutorial-runbook-textual-python2.md).
