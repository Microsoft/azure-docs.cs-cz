---
title: Přehled agentů monitorování Azure | Microsoft Docs
description: Tento článek poskytuje podrobný přehled dostupných agentů Azure, které podporují monitorování virtuálních počítačů hostovaných v Azure nebo hybridním prostředí.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: ae799e9a852b8700399ef695c54b3348174b560c
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069400"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Přehled agentů Azure Monitor 
Výpočetní prostředky, jako jsou virtuální počítače, generují data pro monitorování výkonu a dostupnosti stejně jako [jiné cloudové prostředky](../insights/monitor-azure-resource.md). Výpočetní prostředky i přesto mají hostovaný operační systém a úlohy, které je třeba monitorovat. Shromažďování těchto dat monitorování z prostředku vyžaduje agenta. Tento článek popisuje agenty používané nástrojem Azure Monitor a pomáhá určit, které požadavky je potřeba splnit pro konkrétní prostředí.

## <a name="summary-of-agents"></a>Souhrn agentů

> [!NOTE]
> Azure Monitor v současné době má více agentů z důvodu nedávné konsolidace Azure Monitor a Log Analytics. Oba agenti sdílí některé možnosti, zatímco jiné funkce jsou pro konkrétního agenta jedinečné. V závislosti na vašich požadavcích budete možná potřebovat jednoho z agentů nebo obojí. 

Azure Monitor má tři agenty, které poskytují konkrétní funkce. V závislosti na vašich požadavcích můžete nainstalovat jednoho agenta nebo víc na virtuální počítače a další výpočetní prostředky.

* [Rozšíření Azure Diagnostics](#azure-diagnostic-extension)
* [Agent Log Analytics](#log-analytics-agent)
* [Agent závislostí](#dependency-agent)

Následující tabulka poskytuje rychlé porovnání různých agentů. Podrobnosti najdete v dalších částech tohoto článku.

| | Diagnostické rozšíření Azure | Agent Log Analytics | Agent závislostí |
|:---|:---|:---|:---|
| Podporovaná prostředí | Azure | Azure<br>Jiný Cloud<br>Lokálně | Azure<br>Jiný Cloud<br>Lokálně |
| Operační systémy | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| Závislosti agenta  | Žádný | Žádný | Vyžaduje agenta Log Analytics |
| Data shromážděná | Protokoly událostí<br>Události ETW<br>Syslog<br>Výkon<br>Protokoly IIS<br>Protokoly výstupu trasování aplikace .NET<br>Výpisy stavu systému | Protokoly událostí<br>Syslog<br>Výkon<br>Protokoly IIS<br>Vlastní protokoly<br>Data z řešení | Podrobnosti procesu a závislosti<br>Metriky síťového připojení |
| Data odesílaná do | Azure Storage<br>Azure Monitor metriky<br>Centrum událostí | Protokoly služby Azure Monitor | Protokoly služby Azure Monitor |



## <a name="azure-diagnostic-extension"></a>Diagnostické rozšíření Azure
[Rozšíření Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-overview.md) shromažďuje data monitorování z hostovaného operačního systému a zatížení výpočetních prostředků Azure. Primárně shromažďuje data do Azure Storage. Azure Monitor můžete nakonfigurovat tak, aby se data z úložiště zkopírovala do pracovního prostoru Log Analytics. Data o výkonu hosta můžete také shromažďovat do Azure Monitor metrik.

Diagnostické rozšíření Azure se často označuje jako Windows Azure Diagnostic (WAD) nebo Linux Azure Diagnostic (LAD).


### <a name="scenarios-supported"></a>Podporované scénáře

Mezi scénáře podporované rozšířením Azure Diagnostics patří následující:

* Shromažďovat protokoly a data o výkonu z výpočetních prostředků Azure.
* Archivujte protokoly a data o výkonu z hostovaného operačního systému do služby Azure Storage.
* Pomocí nástroje, jako je [Průzkumník služby Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md), zobrazte data monitorování v úložišti.
* Shromážděte údaje o výkonu v databázi metrik, abyste mohli využívat funkce podporované [Azure monitor metrikami](data-platform-metrics.md) , jako jsou [výstrahy metriky](../../azure-monitor/platform/alerts-metric-overview.md) téměř v reálném čase a [Automatické škálování](autoscale-overview.md). 
* Shromažďovat data monitorování z [úložiště do pracovního prostoru Log Analytics](azure-storage-iis-table.md) , abyste mohli využívat funkce podporované [Azure monitor protokoly](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , jako jsou například [dotazy protokolu](../log-query/log-query-overview.md).
* Odesílat data monitorování nástrojům třetích stran pomocí [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
* Prozkoumejte problémy se spouštěním virtuálních počítačů pomocí [diagnostiky spouštění](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Zkopírujte data z aplikací běžících ve vašem VIRTUÁLNÍm počítači [, abyste Application Insights](diagnostics-extension-to-application-insights.md) mohli integrovat s ostatními monitorováním aplikací.

## <a name="log-analytics-agent"></a>Agent Log Analytics
[Agent Log Analytics](log-analytics-agent.md) shromažďuje data monitorování z hostovaného operačního systému a úloh virtuálních počítačů v Azure, jiných poskytovatelů cloudu a místních. Shromažďuje data do pracovního prostoru Log Analytics.

Agent Log Analytics je stejný agent, kterého používá aplikace System Center Operations Manager a počítače s více domácími agenty ke komunikaci s vaší skupinou pro správu a Azure Monitor současně. Tento agent je také vyžadován některými řešeními v Azure Monitor.

Agent Log Analytics pro systém Windows se často označuje jako Microsoft Management Agent (MMA). Agent Log Analytics pro Linux se často označuje jako agent OMS.


### <a name="scenarios-supported"></a>Podporované scénáře

Mezi scénáře podporované agentem Log Analytics patří následující:

* Shromažďovat protokoly a data o výkonu z virtuálních počítačů v Azure, jiných poskytovatelů cloudu a místních. 
* Shromažďovat data monitorování do pracovního prostoru Log Analytics, abyste mohli využívat funkce podporované [Azure monitor protokoly](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , jako jsou například [dotazy protokolu](../log-query/log-query-overview.md).
* Použijte řešení Azure Monitor monitorování, jako je [Azure monitor pro virtuální počítače](../insights/vminsights-overview.md), [Azure monitor pro kontejnery](../insights/container-insights-overview.md)atd.  
* Spravujte zabezpečení virtuálních počítačů pomocí služby [Azure Sentinel](../../sentinel/overview.md) , která vyžaduje agenta Log Analytics.
* Spravujte zabezpečení virtuálních počítačů pomocí [Azure Security Center](../../security-center/security-center-intro.md) , které vyžadují agenta Log Analytics.
* Využijte funkce [Azure Automation](../../automation/automation-intro.md) k zajištění komplexní správy virtuálních počítačů Azure prostřednictvím jejich životního cyklu.  Příklady těchto funkcí, které vyžadují agenta Log Analytics, zahrnují:
  * [Azure Automation správu](../../automation/automation-update-management.md) aktualizací operačního systému.
  * [Konfigurace stavu Azure Automation](../../automation/automation-dsc-overview.md) pro zachování konzistentního stavu konfigurace.
  * Sledujte změny konfigurace pomocí [Azure Automation Change Tracking a inventáře](../../automation/change-tracking.md).

## <a name="dependency-agent"></a>Agent závislostí
Agent závislostí shromažďuje zjištěná data o procesech spuštěných na virtuálním počítači a závislostech externích procesů. Tento agent je vyžadován pro [Service map](../insights/service-map.md) a [Azure monitor pro virtuální počítače](../insights/vminsights-overview.md)funkce map. Agent závislostí vyžaduje agenta Log Analytics a zapisuje data do pracovního prostoru Log Analytics v Azure Monitor.


## <a name="using-multiple-agents"></a>Použití více agentů
Můžete mít specifické požadavky na použití diagnostického rozšíření Azure nebo agenta Log Analytics pro konkrétní virtuální počítač. Můžete například chtít použít výstrahy metriky, které vyžadují diagnostické rozšíření Azure. Můžete ale také chtít použít funkci map Azure Monitor pro virtuální počítače, která vyžaduje agenta závislostí a agenta Log Analytics. V takovém případě můžete použít více agentů a jedná se o běžný scénář pro zákazníky, kteří od nich vyžadují funkčnost.

### <a name="considerations"></a>Požadavky

- Agent závislostí vyžaduje, aby byl na stejném virtuálním počítači nainstalovaný agent Log Analytics.
- V případě virtuálních počítačů se systémem Linux musí být agent Log Analytics nainstalován před rozšířením diagnostiky Azure.


## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) pro kontrolu požadavků a podporovaných metod pro nasazení agenta do počítačů hostovaných v Azure, ve vašem datovém centru nebo v jiném cloudovém prostředí.

