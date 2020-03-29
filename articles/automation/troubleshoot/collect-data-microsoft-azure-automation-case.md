---
title: Data, která se mají shromažďovat při otevření případu pro Microsoft Azure Automation| Dokumenty společnosti Microsoft
description: Tento článek popisuje některé základní informace, které byste měli shromáždit před otevřením případu pro Azure Automation s podporou Microsoft Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849373"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Data, která se mají shromažďovat při otevření případu pro Microsoft Azure Automation

Tento článek popisuje některé základní informace, které byste měli shromáždit před otevřením případu pro Azure Automation s podporou Microsoft Azure. Tyto informace nejsou nutné k otevření případu. Může však pomoci společnosti Microsoft vyřešit váš problém rychleji. Také můžete být požádáni o tato data pracovníka podpory po otevření případu.

## <a name="collect-more-information"></a>Shromažďujte více informací

Před otevřením případu pro microsoft azure automation podporu, doporučujeme shromáždit následující informace.

### <a name="basic-data-collection"></a>Základní sběr dat

Shromážděte data popsaná v následujícím článku znalostní báze Knowledge Base:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Jak zachytit diagnostiku skriptoposcénou Azure Automation

## <a name="collect-data-depending-on-issue"></a>Shromažďování údajů v závislosti na problému
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>V problémech, které ovlivňují správu aktualizací na Linuxu

1. Kromě položek, které jsou uvedeny v KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), spusťte následující nástroj pro shromažďování protokolů:

   [Sběratel protokolů agentů OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Komprimujte obsah následující složky a pak odešlete komprimovaný soubor do podpory Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Ověřte, zda ID pracovního prostoru, které agent OMS Linux hlásí, je stejné jako pracovní prostor, který je monitorován pro aktualizace.

### <a name="for-issues-that-affect-update-management-on-windows"></a>V problémech, které ovlivňují správu aktualizací v systému Windows

Kromě položek, které jsou uvedeny v [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), postupujte takto:

1. Exportujte následující protokoly událostí do formátu EVTX:

   * Systém
   * Aplikace
   * Zabezpečení
   * Operations Manager
   * Microsoft-SMA/Provozní

2. Ověřte, zda id pracovního prostoru, kterému agent hlásí, je stejné jako pracovní prostor monitorovaný aktualizací systému Windows.

### <a name="for-issues-that-affect-a-job"></a>V problémech, které ovlivňují úlohu

Kromě položek, které jsou uvedeny v [po. 4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), shromažďovat následující informace:

1. Shromážděte číslo **JobID** (pro úlohu, která má problém):

   1. Na webu Portál Azure přejděte na okno **Účty automatizace.**
   2. Vyberte **účet automatizace,** který řešíte.
   3. Vyberte **úlohy**.
   4. Vyberte úlohu, kterou řešíte.
   5. V části **Shrnutí úlohy**vyhledejte **ID úlohy** (GUID).

   ![ID úlohy v podokně Souhrn úlohy](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Shromážděte název účtu:

   1. Na webu Portál Azure přejděte na okno **Účty automatizace.**
   2. Získejte název **účtu automation,** který řešíte.

3. Shromážděte ukázku skriptu, který spouštěte.

4. Shromážděte soubory protokolu:

   1. Na webu Portál Azure přejděte na okno **Účty automatizace.**
   2. Vyberte **účet automatizace,** který řešíte.
   3. Vyberte **úlohy**.
   4. Vyberte úlohu, kterou řešíte.
   5. Vyberte **všechny protokoly**.
   6. Na výsledném noži shromážděte data.

   ![Data uvedená v části Všechny protokoly](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Pro problémy, které ovlivňují moduly

Kromě položek v části "Základní sběr údajů" shromážděte následující informace:

* Kroky, které jste postupovali tak, aby problém lze reprodukovat.
* Snímek obrazovky všech chybových zpráv.
* Snímek obrazovky s aktuálními moduly a jejich čísly verzí.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/).

Případně můžete podat incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
