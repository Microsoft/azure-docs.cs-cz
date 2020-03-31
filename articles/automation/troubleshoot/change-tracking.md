---
title: Řešení problémů se sledováním změn v Azure
description: Zjistěte, jak řešit a řešit problémy pomocí funkce Sledování změn a inventáře Azure Automation.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198526"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Řešení potíží s řešeními Change Tracking a Inventory

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scénář: Záznamy sledování změn se nezobrazují pro počítače s Windows.

#### <a name="issue"></a>Problém

Nevidíte žádné sledování změn nebo výsledky inventáře pro počítače se systémem Windows, které jsou na palubě pro sledování změn.

#### <a name="cause"></a>Příčina

Tato chyba může mít následující příčiny:

* Agent monitorování společnosti Microsoft není spuštěn.
* Komunikace zpět k účtu Automation je blokována.
* Sady Management Pack pro sledování změn se nestahují.
* Virtuální počítač, který je na palubě, mohl pocházet z klonovaného počítače, který nebyl sysprepped s nainstalovaným agentem Microsoft Monitoring Agent.

#### <a name="resolution"></a>Řešení

Níže popsaná řešení mohou pomoci vyřešit váš problém. Pokud stále potřebujete pomoc, můžete shromažďovat diagnostické informace a kontaktovat podporu. V počítači s agentem přejděte na c:\programové soubory\Agent sledování společnosti Microsoft\Agent\Nástroje a spusťte následující příkazy:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Ve výchozím nastavení je povoleno trasování chyb. Chcete-li povolit podrobné chybové zprávy jako v předchozím příkladu, použijte parametr *VER.* Pro trasování informací použijte *INF* při vyvolání **StartTracing.cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Agent monitorování společnosti Microsoft není spuštěn

Ověřte, zda je v počítači spuštěn agent monitorování společnosti Microsoft (HealthService.exe).

##### <a name="communication-to-automation-account-blocked"></a>Komunikace s účtem Automation byla zablokována.

Zkontrolujte Prohlížeč událostí v počítači a vyhledejte všechny události, které mají slovo "changetracking" v nich.

Viz [Automatizace prostředků ve vašem datovém centru nebo cloudu pomocí hybridního runbook workeru,](../automation-hybrid-runbook-worker.md#network-planning) kde se dozvíte o adresách a portech, které musí být povoleny, aby sledování změn fungovalo.

##### <a name="management-packs-not-downloaded"></a>Nestažené sady Management Pack

Ověřte, zda jsou místně nainstalovány následující balíčky sledování změn a správy inventáře:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Virtuální počítač z klonovaného počítače, který nebyl sysprepped

Pokud používáte klonoci bitové kopie, sysprep bitovou kopii první a potom nainstalujte Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scénář: Žádné sledování změn nebo výsledky inventáře na počítačích s Linuxem

#### <a name="issue"></a>Problém

Nevidíte žádné výsledky inventáře nebo sledování změn pro počítače s Linuxem, které jsou na palubě pro sledování změn. 

#### <a name="cause"></a>Příčina
Zde jsou možné příčiny specifické pro tento problém:
* Agent Log Analytics pro Linux není spuštěn.
* Agent Analýzy protokolů pro Linux není správně nakonfigurován.
* Existují konflikty monitorování integrity souborů (FIM).

#### <a name="resolution"></a>Řešení 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agent Log Analytics pro Linux neběží

Ověřte, že daemon pro agenta Log Analytics pro Linux (omsagent) běží na vašem počítači. Spusťte následující dotaz v pracovním prostoru Log Analytics, který je propojen s vaším účtem Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Pokud počítač ve výsledcích dotazu nevidíte, není v poslední době vrácení se změnami. Pravděpodobně došlo k problému s místní konfigurací a měli byste přeinstalovat agenta. Informace o instalaci a konfiguraci naleznete v [tématu Shromažďování dat protokolu s agentem Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Pokud se váš počítač zobrazí ve výsledcích dotazu, ověřte konfiguraci oboru. Viz [Řešení monitorování cílení v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Další řešení tohoto problému naleznete v tématu [Problém: Nevidíte žádná data Linuxu](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agent Log Analytics pro Linux není správně nakonfigurován

Agent Log Analytics pro Linux nemusí být správně nakonfigurován pro kolekci výstupu protokolu a příkazového řádku pomocí nástroje OMS Log Collector. Viz [Sledování změn ve vašem prostředí pomocí řešení Sledování změn](../change-tracking.md).

##### <a name="fim-conflicts"></a>Konflikty FIM

Funkce FIM centra zabezpečení Azure může nesprávně ověřovat integritu souborů Linuxu. Ověřte, zda je FIM funkční a správně nakonfigurovaný pro monitorování souborů Linuxu. Viz [Sledování změn ve vašem prostředí pomocí řešení Sledování změn](../change-tracking.md).

## <a name="next-steps"></a>Další kroky

Pokud problém nevidíte nebo se vám nedaří problém vyřešit, použijte jeden z následujících kanálů pro další podporu.

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s – oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete nastolet incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
