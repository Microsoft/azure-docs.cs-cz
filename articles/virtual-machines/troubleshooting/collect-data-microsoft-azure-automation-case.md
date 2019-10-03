---
title: Data, která se mají shromažďovat při otevření případu pro Microsoft Azure Automation | Microsoft Docs
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
ms.openlocfilehash: 83ee78d369af7fe99de8e7236fe1eb0bc63a942f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846677"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Data, která se mají shromažďovat při otevření případu pro Microsoft Azure Automation

Tento článek popisuje některé základní informace, které byste měli shromáždit před otevřením případu pro Azure Automation s podporou Microsoft Azure. Tyto informace nejsou nutné k otevření případu. Může to ale přispět k rychlejšímu řešení problému Microsoftu. Po otevření případu může být také požádáni o tato data pracovníkem podpory.

## <a name="collect-more-information"></a>Shromažďovat další informace

Než otevřete případ pro podporu Microsoft Azure Automation, doporučujeme, abyste shromáždili následující informace.

### <a name="basic-data-collection"></a>Základní shromažďování dat

Shromážděte údaje popsané v následujícím článku znalostní báze Knowledge Base:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) jak zachytit diagnostiku s využitím skriptu Azure Automation

## <a name="collect-data-depending-on-issue"></a>Shromažďovat data v závislosti na problému
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Problémy ovlivňující Update Management v systému Linux

1. Kromě položek uvedených v článku KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)spusťte následující nástroj pro shromažďování protokolů:

   [Kolektor protokolů agenta OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Zkomprimujte obsah následující složky a potom komprimovaný soubor odešlete podpoře Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Ověřte, že ID pracovního prostoru, ke kterému se oznamuje agent OMS Linux, je stejný jako pracovní prostor monitorovaný pro aktualizace.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Problémy ovlivňující Update Management ve Windows

Kromě položek, které jsou uvedeny v [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), udělejte toto:

1. Exportujte následující protokoly událostí do formátu EVTX:

   * Systém
   * Aplikace
   * Zabezpečení
   * Operations Manager
   * Microsoft-SMA/provozní

2. Ověřte, že ID pracovního prostoru, ke kterému se agent hlásí, je stejný jako pracovní prostor monitorovaný aktualizacemi Windows.

### <a name="for-issues-that-affect-a-job"></a>Pro problémy, které ovlivňují úlohu

Kromě položek uvedených v [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)shromážděte následující informace:

1. Shromáždit číslo **ID** úlohy (pro úlohu, u které dochází k problému):

   1. V Azure Portal otevřete okno **účty Automation** .
   2. Vyberte **účet Automation** , který chcete řešit.
   3. Vyberte **úlohy**.
   4. Vyberte úlohu, kterou chcete řešit.
   5. V části **Souhrn úlohy**vyhledejte **ID úlohy** (GUID).

   ![ID úlohy v podokně souhrnu úlohy](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Shromažďování názvu účtu:

   1. V Azure Portal otevřete okno **účty Automation** .
   2. Získejte název **účtu Automation** , který se chystáte řešit.

3. Shromážděte ukázku skriptu, který používáte.

4. Shromáždit soubory protokolu:

   1. V Azure Portal otevřete okno **účty Automation** .
   2. Vyberte **účet Automation** , který chcete řešit.
   3. Vyberte **úlohy**.
   4. Vyberte úlohu, kterou chcete řešit.
   5. Vyberte **všechny protokoly**.
   6. V výsledném okně Shromážděte data.

   ![Data uvedená v části všechny protokoly](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Pro problémy, které ovlivňují moduly

Kromě položek v části "základní shromažďování dat" shromážděte následující informace:

* Kroky, které jste provedli, aby bylo možné problém reprodukovat.
* Snímek obrazovky se všemi chybovými zprávami.
* Snímek obrazovky s aktuálními moduly a čísly jejich verzí.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, obraťte se na odborníky na Azure na [webu MSDN Azure a Stack Overflow fórech](https://azure.microsoft.com/support/forums/).

Případně můžete také zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.