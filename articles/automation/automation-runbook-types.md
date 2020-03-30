---
title: Typy runbooků Azure Automation
description: 'Popisuje různé typy runbooků, které můžete použít v Azure Automation a důležité informace, které byste měli vzít v úvahu při určování, který typ použít. '
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 6346c29210b6390f11c884ff51e0b60af89bbbb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278607"
---
# <a name="azure-automation-runbook-types"></a>Typy runbooků Azure Automation

Azure Automation podporuje několik typů runbooků, které jsou stručně popsány v následující tabulce.  Následující části poskytují další informace o jednotlivých typech, včetně úvah o tom, kdy použít každý z nich.

| Typ | Popis |
|:--- |:--- |
| [Grafický](#graphical-runbooks)|Založeno na Windows PowerShellu a vytvořené a upravené kompletně v grafickém editoru na webu Azure Portal. |
| [Grafický pracovní postup prostředí PowerShell](#graphical-runbooks)|Založeno na pracovním postupu Prostředí Windows PowerShell a vytvořené a upravené kompletně v grafickém editoru na webu Azure Portal. |
| [PowerShell](#powershell-runbooks) |Textový runbook založený na skriptu prostředí Windows PowerShell. |
| [Pracovní postup PowerShellu](#powershell-workflow-runbooks)|Textový runbook založený na pracovním postupu prostředí Windows PowerShell. |
| [Python](#python-runbooks) |Textový runbook založený na Pythonu. |

## <a name="graphical-runbooks"></a>Grafické runbooky

[Grafické](automation-runbook-types.md#graphical-runbooks) a grafické runbooky pracovního postupu prostředí PowerShell se vytvářejí a upravují pomocí grafického editoru na webu Azure Portal.  Můžete je exportovat do souboru a pak je importovat do jiného účtu automatizace. Ale nemůžete je vytvářet ani upravovat pomocí jiného nástroje. Grafické sady Runbook generují kód prostředí PowerShell, ale nelze jej přímo zobrazit ani upravit. Grafické sady Runbook nelze převést na jeden z [textových formátů](automation-runbook-types.md), ani textový runbook nelze převést do grafického formátu. Grafické sady Runbook lze převést na grafické runbooky pracovního postupu prostředí PowerShell během importu a naopak.

### <a name="advantages"></a>Výhody

* Vizuální vývojový model pro konfiguraci vložení a propojení
* Zaměřte se na to, jak data procházejí procesem
* Vizuálně představují procesy správy
* Zahrnout další runbooky jako podřízené sady Runbook k vytvoření pracovních postupů na vysoké úrovni
* Podporuje modulární programování

### <a name="limitations"></a>Omezení

* Runbook nelze upravit mimo portál Azure.
* Může vyžadovat aktivitu kódu obsahující kód prostředí PowerShell ke spuštění složité logiky.
* Nelze zobrazit nebo přímo upravit kód Prostředí PowerShell, který je vytvořen grafickým pracovním postupem. Kód, který vytvoříte, můžete zobrazit v libovolné aktivity kódu.
* Nelze spustit na Linux hybridní Runbook Worker

## <a name="powershell-runbooks"></a>Sady Runbook prostředí PowerShell

Sady Runbook prostředí PowerShell jsou založené na prostředí Windows PowerShell.  Kód runbooku můžete přímo upravit pomocí textového editoru na webu Azure Portal.  Můžete taky použít libovolný offline textový editor a [importovat runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte veškerou komplexní logiku s kódem Prostředí PowerShell bez dalšísložitosti pracovního postupu prostředí PowerShell.
* Runbook se spustí rychleji než runbooky pracovního postupu prostředí PowerShell, protože ji před spuštěním není nutné zkompilovat.
* Lze spustit v Azure nebo na Linuxu i Windows Hybridní Runbook Pracovníků

### <a name="limitations"></a>Omezení

* Musí být obeznámen s skriptováním prostředí PowerShell.
* Nelze použít [paralelní zpracování](automation-powershell-workflow.md#parallel-processing) k paralelnímu spuštění více akcí.
* Nelze použít [kontrolní body](automation-powershell-workflow.md#checkpoints) k obnovení runbooku, pokud dojde k chybě.
* Runbooky powershellového pracovního postupu a grafické sady Runbook lze zahrnout pouze jako podřízené sady runbook pomocí rutiny Start-AzureAutomationRunbook, která vytvoří novou úlohu.

### <a name="known-issues"></a>Známé problémy

Následují aktuální známé problémy s runbooky prostředí PowerShell.

* Sady Runbook powershellu nemohou načíst nezašifrovaný [datový zdroj proměnných](automation-variables.md) s nulovou hodnotou.
* Sady Runbook prostředí PowerShell nemohou *~* načíst datový zdroj s [proměnnou](automation-variables.md) v názvu.
* Get-Process ve smyčce v runbooku prostředí PowerShell může dojít k selhání po asi 80 iterací.
* Runbook prostředí PowerShell může selhat, pokud se pokusí zapsat velké množství dat do výstupního datového proudu najednou.   Tento problém můžete obvykle vyřešit tak, že při práci s velkými objekty vystavíte pouze informace, které potřebujete.  Například místo výstupu něco jako *Get-Process*, můžete výstup pouze požadovaná pole s *Get-Process | Vyberte Název_procesu, CPU*.

## <a name="powershell-workflow-runbooks"></a>Runbooky pracovního postupu prostředí PowerShell

Runbooky pracovního postupu prostředí PowerShell jsou textové sady runbooky založené na [pracovním postupu prostředí Windows PowerShell](automation-powershell-workflow.md).  Kód runbooku můžete přímo upravit pomocí textového editoru na webu Azure Portal.  Můžete taky použít libovolný offline textový editor a [importovat runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte veškerou komplexní logiku pomocí kódu pracovního postupu prostředí PowerShell.
* Pokud dojde k chybě, použijte [kontrolní body](automation-powershell-workflow.md#checkpoints) k obnovení runbooku.
* Paralelní [zpracování](automation-powershell-workflow.md#parallel-processing) slouží k paralelním akcím.
* Může obsahovat další grafické sady Runbook a runbooky Pracovního postupu prostředí PowerShell jako podřízené sady runbooků pro vytváření pracovních postupů na vysoké úrovni.

### <a name="limitations"></a>Omezení

* Autor musí být obeznámen s pracovním postupem prostředí PowerShell.
* Aplikace Runbook se musí zabývat další složitostí pracovního postupu prostředí PowerShell, jako jsou [rekonstruované objekty](automation-powershell-workflow.md#code-changes).
* Spuštění sady Runbook trvá déle než sady Runbook prostředí PowerShell, protože je potřeba ji před spuštěním zkompilovat.
* Sady Runbook prostředí PowerShell lze zahrnout pouze jako podřízené runbooky pomocí rutiny Start-AzureAutomationRunbook, která vytvoří novou úlohu.
* Nelze spustit na Linux hybridní Runbook Worker

## <a name="python-runbooks"></a>Runbooky pythonu

Runbooky pythonu se kompilují v Pythonu 2.  Kód runbooku můžete přímo upravit pomocí textového editoru na webu Azure Portal nebo pomocí offline textového editoru a [importovat runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Využijte robustní knihovny Pythonu.
* Lze spustit v Azure nebo na obou Linux Hybridní Runbook pracovníků. Windows Hybrid Runbook Workers jsou podporovány s [nainstalovanou python2.7.](https://www.python.org/downloads/release/latest/python2)

### <a name="limitations"></a>Omezení

* Musí být obeznámen s skriptováním v Pythonu.
* V současné době je podporován pouze Python 2, což znamená, že specifické funkce Pythonu 3 se nezdaří.
* Chcete-li používat knihovny třetích stran, musíte [importovat balíček](python-packages.md) do účtu automatizace, aby byl použit.

## <a name="considerations"></a>Požadavky

Při určování typu, který typ se má použít pro konkrétní runbook, vezměte v úvahu následující další aspekty.

* Runbooky nelze převést z grafického na textový typ nebo naopak.
* Existují omezení pomocí runbooků různých typů jako podřízené hospo- Další informace najdete [v tématu Podřízené runbooky v Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření grafických runbooků najdete [v tématu Vytváření grafických knih v Azure Automation](automation-graphical-authoring-intro.md)
* Informace o rozdílech mezi pracovními postupy Prostředí PowerShell a PowerShell pro sady Runbook najdete v [tématu Učení pracovního postupu prostředí Windows PowerShell](automation-powershell-workflow.md)
* Další informace o vytvoření nebo importu sady Runbook najdete v [tématu Vytvoření nebo import sady Runbook](manage-runbooks.md)
* Další informace o prostředí PowerShell, včetně jazykových odkazů a výukových modulů, najdete v dokumentu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
