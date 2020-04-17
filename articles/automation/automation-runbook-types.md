---
title: Typy runbooků Azure Automation
description: Popisuje různé typy runbooků, které můžete použít v Azure Automation a důležité informace pro určení, který typ použít.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535515"
---
# <a name="azure-automation-runbook-types"></a>Typy runbooků Azure Automation

Služba automatizace automatizace Azure podporuje několik typů runbooků, jak je definováno v následující tabulce. Další informace o prostředí automatizace procesů najdete v tématu [spuštění sady Runbook v Azure Automation](automation-runbook-execution.md).

| Typ | Popis |
|:--- |:--- |
| [Grafický](#graphical-runbooks)|Grafické runbooky založené na Windows PowerShellu a vytvořené a upravené kompletně v grafickém editoru na webu Azure Portal. |
| [Grafický pracovní postup prostředí PowerShell](#graphical-runbooks)|Grafická runbook založená na pracovním postupu Prostředí Windows PowerShell a vytvořená a upravená kompletně v grafickém editoru na webu Azure Portal. |
| [PowerShell](#powershell-runbooks) |Textový runbook založený na skriptování prostředí Windows PowerShell. |
| [Pracovní postup PowerShellu](#powershell-workflow-runbooks)|Textový runbook založený na skriptování pracovního postupu prostředí Windows PowerShell. |
| [Python](#python-runbooks) |Textový runbook založený na skriptování v Pythonu. |

Při určování typu, který typ se má použít pro konkrétní runbook, vezměte v úvahu následující aspekty.

* Runbooky nelze převést z grafického na textový typ ani naopak.
* Existují omezení při použití runbooků různých typů jako podřízených runbooků. Další informace najdete [v tématu Podřízené runbooky v Azure Automation](automation-child-runbooks.md).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Grafické runbooky

Pomocí grafického editoru na webu Azure Portal můžete vytvářet a upravovat grafické a grafické runbooky pracovního postupu prostředí PowerShell. Tento typ sady Runbook však nelze vytvořit ani upravit jiným nástrojem. Hlavní rysy grafických runbooků:

* Lze exportovat do souborů v účtu automatizace a potom importovat do jiného účtu automatizace. 
* Generovat kód Prostředí PowerShell. 
* Během importu lze převést do nebo z grafických runbooků pracovního postupu prostředí PowerShell. 

### <a name="advantages"></a>Výhody

* Použijte vizuální model vytváření insert-link-configure.
* Zaměřte se na toky dat procesem.
* Vizuálně představují procesy správy.
* Zahrňte další runbooky jako podřízené sady Runbook a vytvořte pracovní postupy na vysoké úrovni.
* Povzbuzujte modulární programování.

### <a name="limitations"></a>Omezení

* Mimo portál Azure portal nelze vytvářet ani upravovat.
* Může vyžadovat aktivitu kódu obsahující kód prostředí PowerShell ke spuštění složité logiky.
* Nelze převést na jeden z [formátů textu](automation-runbook-types.md), ani nelze převést text runbook do grafického formátu. 
* Nelze zobrazit nebo přímo upravit kód prostředí PowerShell, který vytvoří grafický pracovní postup. Kód, který vytvoříte, můžete zobrazit v libovolné aktivitě kódu.
* Nelze spustit runbooky na Linux hybridní runbook worker. Viz [Automatizace prostředků v datovém centru nebo cloudu pomocí hybridního pracovníka runbooku](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>Sady Runbook prostředí PowerShell

Sady Runbook prostředí PowerShell jsou založené na prostředí Windows PowerShell. Kód runbooku můžete přímo upravit pomocí textového editoru na webu Azure Portal.  Můžete taky použít libovolný offline textový editor a [importovat runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte veškerou komplexní logiku s kódem Prostředí PowerShell bez dalšísložitosti pracovního postupu prostředí PowerShell.
* Spouštějte rychleji než runbooky pracovního postupu prostředí PowerShell, protože je před spuštěním není nutné zkompilovat.
* Spouštět v Azure a na hybridní chodrunpro Windows i Linux.

### <a name="limitations"></a>Omezení

* Skriptování prostředí PowerShell musíte znát.
* Sady Runbook nelze použít [paralelní zpracování](automation-powershell-workflow.md#parallel-processing) k provádění více akcí paralelně.
* Sady Runbook nemohou používat [kontrolní body](automation-powershell-workflow.md#checkpoints) k obnovení sady Runbook, pokud dojde k chybě.
* Pomocí rutiny [Start-AzAutomationRunbook,](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) která vytvoří novou úlohu, můžete jako podřízené sady Runbook zahrnout pouze runbooky pracovního postupu prostředí PowerShell a grafické sady Runbook.

### <a name="known-issues"></a>Známé problémy

V sadách Runbooků prostředí PowerShell jsou aktuální známé problémy:

* Sady Runbook prostředí PowerShell nemohou načíst nezašifrovaný [datový zdroj proměnných](automation-variables.md) s nulovou hodnotou.
* Sady Runbook prostředí PowerShell nemohou `*~*` načíst datový zdroj s proměnnou v názvu.
* Operace [get-process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) ve smyčce v runbooku prostředí PowerShell může selpadnout po přibližně 80 iterací.
* Runbook prostředí PowerShell může selhat, pokud se pokusí zapsat velké množství dat do výstupního datového proudu najednou. Tento problém můžete obvykle vyřešit tím, že výstup runbooku pouze informace potřebné pro práci s velkými objekty. Například místo použití `Get-Process` bez omezení můžete mít výstup rutiny pouze požadované parametry `Get-Process | Select ProcessName, CPU`jako v .

## <a name="powershell-workflow-runbooks"></a>Runbooky pracovního postupu prostředí PowerShell

Runbooky pracovního postupu prostředí PowerShell jsou textové sady runbooky založené na [pracovním postupu prostředí Windows PowerShell](automation-powershell-workflow.md). Kód runbooku můžete přímo upravit pomocí textového editoru na webu Azure Portal. Můžete taky použít libovolný offline textový editor a [importovat runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte veškerou komplexní logiku pomocí kódu pracovního postupu prostředí PowerShell.
* Pokud dojde k chybě, použijte [kontrolní body](automation-powershell-workflow.md#checkpoints) k obnovení provozu.
* Paralelní [zpracování](automation-powershell-workflow.md#parallel-processing) použijte k paralelním akcím.
* Může obsahovat další grafické sady Runbook a runbooky Pracovního postupu prostředí PowerShell jako podřízené sady runbooků pro vytváření pracovních postupů na vysoké úrovni.

### <a name="limitations"></a>Omezení

* Musíte být obeznámeni s pracovním postupem prostředí PowerShell.
* Sady Runbook se musí zabývat další složitostí pracovního postupu prostředí PowerShell, jako jsou [například rekonstruované objekty](automation-powershell-workflow.md#code-changes).
* Spuštění sady Runbook trvá déle než sady Runbook prostředí PowerShell, protože je před spuštěním musí být zkompilovány.
* Sady Runbook prostředí PowerShell můžete zahrnout pouze `Start-AzAutomationRunbook` jako podřízené runbooky pomocí rutiny.
* Sady Runbook nelze spustit na Linux hybridní runbook worker.

## <a name="python-runbooks"></a>Runbooky pythonu

Runbooky pythonu se kompilují v Pythonu 2. Kód runbooku můžete přímo upravit pomocí textového editoru na webu Azure Portal. Můžete taky použít offline textový editor a [importovat runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Použijte robustní knihovny Pythonu.
* Můžete běžet v Azure nebo na Linux u hybridních runbookpracovníků. Windows Hybrid Runbook Workers jsou podporovány s [nainstalovanou python2.7.](https://www.python.org/downloads/release/latest/python2)

### <a name="limitations"></a>Omezení

* Musíte být obeznámeni s skriptováním v Pythonu.
* V současné době je podporován pouze Python 2. Všechny funkce specifické pro Python 3 se nezdaří.
* Chcete-li používat knihovny třetích stran, musíte [importovat balíčky](python-packages.md) do účtu Automatizace.

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření grafických knih runbooků najdete [v tématu Vytváření grafických funkcí v Azure Automation](automation-graphical-authoring-intro.md).
* Informace o rozdílech mezi pracovními postupy Prostředí PowerShell a PowerShell pro sady Runbook najdete v [tématu Učení pracovního postupu prostředí Windows PowerShell](automation-powershell-workflow.md).
* Další informace o tom, jak vytvořit nebo importovat runbook, najdete v tématu [Správa runbooků v Azure Automation](manage-runbooks.md).
* Další informace o PowerShellu, včetně jazykových referenčních a výukových modulů, najdete v [části Dokumenty prostředí PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
