---
title: Řešení potíží s Change Tracking a inventářem
description: Naučte se řešit problémy s Azure Automation Change Tracking a inventáře řešení potíží.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582150"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Řešení problémů s Change Tracking a inventářem

Tento článek popisuje, jak řešit problémy s Azure Automation Change Tracking a inventáře.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scénář: u počítačů s Windows se nezobrazuje Change Tracking a záznamy inventáře.

#### <a name="issue"></a>Problém

Pro počítače s Windows, které jsou připojené, se nezobrazuje žádné výsledky Change Tracking a inventáře.

#### <a name="cause"></a>Příčina

Tato chyba může mít následující příčiny:

* Agent Azure Log Analytics pro Windows není spuštěný.
* Komunikace zpět s účtem Automation je zablokovaná.
* Sady Management Pack pro Change Tracking a inventář se nestáhnou.
* Virtuální počítač, který se připojuje, může pocházet z klonovaného počítače, který se nástroje Sysprep s nainstalovaným agentem Log Analytics pro Windows.

#### <a name="resolution"></a>Řešení

Na počítači Log Analytics agenta, otevřete umístění **C:\Program Files\Microsoft monitoring Agent\Agent\Tools** a spusťte následující příkazy:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Pokud stále potřebujete pomoc, můžete shromáždit diagnostické informace a kontaktovat podporu.

> [!NOTE]
> Agent Log Analytics ve výchozím nastavení umožňuje trasování chyb. Chcete-li povolit podrobné chybové zprávy jako v předchozím příkladu, použijte `VER` parametr. Pro trasování informací použijte `INF` při vyvolání. `StartTracing.cmd`

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agent Log Analytics pro Windows není spuštěný.

Ověřte, zda je na tomto počítači spuštěný agent Log Analytics pro systém Windows (**HealthService. exe**).

##### <a name="communication-to-automation-account-blocked"></a>Komunikace s blokovaným účtem služby Automation

Zkontrolujte Prohlížeč událostí na počítači a vyhledejte všechny události, které obsahují slovo `changetracking` .

Další informace o adresách a portech, které musí být povolené pro Change Tracking a inventarizaci, najdete v tématu [Automatizace prostředků ve vašem datovém centru nebo cloudu pomocí Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Sady Management Pack nebyly staženy

Ověřte, zda jsou místně nainstalovány následující Change Tracking a sady Management Pack inventáře:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VIRTUÁLNÍ počítač z klonovaného počítače, který nebyl nástroje Sysprep

Pokud používáte Klonovaný obrázek, napřed nejprve nástroj Sysprep a poté nainstalujte agenta Log Analytics pro systém Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scénář: žádné Change Tracking a výsledky inventáře na počítačích se systémem Linux

#### <a name="issue"></a>Problém

Nevidíte žádné Change Tracking a výsledky inventáře pro počítače se systémem Linux, které jsou pro řešení připojené. 

#### <a name="cause"></a>Příčina
Tady jsou možné příčiny specifické pro tento problém:
* Agent Log Analytics pro Linux neběží.
* Agent Log Analytics pro Linux není správně nakonfigurovaný.
* Došlo ke konfliktu monitorování integrity souborů (FIM).

#### <a name="resolution"></a>Řešení 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agent Log Analytics pro Linux není spuštěný.

Ověřte, že je na vašem počítači spuštěn démon pro agenta Log Analytics pro Linux (**omsagent**). Spusťte následující dotaz v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Pokud se Váš počítač nezobrazuje ve výsledcích dotazu, nedošlo v poslední době k vrácení se změnami. Pravděpodobně došlo k potížím s místní konfigurací a je třeba agenta přeinstalovat. Informace o instalaci a konfiguraci najdete v tématu [shromáždění dat protokolu pomocí agenta Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

Pokud se Váš počítač zobrazí ve výsledcích dotazu, ověřte konfiguraci oboru. Informace najdete [v tématu cílení řešení monitorování v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Další řešení potíží s tímto problémem najdete v tématu [problém: nevidíte žádná data pro Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agent Log Analytics pro Linux není správně nakonfigurovaný.

Agent Log Analytics pro Linux nemusí být správně nakonfigurovaný pro protokoly a výstupní kolekce příkazového řádku pomocí nástroje kolektor protokolů OMS. Podívejte se [na téma sledování změn ve vašem prostředí pomocí řešení Change Tracking a inventáře](../change-tracking.md).

##### <a name="fim-conflicts"></a>Konflikty FIM

Funkce FIM Azure Security Center možná nesprávně ověřuje integritu souborů Linux. Ověřte, že je produkt FIM funkční a správně nakonfigurovaný pro monitorování souborů systému Linux. Podívejte se [na téma sledování změn ve vašem prostředí pomocí řešení Change Tracking a inventáře](../change-tracking.md).

## <a name="next-steps"></a>Další kroky

Pokud tady nevidíte svůj problém nebo nemůžete problém vyřešit, zkuste další podporu vyzkoušet u některého z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se [@AzureSupport](https://twitter.com/azuresupport)pomocí oficiálního Microsoft Azure účtu pro zlepšení prostředí pro zákazníky. Podpora Azure spojuje komunitu Azure s odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.
