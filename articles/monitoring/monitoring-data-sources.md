---
title: Zdroje dat v Azure monitorování | Dokumentace Microsoftu
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
ms.date: 07/05/2018
ms.author: bwren
ms.openlocfilehash: 19a38473f1ce23b5a21ef5a29b3f3dc817b92dfd
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991177"
---
# <a name="sources-of-monitoring-data-in-azure"></a>Zdroje dat v Azure monitorování
Tento článek popisuje, k dispozici pro sledování stavu a výkonu vašich prostředků Azure a aplikace běžící na nich data.  Shromažďovat a analyzovat tato data pomocí nástrojů popsaných v [shromažďování údajů o monitorování dat v Azure](monitoring-data-collection.md)

Sledování dat v Azure pochází z různých zdrojů, které mohou být uspořádány do vrstev, na nejvyšší úrovni se vaše aplikace a na nejnižší úrovni se platformy Azure. To je znázorněno v následujícím diagramu s každou vrstvou podrobně popsány v následujících částech.

![Úrovně dat monitorování](media/monitoring-data-sources/monitoring-tiers.png)


## <a name="azure-platform"></a>Platforma Azure
Telemetrická data týkající se stavu a operace Azure samotné zahrnuje data o provozu a správy předplatného Azure nebo tenanta. V protokolu aktivit Azure a protokoly auditu ze služby Azure Active Directory obsahuje služby stavu datového úložiště.

![Azure kolekce](media/monitoring-data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../monitoring-and-diagnostics/monitoring-service-notifications.md) poskytuje informace o stavu služby Azure ve vašem předplatném, které využívají vaše aplikace a prostředky. Můžete vytvářet výstrahy, které oznamuje aktuální a očekávaná zásadní potíže, které můžou ovlivnit vaši aplikaci. Stav služby záznamy ukládají v [protokol aktivit Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), takže můžete zobrazit v Průzkumníku protokolu aktivity a zkopírujte je do Log Analytics.

### <a name="azure-activity-log"></a>Protokol aktivit v Azure
[Protokolu aktivit Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) obsahuje záznamy stavu služby spolu s záznamů na změny konfigurace provedené u vašich prostředků Azure. Protokol aktivit je k dispozici pro všechny prostředky Azure a představuje jejich _externí_ zobrazení. Konkrétní typy záznamů v protokolu aktivit, které jsou popsány v [schéma událostí protokolu aktivit Azure](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Můžete zobrazit na její stránce portálu Azure portal nebo zobrazení protokolů z více zdrojů v protokolu aktivit pro určitý prostředek [Průzkumníku protokol aktivity](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Je obzvláště užitečné ke kopírování položky protokolu do Log Analytics zkombinovat s dalšími daty monitorování. Můžete také odeslat do jiných umístění pomocí [Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).


### <a name="azure-active-directory-audit-logs"></a>Protokoly auditování Azure Active Directory
[Generování sestav v Azure Active Directory](../active-directory/active-directory-reporting-azure-portal.md) obsahuje historii přihlašovací aktivitu a revizní záznam změn provedených v rámci konkrétního tenanta. Nelze kombinovat aktuálně data auditu služby Azure Active Directory se s dalšími daty monitorování je dostupný prostřednictvím služby Azure Active Directory a [Azure Active Directory API pro vytváření sestav](../active-directory/active-directory-reporting-api-getting-started-azure-portal.md).


## <a name="azure-services"></a>Služby Azure
Metriky a prostředků úrovně diagnostické protokoly poskytují informace o _interní_ operace prostředků Azure. Tyto jsou dostupné pro většinu služeb Azure služby a řešení pro správu poskytování dalších přehledů o konkrétní služby.

![Kolekce prostředků Azure](media/monitoring-data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Metriky
Většina služeb Azure bude generovat metriky, které zahrnují jejich výkon a operace. Konkrétní [metriky se liší pro jednotlivé typy prostředků](../monitoring-and-diagnostics/monitoring-supported-metrics.md).  Jsou přístupné z Průzkumníka metrik a je možné zkopírovat do Log Analytics k analýze trendů a další.


### <a name="resource-diagnostic-logs"></a>Protokolů diagnostiky prostředků
Poskytuje informace o operace prováděné s prostředky Azure zdrojem pro úroveň protokolu aktivit [diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) poskytují přehled o provozu vlastní prostředek.   Požadavky na konfiguraci a obsah těchto protokolů [se liší podle typu prostředku](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

Nelze přímo zobrazit diagnostické protokoly na webu Azure Portal, ale můžete [odeslání do úložiště Azure pro archivaci](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) a exportovat je do [centra událostí](../event-hubs/event-hubs-what-is-event-hubs.md) pro přesměrování do dalších služeb nebo [do protokolu Analytics](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) pro analýzu. Některé prostředky dokáže zapisovat přímo do Log Analytics, zatímco ostatní teprve pak ji bude zapisovat do účtu úložiště [importovat do Log Analytics](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="management-solutions"></a>Řešení pro správu
 [Řešení pro správu](../monitoring/monitoring-solutions.md) shromažďování dat k poskytování dalších přehledů o operaci konkrétní službu. Shromažďovat data do Log Analytics, kde mohou být analyzovány pomocí [dotazovací jazyk](../log-analytics/log-analytics-log-search.md) nebo zobrazení, které jsou typicky zahrnuty v řešení.

## <a name="guest-operating-system"></a>Hostovaný operační systém
Kromě telemetrii generovanou všech služeb Azure mají výpočetní prostředky hostovaného operačního systému pro monitorování. Při instalaci jedné nebo více agentů může shromažďovat telemetrická data z hosta do stejné nástroje pro sledování jako samotné služby Azure.

![Kolekce prostředků Azure compute](media/monitoring-data-sources/compute-resource-collection.png)

### <a name="diagnostic-extension"></a>Diagnostické rozšíření
S [rozšíření Azure Diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md), je možné shromažďovat protokoly a výpočetní prostředky, data o výkonu z klientského operačního systému Azure. Metriky a protokoly shromážděná z klientů se ukládají v účtu služby Azure storage, který můžete [konfigurace Log Analytics pro import](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).  Průzkumník metrik rozumí čtení z účtu úložiště a bude obsahovat metriky klienta pomocí jiné shromažďovat metriky.


### <a name="log-analytics-agent"></a>Agenta log Analytics
Agenta Log Analytics můžete nainstalovat na Windows nebo Linux virtuálního počítače nebo fyzického počítače. Virtuální počítač může běžet v Azure, jiného cloudu nebo místně.  Agent se připojí ke službě Log Analytics buď přímo nebo prostřednictvím [připojené skupiny pro správu System Center Operations Manager](../log-analytics/log-analytics-om-agents.md) a umožňuje shromažďovat data z [zdroje dat](../log-analytics/log-analytics-data-sources.md) , které nakonfigurujete nebo z [řešení pro správu](../monitoring/monitoring-solutions.md) , poskytování dalších přehledů o aplikace běžící na agentovi.

### <a name="service-map"></a>Mapa služeb
[Řešení Service Map](../operations-management-suite/operations-management-suite-service-map.md) vyžaduje, aby Agent závislost na virtuální počítače s Windows a Linux. Tento postup funguje s Log Analytics, agent shromažďuje data o procesy spuštěné na virtuálním počítači a závislosti v externím procesu. Ukládá tato data do Log Analytics a zahrnuje konzolu, která vizuálně zobrazí data, která shromažďuje kromě jiných dat uložených ve službě Log Analytics.

## <a name="applications"></a>Aplikace
Kromě telemetrická data, která vaše aplikace může zapisovat do hostovaného operačního systému, podrobné application monitoring pro aplikace se provádí pomocí [Application Insights](https://docs.microsoft.com/azure/application-insights/). Application Insights může shromažďovat data z aplikací běžících na různých platformách. Aplikace může běžet v Azure, jiného cloudu nebo místně.

![Shromažďování dat aplikací](media/monitoring-data-sources/application-collection.png)


#### <a name="application-data"></a>Data aplikací
Když povolíte Application Insights pro aplikace nainstalováním Instrumentační balíček, shromažďuje metriky a protokoly týkajících se výkonu a operací aplikace. To zahrnuje podrobné informace o zobrazení stránek, žádosti o aplikace a výjimky. Application Insights ukládá data, která shromažďuje metriky Azure a Log Analytics. Zahrnuje rozsáhlé nástroje pro analýzu těchto dat, ale můžete také analyzovat, jak s daty z jiných zdrojů pomocí nástrojů, jako je Průzkumník metrik a prohledávání protokolů.

Můžete také použít službu Application Insights do [vytvořit vlastní metrika](../application-insights/app-insights-api-custom-events-metrics.md).  To vám umožňuje definovat vlastní logiku pro výpočet číselné hodnoty a pak tuto hodnotu s ostatními metrikami, které lze k němu přistupovat z Průzkumníku metrik a použít pro ukládání [automatického škálování](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) a upozornění na metriku.

#### <a name="dependencies"></a>Závislosti
Pokud si chcete monitorovat různé logické operace aplikace, musíte [shromažďování telemetrických dat napříč více komponent](../application-insights/app-insights-transaction-diagnostics.md). Application Insights podporuje [distribuované korelace telemetrie](../application-insights/application-insights-correlation.md) který identifikuje závislosti mezi komponentami, abyste mohli analyzovat společně.

#### <a name="availability-tests"></a>Testy dostupnosti
[Test dostupnosti](../application-insights/app-insights-monitor-web-app-availability.md) ve službě Application Insights umožňují otestovat dostupnost a odezvu vaší aplikace z různých míst na veřejném Internetu. Můžete provést jednoduché ping testu k ověření, že je aplikace aktivní nebo vytvoření webového testu, který simuluje uživatelský scénář pomocí sady Visual Studio.  Testy dostupnosti nevyžadují žádné instrumentace v aplikaci.

## <a name="next-steps"></a>Další postup

- Další informace o [typy data monitorování a nástroje Azure](monitoring-data-collection.md) umožňují shromažďovat a analyzovat je.
