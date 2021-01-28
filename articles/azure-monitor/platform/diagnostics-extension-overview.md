---
title: Přehled rozšíření Azure Diagnostics
description: Použití diagnostiky Azure pro ladění, měření výkonu, monitorování, analýzy provozu v cloudových službách, virtuálních počítačích a Service Fabric
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 4d424a22a26119dcb3ef6a0e5c4f3dc0c13b1aa4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927582"
---
# <a name="azure-diagnostics-extension-overview"></a>Přehled rozšíření Azure Diagnostics
Azure Diagnostics rozšíření je [Agent v Azure monitor](agents-overview.md) , který shromažďuje data monitorování z hostovaného operačního systému výpočetních prostředků Azure, včetně virtuálních počítačů. Tento článek obsahuje přehled rozšíření Azure Diagnostics, včetně specifických funkcí, které podporuje, a možností instalace a konfigurace. 

> [!NOTE]
> Azure Diagnostics rozšíření je jedním z agentů, které jsou k dispozici ke shromažďování dat monitorování z hostovaného operačního systému výpočetních prostředků. Popis různých agentů a pokynů k výběru vhodných agentů pro vaše požadavky najdete v tématu [přehled Azure Monitorch agentů ](agents-overview.md) .

## <a name="primary-scenarios"></a>Primární scénáře
K primárním scénářům řešeným pomocí rozšíření pro diagnostiku patří:

- Shromážděte metriky hostů do metrik Azure Monitor.
- Odeslání protokolů hosta a metrik do služby Azure Storage pro archivaci.
- Posílání protokolů hostů a metrik do Azure Event hub, aby se odesílaly mimo Azure.


## <a name="comparison-to-log-analytics-agent"></a>Porovnání s agentem Log Analytics
Agenta Log Analytics v Azure Monitor lze také použít ke shromažďování dat monitorování z hostovaného operačního systému virtuálních počítačů. V závislosti na vašich požadavcích se můžete rozhodnout použít buď nebo. Podrobné porovnání agentů Azure Monitor najdete v tématu [přehled Azure Monitorch agentů](agents-overview.md) . 

Mezi hlavní rozdíly, které je potřeba vzít v úvahu, patří:

- Rozšíření Azure Diagnostics se dá použít jenom s virtuálními počítači Azure. Agent Log Analytics se dá použít s virtuálními počítači v Azure, jinými cloudy a místním prostředím.
- Rozšíření Azure Diagnostics odesílá data do Azure Storage, [Azure monitor metriky](data-platform-metrics.md) (pouze Windows) a Event Hubs. Agent Log Analytics shromažďuje data do [protokolů Azure monitor](data-platform-logs.md).
- Agent Log Analytics je vyžadován pro [řešení](../monitor-reference.md#insights-and-core-solutions), [Azure monitor pro virtuální počítače](../insights/vminsights-overview.md)a další služby, jako je například [Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Náklady
Pro diagnostické rozšíření Azure se neúčtují žádné náklady, ale pro ingestování dat vám můžou být účtovány poplatky. Podívejte se na [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/) pro cíl, kde shromažďujete data.

## <a name="data-collected"></a>Shromažďovaná data
V následujících tabulkách najdete seznam dat, která se můžou shromažďovat v diagnostickém rozšíření pro Windows a Linux.

### <a name="windows-diagnostics-extension-wad"></a>Rozšíření Windows Diagnostics (WAD)

| Zdroj dat | Popis |
| --- | --- |
| Protokoly událostí systému Windows   | Události z protokolu událostí systému Windows. |
| Čítače výkonu | Číselné hodnoty, které měří výkon různých aspektů operačního systému a zatížení. |
| Protokoly IIS             | Informace o použití pro weby služby IIS běžící v hostovaném operačním systému. |
| Protokoly aplikací     | Sleduje zprávy zapsané vaší aplikací. |
| Protokoly EventSource .NET |Psaní kódu pro události pomocí třídy [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) .NET |
| [Protokoly ETW založené na manifestech](/windows/desktop/etw/about-event-tracing) |Trasování událostí pro události systému Windows generované jakýmkoli procesem. |
| Výpisy stavu systému (protokoly)   | Informace o stavu procesu, pokud dojde k chybě aplikace. |
| Protokoly založené na souborech    | Protokoly vytvořené vaší aplikací nebo službou. |
| Diagnostické protokoly agenta | Informace o Azure Diagnostics sebe sama. |


### <a name="linux-diagnostics-extension-lad"></a>Diagnostické rozšíření pro Linux (LAD)

| Zdroj dat | Popis |
| --- | --- |
| Syslog | Události odeslané do systému protokolování událostí pro Linux.   |
| Čítače výkonu  | Číselné hodnoty, které měří výkon různých aspektů operačního systému a zatížení. |
| Soubory protokolu | Záznamy odesílané do protokolu založeného na souborech.  |

## <a name="data-destinations"></a>Cíle dat
Diagnostické rozšíření Azure pro systémy Windows i Linux vždy shromažďuje data do účtu Azure Storage. Přečtěte si téma [instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](diagnostics-extension-windows-install.md) a [použijte diagnostické rozšíření systému Linux k monitorování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md) pro seznam konkrétních tabulek a objektů blob, kde jsou tato data shromažďována.

Nakonfigurujte jednu nebo více *datových umyvadel* pro odesílání dat do dalších dalších cílů. V následujících částech najdete seznam umyvadel dostupných pro diagnostické rozšíření systému Windows a Linux.

### <a name="windows-diagnostics-extension-wad"></a>Rozšíření Windows Diagnostics (WAD)

| Cíl | Popis |
|:---|:---|
| Metriky Azure Monitoru | Shromažďovat údaje o výkonu pro Azure Monitor metriky. Viz [odeslání metriky hostovaného operačního systému do databáze metriky Azure monitor](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Event Hubs | K posílání dat mimo Azure použijte Azure Event Hubs. Přečtěte si téma [streamování dat Azure Diagnostics do Event Hubs](diagnostics-extension-stream-event-hubs.md) |
| Objekty blob Azure Storage | Zápis do dat do objektů BLOB v Azure Storage kromě tabulek. |
| Application Insights | Shromažďovat data z aplikací běžících ve vašem VIRTUÁLNÍm počítači a Application Insights je integrovat s ostatními monitorováním aplikací. Viz [odeslání diagnostických dat do Application Insights](diagnostics-extension-to-application-insights.md). |

Data WAD můžete také shromažďovat z úložiště do pracovního prostoru Log Analytics, abyste je mohli analyzovat pomocí protokolů Azure Monitor, i když se pro tuto funkci obvykle používá agent Log Analytics. Může odesílat data přímo do pracovního prostoru Log Analytics a podporuje řešení a přehledy, které poskytují další funkce.  Viz [shromáždění diagnostických protokolů Azure z Azure Storage](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Diagnostické rozšíření pro Linux (LAD)
LAD zapisuje data do tabulek v Azure Storage. Podporuje jímky v následující tabulce.

| Cíl | Popis |
|:---|:---|
| Event Hubs | K posílání dat mimo Azure použijte Azure Event Hubs. |
| Objekty blob Azure Storage | Zápis do dat do objektů BLOB v Azure Storage kromě tabulek. |
| Metriky Azure Monitoru | Kromě LAD nainstalujte agenta telegraf. Viz [shromáždění vlastních metrik pro virtuální počítač se systémem Linux pomocí agenta InfluxData telegraf](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Instalace a konfigurace
Diagnostické rozšíření je implementováno jako [rozšíření virtuálního počítače](../../virtual-machines/extensions/overview.md) v Azure, takže podporuje stejné možnosti instalace pomocí Správce prostředků šablon, PowerShellu a rozhraní příkazového řádku. Obecné informace o instalaci a údržbě rozšíření virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů pro Windows](../../virtual-machines/extensions/features-windows.md) a [rozšíření virtuálních počítačů pro Linux](../../virtual-machines/extensions/features-linux.md) .

Můžete taky nainstalovat a nakonfigurovat diagnostické rozšíření Windows a Linux v Azure Portal v části **nastavení diagnostiky** v nabídce **monitorování** virtuálního počítače.

Podrobnosti o instalaci a konfiguraci diagnostického rozšíření pro Windows a Linux najdete v následujících článcích.

- [Instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](diagnostics-extension-windows-install.md)
- [Použití diagnostického rozšíření Linuxu k monitorování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Další dokumentace

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Webové role a role pracovních procesů Azure Cloud Service (Classic)
- [Seznámení s monitorováním cloudové služby](../../cloud-services/cloud-services-how-to-monitor.md)
- [Povolení Azure Diagnostics v Azure Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights pro Azure Cloud Services](../app/cloudservices.md)<br>[Trasování toku Cloud Services aplikace pomocí Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitorování a diagnostika služeb v nastavení místních počítačů pro vývoj](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Další kroky


* Naučte se [používat čítače výkonu v Azure Diagnostics](../../cloud-services/diagnostics-performance-counters.md).
* Pokud máte potíže s diagnostikou spouštění nebo hledáním dat v tabulkách úložiště Azure, přečtěte si téma [řešení potíží Azure Diagnostics](diagnostics-extension-troubleshooting.md)
