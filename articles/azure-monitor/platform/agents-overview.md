---
title: Přehled agentů monitorování Azure| Dokumenty společnosti Microsoft
description: Tento článek obsahuje podrobný přehled agentů Azure, kteří jsou k dispozici a podporují monitorování virtuálních počítačů hostovaných v Azure nebo hybridním prostředí.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249084"
---
# <a name="overview-of-azure-monitor-agents"></a>Přehled agentů Azure Monitoru

Virtuální počítače a další výpočetní prostředky vyžadují agenta ke shromažďování dat monitorování k měření výkonu a dostupnosti jejich hostovaného operačního systému a úloh. Tento článek popisuje agenty používané službou Azure Monitor a pomáhá určit, které je třeba splnit požadavky pro konkrétní prostředí.

> [!NOTE]
> Azure Monitor má aktuálně více agentů z důvodu nedávné konsolidace Azure Monitor a Log Analytics. Zatímco tam může být překrytí v jejich funkcích, každý má jedinečné schopnosti. V závislosti na vašich požadavcích můžete potřebovat jednoho nebo více agentů ve virtuálních počítačích. 

Můžete mít konkrétní sadu požadavků, které nelze zcela splněny s jedním agentem pro konkrétní virtuální počítač. Můžete například chtít použít upozornění metriky, které vyžaduje rozšíření diagnostiky Azure, ale také chcete využít funkce Azure Monitor pro virtuální počítače, který vyžaduje agenta Log Analytics a agentzávislostí. V takových případech můžete použít více agentů a toto je běžný scénář pro zákazníky, kteří od každého z nich vyžadují funkce.

## <a name="summary-of-agents"></a>Shrnutí agentů

Následující tabulky poskytují rychlé porovnání agentů Azure Monitoru pro Windows a Linux. Další podrobnosti o každém z nich jsou uvedeny v následující části. 

### <a name="windows-agents"></a>Agenti systému Windows

| | Diagnostika<br>rozšíření (WAD) | Log Analytics<br>Agent | Závislost<br>Agent |
|:---|:---|:---|:---|
| Podporovaná prostředí | Azure | Azure<br>Další cloud<br>Lokálně | Azure<br>Další cloud<br>Lokálně | 
| Požadavky na zástupce  | Žádný | Žádný | Vyžaduje agenta analýzy protokolů |
| Shromažďovaná data | Protokoly událostí<br>Trasování událostí pro Windows – události<br>Výkon<br>Protokoly založené na souborech<br>Protokoly IIS<br>Protokoly aplikací .NET<br>Výpisy stavu systému<br>Protokoly diagnostiky agentů | Protokoly událostí<br>Výkon<IIS logs><br>Protokoly založené na souborech<br>Přehledy a řešení<br>Další služby | Podrobnosti a závislosti procesu<br>Metriky síťového připojení |
| Data odeslaná | Azure Storage<br>Metriky azure monitoru<br>Centrum událostí | Protokoly služby Azure Monitor | Protokoly služby Azure Monitor |


### <a name="linux-agents"></a>Agenti systému Linux

| | Diagnostika<br>prodloužení (LAD) | Telegraf<br>Agent | Log Analytics<br>Agent | Závislost<br>Agent |
|:---|:---|:---|:---|:---|
| Podporovaná prostředí | Azure | Azure<br>Další cloud<br>Lokálně | Azure<br>Další cloud<br>Lokálně | Azure<br>Další cloud<br>Lokálně |
| Požadavky na zástupce  | Žádný | Žádný | Žádný | Vyžaduje agenta analýzy protokolů |
| Shromažďovaná data | Syslog<br>Výkon | Výkon | Syslog<br>Výkon| Podrobnosti a závislosti procesu<br>Metriky síťového připojení |
| Data odeslaná | Azure Storage<br>Centrum událostí | Metriky azure monitoru | Protokoly služby Azure Monitor | Protokoly služby Azure Monitor |

## <a name="log-analytics-agent"></a>Agent Log Analytics

[Agent Log Analytics](log-analytics-agent.md) shromažďuje data monitorování z hostovaného operačního systému a úlohy virtuálních počítačů v Azure, jiných poskytovatelů cloudu a místních. Shromažďuje data do pracovního prostoru Log Analytics. Agent Log Analytics je stejný agent, který používá Správce operations manageru System Center a můžete komunikovat s vaší skupinou pro správu a službou Azure Monitor současně. Tento agent je také vyžadován určitými přehledy a řešeními ve službě Azure Monitor.


> [!NOTE]
> Agent Log Analytics pro Systém Windows se často označuje jako Microsoft Monitoring Agent (MMA). Agent Log Analytics pro Linux se často označuje jako agent OMS.



Pokud potřebujete:

* Shromažďujte protokoly a údaje o výkonu z virtuálních nebo fyzických počítačů mimo Azure. 
* Odesílejte data do pracovního prostoru Log Analytics, abyste využili funkce podporované [protokoly monitorování Azure,](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) jako jsou [dotazy protokolu](../log-query/log-query-overview.md).
* Použijte [Azure Monitor pro virtuální počítače,](../insights/vminsights-overview.md) který umožňuje sledovat vaše virtuální počítače ve velkém měřítku a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech..  
* Spravujte zabezpečení svých virtuálních počítačů pomocí [Azure Security Center](../../security-center/security-center-intro.md) nebo Azure [Sentinelu](../../sentinel/overview.md).
* Využijte [správu aktualizací Azure Automation ,](../../automation/automation-update-management.md) [konfiguraci stavu Azure Automation](../../automation/automation-dsc-overview.md)nebo sledování změn a [inventáře Azure Automation k](../../automation/change-tracking.md) zajištění komplexní správy virtuálních počítačů Azure
* Ke sledování konkrétní služby nebo aplikace používejte různá [řešení.](../monitor-reference.md#insights-and-core-solutions)

Mezi omezení agenta Analýzy protokolů patří:

- Nelze odesílat data do metrik azure monitoru, Azure Storage nebo Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Rozšíření diagnostiky Azure

[Rozšíření Diagnostika Azure](diagnostics-extension-overview.md) shromažďuje data monitorování z hostovaného operačního systému a úlohy virtuálních počítačů Azure a dalších výpočetních prostředků. Primárně shromažďuje data do Azure Storage, ale také umožňuje definovat jímky dat pro odesílání dat také do jiných cílů, jako jsou metriky Azure Monitor a Azure Event Hubs.

Diagnostické rozšíření Azure použijte, pokud potřebujete:

- Odesílejte data do Azure Storage k archivaci nebo k jejich analýze pomocí nástrojů, jako je [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Odesílejte data do [metrik azure monitoru,](data-platform-metrics.md) abyste je analyzovali pomocí [průzkumníka metrik](metrics-getting-started.md) a využili výhod funkcí, jako jsou upozornění [metrik](../../azure-monitor/platform/alerts-metric-overview.md) v reálném čase a [automatické škálování](autoscale-overview.md) (jenom Windows).
- Odesílejte data do nástrojů třetích stran pomocí [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Shromážděte [diagnostiku spouštění](../../virtual-machines/troubleshooting/boot-diagnostics.md) a prozkoumejte problémy se spouštěním virtuálních vod.

Mezi omezení rozšíření diagnostiky Azure patří:

- Dá se použít jenom s prostředky Azure.
- Omezená možnost odesílat data do protokolů azure monitoru.

## <a name="telegraf-agent"></a>Telegrafagent

[Agent InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) se používá ke shromažďování dat o výkonu z počítačů S IŠ do metrik azure monitoru.

Pokud potřebujete:

* Odesílejte data do [metrik azure monitoru,](data-platform-metrics.md) abyste je analyzovali pomocí [průzkumníka metrik](metrics-getting-started.md) a využili výhod funkcí, jako jsou upozornění [metrik](../../azure-monitor/platform/alerts-metric-overview.md) v reálném čase a [automatické škálování](autoscale-overview.md) (jenom Linux). 



## <a name="dependency-agent"></a>Agent závislostí

Agent závislostí shromažďuje zjištěná data o procesech spuštěných ve virtuálním počítači a závislostech externího procesu. 

Agentzávislostí použijte, pokud potřebujete:

* Použijte funkci Azure [Monitor pro virtuální počítače](../insights/vminsights-overview.md) nebo řešení mapy [služeb.](../insights/service-map.md)

Při použití agenta závislostí zvažte následující skutečnosti:

- Agent závislostí vyžaduje, aby byl agent Analýzy protokolů nainstalován na stejném virtuálním počítači.
- Na virtuálních počítačích s Linuxem musí být agent Analýzy protokolů nainstalovaný před rozšířením Diagnostika Azure.

## <a name="extensions-compared-to-agents"></a>Rozšíření ve srovnání s agenty

Rozšíření Log Analytics pro [Windows](../../virtual-machines/extensions/oms-windows.md) a [Linux](../../virtual-machines/extensions/oms-linux.md) nainstaluje agenta Log Analytics na virtuální počítače Azure. Rozšíření závislostí monitorování Azure pro [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) nainstaluje agenta závislostí na virtuálních počítačích Azure. Jedná se o stejné agenty popsané výše, ale umožňují je spravovat prostřednictvím [rozšíření virtuálních strojů](../../virtual-machines/extensions/overview.md). K instalaci a správě agentů byste měli používat rozšíření, kdykoli je to možné.


## <a name="next-steps"></a>Další kroky

Další podrobnosti o jednotlivých agentech získáte na následující straně:

- [Přehled agenta analýzy protokolů](log-analytics-agent.md)
- [Přehled rozšíření Diagnostika Azure](diagnostics-extension-overview.md)
- [Shromažďování vlastních metrik pro virtuální počítač s Linuxem s agentem InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)
