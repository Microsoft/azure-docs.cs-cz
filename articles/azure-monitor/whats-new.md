---
title: Co je nového v dokumentaci k Azure Monitor
description: Důležité aktualizace Azure Monitor dokumentace se každý měsíc aktualizovaly.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 04/06/2020
ms.openlocfilehash: 8701acc6ac10dd9d31df99bb04dd030d156250e2
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871399"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Co je nového v dokumentaci k Azure Monitor?
Tento článek obsahuje seznam Azure Monitorch článků, které jsou buď nové, nebo byly významně aktualizované. Bude aktualizován první týden v měsíci, aby zahrnoval aktualizace článků z předchozího měsíce.

## <a name="april-2020"></a>Duben 2020

### <a name="general"></a>Obecné
- Azure Monitor oddílem přidaných uživatelem [spravovaný klíč](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys.md) na asynchronních operacích
- [Správa pracovních prostorů Log Analytics v](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access.md) oddílech s aktualizovanými vlastními protokoly Azure monitor.

### <a name="alerts"></a>Výstrahy
- [Pravidla akcí pro výstrahy Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules) – přidané video
- [Přehled upozorňování a monitorování oznámení v Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview.md) – přidané video

### <a name="application-insights"></a>Application Insights
- [Mapa aplikace v Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-map.md) – přidala se konfigurace názvů cloudových rolí pro agenta Java.
- [Reference k rozhraní API agenta Azure Application Insights .NET](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference.md) – konsolidovaná Reference k rozhraní API.
- [IP adresy, které používá Application Insights a Log Analytics](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses.md) -aktualizované IP adresy pro rozhraní API pro App Insights a Log Analytics, Webhooky skupin akcí, Azure USA státní správy.
- [Monitorování aplikací Java kdekoli](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config.md) – nový článek
- [Monitorování aplikací v jazyce Java v jakémkoli prostředí](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent.md) – nový článek
- [Monitorovat aplikace Java spuštěné v jakémkoli prostředí](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments.md) – nový článek
- [Monitorovat aplikace Java běžící na místním](https://docs.microsoft.com/azure/azure-monitor/app/java-on-premises.md) a novém článku
- [Odebrání Application Insights v aplikaci Visual Studio](https://docs.microsoft.com/azure/azure-monitor/app/remove-application-insights.md) – nový článek
- [Vzorkování telemetrie v Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/sampling) – oprava v ukázce s pevnou sazbou v Pythonu.

### <a name="containers"></a>Containers
- [Konfigurace Azure Red Hat OpenShift v4. x s Azure monitor for Containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-azure-redhat4-setup) – nový článek
- [Ruční oprava problémů s synchronizací ServiceNow](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-resync-servicenow) – nový článek
- [Postup zastavení monitorování clusteru Azure a Red Hat OpenShift v4](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-openshift-v4) – nový článek
- [Postup zastavení monitorování clusteru Azure Red Hat OpenShift V3](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-openshift-v3) – nový článek
- [Postup zastavení monitorování hybridního Kubernetes clusteru](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-hybrid) – nový článek

### <a name="insights"></a>Insights
- [Monitorování trezorů klíčů Azure pomocí Azure monitor pro trezory klíčů (Preview)](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview) – nový článek

### <a name="logs"></a>Protokoly
- [Omezení služby Azure monitor](https://docs.microsoft.com/azure/azure-monitor/service-limits) – přidaná uživatelská omezení uživatelských dotazů.
- [Správa využití a nákladů na protokoly Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage) – pro clustery se přidávají poplatky za protokoly. Přidaný dotaz Kusto, který umožňuje zákazníkům se staršími cenovými úrovněmi na základě počtu uzlů určit, jestli se mají přesunout na úroveň rezervace za GB nebo kapacitu.

### <a name="metrics"></a>Metriky
- [Pokročilé funkce](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) oddílu agregace služby Azure Průzkumník metrik – přidáno

### <a name="workbooks"></a>Workbooks
- [Azure monitor sešity a šablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-automate) – přidala se šablona Resource Manageru pro nasazení šablony sešitu.



## <a name="march-2020"></a>Březen 2020

### <a name="general"></a>Obecné
- [Přehled Azure monitor](overview.md) – přidání Azure monitor přehledu videa
- [Azure monitor konfiguraci klíče spravovaného zákazníkem](platform/customer-managed-keys.md) – obecné aktualizace.
- [Referenční informace o Azure monitor dat](/azure/azure-monitor/reference) – nový web

### <a name="alerts"></a>Výstrahy

- [Umožňuje vytvářet, zobrazovat a spravovat výstrahy protokolu aktivit v Azure monitor](platform/alerts-activity-log.md) – další vysvětlení správce prostředků šablony.
- [Pochopte, jak budou výstrahy metriky fungovat v Azure Monitor.](platform/alerts-metric-overview.md) – Aktualizováno pro státní podporu.
- [Řešení potíží s Azure monitor výstrahami a oznámeními](platform/alerts-troubleshoot.md) – nový článek

### <a name="application-insights"></a>Application Insights
- [Automatizace Azure Application Insights pomocí prostředí PowerShell](app/powershell.md) – přidané příklady ARMClient
- [Průběžný export telemetrie z Application Insights](app/export-telemetry.md) – přidejte tabulku s podrobnostmi o exportní struktuře.
- [Povolí Snapshot debugger pro aplikace .NET v](app/snapshot-debugger-appservice.md) příkladu přidaných Azure App Service šablony Správce prostředků.
- [Spravujte využití a náklady na Azure Application Insights](app/pricing.md) – přibyly informace o upozornění na data Cap.
- [Monitorujte aplikace v Pythonu pomocí Azure monitor (Preview)](app/opencensus-python.md) – přidané standardní metriky.
- [Podpora zdrojového mapování pro aplikace JavaScriptu – Azure Monitor Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/source-map-support) – nový článek


### <a name="containers"></a>Containers
- [Nejčastější dotazy Azure monitor](faq.md) – aktualizace pro Azure monitor pro kontejnery.
- [Konfigurace monitorování GPU pomocí Azure monitor for Containers](insights/container-insights-gpu-monitoring.md) – nový článek

### <a name="insights"></a>Insights
- [Řešení pro správu Office 365 v Azure](insights/solution-office-365.md) – aktualizované datum vyřazení

### <a name="logs"></a>Protokoly
- [Optimalizujte dotazy protokolu v Azure monitor](log-query/query-optimization.md) stav procesoru pro analýzu XML a JSON.
- [Odstranění a obnovení Azure Log Analytics pracovního prostoru](platform/delete-workspace.md) – přidání řešení potíží
- [Použijte protokoly Azure monitor s využitím Azure Logic Apps a automatizace](platform/logicapp-flow-connector.md) – Aktualizováno pro nové konektory Azure monitor.

### <a name="metrics"></a>Metriky
- [Nepoužívané metriky disku v Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/portal-disk-metrics-deprecation) – nový článek.
- [Kurz – vytvoření grafu metrik ve Azure monitor](learn/tutorial-metrics-explorer.md) – přidáno video

### <a name="platform-logs"></a>Protokoly platformy
- [Shromažďování a analýza protokolu aktivit Azure v Azure monitor](platform/activity-log-collect.md) přepsání pro lepší vysvětlení shromažďování protokolu aktivit s nastavením diagnostiky.

### <a name="virtual-machines"></a>Virtuální počítače
- [Monitorujte virtuální počítače Azure pomocí Azure monitor](insights/monitor-vm-azure.md) – nový článek.
- [Rychlý Start: monitorování virtuálních počítačů Azure pomocí Azure monitor](learn/quick-monitor-azure-vm.md) -aktualizovaných k přidání Azure monitor pro virtuální počítače
- [Výstrahy z Azure monitor pro virtuální počítače](insights/vminsights-alerts.md) – nový článek
- [Povolit Azure monitor pro virtuální počítače přehled](insights/vminsights-enable-overview.md) – aktualizované odkazy na stažení agenta.


Obecné aktualizace pro obecnou dostupnost Azure Monitor pro virtuální počítače
- [Co je Azure Monitor pro virtuální počítače?](insights/vminsights-overview.md)
- [Nejčastější dotazy k Azure Monitor pro virtuální počítače (GA)](insights/vminsights-ga-release-faq.md) 
- [Povolení Azure Monitor pro virtuální počítače pomocí Azure Policy](insights/vminsights-enable-at-scale-policy.md) 
- [Jak vynést výkon grafu pomocí Azure Monitor pro virtuální počítače](insights/vminsights-performance.md)
- [Dotazování protokolů z Azure Monitor pro virtuální počítače](insights/vminsights-log-search.md)
- [Zobrazení závislostí aplikace pomocí Azure Monitor pro virtuální počítače](insights/vminsights-maps.md) 


### <a name="visualizations"></a>Vizualizace
- [Vizualizace dat z Azure monitor](visualizations.md) -aktualizováno na poznámku plánované vyřazení návrháře zobrazení.





## <a name="february-2020"></a>Únor 2020

### <a name="agents"></a>Agenti
Více aktualizací jako součást přepisu obsahu diagnostického rozšíření.

- [Přehled agentů monitorování Azure](platform/agents-overview.md) – restrukturované tabulky pro lepší objasnění jedinečných funkcí každého agenta.
- [Přehled rozšíření Azure Diagnostics](platform/diagnostics-extension-overview.md) – dokončení přepisu
- [Použijte úložiště objektů BLOB pro službu IIS a úložiště tabulek pro události v Azure monitor](platform/diagnostics-extension-logs.md) – obecné přepsání pro aktualizace a přehlednost.
- [Nainstalujte a nakonfigurujte rozšíření Windows Azure Diagnostics (WAD)](platform/diagnostics-extension-windows-install.md) – nový článek. 
- [Schéma rozšíření Windows Diagnostics](platform/diagnostics-extension-schema-windows.md) – změna uspořádání.
- [Posílání dat z rozšíření Windows Azure Diagnostics do Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md) – kompletně přepsáno a aktualizováno.
- [Ukládání a zobrazení diagnostických dat v Azure Storage](/azure/cloud-services/diagnostics-extension-to-storage) – kompletně přepsáno a aktualizováno.
- [Log Analytics rozšíření pro virtuální počítače pro Windows](../virtual-machines/extensions/oms-windows.md) – lépe upřesňuje vztahy s agentem Log Analytics.
- [Azure monitor rozšíření pro virtuální počítače pro Linux](../virtual-machines/extensions/oms-linux.md) – lépe upřesňuje vztahy s agentem Log Analytics.




### <a name="application-insights"></a>Application Insights
- [Připojovací řetězce v Azure Application Insights](app/sdk-connection-string.md) – nový článek.

### <a name="insights-and-solutions"></a>Přehledy a řešení

#### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
- [Integrace Azure Active Directory se službou Azure Kubernetes](../aks/azure-ad-integration.md) – přidání poznámky k vytvoření klientské aplikace pro podporu clusteru s POVOLENým RBAC pro podporu Azure monitor pro kontejnery.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
- [Nejčastější dotazy Azure monitor pro virtuální počítače (GA)](insights/vminsights-ga-release-faq.md) – Změna způsobu ukládání dat o výkonu.

#### <a name="office-365"></a>Office 365
- [Řešení pro správu Office 365 v Azure](insights/solution-office-365.md) – aktualizované datum vyřazení


### <a name="logs"></a>Protokoly
- [Optimalizujte dotazy protokolu v Azure monitor](log-query/query-optimization.md) – nový článek.
- [Spravujte využití a náklady na protokoly Azure monitor](platform/manage-cost-storage.md) – vylepšené Ukázkové dotazy vám pomůžou pochopit vaše využití.

### <a name="metrics"></a>Metriky
- [Azure monitor metriky platformy exportovatelné prostřednictvím nastavení diagnostiky](platform/metrics-supported-export-diagnostic-settings.md) – Přidání oddílu při změně chování pro hodnoty null a nulové hodnoty.


### <a name="visualizations"></a>Vizualizace
Několik nových článků pro Průvodce převodem návrháře zobrazení na sešity.

- [Průvodce přechodem návrháře Azure monitor do sešitů](platform/view-designer-conversion-overview.md) – nový článek.
- [Možnosti převodu Azure monitor návrháře zobrazení na sešity](platform/view-designer-conversion-options.md) – nový článek
- [Azure monitor návrháře zobrazení v sešitech převod dlaždic](platform/view-designer-conversion-tiles.md) – nový článek
- [Souhrn převodu a přístup k novému článku pro návrháře zobrazení Azure monitor na sešity](platform/view-designer-conversion-access.md)
- [Azure monitor návrháře zobrazení na sešity převod běžných úkolů](platform/view-designer-conversion-tasks.md) – nový článek
- [Příklady převodu Azure monitor návrháře zobrazení na sešity](platform/view-designer-conversion-examples.md) – nový článek.




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
- [Konfigurace Azure monitor pro shromažďování dat agenta kontejnerů](insights/container-insights-agent-config.md) – Přidání podrobností pro upgrade agenta na Azure Red Hat OpenShift a přidání dalších informací k odlišení metod pro upgrade agenta.
- [Vytvořte výstrahy výkonu pro Azure monitor pro kontejnery](insights/container-insights-alerts.md) – revidované informace a aktualizované kroky pro vytvoření výstrahy pro data o výkonu uložené v pracovním prostoru pomocí výstrah kontextu v pracovním prostoru.
- [Monitorování Kubernetes s využitím Azure monitor for Containers](insights/container-insights-analyze.md) – aktualizace článku s přehledem a článku o analýze týkající se podpory clusterů Windows Kubernetes.
- [Konfigurace clusterů Azure Red Hat OpenShift s využitím Azure monitor for Containers](insights/container-insights-azure-redhat-setup.md) – Přidání podrobností pro upgrade agenta na Azure Red Hat OpenShift a přidání dalších informací k odlišení metod pro upgrade agenta.
- [Nakonfigurujte hybridní clustery Kubernetes s Azure monitor for Containers](insights/container-insights-hybrid-setup.md) – aktualizováno tak, aby odrážely přidanou podporu pro zabezpečený port: 10250 s cAdvisor Kubelet.
- [Jak spravovat Azure monitor for Containers agent](insights/container-insights-manage-agent.md) – aktualizované podrobnosti týkající se chování a konfigurace likvidace metriky pomocí Azure Red Hat OpenShift ve srovnání s jinými typy clusterů Kubernetes.
- [Konfigurace Azure monitor pro kontejnery Prometheus integraci](insights/container-insights-prometheus-integration.md) – aktualizované podrobnosti související s chováním a konfigurací vyřazení metriky s využitím Azure Red Hat OpenShift v porovnání s jinými typy clusterů Kubernetes.
- [Jak aktualizovat Azure monitor pro kontejnery pro metriky](insights/container-insights-update-metrics.md) – aktualizované podrobnosti týkající se chování a konfigurace vyřazení metriky pomocí Azure Red Hat OpenShift ve srovnání s jinými typy clusterů Kubernetes.


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
- [Application Insights pro aplikace pracovní služby Worker (jiné aplikace než HTTP)](app/worker-service.md) – Přidali jsme výchozí úroveň protokolování do kódu jazyka C#. Referenční verze balíčku se aktualizovala.
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