---
title: Přehled rozšíření Diagnostika Azure
description: Použití diagnostiky Azure pro ladění, měření výkonu, monitorování, analýzu provozu v cloudových službách, virtuálních počítačích a infrastruktuře služeb
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672374"
---
# <a name="azure-diagnostics-extension-overview"></a>Přehled rozšíření Diagnostika Azure
Rozšíření Azure Diagnostics je [agent ve službě Azure Monitor,](agents-overview.md) který shromažďuje data monitorování z hostovaného operačního systému výpočetních prostředků Azure včetně virtuálních počítačů. Tento článek obsahuje přehled rozšíření Diagnostika Azure, včetně konkrétních funkcí, které podporuje a možnosti pro instalaci a konfiguraci. 

> [!NOTE]
> Rozšíření Azure Diagnostics je jedním z agentů, kteří jsou k dispozici pro shromažďování dat monitorování z hostovaného operačního systému výpočetních prostředků. Viz [Přehled agentů Azure Monitoru](agents-overview.md) pro popis různých agentů a pokyny k výběru příslušných agentů pro vaše požadavky.

## <a name="comparison-to-log-analytics-agent"></a>Porovnání s agentem Analýzy protokolů
Agent log analytics v Azure Monitoru lze také ke shromažďování dat monitorování z hostovaného operačního systému virtuálních počítačů. Můžete se rozhodnout použít jeden nebo oba v závislosti na vašich požadavcích. Podrobný přehled [agentů Azure Monitoru](agents-overview.md) najdete v článku Podrobný porovnání agentů Azure Monitoru. 

Hlavní rozdíly, které je třeba zvážit, jsou:

- Rozšíření Diagnostika Azure lze použít pouze s virtuálními počítači Azure. Agent log analytics lze použít s virtuálními počítači v Azure, jiných cloudů a místní.
- Rozšíření Diagnostika Azure odesílá data do Azure Storage, [metriky Azure Monitor](data-platform-metrics.md) (jenom windows) a centra událostí. Agent Log Analytics shromažďuje data do [protokolů monitorování Azure](data-platform-logs.md).
- Agent Log Analytics je vyžadován pro [řešení](../monitor-reference.md#insights-and-core-solutions), [Azure Monitor pro virtuální počítače](../insights/vminsights-overview.md)a další služby, jako je Azure Security [Center](/azure/security-center/).

## <a name="costs"></a>Náklady
Neexistuje žádné náklady na rozšíření Diagnostika Azure, ale může vám vzniknout poplatky za pomohou za pomohou za pozůstalá data. Zkontrolujte [ceny Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) u cíle, kde shromažďujete data.

## <a name="data-collected"></a>Shromažďovaná data
V následujících tabulkách jsou uvedena data, která mohou být shromažďována v rozšíření diagnostiky systému Windows a Linux.

### <a name="windows-diagnostics-extension-wad"></a>Rozšíření diagnostiky systému Windows (WAD)

| Zdroj dat | Popis |
| --- | --- |
| Protokoly událostí systému Windows   | Události z protokolu událostí systému Windows. |
| Čítače výkonu | Číselné hodnoty měření výkonu různých aspektů operačního systému a úloh. |
| Protokoly IIS             | Informace o použití webů služby IIS spuštěných v hostovaném operačním systému. |
| Protokoly aplikací     | Trasování zpráv napsaných vaší aplikací. |
| Protokoly zdroje událostí .NET |Události zápisu kódu pomocí třídy .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| [Protokoly ETW založené na manifestu](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Trasování událostí pro události systému Windows generované libovolným procesem. |
| Výpisy stavu (protokoly)   | Informace o stavu procesu, pokud dojde k chybě aplikace. |
| Protokoly založené na souborech    | Protokoly vytvořené vaší aplikací nebo službou. |
| Diagnostické protokoly agentů | Informace o samotné diagnostice Azure. |


### <a name="linux-diagnostics-extension-lad"></a>Rozšíření diagnostiky Linuxu (LAD)

| Zdroj dat | Popis |
| --- | --- |
| Syslog | Události odeslané do systému protokolování událostí Linuxu.   |
| Čítače výkonu  | Číselné hodnoty měření výkonu různých aspektů operačního systému a úloh. |
| Soubory protokolu | Položky odeslané do protokolu založeného na souboru.  |

## <a name="data-destinations"></a>Cíle dat
Rozšíření Azure Diagnostic pro Windows i Linux vždy shromažďuje data do účtu Azure Storage. V [tématu Instalace a konfigurace rozšíření diagnostiky Windows Azure (WAD)](diagnostics-extension-windows-install.md) a [použití linuxového diagnostického rozšíření ke sledování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md) pro seznam konkrétních tabulek a objektů BLOB, kde se tato data shromažďují.

Nakonfigurujte jeden nebo více *jímek dat* pro odesílání dat do jiných dalších cílů. V následujících částech jsou uvedeny jímky, které jsou k dispozici pro rozšíření diagnostiky systému Windows a Linux.

### <a name="windows-diagnostics-extension-wad"></a>Rozšíření diagnostiky systému Windows (WAD)

| Cíl | Popis |
|:---|:---|
| Metriky azure monitoru | Shromažďujte údaje o výkonu do metrik Azure Monitoru. Viz [Odesílání metrik hostovaného operačního systému do databáze metrik Azure Monitor](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Event Hubs | Azure Event Hubs slouží k odesílání dat mimo Azure. Viz [Streamování dat diagnostiky Azure do centra událostí](diagnostics-extension-stream-event-hubs.md) |
| Objekty BLOB služby Azure Storage | Kromě tabulek zapisujete do objektů BLOB do objektů BLOB ve službě Azure Storage. |
| Application Insights | Shromažďujte data z aplikací spuštěné ve vašem virtuálním počítači do Application Insights a integrujte je s jiným monitorováním aplikací. Viz [Odesílání diagnostických dat do application insights](diagnostics-extension-to-application-insights.md). |

Můžete také shromažďovat data WAD z úložiště do pracovního prostoru Log Analytics analyzovat pomocí protokolů Azure Monitor, i když agent Log Analytics se obvykle používá pro tuto funkci. Může odesílat data přímo do pracovního prostoru Log Analytics a podporuje řešení a přehledy, které poskytují další funkce.  Viz [Shromažďování diagnostických protokolů Azure z Azure Storage](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Rozšíření diagnostiky Linuxu (LAD)
LAD zapisuje data do tabulek ve službě Azure Storage. Podporuje jímky v následující tabulce.

| Cíl | Popis |
|:---|:---|
| Event Hubs | Azure Event Hubs slouží k odesílání dat mimo Azure. |
| Objekty BLOB služby Azure Storage | Kromě tabulek zapisujete do objektů BLOB do objektů BLOB ve službě Azure Storage. |
| Metriky azure monitoru | Nainstalujte agent Telegraf kromě LAD. Viz [Shromáždit vlastní metriky pro virtuální počítač s Linuxem s agentem InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Instalace a konfigurace
Rozšíření Diagnostika se implementuje jako [rozšíření virtuálního počítače](../../virtual-machines/extensions/overview.md) v Azure, takže podporuje stejné možnosti instalace pomocí šablon Správce prostředků, PowerShellu a CLI. Viz [Rozšíření virtuálních strojů a funkce pro Rozšíření pro Windows](../../virtual-machines/extensions/features-windows.md) a virtuální počítače a funkce pro [Linux](../../virtual-machines/extensions/features-linux.md) obecné podrobnosti o instalaci a údržbě rozšíření virtuálních strojů.

Můžete také nainstalovat a nakonfigurovat diagnostické rozšíření Windows i Linux na webu Azure Portal v části **Nastavení diagnostiky** v části **Monitorování** v nabídce virtuálního počítače.

Podrobnosti o instalaci a konfiguraci rozšíření diagnostiky pro Windows a Linux naleznete v následujících článcích.

- [Instalace a konfigurace rozšíření diagnostiky Windows Azure (WAD)](diagnostics-extension-windows-install.md)
- [Použití diagnostického rozšíření Linuxu k monitorování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Další dokumentace

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Webové a pracovní role Azure Cloud Service (klasické)
- [Úvod do monitorování cloudových služeb](../../cloud-services/cloud-services-how-to-monitor.md)
- [Povolení diagnostiky Azure v Cloudových službách Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Přehledy aplikací pro cloudové služby Azure](../app/cloudservices.md)<br>[Sledování toku aplikace cloudových služeb pomocí Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitorování a diagnostika služeb v nastavení místních počítačů pro vývoj](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Další kroky


* Naučte se [používat čítače výkonu v Azure Diagnostics](../../cloud-services/diagnostics-performance-counters.md).
* Pokud máte potíže se spuštěním diagnostiky nebo hledáním dat v tabulkách úložiště Azure, přečtěte si informace [o tom, jak se potíže s azure diagnostikou](diagnostics-extension-troubleshooting.md)

