---
title: Typy Runbooků služby Azure Automation
description: 'Popisuje různé typy sad runbook, které můžete použít v Azure Automation a důležité informace, které byste měli vzít v úvahu při určování, který typ používat. '
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 94fbc1cf0232eaf48bb0be3c6edc0542142e4681
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="azure-automation-runbook-types"></a>Typy runbooků služby Azure Automation
Služby Azure Automation podporuje několik typů sad runbook, které jsou popsány v následující tabulce.  V níže uvedených částech poskytují další informace o jednotlivých typech včetně důležité informace týkající se použití každé.

| Typ | Popis |
|:--- |:--- |
| [Grafický](#graphical-runbooks) |Na základě prostředí Windows PowerShell a vytvořeno a upravená zcela v grafický editor na portálu Azure. |
| [Pracovní postup grafické prostředí PowerShell](#graphical-runbooks) |Podle pracovního postupu prostředí Windows PowerShell a vytvořit a upravit zcela v grafickém editoru na portálu Azure. |
| [PowerShell](#powershell-runbooks) |Text runbook založené na skriptu prostředí Windows PowerShell. |
| [Pracovní postup PowerShellu](#powershell-workflow-runbooks) |Text runbook podle pracovního postupu prostředí Windows PowerShell. |
| [Python](#python-runbooks) |Text runbook podle Python. |

## <a name="graphical-runbooks"></a>Grafické runbooky
[Grafické](automation-runbook-types.md#graphical-runbooks) a runbooky pracovních postupů grafické prostředí PowerShell jsou vytvořeny a upravit pomocí grafického editoru na portálu Azure.  Můžete je exportovat do souboru a pak je importovat do jiného účtu automation, ale nelze vytvořit nebo upravit je jiný nástroj.  Grafické runbooky generování kódu prostředí PowerShell, ale nemůžou přímo zobrazovat nebo upravovat kód. Grafické runbooky nelze převést na jednu z [textových formátů](automation-runbook-types.md), ani lze sadu runbook text převést na grafické formátu. Grafické runbooky lze převést na runbooky pracovních postupů grafické prostředí PowerShell během importu a naopak.

### <a name="advantages"></a>Výhody
* Visual vložení odkazu nakonfigurovat pro tvorbu modelu  
* Zaměřit se na tok dat prostřednictvím procesu  
* Vizuální představují procesů správy  
* Zahrnout další sady runbook jako podřízené sady runbook k vytváření pracovních postupů vysoké úrovně  
* Podporuje častější modulární programování  


### <a name="limitations"></a>Omezení
* Nelze upravit runbook mimo portál Azure.
* Může vyžadovat aktivitu kód obsahující kód prostředí PowerShell k provedení komplexní logiku.
* Nelze zobrazit, nebo přímo upravit kód prostředí PowerShell, který byl vytvořený grafický workflow. Všimněte si, že se zobrazí kód, který vytvoříte v žádné aktivity kódu.

## <a name="powershell-runbooks"></a>Powershellové runbooky
Powershellové runbooky jsou založené na prostředí Windows PowerShell.  Přímo upravovat kód sadu runbook pomocí textového editoru na portálu Azure.  Můžete použít také všechny offline textovém editoru a [naimportujte sadu runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) do Azure Automation.

### <a name="advantages"></a>Výhody
* Implementujte všechny komplexní logiku kódem Powershellu bez další složitosti pracovního postupu prostředí PowerShell. 
* Sada Runbook spustí rychleji než runbooky pracovních postupů Powershellu vzhledem k tomu nemusí být zkompilovány dřív, než spustíte.

### <a name="limitations"></a>Omezení
* Musí být obeznámeni s skriptů prostředí PowerShell.
* Nelze použít [paralelní zpracování](automation-powershell-workflow.md#parallel-processing) k provedení několika akcí paralelně.
* Nelze použít [kontrolní body](automation-powershell-workflow.md#checkpoints) runbook v případě chyby pokračovat.
* Runbooky pracovních postupů Powershellu a grafické runbooky lze pouze zahrnuty jako podřízené sady runbook pomocí rutiny Start-AzureAutomationRunbook, která vytvoří novou úlohu.

### <a name="known-issues"></a>Známé problémy
Toto jsou aktuální známé problémy s Powershellovými runbooky.

* Powershellové runbooky nelze načíst nezašifrované [variabilní prostředek](automation-variables.md) s hodnotou null.
* Nelze načíst Powershellové runbooky [variabilní prostředek](automation-variables.md) s *~* v názvu.
* Get-Process ve smyčce v prostředí PowerShell runbook může dojít k chybě po přibližně 80 iterací. 
* Powershellový runbook může selhat, pokud se pokusí o velmi velké množství dat najednou zapisovat do výstupního datového proudu.   Tento problém můžete vyřešit obvykle podle výstupu pouze informace, které je třeba při práci s rozsáhlé objekty.  Například místo výstup podobný vytvořeného *Get-Process*, výstup můžete právě povinná pole s *Get-Process | Vyberte název_procesu procesoru*.

## <a name="powershell-workflow-runbooks"></a>Runbooky pracovních postupů Powershellu
Runbooky pracovních postupů Powershellu jsou text sad runbook na základě [pracovního postupu prostředí Windows PowerShell](automation-powershell-workflow.md).  Přímo upravovat kód sadu runbook pomocí textového editoru na portálu Azure.  Můžete použít také všechny offline textovém editoru a [naimportujte sadu runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) do Azure Automation.

### <a name="advantages"></a>Výhody
* Implementujte všechny komplexní logiku s kódem pracovního postupu Powershellu.
* Použití [kontrolní body](automation-powershell-workflow.md#checkpoints) runbook v případě chyby pokračovat.
* Použití [paralelní zpracování](automation-powershell-workflow.md#parallel-processing) k provedení několika akcí paralelně.
* Jako podřízené sady runbook pro vytvoření základní pracovní postupy mohou zahrnovat jiné grafické runbooky a runbooky pracovních postupů Powershellu.

### <a name="limitations"></a>Omezení
* Autor musí být obeznámeni s pracovním postupem prostředí PowerShell.
* Sady Runbook musí řešit další složitosti pracovního postupu prostředí PowerShell, jako [deserializovat objekty](automation-powershell-workflow.md#code-changes).
* Runbook trvá delší dobu než Powershellové runbooky vzhledem k tomu, že je nutné být zkompilovány dřív, než spustíte.
* Powershellové runbooky může být pouze zahrnuta jako podřízené sady runbook pomocí rutiny Start-AzureAutomationRunbook, která vytvoří novou úlohu.

## <a name="python-runbooks"></a>Sady runbook Python
Sady runbook Python zkompilovat pod Python 2.  Lze přímo upravit kód sadu runbook pomocí textového editoru na portálu Azure, nebo můžete použít všechny offline textovém editoru a [naimportujte sadu runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) do Azure Automation.

### <a name="advantages"></a>Výhody
* Využívat robustní standardní knihovny jazyka Python.

### <a name="limitations"></a>Omezení
* Musí být obeznámeni s Python skriptování.
* Pouze Python 2 je podporován v okamžiku, což znamená, že určité funkce Python 3 se nezdaří.

### <a name="known-issues"></a>Známé problémy
Toto jsou aktuální známé problémy s runbooky Python.

* Aby bylo možné využívat knihovny třetích stran, musí být spuštěn na sadu runbook [Windows hybridní pracovní proces Runbooku](https://docs.microsoft.com/azure/automation/automation-windows-hrw-install) nebo [Linux hybridní pracovní proces Runbooku](https://docs.microsoft.com/azure/automation/automation-linux-hrw-install) s knihovnami již nainstalován v počítači před Sada runbook je spuštěna.

## <a name="considerations"></a>Požadavky
Byste měli vzít v úvahu následující další aspekty při určování, který typ používat konkrétní sady runbook.

* Sady runbook z grafického nelze převést na typ textové nebo naopak.
* Existují omezení pomocí runbooků různých typů jako podřízené sady runbook.  V tématu [podřízené runbooky ve službě Azure Automation](automation-child-runbooks.md) Další informace.

## <a name="next-steps"></a>Další postup
* Další informace o vytváření grafický runbook najdete v tématu [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md)
* Chcete-li pochopit rozdíly mezi prostředí PowerShell a prostředí PowerShell najdete v části pracovní postupy pro sady runbook, [Learning pracovního postupu prostředí Windows PowerShell](automation-powershell-workflow.md)
* Další informace o tom, jak vytvořit nebo importovat sady Runbook najdete v tématu [vytvoření nebo import Runbooku](automation-creating-importing-runbook.md)

