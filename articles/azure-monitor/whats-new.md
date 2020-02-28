---
title: Co je nového v dokumentaci k Azure Monitor
description: Důležité aktualizace Azure Monitor dokumentace se každý měsíc aktualizovaly.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/05/2020
ms.openlocfilehash: ecee13850e735f827a5465e0f49039f514afe233
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657737"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Co je nového v dokumentaci k Azure Monitor?
Tento článek obsahuje seznam Azure Monitorch článků, které jsou buď nové, nebo byly významně aktualizované. Bude aktualizován první týden v měsíci, aby zahrnoval aktualizace článků z předchozího měsíce.

## <a name="january-2020"></a>Leden 2020

### <a name="general"></a>Obecné
- [Co je monitorované pomocí Azure Monitor?](monitor-reference.md) – Nový článek.

### <a name="agents"></a>Agenti
- [Shromažďovat data protokolu v](platform/log-analytics-agent.md) tabulce požadavků brány firewall pro službu Azure Log Analytics agent – aktualizované


### <a name="alerts"></a>Výstrahy
- [Vytváření a Správa skupin akcí ve Azure Portal](platform/action-groups.md) – nastavení odebrané pro funkce v2, které už nejsou potřeba.
- [Vytvořte upozornění na metriku správce prostředků s](platform/alerts-metric-create-templates.md) příkladem přidaným šablonou pro parametr *ignoreDataBefore* .  Přidali jsme omezení pro pravidla s více kritérii.
- [Použití Log Analytics výstrah REST API](platform/api-alerts.md) – ukázka formátu JSON byla opravena.


### <a name="application-insights"></a>Application Insights
- [IP adresy, které používá Application Insights a Log Analytics](app/ip-addresses.md) – aktualizuje část test dostupnosti s postupem, jak přidat pravidlo příchozího portu pro povolení provozu pomocí skupin zabezpečení sítě Azure.
- [Řešení potíží se službou Azure Application Insights Profiler](app/profiler-troubleshooting.md) – aktualizace obecného řešení potíží
- [Vzorkování telemetrie v Azure Application Insights](app/sampling.md) – aktualizováno a restrukturalizace, aby se zlepšila čitelnost na základě zpětné vazby od zákazníků.


### <a name="data-security"></a>Zabezpečení dat
- [Azure monitor konfiguraci klíče spravovaného zákazníkem](platform/customer-managed-keys.md) – nový článek.

### <a name="insights-and-solutions"></a>Přehledy a řešení

#### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
- [Konfigurace Azure monitor pro shromažďování dat agenta kontejnerů](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config) – Přidání podrobností pro upgrade agenta na Azure Red Hat OpenShift a přidání dalších informací k odlišení metod pro upgrade agenta.
- [Vytvořte výstrahy výkonu pro Azure monitor pro kontejnery](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-alerts) – revidované informace a aktualizované kroky pro vytvoření výstrahy pro data o výkonu uložené v pracovním prostoru pomocí výstrah kontextu v pracovním prostoru.
- [Monitorování Kubernetes s využitím Azure monitor for Containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze) – aktualizace článku s přehledem a článku o analýze týkající se podpory clusterů Windows Kubernetes.
- [Konfigurace clusterů Azure Red Hat OpenShift s využitím Azure monitor for Containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-azure-redhat-setup) – Přidání podrobností pro upgrade agenta na Azure Red Hat OpenShift a přidání dalších informací k odlišení metod pro upgrade agenta.
- [Nakonfigurujte hybridní clustery Kubernetes s Azure monitor for Containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-hybrid-setup) – aktualizováno tak, aby odrážely přidanou podporu pro zabezpečený port: 10250 s cAdvisor Kubelet.
- [Jak spravovat Azure monitor for Containers agent](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-manage-agent) – aktualizované podrobnosti týkající se chování a konfigurace likvidace metriky pomocí Azure Red Hat OpenShift ve srovnání s jinými typy clusterů Kubernetes.
- [Konfigurace Azure monitor pro kontejnery Prometheus integraci](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration) – aktualizované podrobnosti související s chováním a konfigurací vyřazení metriky s využitím Azure Red Hat OpenShift v porovnání s jinými typy clusterů Kubernetes.
- [Jak aktualizovat Azure monitor pro kontejnery pro metriky](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-update-metrics) – aktualizované podrobnosti týkající se chování a konfigurace vyřazení metriky pomocí Azure Red Hat OpenShift ve srovnání s jinými typy clusterů Kubernetes.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
- [Nejčastější dotazy k Azure monitor pro virtuální počítače (GA)](insights/vminsights-ga-release-faq.md) – přidané informace o tom, jak upgradovat pracovní prostor a agenty na novou verzi.

#### <a name="office-365"></a>Office 365
- [Řešení pro správu office 365 v Azure](insights/solution-office-365.md) – podrobnosti a nejčastější dotazy k migraci na řešení Office 365 v Azure Sentinel. Oddíl připojování se odebral.



### <a name="logs"></a>Protokoly
- [Správa pracovních prostorů Log Analytics v Azure monitor](platform/manage-access.md) – aktualizace akcí.
- [Spravujte využití a náklady na protokoly Azure monitor](platform/manage-cost-storage.md) – Přidali jsme do oddílu cenového modelu objasnění při výpočtu objemu dat.
- [Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat šablonu Log Analytics pracovní prostor](platform/template-workspace-configuration.md) – aktualizovaná šablona s novými cenovými úrovněmi.


### <a name="platform-logs"></a>Protokoly platformy
- [Shromažďování protokolů aktivit Azure s nastavením diagnostiky – Azure monitor](platform/diagnostic-settings-legacy.md) – Další informace o změněných vlastnostech.
- [Exportujte protokol aktivit Azure](platform/activity-log-export.md) – Aktualizováno pro změny uživatelského rozhraní. 





## <a name="december-2019"></a>Prosinec 2019

### <a name="agents"></a>Agenti
- [Připojte počítače se systémem Linux k Azure monitor](platform/agent-linux.md) – nový článek.

### <a name="alerts"></a>Výstrahy
- [Vytvoření upozornění na metriku s](platform/alerts-metric-create-templates.md) příkladem pro vlastní metriku správce prostředků přidány šablony
- [Vytváření výstrah s dynamickými mezními hodnotami v sekci Azure monitor](platform/alerts-dynamic-thresholds.md) -added při interpretaci dynamických mezních grafů.
- [Přehled upozorňování a monitorování oznámení v Azure](platform/alerts-overview.md) – aktualizovaný dotaz na diagram prostředků
- [Podporované prostředky pro výstrahy metriky v Azure monitor](platform/alerts-metric-near-real-time.md) – podporuje se aktualizace metrik a dimenzí.
- [Přepne z rozhraní API upozornění starší verze Log Analytics do nového rozhraní API Azure Alerts](platform/alerts-log-api-switch.md) – přidání poznámky k názvu změněné výstrahy.
- [Pochopte, jak budou výstrahy metriky fungovat v Azure Monitor.](platform/alerts-metric-overview.md) – Byly přidány podporované typy prostředků pro monitorování ve velkém měřítku.

### <a name="application-insights"></a>Application Insights
- [Application Insights pro aplikace pracovní služby Worker (jiné aplikace než HTTP)](app/worker-service.md) – přidala se C# výchozí úroveň protokolování do kódu. Referenční verze balíčku se aktualizovala.
- [Odkaz na ApplicationInsights. config –](app/configuration-with-applicationinsights-config.md) ukázkový kód aktualizovaný službou Azure.
- [Automatizace Application Insights Azure pomocí PowerShellu](app/powershell.md) – aktualizace šablony Správce prostředků
- [Azure Monitor Application Insights balíčky NuGet](app/nuget.md) – aktualizované verze balíčků.
- [Vytvoření nového prostředku Application Insights Azure](app/create-new-resource.md) – poznámka přidána k globálně jedinečnému názvu
- [Diagnostika pomocí Live Metrics Stream – požadavek na](app/live-stream.md) verzi sady SDK ASP.NET Core Application Insights
- [Čítače událostí v Application Insights](app/eventcounters.md) -aktualizovaná kategorie a tabulka do customMetrics.
- [Prozkoumejte protokoly trasování Java v Azure Application Insights](app/java-trace-logs.md) – Přidání konfigurace pro mezní hodnotu protokolování agenta Java.
- [IP adresy, které používá Application Insights a Log Analytics](app/ip-addresses.md) -aktualizované IP adresy pro Live Metrics Stream.
- [Monitorování výkonu Azure App Services](app/azure-web-apps.md) – přidání podpory pro ASP.NET Core 3,0. 
- [Monitorování aplikací Pythonu pomocí Azure monitor (Preview)](app/opencensus-python.md) – přidalo se vyjasnění mapování schématu Pythonu OpenCensus do Azure. Monitorování schématu
- [Poznámky k verzi pro Azure Application Insights](app/release-notes.md) – přidané poznámky pro starší verze.
- [Kanály telemetrie v Azure Application Insights](app/telemetry-channels.md) – aktualizace doby trvání zahozených dat během delšího období ztráty připojení.
- [Vzorkování telemetrie v Azure Application Insights](app/sampling.md) – opravený fragment kódu pro vlastní TelemetryInitializer.
- [Řešení potíží s Application Insights ve webovém projektu Java](app/java-troubleshoot.md) – odstraněné příkazy týkající se nepodporované kolekce závislostí v JDK 9.

### <a name="insights-and-solutions"></a>Přehledy a řešení
- [Azure monitor pro kontejnery často kladené otázky](insights/container-insights-faq.md) – přidání otázky do polí obrázek a název.
- [Řešení Azure SQL Analytics v Azure monitor](insights/azure-sql.md) -aktualizované databázi čekají na podporu spravované instance.
- [Konfigurace Azure monitor pro shromažďování dat agenta kontejnerů](insights/container-insights-agent-config.md) – přidáno nastavení pro enrich_container_logs.
- [Nakonfigurujte hybridní clustery Kubernetes s Azure monitor pro kontejnery](insights/container-insights-hybrid-setup.md) – přidání části řešení potíží.
- [Monitorovat stav replikace služby Active Directory s aktualizací Azure monitor](insights/ad-replication-status.md) -.NET Framework.
- [Řešení Network Performance Monitor v Azure](insights/network-performance-monitor.md) – přidání podporovaných oblastí
- [Optimalizujte prostředí Active Directory pomocí Azure Monitor](insights/ad-assessment.md) .NET Framework aktualizace požadovaných součástí.
- [Optimalizujte SQL Server prostředí pomocí](insights/sql-assessment.md) aktualizovaného předpokladu Azure monitor .NET Framework.
- [Optimalizujte System Center Operations Manager prostředí pomocí](insights/scom-assessment.md) aktualizace požadovaných součástí Azure Log Analytics .NET Framework.
- [Podporovaná připojení ke službě IT Service Management Connector v Azure Log Analytics](platform/itsmc-connections.md) – přidání nového York k požadovanému ID klienta a tajnému kódu klienta.

### <a name="logs"></a>Protokoly
- [Odstraní a obnoví Azure Log Analytics Workspace](platform/delete-workspace.md) – přidala se metoda PowerShellu.
- [Návrh Azure monitor protokolů nasazení](platform/design-logs-deployment.md) – míra ingestování pro pracovní prostor se zvýšila.

### <a name="metrics"></a>Metriky
- [Azure monitor metriky platforem exportovatelné prostřednictvím nastavení diagnostiky](platform/metrics-supported-export-diagnostic-settings.md) – nový článek

### <a name="platform-logs"></a>Protokoly platformy
Několik článků bylo aktualizováno v rámci změny struktury obsahu pro protokoly platformy na základě nové funkce pro konfiguraci protokolu aktivit pomocí nastavení diagnostiky.

- [Archivace protokolů prostředků Azure do účtu úložiště](platform/resource-logs-collect-storage.md)
- [Schéma událostí protokolu aktivit Azure](platform/activity-log-schema.md)
- [Omezení služby Azure Monitor](service-limits.md)
- [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics](platform/activity-log-collect.md)
- [Shromažďování protokolů aktivit Azure s nastavením diagnostiky (Preview) – Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Shromažďování protokolů aktivit Azure do pracovního prostoru Log Analytics napříč klienty Azure](platform/activity-log-collect-tenants.md)
- [Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics](platform/resource-logs-collect-workspace.md)
- [Vytvoření nastavení diagnostiky v Azure pomocí šablony Správce prostředků](platform/diagnostic-settings-template.md)
- [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik v Azure](platform/diagnostic-settings.md)
- [Exportovat protokol aktivit Azure](platform/activity-log-export.md)
- [Přehled protokolů platformy Azure](platform/platform-logs-overview.md)
- [Streamování dat monitorování Azure do centra událostí](platform/stream-monitoring-data-event-hubs.md)
- [Streamování protokolů platformy Azure do centra událostí](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Rychlé starty a kurzy

- [Vytvoření grafu metrik v Azure monitor](learn/tutorial-metrics-explorer.md) – nový článek
- [Shromažďovat protokoly prostředků z prostředku Azure a analyzovat je pomocí Azure monitor](learn/tutorial-resource-logs.md) – nový článek.
- [Monitorujte prostředek Azure pomocí Azure monitor](learn/quick-monitor-azure-resource.md) – nový článek.
   
## <a name="next-steps"></a>Další kroky

- Pokud se chcete přispívat k dokumentaci Azure Monitor, přečtěte si [příručku pro přispěvatele docs](https://docs.microsoft.com/contribute/).