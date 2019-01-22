---
title: Testování runbooku ve službě Azure Automation
description: Před publikováním runbooku ve službě Azure Automation, takže ji můžete otestovat zajistit, že to funguje podle očekávání.  Tento článek popisuje, jak otestovat sadu runbook a zobrazit její výsledek.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 95e3f4426fab8ed3ff28877607dee8694962e79f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422467"
---
# <a name="testing-a-runbook-in-azure-automation"></a>Testování runbooku ve službě Azure Automation
Při testování sady runbook [koncept](automation-creating-importing-runbook.md#publishing-a-runbook) spuštění a jsou dokončeny všechny akce, které aplikace provádí. Nevytvoří se žádná historie úlohy, ale [výstup](automation-runbook-output-and-messages.md#output-stream) a [upozornění a chyby](automation-runbook-output-and-messages.md#message-streams) datové proudy jsou zobrazeny v testovací podokno výstup. Zprávy [podrobné Stream](automation-runbook-output-and-messages.md#message-streams) se zobrazují v podokně výstupu pouze tehdy, pokud [proměnnou $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) je nastavena na pokračovat.

I v případě, že je právě spuštěna verze konceptu, sady runbook dál normálně spouští pracovní postup a dělá všechny akce s prostředky v prostředí. Z tohoto důvodu jste měli runbooky testovat jenom na prostředky mimo produkční.

Postup pro každý test [typu runbook](automation-runbook-types.md) je stejné a zde není žádný rozdíl v testování mezi textový editor a grafickém editoru na webu Azure Portal.  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>Otestování runbooku na portálu Azure portal
Můžete spolupracovat s kterýmkoli [typ runbooku](automation-runbook-types.md) na webu Azure Portal.

1. Otevřete jeho koncept runbooku buď [textový editor](automation-edit-textual-runbook.md) nebo [grafický editor](automation-graphical-authoring-intro.md).
2. Klikněte na tlačítko **Test** tlačítko otevřete okno Test.
3. Pokud má runbook parametry, budou uvedené v levém podokně, ve kterém můžete zadat hodnoty, které se použijí pro test.
4. Pokud chcete spustit test [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), změňte **parametrů běhu** k **Hybrid Worker** a vyberte název cílové skupiny.  V opačném případě ponechte výchozí **Azure** pro spuštění testu v cloudu.
5. Klikněte na tlačítko **Start** tlačítko pro spuštění testu.
6. Pokud je sada runbook [pracovního postupu Powershellu](automation-runbook-types.md#powershell-workflow-runbooks) nebo [grafický](automation-runbook-types.md#graphical-runbooks), potom můžete ukončit nebo jej pozastavit, pokud je testován s tlačítky pod podoknem výstupu. Když runbook pozastavíte, dokončí aktuální aktivitu teprve pak se pozastaví. Po pozastavení sady runbook můžete ji zastavit nebo restartovat.
7. Zkontrolujte výstup z runbooku v podokně výstup.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak vytvořit nebo importovat runbook, najdete v článku [vytvoření nebo import runbooku ve službě Azure Automation](automation-creating-importing-runbook.md)
* Další informace o vytváření grafického obsahu najdete v článku [Vytváření grafického obsahu v Azure Automation](automation-graphical-authoring-intro.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).
* Další informace o konfiguraci sady runbook vrátit stavové zprávy a chyby, včetně doporučené postupy, najdete v článku [Runbook výstup a zprávy ve službě Azure Automation](automation-runbook-output-and-messages.md)


