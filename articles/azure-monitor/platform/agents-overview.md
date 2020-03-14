---
title: Přehled agentů monitorování Azure | Microsoft Docs
description: Tento článek poskytuje podrobný přehled dostupných agentů Azure, které podporují monitorování virtuálních počítačů hostovaných v Azure nebo hybridním prostředí.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249084"
---
# <a name="overview-of-azure-monitor-agents"></a>Přehled agentů Azure Monitor

Virtuální počítače a jiné výpočetní prostředky vyžadují od agenta shromažďování dat monitorování, aby bylo možné měřit výkon a dostupnost hostovaného operačního systému a úloh. Tento článek popisuje agenty používané nástrojem Azure Monitor a pomáhá určit, které požadavky je potřeba splnit pro konkrétní prostředí.

> [!NOTE]
> Azure Monitor v současné době má více agentů z důvodu nedávné konsolidace Azure Monitor a Log Analytics. I když se může ve svých funkcích překrývat, každá z nich má jedinečné možnosti. V závislosti na vašich požadavcích budete možná potřebovat jednoho nebo více agentů na virtuálních počítačích. 

Můžete mít konkrétní sadu požadavků, které nelze zcela splnit jediným agentem pro konkrétní virtuální počítač. Můžete například chtít použít výstrahy metriky, které vyžadují rozšíření Azure Diagnostics, ale také chcete využívat funkce Azure Monitor pro virtuální počítače, které vyžadují agenta Log Analytics a agenta závislostí. V takových případech můžete použít více agentů a jedná se o běžný scénář pro zákazníky, kteří od nich vyžadují funkčnost.

## <a name="summary-of-agents"></a>Souhrn agentů

Následující tabulky poskytují rychlé porovnání Azure Monitor agentů pro systémy Windows a Linux. Další podrobnosti najdete v níže uvedené části. 

### <a name="windows-agents"></a>Agenti systému Windows

| | Diagnostika<br>rozšíření (WAD) | Log Analytics<br>agent | Závislost<br>agent |
|:---|:---|:---|:---|
| Podporovaná prostředí | Azure | Azure<br>Jiný Cloud<br>Místní | Azure<br>Jiný Cloud<br>Místní | 
| Požadavky agenta  | Žádné | Žádné | Vyžaduje agenta Log Analytics |
| Data shromážděná | Protokoly událostí<br>Události ETW<br>Výkon<br>Protokoly založené na souborech<br>Protokoly IIS<br>Protokoly aplikací .NET<br>Výpisy stavu systému<br>Protokoly diagnostiky agenta | Protokoly událostí<br><IIS logs> výkonu<br>Protokoly založené na souborech<br>Přehledy a řešení<br>Další služby | Podrobnosti procesu a závislosti<br>Metriky síťového připojení |
| Data odesílaná do | Azure Storage<br>Azure Monitor metriky<br>Centrum událostí | Protokoly služby Azure Monitor | Protokoly služby Azure Monitor |


### <a name="linux-agents"></a>Agenti systému Linux

| | Diagnostika<br>rozšíření (LAD) | Telegraf<br>agent | Log Analytics<br>agent | Závislost<br>agent |
|:---|:---|:---|:---|:---|
| Podporovaná prostředí | Azure | Azure<br>Jiný Cloud<br>Místní | Azure<br>Jiný Cloud<br>Místní | Azure<br>Jiný Cloud<br>Místní |
| Požadavky agenta  | Žádné | Žádné | Žádné | Vyžaduje agenta Log Analytics |
| Data shromážděná | Syslog<br>Výkon | Výkon | Syslog<br>Výkon| Podrobnosti procesu a závislosti<br>Metriky síťového připojení |
| Data odesílaná do | Azure Storage<br>Centrum událostí | Azure Monitor metriky | Protokoly služby Azure Monitor | Protokoly služby Azure Monitor |

## <a name="log-analytics-agent"></a>Agent Log Analytics

[Agent Log Analytics](log-analytics-agent.md) shromažďuje data monitorování z hostovaného operačního systému a úloh virtuálních počítačů v Azure, jiných poskytovatelů cloudu a místních. Shromažďuje data do pracovního prostoru Log Analytics. Agent Log Analytics je stejný agent, kterého používá aplikace System Center Operations Manager, a můžete mít více domácích počítačů agentů ke komunikaci se skupinou pro správu a Azure Monitor současně. Tento agent je také vyžadován některými přehledy a řešeními v Azure Monitor.


> [!NOTE]
> Agent Log Analytics pro systém Windows se často označuje jako Microsoft Monitoring Agent (MMA). Agent Log Analytics pro Linux se často označuje jako agent OMS.



Agenta Log Analytics použijte v případě, že potřebujete:

* Shromažďovat protokoly a data o výkonu z virtuálních nebo fyzických počítačů mimo Azure. 
* Odešlete data do pracovního prostoru Log Analytics, abyste mohli využívat funkce podporované [Azure monitor protokoly](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , jako jsou například [dotazy protokolu](../log-query/log-query-overview.md).
* Použijte [Azure monitor pro virtuální počítače](../insights/vminsights-overview.md) , které vám umožní monitorovat vaše virtuální počítače ve velkém měřítku a monitorovat jejich procesy a závislosti na dalších prostředcích a externích procesech.  
* Spravujte zabezpečení virtuálních počítačů pomocí [Azure Security Center](../../security-center/security-center-intro.md) nebo [Azure Sentinel](../../sentinel/overview.md).
* Pro zajištění komplexní správy virtuálních počítačů Azure použijte [Azure Automation správu aktualizací](../../automation/automation-update-management.md), [konfiguraci Azure Automation](../../automation/automation-dsc-overview.md)nebo [Azure Automation Change Tracking a inventarizaci](../../automation/change-tracking.md) .
* Pomocí různých [řešení](../monitor-reference.md#insights-and-core-solutions) můžete monitorovat konkrétní službu nebo aplikaci.

Mezi omezení agenta Log Analytics patří:

- Nejde odesílat data do Azure Monitor metrik, Azure Storage nebo Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Rozšíření diagnostiky Azure

[Rozšíření Azure Diagnostics](diagnostics-extension-overview.md) shromažďuje data monitorování z hostovaného operačního systému a úloh virtuálních počítačů Azure a dalších výpočetních prostředků. Primárně shromažďuje data do Azure Storage, ale také umožňuje definovat datové páry k odesílání dat do jiných cílů, jako jsou Azure Monitor metriky a Azure Event Hubs.

Diagnostické rozšíření Azure použijte v případě, že potřebujete:

- Odeslat data do Azure Storage pro archivaci nebo ji analyzovat pomocí nástrojů, jako je [Průzkumník služby Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Odešlete data do [Azure monitor metriky](data-platform-metrics.md) , abyste je mohli analyzovat pomocí [Průzkumníka metrik](metrics-getting-started.md) a mohli využívat funkce, jako jsou [výstrahy metriky](../../azure-monitor/platform/alerts-metric-overview.md) téměř v reálném čase a [Automatické škálování](autoscale-overview.md) (jenom Windows).
- Posílání dat do nástrojů třetích stran pomocí [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Shromažďovat [diagnostiku spouštění](../../virtual-machines/troubleshooting/boot-diagnostics.md) a prozkoumat problémy se spouštěním virtuálního počítače

Mezi omezení Azure Diagnostics Extension patří:

- Dá se použít jenom s prostředky Azure.
- Omezená schopnost odesílat data do protokolů Azure Monitor.

## <a name="telegraf-agent"></a>Agent telegraf

[Agent InfluxData telegraf](collect-custom-metrics-linux-telegraf.md) se používá ke shromažďování údajů o výkonu z počítačů se systémem Linux pro Azure monitor metrik.

Telegraf agenta použijte v případě, že potřebujete:

* Odešlete data do [Azure monitor metriky](data-platform-metrics.md) , abyste je mohli analyzovat pomocí [Průzkumníka metrik](metrics-getting-started.md) a mohli využívat funkce, jako jsou [výstrahy metriky](../../azure-monitor/platform/alerts-metric-overview.md) téměř v reálném čase a [Automatické škálování](autoscale-overview.md) (jenom Linux). 



## <a name="dependency-agent"></a>Agent závislostí

Agent závislostí shromažďuje zjištěná data o procesech spuštěných na virtuálním počítači a závislostech externích procesů. 

Agenta závislostí použijte v případě, že potřebujete:

* Použijte funkci map [Azure monitor pro virtuální počítače](../insights/vminsights-overview.md) nebo řešení [Service map](../insights/service-map.md) .

Při použití agenta závislostí Vezměte v úvahu tyto skutečnosti:

- Agent závislostí vyžaduje, aby byl na stejném virtuálním počítači nainstalovaný agent Log Analytics.
- V případě virtuálních počítačů se systémem Linux musí být agent Log Analytics nainstalován před rozšířením diagnostiky Azure.

## <a name="extensions-compared-to-agents"></a>Rozšíření v porovnání s agenty

Log Analytics rozšíření pro [Windows](../../virtual-machines/extensions/oms-windows.md) a [Linux](../../virtual-machines/extensions/oms-linux.md) nainstalujte agenta Log Analytics na virtuální počítače Azure. Rozšíření závislostí Azure Monitor pro [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) instalují agenta závislostí na virtuálních počítačích Azure. Jedná se o stejné výše popsané agenty, ale umožňují vám jejich správu prostřednictvím [rozšíření virtuálních počítačů](../../virtual-machines/extensions/overview.md). Pokud je to možné, měli byste použít rozšíření k instalaci a správě agentů.


## <a name="next-steps"></a>Další kroky

Další podrobnosti o jednotlivých agentech získáte v následujících verzích:

- [Přehled agenta Log Analytics](log-analytics-agent.md)
- [Přehled rozšíření Azure Diagnostics](diagnostics-extension-overview.md)
- [Shromažďování vlastních metrik pro virtuální počítač se systémem Linux pomocí agenta InfluxData telegraf](collect-custom-metrics-linux-telegraf.md)
