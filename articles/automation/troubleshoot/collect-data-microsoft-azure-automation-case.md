---
title: Data, která se mají shromažďovat při otevření případu pro Microsoft Azure Automation| Dokumenty společnosti Microsoft
description: Tento článek popisuje některé informace, které byste měli shromáždit před otevřením případu pro Azure Automation s podporou Microsoft Azure.
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
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679406"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Data, která se mají shromažďovat při otevření případu pro Microsoft Azure Automation

Tento článek popisuje některé informace, které byste měli shromáždit před otevřením případu pro Azure Automation s podporou Microsoft Azure. Tyto informace nejsou nutné k otevření případu. Může však pomoci společnosti Microsoft vyřešit váš problém rychleji. Také můžete být požádáni o tato data pracovníka podpory po otevření případu.

## <a name="basic-data"></a>Základní údaje

Shromážděte základní data popsaná v článku znalostní báze Knowledge Base [4034605 – Jak zachytit diagnostiku skriptovnou Azure Automation](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Data pro problémy se správou aktualizací na Linuxu

1. Kromě položek, které jsou uvedeny v KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), spusťte následující nástroj pro shromažďování protokolů:

   [Sběratel protokolů agentů OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Komprimujte obsah složky **/var/opt/microsoft/omsagent/run/automationworker/** a potom odešlete komprimovaný soubor do podpory Azure.
 
3. Ověřte, zda je ID pracovního prostoru, na který sestaví agent analýzy protokolů pro Linux, stejné jako ID pracovního prostoru monitorovaného pro aktualizace.

## <a name="data-for-update-management-issues-on-windows"></a>Data pro problémy se správou aktualizací v systému Windows

1. Shromažďovat údaje o položkách uvedených v [po. 4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exportujte následující protokoly událostí do formátu EVTX:

   * Systém
   * Aplikace
   * Zabezpečení
   * Operations Manager
   * Microsoft-SMA/Provozní

3. Ověřte, zda je ID pracovního prostoru, do kterého agent hlásí, stejné jako ID pracovního prostoru monitorovaného aktualizací systému Windows.

## <a name="data-for-job-issues"></a>Data pro problémy s úlohami

1. Shromažďovat údaje o položkách uvedených v [po. 4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Shromážděte ID úlohy pro úlohu, která má problém:

   1. Na webu Azure Portal přejděte na **účty automatizace**.
   2. Vyberte účet automatizace, který řešíte, a poznamenejte si název.
   3. Vyberte **úlohy**.
   4. Vyberte úlohu, kterou řešíte.
   5. V podokně Souhrn úloh vyhledejte hodnotu GUID v **ID úlohy**.

   ![ID úlohy v podokně Souhrn úlohy](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Shromážděte ukázku skriptu, který používáte.

4. Shromážděte soubory protokolu:

   1. Na webu Azure Portal přejděte na **účty automatizace**.
   2. Vyberte účet automatizace, který řešíte.
   3. Vyberte **úlohy**.
   4. Vyberte úlohu, kterou řešíte.
   5. Vyberte **všechny protokoly**.
   6. Ve výsledném podokně shromážděte data.

   ![Data uvedená v části Všechny protokoly](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Data pro problémy s moduly

Kromě základních [datových položek](#basic-data)získáte následující informace:

* Kroky, které jste postupovali, aby bylo možné problém reprodukovat.
* Snímky obrazovky všech chybových zpráv.
* Screenshoty z aktuálních modulů a jejich čísla verzí.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další pomoc:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport)se s oficiálním účtem Microsoft Azure a vylepšete tak zákaznickou zkušenost propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
