---
title: Řešení potíží s Azure Change Tracking
description: Naučte se řešit problémy a řešit potíže s funkcí Azure Automation Change Tracking a inventáře.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198526"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Řešení potíží s řešeními Change Tracking a Inventory

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scénář: u počítačů s Windows se nezobrazuje Change Trackingé záznamy

#### <a name="issue"></a>Problém

Pro počítače s Windows, které jsou zaregistrované pro Change Tracking, se nezobrazuje žádné Change Tracking ani výsledky inventáře.

#### <a name="cause"></a>Příčina

Tato chyba může mít následující příčiny:

* Microsoft Monitoring Agent neběží.
* Komunikace zpět s účtem Automation je zablokovaná.
* Sady Management Pack pro Change Tracking se nestahují.
* Virtuální počítač, který se připojuje, může pocházet z klonovaného počítače, který se nástroje Sysprep s nainstalovanou Microsoft Monitoring Agent.

#### <a name="resolution"></a>Řešení

Řešení uvedená níže můžou přispět k vyřešení vašeho problému. Pokud stále potřebujete pomoc, můžete shromáždit diagnostické informace a kontaktovat podporu. Na počítači agenta přejděte do složky C:\Program Files\Microsoft monitoring Agent\Agent\Tools a spusťte následující příkazy:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Ve výchozím nastavení je povoleno trasování chyb. Chcete-li povolit podrobné chybové zprávy jako v předchozím příkladu, použijte parametr *ver* . V případě trasování informací použijte při volání **StartTracing. cmd** *soubor INF* .

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent neběží.

Ověřte, že na tomto počítači běží Microsoft Monitoring Agent (HealthService. exe).

##### <a name="communication-to-automation-account-blocked"></a>Komunikace s blokovaným účtem služby Automation

Zkontrolujte Prohlížeč událostí na počítači a vyhledejte všechny události, které mají v nich slovo "sledování změn ve".

Informace o adresách a portech, které musí Change Tracking fungovat, najdete v tématu [Automatizace prostředků ve vašem datovém centru nebo cloudu pomocí Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#network-planning) .

##### <a name="management-packs-not-downloaded"></a>Sady Management Pack nebyly staženy

Ověřte, zda jsou místně nainstalovány následující Change Tracking a sady Management Pack inventáře:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VIRTUÁLNÍ počítač z klonovaného počítače, který nebyl nástroje Sysprep

Pokud používáte Klonovaný obrázek, napřed nejprve nástroj Sysprep a pak nainstalujte Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scénář: žádné Change Tracking ani výsledky inventáře na počítačích se systémem Linux

#### <a name="issue"></a>Problém

Nevidíte žádné inventáře ani výsledky Change Tracking pro počítače se systémem Linux, které jsou zaregistrované pro Change Tracking. 

#### <a name="cause"></a>Příčina
Tady jsou možné příčiny specifické pro tento problém:
* Agent Log Analytics pro Linux neběží.
* Agent Log Analytics pro Linux není správně nakonfigurovaný.
* Došlo ke konfliktu monitorování integrity souborů (FIM).

#### <a name="resolution"></a>Řešení 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agent Log Analytics pro Linux není spuštěný.

Ověřte, že je na vašem počítači spuštěn démon pro agenta Log Analytics pro Linux (omsagent). Spusťte následující dotaz v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Pokud se Váš počítač nezobrazuje ve výsledcích dotazu, nedošlo v poslední době k vrácení se změnami. Pravděpodobně došlo k potížím s místní konfigurací a je třeba agenta přeinstalovat. Informace o instalaci a konfiguraci najdete v tématu [shromáždění dat protokolu pomocí agenta Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Pokud se Váš počítač zobrazí ve výsledcích dotazu, ověřte konfiguraci oboru. Informace najdete [v tématu cílení řešení monitorování v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Další řešení potíží s tímto problémem najdete v tématu [problém: nevidíte žádná data pro Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agent Log Analytics pro Linux není správně nakonfigurovaný.

Agent Log Analytics pro Linux se nemusí správně nakonfigurovat pro výstupní kolekci protokolů a příkazového řádku pomocí nástroje kolektor protokolů OMS. Podívejte se [na téma sledování změn ve vašem prostředí pomocí Change Tracking řešení](../change-tracking.md).

##### <a name="fim-conflicts"></a>Konflikty FIM

Funkce FIM Azure Security Center možná nesprávně ověřuje integritu souborů Linux. Ověřte, že je produkt FIM funkční a správně nakonfigurovaný pro monitorování souborů systému Linux. Podívejte se [na téma sledování změn ve vašem prostředí pomocí Change Tracking řešení](../change-tracking.md).

## <a name="next-steps"></a>Další kroky

Pokud nevidíte svůj problém nebo ho nemůžete vyřešit, použijte pro další podporu jeden z následujících kanálů.

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
