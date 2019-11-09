---
title: Azure Automation typy runbooků
description: 'Popisuje různé typy sad Runbook, které lze použít v Azure Automation a s důležitými informacemi, které byste měli vzít v úvahu při určování typu, který se má použít. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6c29015e2fd327d74183d5fbbd6214152507e517
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886771"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation typy runbooků

Azure Automation podporuje několik typů sad Runbook, které jsou stručně popsány v následující tabulce.  Níže uvedené části poskytují další informace o každém typu, včetně důležitých informací o tom, kdy se mají použít.

| Typ | Popis |
|:--- |:--- |
| [Grafický](#graphical-runbooks)|V závislosti na prostředí Windows PowerShell a vytvoření a upravení zcela v grafickém editoru v Azure Portal. |
| [Grafický pracovní postup PowerShellu](#graphical-runbooks)|Na základě pracovního postupu prostředí Windows PowerShell a jeho úplného vytvoření a úprav v grafickém editoru v Azure Portal. |
| [PowerShell](#powershell-runbooks) |Textový Runbook založený na skriptu Windows PowerShellu |
| [Pracovní postup PowerShellu](#powershell-workflow-runbooks)|Textový Runbook založený na pracovním postupu prostředí Windows PowerShell. |
| [Python](#python-runbooks) |Textový Runbook založený na Pythonu |

## <a name="graphical-runbooks"></a>Grafické Runbooky

[Grafické](automation-runbook-types.md#graphical-runbooks) a grafické Runbooky pracovních postupů PowerShellu se vytvářejí a upravují pomocí grafického editoru v Azure Portal.  Můžete je exportovat do souboru a pak je importovat do jiného účtu Automation. Nemůžete je ale vytvářet ani upravovat pomocí jiného nástroje. Grafické Runbooky generují kód prostředí PowerShell, ale nelze jej přímo zobrazit ani upravovat. Grafické Runbooky nelze převést na jeden z [formátů textu](automation-runbook-types.md), ani nelze textový Runbook převést do grafického formátu. Grafické Runbooky je možné převést na grafické Runbooky pracovních postupů PowerShellu během importu a jiným způsobem.

### <a name="advantages"></a>Výhody

* Vizuální vložení – propojení – Konfigurace vytváření modelu
* Zaměřte se na to, jak tok dat projde procesem
* Vizuálně znázornit procesy správy
* Zahrnutí dalších sad Runbook jako podřízených runbooků pro vytváření pracovních postupů vysoké úrovně
* Podporuje Modulární programování

### <a name="limitations"></a>Omezení

* Sadu Runbook nelze upravovat mimo Azure Portal.
* Může vyžadovat aktivitu kódu obsahující kód prostředí PowerShell ke spuštění komplexní logiky.
* Nelze zobrazit nebo přímo upravit kód prostředí PowerShell, který je vytvořen pomocí grafického pracovního postupu. Kód, který vytvoříte, můžete zobrazit v aktivitách kódu.
* Nejde na Hybrid Runbook Worker Linux.

## <a name="powershell-runbooks"></a>PowerShellové Runbooky

Runbooky PowerShellu jsou založené na Windows PowerShellu.  Kód sady Runbook můžete přímo upravovat pomocí textového editoru v Azure Portal.  Můžete také použít libovolný textový editor offline a [importovat Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte všechny komplexní logiky pomocí kódu PowerShellu bez dalších složitosti pracovního postupu PowerShellu.
* Sada Runbook začíná rychleji než Runbooky pracovních postupů PowerShellu, protože nemusí být zkompilována před spuštěním.
* Může být spuštěný v Azure nebo na Windows Hybrid Runbook Worker pro Linux i Windows

### <a name="limitations"></a>Omezení

* Musí být obeznámený s skriptováním PowerShellu.
* [Paralelní zpracování](automation-powershell-workflow.md#parallel-processing) nelze použít k paralelnímu spuštění více akcí.
* Pokud dojde k chybě, nelze obnovit sadu Runbook pomocí [kontrolních bodů](automation-powershell-workflow.md#checkpoints) .
* Runbooky pracovních postupů PowerShellu a grafické Runbooky můžou být zahrnuté jenom jako podřízené Runbooky pomocí rutiny Start-AzureAutomationRunbook, která vytvoří novou úlohu.

### <a name="known-issues"></a>Známé problémy

Níže jsou uvedené aktuální známé problémy s Runbooky PowerShellu.

* PowerShellové Runbooky nemůžou načíst nešifrovaný [variabilní prostředek](automation-variables.md) s hodnotou null.
* Runbooky PowerShellu nemůžou načíst [proměnnou Asset](automation-variables.md) s *~* v názvu.
* Rutina Get-Process ve smyčce v Runbooku PowerShellu může po přibližně 80 iterací selhat.
* PowerShellový Runbook může selhat, pokud se pokusí zapsat velké množství dat do výstupního datového proudu najednou.   Tento problém obvykle můžete obejít tak, že při práci s velkými objekty dodáte jenom informace, které potřebujete.  Například místo výstupu nějakého typu *GET-Process*můžete výstupovat pouze povinná pole s *GET-Process | Vyberte Process, CPU*.

## <a name="powershell-workflow-runbooks"></a>Runbooky pracovních postupů PowerShellu

Runbooky pracovních postupů PowerShellu jsou textové Runbooky založené na [pracovním postupu prostředí Windows PowerShell](automation-powershell-workflow.md).  Kód sady Runbook můžete přímo upravovat pomocí textového editoru v Azure Portal.  Můžete také použít libovolný textový editor offline a [importovat Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Implementujte všechny komplexní logiky pomocí kódu pracovního postupu PowerShellu.
* Pokud dojde k chybě, můžete sadu Runbook obnovit pomocí [kontrolních bodů](automation-powershell-workflow.md#checkpoints) .
* Paralelní [zpracování](automation-powershell-workflow.md#parallel-processing) slouží k paralelnímu provádění více akcí.
* Může zahrnovat další grafické Runbooky a runbooky pracovních postupů PowerShellu jako podřízené Runbooky pro vytváření pracovních postupů vysoké úrovně.

### <a name="limitations"></a>Omezení

* Autor musí znát pracovní postup PowerShellu.
* Sada Runbook se musí vypořádat s dalšími složitostmi pracovního postupu PowerShellu, jako jsou [deserializovatelné objekty](automation-powershell-workflow.md#code-changes).
* Spuštění sady Runbook trvá déle než PowerShellové Runbooky, protože před spuštěním je potřeba ji zkompilovat.
* Runbooky PowerShellu můžou být zahrnuté jenom jako podřízené Runbooky pomocí rutiny Start-AzureAutomationRunbook, která vytvoří novou úlohu.
* Nejde na Hybrid Runbook Worker Linux.

## <a name="python-runbooks"></a>Runbooky Python

Runbooky Pythonu se zkompiluje v Pythonu 2.  Můžete přímo upravit kód sady Runbook pomocí textového editoru v Azure Portal nebo pomocí textového editoru v režimu offline a [importovat Runbook](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Výhody

* Využijte robustní knihovny Pythonu.
* Můžou být spuštěné v Azure nebo na obou pracovních procesech pro Linux Hybrid Runbook Worker. Aplikace Windows Hybrid Runbook Worker je podporována s nainstalovanou [Python 2.7](https://www.python.org/downloads/release/latest/python2) .

### <a name="limitations"></a>Omezení

* Musí být obeznámené se skriptováním v Pythonu.
* V tuto chvíli se podporuje jenom Python 2, což znamená, že specifické funkce Python 3 selžou.
* Chcete-li použít knihovny třetích stran, je nutné [importovat balíček](python-packages.md) do účtu Automation, aby jej bylo možné použít.

## <a name="considerations"></a>Požadavky

Při určování typu, který se má použít pro konkrétní Runbook, vezměte v úvahu následující další předpoklady.

* Runbooky nemůžete převést z grafického na textový typ nebo jiným způsobem.
* Existují omezení využívající Runbooky různých typů jako podřízená sada Runbook. Další informace naleznete v tématu [podřízené Runbooky v Azure Automation](automation-child-runbooks.md).

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření grafického Runbooku najdete v tématu věnovaném [vytváření grafických postupů v Azure Automation](automation-graphical-authoring-intro.md) .
* Informace o rozdílech mezi pracovními postupy PowerShellu a PowerShellem pro Runbooky najdete v tématu [výuka pracovního postupu prostředí Windows PowerShell](automation-powershell-workflow.md) .
* Další informace o tom, jak vytvořit nebo importovat Runbook, najdete v tématu [Vytvoření nebo import Runbooku](manage-runbooks.md) .
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](https://docs.microsoft.com/powershell/scripting/overview).
