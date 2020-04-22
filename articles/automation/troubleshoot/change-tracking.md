---
title: Řešení problémů se sledováním změn a inventářem
description: Zjistěte, jak řešit a řešit problémy s řešením Azure Automation Change Tracking and Inventory.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679353"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Poradce při potížích se sledováním změn a inventářem

Tento článek popisuje, jak řešit problémy se sledováním změn a inventářem.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scénář: Záznamy sledování změn a zásob se nezobrazují pro počítače s Windows.

#### <a name="issue"></a>Problém

U počítačů s Windows, které jsou na palubě, se nezobrazují žádné výsledky sledování změn a inventáře.

#### <a name="cause"></a>Příčina

Tato chyba může mít následující příčiny:

* Agent analýzy protokolů pro Systém Windows není spuštěn.
* Komunikace zpět k účtu Automation je blokována.
* Balíčky sledování změn a správy inventáře se nestahují.
* Virtuální počítač je na palubě může pocházet z klonovaného počítače, který nebyl sysprepped s agentem Log Analytics pro systém Windows nainstalován.

#### <a name="resolution"></a>Řešení

V počítači agenta Log Analytics přejděte na **c:\programfiles\Microsoft Monitoring Agent\Agent\Tools** a spusťte následující příkazy:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Pokud stále potřebujete pomoc, můžete shromažďovat diagnostické informace a kontaktovat podporu. 

> [!NOTE]
> Agent Log Analyticss umožňuje ve výchozím nastavení trasování chyb. Chcete-li povolit podrobné chybové zprávy jako `VER` v předchozím příkladu, použijte parametr. Pro informace stopy, použijte `INF` při `StartTracing.cmd`vyvolání .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agent analýzy protokolů pro systém Windows není spuštěn

Ověřte, zda je v počítači spuštěn agent Analýzy protokolů pro systém Windows **(HealthService.exe).**

##### <a name="communication-to-automation-account-blocked"></a>Komunikace s účtem Automation byla zablokována.

Zkontrolujte Prohlížeč událostí v počítači a vyhledejte všechny události, které mají slovo `changetracking` v nich.

Viz [Automatizace prostředků ve vašem datovém centru nebo cloudu pomocí hybridního runbook workeru,](../automation-hybrid-runbook-worker.md#network-planning) kde se dozvíte o adresách a portech, které musí být povoleny, aby sledování změn a inventář fungovaly.

##### <a name="management-packs-not-downloaded"></a>Nestažené sady Management Pack

Ověřte, zda jsou místně nainstalovány následující balíčky sledování změn a správy inventáře:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Virtuální počítač z klonovaného počítače, který nebyl sysprepped

Pokud používáte klonoci bitové kopie, sysprep bitovou kopii první a potom nainstalujte agenta Log Analytics pro Systém Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scénář: Žádné sledování změn a výsledky inventarizace na počítačích s Linuxem

#### <a name="issue"></a>Problém

Nevidíte žádné výsledky inventáře a sledování změn pro počítače s Linuxem, které jsou na palubě pro řešení. 

#### <a name="cause"></a>Příčina
Zde jsou možné příčiny specifické pro tento problém:
* Agent Log Analytics pro Linux není spuštěn.
* Agent Analýzy protokolů pro Linux není správně nakonfigurován.
* Existují konflikty monitorování integrity souborů (FIM).

#### <a name="resolution"></a>Řešení 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agent Log Analytics pro Linux neběží

Ověřte, zda je v počítači spuštěn daemon pro agenta Log Analytics pro Linux **(omsagent).** Spusťte následující dotaz v pracovním prostoru Log Analytics, který je propojen s vaším účtem Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Pokud počítač ve výsledcích dotazu nevidíte, není v poslední době vrácení se změnami. Pravděpodobně došlo k problému s místní konfigurací a měli byste přeinstalovat agenta. Informace o instalaci a konfiguraci naleznete v [tématu Shromažďování dat protokolu s agentem Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Pokud se váš počítač zobrazí ve výsledcích dotazu, ověřte konfiguraci oboru. Viz [Řešení monitorování cílení v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Další řešení tohoto problému naleznete v [tématu Problém: Nevidíte žádná data Linuxu](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agent Log Analytics pro Linux není správně nakonfigurován

Agent Log Analytics pro Linux nemusí být správně nakonfigurován pro kolekci výstupu protokolu a příkazového řádku pomocí nástroje OMS Log Collector. Viz [Sledování změn ve vašem prostředí pomocí řešení Sledování změn a Zásoby](../change-tracking.md).

##### <a name="fim-conflicts"></a>Konflikty FIM

Funkce FIM centra zabezpečení Azure může nesprávně ověřovat integritu souborů Linuxu. Ověřte, zda je FIM funkční a správně nakonfigurovaný pro monitorování souborů Linuxu. Viz [Sledování změn ve vašem prostředí pomocí řešení Sledování změn a Zásoby](../change-tracking.md).

## <a name="next-steps"></a>Další kroky

Pokud problém nevidíte výše nebo nemůžete problém vyřešit, vyzkoušejte další podporu jedním z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport)se s oficiálním účtem Microsoft Azure a vylepšete tak zákaznickou zkušenost propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
