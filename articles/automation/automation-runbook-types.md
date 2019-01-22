---
title: Typy Runbooků ve službě Azure Automation
description: 'Popisuje různé typy sad runbook, které můžete v Azure Automation a důležité informace, které byste měli vzít v úvahu při určování typ. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 572d887a14aa25e45f0ad74660c5d3fbb1d32851
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425624"
---
# <a name="azure-automation-runbook-types"></a>Typy runbooků Azure Automation

Azure Automation podporuje několik typů sad runbook, které jsou popsány v následující tabulce.  Následující části obsahují další informace o jednotlivých typech, včetně posouzení toho, kdy použít.

| Typ | Popis |
|:--- |:--- |
| [Grafický](#graphical-runbooks) |Na základě prostředí Windows PowerShell a vytvořené a upravené zcela v grafickém editoru na webu Azure portal. |
| [Grafický Powershellový pracovní postup](#graphical-runbooks) |Podle pracovního postupu prostředí Windows PowerShell a vytvoří a upravují výhradně v grafickém editoru na webu Azure portal. |
| [PowerShell](#powershell-runbooks) |Textového runbooku, který založené na skriptu prostředí Windows PowerShell. |
| [Pracovní postup PowerShellu](#powershell-workflow-runbooks) |Textového runbooku, který podle pracovního postupu prostředí Windows PowerShell. |
| [Python](#python-runbooks) |Textového runbooku, který založeno na Pythonu. |

## <a name="graphical-runbooks"></a>Grafické runbooky

[Grafické](automation-runbook-types.md#graphical-runbooks) a grafický Powershellový pracovní postup sady runbook vytvářejí se a upravují v grafickém editoru na webu Azure Portal.  Můžete je exportovat do souboru a následně je importovat do jiného účtu automation, ale nejde vytvořit nebo upravit pomocí jiného nástroje.  Grafické runbooky generování kódu Powershellu, ale nelze přímo zobrazit nebo upravit kód. Grafické runbooky se nedá převést na jeden z [textových formátů](automation-runbook-types.md), ani lze sadu runbook text převést na grafický formát. Grafické runbooky lze převést na sady runbook grafický Powershellový pracovní postup při importu a naopak.

### <a name="advantages"></a>Výhody

* Vložit odkaz Konfigurovat vytváření modelu Visual  
* Zaměřte se na tok dat v rámci procesu  
* Vizuálně reprezentují procesů správy  
* Zahrnout další sady runbook jako podřízené sady runbook vysoké úrovně pracovní postupy  
* Může vést ke vzniku modulární programování  

### <a name="limitations"></a>Omezení

* Nelze upravit runbook mimo web Azure portal.
* Může vyžadovat aktivitě s kódem, která obsahuje kód Powershellu provádět komplexní logiku.
* Nelze zobrazit nebo upravit přímo kódem Powershellu, který je vytvořen pomocí grafického pracovního postupu. Všimněte si, že můžete zobrazit kód, který můžete vytvořit libovolný kód aktivity.

## <a name="powershell-runbooks"></a>Powershellové runbooky

Powershellové runbooky jsou založené na prostředí Windows PowerShell.  Přímo upravovat kód sady runbook pomocí textového editoru na webu Azure Portal.  Můžete také použít libovolný editor offline text a [naimportujte sadu runbook](automation-creating-importing-runbook.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte všechny komplexní logiku s kódem Powershellu bez dalších složitostí pracovního postupu Powershellu.
* Runbook spouští rychleji než runbooky pracovních postupů Powershellu, protože není nutné sestavit před spuštěním.

### <a name="limitations"></a>Omezení

* Musí být znalost skriptování Powershellu.
* Nelze použít [paralelní zpracování](automation-powershell-workflow.md#parallel-processing) umožňuje provádět více akcí souběžně.
* Nelze použít [kontrolní body](automation-powershell-workflow.md#checkpoints) sady runbook v případě chyby pokračovat.
* Runbooky pracovních postupů Powershellu a grafické runbooky pouze lze vložit jako podřízené sady runbook pomocí rutiny Start-AzureAutomationRunbook, která vytvoří novou úlohu.

### <a name="known-issues"></a>Známé problémy

Toto jsou aktuální známé problémy s Powershellovými runbooky.

* Powershellové runbooky nejde načíst nezašifrované [variabilní prostředek](automation-variables.md) s hodnotou null.
* Nelze načíst Powershellové runbooky [variabilní prostředek](automation-variables.md) s *~* v názvu.
* Get-Process ve smyčce v Powershellu runbook může chybově ukončit po přibližně 80 iteracích.
* Powershellový runbook může selhat, pokud se pokusí o zápis velmi velké množství dat najednou do výstupního datového proudu.   Tento problém můžete obejít obvykle ve výstupu pouze informace, které potřebujete při práci s velké objekty.  Například místo výstupu něco jako *Get-Process*, můžete výstup jenom do požadovaných polí *Get-Process | Vyberte název procesu, procesoru*.

## <a name="powershell-workflow-runbooks"></a>Runbooky pracovních postupů Powershellu

Runbooky pracovních postupů Powershellu jsou textové runbooky vycházející [pracovního postupu prostředí Windows PowerShell](automation-powershell-workflow.md).  Přímo upravovat kód sady runbook pomocí textového editoru na webu Azure Portal.  Můžete také použít libovolný editor offline text a [naimportujte sadu runbook](automation-creating-importing-runbook.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte všechny komplexní logiku s kódem pracovního postupu Powershellu.
* Použití [kontrolní body](automation-powershell-workflow.md#checkpoints) sady runbook v případě chyby pokračovat.
* Použití [paralelní zpracování](automation-powershell-workflow.md#parallel-processing) umožňuje provádět více akcí souběžně.
* Jako podřízené sady runbook vysoké úrovně pracovní postupy mohou zahrnovat jiné grafické runbooky a runbooky pracovních postupů Powershellu.

### <a name="limitations"></a>Omezení

* Autor musí být obeznámeni s pracovního postupu Powershellu.
* Sady Runbook musí čelit další složitosti pracovních postupů Powershellu, jako [deserializovat objekty](automation-powershell-workflow.md#code-changes).
* Sada Runbook provede delší dobu než sady runbook Powershellu, protože musí být zkompilovány před spuštěním.
* Powershellové runbooky může být pouze zahrnuta jako podřízené sady runbook pomocí rutiny Start-AzureAutomationRunbook, která vytvoří novou úlohu.

## <a name="python-runbooks"></a>Sady runbook Pythonu

Runbook Python kompilaci Python 2.  Můžete přímo upravit kód sady runbook pomocí textového editoru na webu Azure Portal nebo můžete použít libovolný editor offline text a [naimportujte sadu runbook](automation-creating-importing-runbook.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Využijte robustní knihoven Pythonu.

### <a name="limitations"></a>Omezení

* Musí být obeznámeni s skriptů Pythonu.
* Pouze Python 2 se podporuje v tuto chvíli, což znamená, že konkrétní funkce Python 3 se nezdaří.
* Pokud si chcete využívat knihovny třetích stran, musíte [importu balíčku ho](python-packages.md) do účtu Automation, který se má použít.

## <a name="considerations"></a>Požadavky

Byste měli provést v úvahu následující další aspekty při určování, který typ použít konkrétní sady runbook.

* Sady runbook nelze převést z grafického textový typ nebo naopak.
* Existují omezení pomocí runbooků různých typů jako podřízené sady runbook.  Zobrazit [podřízené runbooky ve službě Azure Automation](automation-child-runbooks.md) Další informace.

## <a name="next-steps"></a>Další postup

* Chcete-li další informace o vytváření grafického runbooku, přečtěte si téma [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md)
* Chcete-li pochopit rozdíly mezi prostředí PowerShell a prostředí PowerShell pracovních postupů pro sady runbook, naleznete v tématu [pracovním postupu Windows Powershellu](automation-powershell-workflow.md)
* Další informace o tom, jak vytvořit nebo importovat Runbook najdete v tématu [vytvoření nebo import Runbooku](automation-creating-importing-runbook.md)

