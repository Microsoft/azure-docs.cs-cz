---
title: Testování runbooku ve službě Azure Automation
description: Před publikováním runbooku ve službě Azure Automation, můžete otestovat k zajištění, že to funguje podle očekávání.  Tento článek popisuje postup testování sady runbook a zobrazíte jeho výsledek.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 780d197943737c3b821cbcec3afeeb60846ddf39
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="testing-a-runbook-in-azure-automation"></a>Testování runbooku ve službě Azure Automation
Při testování sady runbook [koncept](automation-creating-importing-runbook.md#publishing-a-runbook) se spustí a všechny akce, které provádí se dokončí. Nevytvoří se žádné historie úlohy, ale [výstup](automation-runbook-output-and-messages.md#output-stream) a [upozornění a chyb](automation-runbook-output-and-messages.md#message-streams) datové proudy, které se zobrazují v testovací podokno výstup. Zprávy pro [podrobný datový proud](automation-runbook-output-and-messages.md#message-streams) se zobrazují v podokně výstupu pouze tehdy, pokud [proměnnou $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) je nastaven na pokračovat.

I když je právě spuštěna verze konceptu, runbook dál normálně spouští pracovní postup a dělá všechny akce u prostředků v prostředí. Z tohoto důvodu jste měli runbooky testovat jenom na prostředky mimo produkční.

Postup testování jednotlivých [typ runbooku](automation-runbook-types.md) je stejné a že je žádné rozdíly při testování mezi textový editor a grafický editor na webu Azure portal.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Otestování sady runbook na portálu Azure
Můžete spolupracovat s kterýmkoli [typ runbooku](automation-runbook-types.md) na portálu Azure.

1. Otevřete jeho koncept runbooku buď [textový editor](automation-edit-textual-runbook.md) nebo [grafický editor](automation-graphical-authoring-intro.md).
2. Klikněte **Test** tlačítko otevřete okno Test.
3. Pokud sada runbook obsahuje parametry, budou uvedené v levém podokně, kde můžete zadat hodnoty má být použit pro test.
4. Pokud chcete spustit test [hybridní pracovní proces Runbooku](automation-hybrid-runbook-worker.md), pak změňte **spustit nastavení** k **hybridní pracovní proces** a vyberte název cílové skupiny.  Jinak použijte výchozí **Azure** ke spuštění testu v cloudu.
5. Klikněte **spustit** tlačítko spuštění testu.
6. Pokud je sada runbook [pracovního postupu Powershellu](automation-runbook-types.md#powershell-workflow-runbooks) nebo [grafický](automation-runbook-types.md#graphical-runbooks), pak můžete zastavit nebo pozastavit ji, pokud se testuje tlačítky pod podoknem výstupu. Když runbook pozastavíte, dokončí aktuální aktivitu teprve pak se pozastaví. Jakmile je pozastavená sada runbook, můžete případně ji zastavte nebo ho restartovat.
7. Zkontrolujte výstup z runbooku v podokně výstup.

## <a name="next-steps"></a>Další kroky
* Další postupy k vytvoření nebo import sady runbook najdete v tématu [vytvoření nebo import runbooku ve službě Azure Automation](automation-creating-importing-runbook.md)
* Další informace o vytváření grafického obsahu najdete v článku [Vytváření grafického obsahu v Azure Automation](automation-graphical-authoring-intro.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* Další informace o konfiguraci sady runbook vrátí stavové zprávy a chyby, včetně doporučené postupy, najdete v části [Runbook výstup a zprávy v Azure Automation.](automation-runbook-output-and-messages.md)

