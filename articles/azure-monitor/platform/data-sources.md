---
title: Zdroje dat ve službě Azure Monitor | Dokumentace Microsoftu
description: Popisuje data, která je k dispozici pro sledování stavu a výkonu vašich prostředků Azure a aplikace běžící na ně.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: bwren
ms.openlocfilehash: cb38a8a4c6e92dae1ed4d505c810027fb93e4a3c
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871421"
---
# <a name="sources-of-data-in-azure-monitor"></a>Zdroje dat ve službě Azure Monitor
Tento článek popisuje zdroje dat shromážděných službou Azure Monitor k monitorování stavu a výkonu svých prostředků a aplikace běžící na ně. Tyto prostředky může být v Azure, v jiném cloudu nebo lokálně.  Zobrazit [Data shromážděná službou Azure Monitor](data-collection.md) podrobnosti o tom, jak tato data uložená a jak ji zobrazit.

Sledování dat v Azure pochází z různých zdrojů, které mohou být uspořádány do vrstev, nejvyšší úrovně se vaše aplikace a operačních systémů a nižší úrovně, které jsou součástí platformy Azure. To je znázorněno v následujícím diagramu s každou vrstvou podrobně popsány v následujících částech.

![Úrovně dat monitorování](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Azure Tenant
Telemetrické údaje vztahující se k tenantovi Azure se shromažďují z celého tenanta služeb, jako je Azure Active Directory.

![Kolekce Azure klienta](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Protokoly auditování Azure Active Directory
[Generování sestav v Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) obsahuje historii přihlašovací aktivitu a revizní záznam změn provedených v rámci konkrétního tenanta. Tyto protokoly auditu se dají zapisovat do Log Analytics k analýze s ostatními daty protokolu.


## <a name="azure-platform"></a>Platforma Azure
Telemetrická data týkající se stavu a operace Azure samotné zahrnuje data o provozu a správy předplatného Azure. Zahrnuje službu health data uložená v protokolu aktivit Azure a protokoly auditu ze služby Azure Active Directory.

![Kolekce předplatného Azure](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../monitoring-and-diagnostics/monitoring-service-notifications.md) poskytuje informace o stavu služby Azure ve vašem předplatném, které využívají vaše aplikace a prostředky. Můžete vytvářet výstrahy, které oznamuje aktuální a očekávaná zásadní potíže, které můžou ovlivnit vaši aplikaci. Stav služby záznamy ukládají v [protokol aktivit Azure](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), takže můžete zobrazit v Průzkumníku protokolu aktivity a zkopírujte je do Log Analytics.

### <a name="azure-activity-log"></a>Protokol aktivit Azure
[Protokolu aktivit Azure](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) obsahuje záznamy stavu služby spolu s záznamů na změny konfigurace provedené u vašich prostředků Azure. Protokol aktivit je k dispozici pro všechny prostředky Azure a představuje jejich _externí_ zobrazení. Konkrétní typy záznamů v protokolu aktivit, které jsou popsány v [schéma událostí protokolu aktivit Azure](../../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Můžete zobrazit na její stránce portálu Azure portal nebo zobrazení protokolů z více zdrojů v protokolu aktivit pro určitý prostředek [Průzkumníku protokol aktivity](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Je obzvláště užitečné ke kopírování položky protokolu do Log Analytics zkombinovat s dalšími daty monitorování. Můžete také odeslat do jiných umístění pomocí [Event Hubs](../../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).



## <a name="azure-services"></a>Služby Azure
Metriky a prostředků úrovně diagnostické protokoly poskytují informace o _interní_ operace prostředků Azure. Tyto jsou dostupné pro většinu služeb Azure služby a řešení pro správu poskytování dalších přehledů o konkrétní služby.

![Kolekce prostředků Azure](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Metriky
Většina služeb Azure vygeneruje [platformy metriky](data-collection.md#metrics) , které reflektují jejich výkon a operace. Konkrétní [metriky se liší pro jednotlivé typy prostředků](../../monitoring-and-diagnostics/monitoring-supported-metrics.md).  Jsou přístupné z Průzkumníka metrik a je možné zkopírovat do Log Analytics k analýze trendů a další.


### <a name="resource-diagnostic-logs"></a>Protokolů diagnostiky prostředků
Poskytuje informace o operace prováděné s prostředky Azure zdrojem pro úroveň protokolu aktivit [diagnostické protokoly](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) poskytují přehled o provozu vlastní prostředek.   Požadavky na konfiguraci a obsah těchto protokolů [se liší podle typu prostředku](../../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

Nelze přímo zobrazit diagnostické protokoly na webu Azure Portal, ale můžete [odeslání do úložiště Azure pro archivaci](../../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) a exportovat je do [centra událostí](../../event-hubs/event-hubs-about.md) pro přesměrování do dalších služeb nebo [do protokolu Analytics](../../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) pro analýzu. Některé prostředky dokáže zapisovat přímo do Log Analytics, zatímco ostatní teprve pak ji bude zapisovat do účtu úložiště [importovat do Log Analytics](../../azure-monitor/platform/azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Řešení monitorování
 [Řešení monitorování](../../azure-monitor/insights/solutions.md) shromažďování dat k poskytování dalších přehledů o fungování nějaká konkrétní služba nebo aplikace. Shromažďovat data do Log Analytics, kde mohou být analyzovány pomocí [dotazovací jazyk](../../azure-monitor/log-query/log-query-overview.md) nebo [zobrazení](../../azure-monitor/platform/view-designer.md) , které jsou typicky zahrnuty v řešení.

## <a name="guest-operating-system"></a>Hostovaný operační systém
Výpočetní prostředky v Azure, v ostatních cloudech a v místním mít hostovaný operační systém pro monitorování. Při instalaci jedné nebo více agentů může shromažďovat telemetrická data z hosta do stejné nástroje pro sledování jako samotné služby Azure.

![Kolekce prostředků Azure compute](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Diagnostické rozšíření Azure
Pomocí rozšíření Azure Diagnostics poskytuje základní úroveň monitorování tím, že shromažďování protokolů a výpočetní prostředky, data o výkonu z klientského operačního systému Azure.   

### <a name="log-analytics-agent"></a>Agenta log Analytics
Komplexní monitorování a správa virtuálních počítačů s Windows nebo Linuxem nebo fyzického počítače se dodává se agenta Log Analytics. Může být spuštěný virtuální počítač v Azure, jiného cloudu nebo místně a agent se připojí ke službě Log Analytics přímo nebo pomocí System Center Operations Manager a umožňuje shromažďovat data z [zdroje dat](../../azure-monitor/platform/agent-data-sources.md) , kterou jste konfigurace nebo z [řešení monitorování](../../azure-monitor/insights/solutions.md) , poskytování dalších přehledů o aplikace běžící na virtuálním počítači.

### <a name="dependency-agent"></a>Agent závislostí
[Řešení Service Map](../insights/service-map.md) a [monitorování Azure pro virtuální počítače](../../azure-monitor/insights/vminsights-overview.md) vyžaduje, aby Agent závislost na virtuální počítače s Windows a Linux. To se integruje s agenta Log Analytics shromažďuje zjištěná data o procesy spuštěné na virtuálním počítači a závislosti externího procesu. Ukládá tato data do Log Analytics a vizualizuje zjištěných vzájemně propojených součástí.  

Pokud chcete lépe pochopit rozdíly mezi agenty a která bude použita v závislosti na vašich požadavků na monitorování, přečtěte si téma [přehled agentů monitorování](agents-overview.md).

## <a name="applications"></a>Aplikace
Kromě telemetrická data, která vaše aplikace může zapisovat do hostovaného operačního systému, podrobné application monitoring pro aplikace se provádí pomocí [Application Insights](https://docs.microsoft.com/azure/application-insights/). Application Insights může shromažďovat data z aplikací běžících na různých platformách. Aplikace může běžet v Azure, jiného cloudu nebo místně.

![Shromažďování dat aplikací](media/data-sources/application-collection.png)


### <a name="application-data"></a>Data aplikací
Když povolíte Application Insights pro aplikace nainstalováním Instrumentační balíček, shromažďuje metriky a protokoly týkajících se výkonu a operací aplikace. To zahrnuje podrobné informace o zobrazení stránek, žádosti o aplikace a výjimky. Application Insights ukládá data, která shromažďuje metriky Azure a Log Analytics. Zahrnuje rozsáhlé nástroje pro analýzu těchto dat, ale můžete také analyzovat, jak s daty z jiných zdrojů pomocí nástrojů, jako je Průzkumník metrik a prohledávání protokolů.

Můžete také použít službu Application Insights do [vytvořit vlastní metrika](../../application-insights/app-insights-api-custom-events-metrics.md).  To vám umožňuje definovat vlastní logiku pro výpočet číselné hodnoty a pak tuto hodnotu s ostatními metrikami, které lze k němu přistupovat z Průzkumníku metrik a použít pro ukládání [automatického škálování](../../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) a upozornění na metriku.

### <a name="dependencies"></a>Závislosti
Pokud si chcete monitorovat různé logické operace aplikace, musíte [shromažďování telemetrických dat napříč více komponent](../../application-insights/app-insights-transaction-diagnostics.md). Application Insights podporuje [distribuované korelace telemetrie](../../application-insights/application-insights-correlation.md) který identifikuje závislosti mezi komponentami, abyste mohli analyzovat společně.

### <a name="availability-tests"></a>Testy dostupnosti
[Testy dostupnosti](../../application-insights/app-insights-monitor-web-app-availability.md) ve službě Application Insights umožňují otestovat dostupnost a odezvu vaší aplikace z různých míst na veřejném Internetu. Můžete provést jednoduché ping testu k ověření, že je aplikace aktivní nebo vytvoření webového testu, který simuluje uživatelský scénář pomocí sady Visual Studio.  Testy dostupnosti nevyžadují žádné instrumentace v aplikaci.

## <a name="custom-sources"></a>Vlastní zdroje
Kromě standardní úrovní aplikace budete muset další prostředky, které mají telemetrie, která není možné shromáždit s jinými zdroji dat monitorování. Pro tyto prostředky budete muset napsat tato data pomocí rozhraní API Azure Monitor.

![Kolekce vlastních dat](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>Rozhraní API kolekce dat
Azure Monitor může shromažďovat data protokolu z jakéhokoli klienta REST pomocí [rozhraní API kolekce dat](../../azure-monitor/platform/data-collector-api.md). To umožňuje vytvářet vlastní scénáře monitorování a rozšiřování monitorování na prostředky, které Nezveřejňují telemetrická data prostřednictvím dalších zdrojů.

## <a name="next-steps"></a>Další postup

- Další informace o [typy monitorování dat shromážděných službou Azure Monitor](data-collection.md) a jak zobrazit a analyzovat tato data.
