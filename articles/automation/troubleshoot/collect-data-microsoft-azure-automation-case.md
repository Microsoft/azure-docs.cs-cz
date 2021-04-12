---
title: Data, která se mají shromažďovat při otevírání případu pro Microsoft Azure Automation | Microsoft Docs
description: Tento článek popisuje informace, které je potřeba shromáždit před otevřením případu pro Azure Automation s podporou Microsoft Azure.
services: automation
ms.subservice: ''
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.openlocfilehash: 3d2c0cf780b992d7bb464969c50d60ffa5093e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896558"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>Data, která se mají shromažďovat při otevření případu pro Microsoft Azure Automation

Tento článek popisuje některé informace, které byste měli shromáždit před otevřením případu pro Azure Automation s podporou Microsoft Azure. Tyto informace nejsou nutné k otevření případu. Může to ale přispět k rychlejšímu řešení problému Microsoftu. Po otevření případu může být také požádáni o tato data pracovníkem podpory.

## <a name="basic-data"></a>Základní data

Shromážděte základní údaje popsané v článku znalostní báze [4034605 – jak zachytit diagnostiku Azure Automation](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Data pro Update Management problémy v systému Linux

1. Kromě položek uvedených v článku KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)spusťte následující nástroj pro shromažďování protokolů:

   [Kolektor protokolů agenta OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Zkomprimujte obsah složky **/var/opt/Microsoft/omsagent/Run/automationworker/** a potom komprimovaný soubor odešlete podpoře Azure.
 
3. Ověřte, že ID pracovního prostoru, na který Log Analytics Agent pro Linux, je stejný jako ID pracovního prostoru monitorovaného pro aktualizace.

## <a name="data-for-update-management-issues-on-windows"></a>Data pro Update Management problémy ve Windows

1. Shromážděte data pro položky uvedené v [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exportujte následující protokoly událostí do formátu EVTX:

   * Systém
   * Aplikace
   * Zabezpečení
   * Operations Manager
   * Microsoft-SMA/provozní

3. Ověřte, že ID pracovního prostoru, ke kterému se agent hlásí, je stejné jako ID pracovního prostoru monitorovaného aktualizacemi Windows.

## <a name="data-for-job-issues"></a>Data pro problémy s úlohou

1. Shromážděte data pro položky uvedené v [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Shromáždit ID úlohy, u které došlo k problému:

   1. V Azure Portal přejdete na **účty Automation**.
   2. Vyberte účet Automation, který chcete řešit, a poznamenejte si název.
   3. Vyberte **úlohy**.
   4. Vyberte úlohu, kterou chcete řešit.
   5. V podokně Souhrn úlohy vyhledejte hodnotu identifikátor GUID v **ID úlohy**.

   ![ID úlohy v podokně souhrnu úlohy](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Shromážděte ukázku skriptu, který používáte.

4. Shromáždit soubory protokolu:

   1. V Azure Portal přejdete na **účty Automation**.
   2. Vyberte účet Automation, který chcete řešit.
   3. Vyberte **úlohy**.
   4. Vyberte úlohu, kterou chcete řešit.
   5. Vyberte **všechny protokoly**.
   6. Ve výsledném podokně Shromážděte data.

   ![Data uvedená v části všechny protokoly](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Data pro problémy s modulem

Kromě [základních datových položek](#basic-data)shromážděte následující informace:

* Kroky, které jste následovali, aby bylo možné problém reprodukovat.
* Snímky obrazovky se všemi chybovými zprávami.
* Snímky obrazovky aktuálních modulů a jejich čísla verzí.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další informace:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) , oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
