---
title: Co je nového v dokumentaci k Azure Monitoru
description: Významné aktualizace dokumentace Azure Monitor aktualizovány každý měsíc.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: a2e6d2a459a6713aa9372496fc3a933c0a886ed9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802600"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Co je nového v dokumentaci k Azure Monitoru?
Tento článek obsahuje seznamy článků Azure Monitor, které jsou nové nebo byly výrazně aktualizovány. Bude aktualizován první týden každého měsíce, aby zahrnoval aktualizace článků z předchozího měsíce.

## <a name="march-2020"></a>Březen 2020

### <a name="general"></a>Obecné
- [Přehled Azure Monitoru](overview.md) – přidáno video s přehledem Azure Monitoru.
- [Konfigurace klíče spravované ho zákazníkem Azure](platform/customer-managed-keys.md) – obecné aktualizace.
- [Odkaz na data Azure Monitor](/azure/azure-monitor/reference) – nový web.

### <a name="alerts"></a>Výstrahy

- [Vytváření, zobrazení a správa výstrah protokolu aktivit v Azure Monitor](platform/alerts-activity-log.md) – další vysvětlení šablony Správce prostředků.
- [Zjistěte, jak fungují upozornění na metriky v Azure Monitoru.](platform/alerts-metric-overview.md) - Aktualizováno pro vládní podporu.
- [Poradce při potížích s výstrahami a oznámeními azure monitoru](platform/alerts-troubleshoot.md) – nový článek

### <a name="application-insights"></a>Application Insights
- [Automatizujte Přehledy aplikací Azure pomocí PowerShellu](app/powershell.md) – přidány příklady klientů ARMClient.
- [Nepřetržitý export telemetrie z Application Insights](app/export-telemetry.md) – Přidejte tabulku s podrobnostmi o struktuře exportu.
- [Povolit ladicí program snímků pro aplikace .NET ve službě Azure App Service](app/snapshot-debugger-appservice.md) – příklad šablony Správce prostředků.
- [Správa využití a nákladů pro Azure Application Insights](app/pricing.md) – přidány informace o upozornění limitu dat.
- [Monitorujte aplikace pythonu pomocí Azure Monitoru (preview)](app/opencensus-python.md) – přidány standardní metriky.
- [Podpora zdrojové mapy pro aplikace JavaScriptu – Azure Monitor Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/source-map-support) – Nový článek.


### <a name="containers"></a>Containers
- [Nejčastější dotazy k monitorování Azure](faq.md) – aktualizace pro Azure Monitor pro kontejnery.
- [Konfigurace monitorování GPU pomocí Azure Monitoru pro kontejnery](insights/container-insights-gpu-monitoring.md) – nový článek

### <a name="insights"></a>Insights
- [Řešení pro správu Office 365 v Azure](insights/solution-office-365.md) – aktualizované datum vyřazení.

### <a name="logs"></a>Protokoly
- [Optimalizace dotazů protokolu v Azure Monitor](log-query/query-optimization.md) – přidána podmínka procesoru pro analýzu XML a JSON.
- [Odstranění a obnovení pracovního prostoru Azure Log Analytics](platform/delete-workspace.md) – přidáno řešení potíží.
- [Pomocí protokolů monitorování Azure s aplikacemi Azure Logic Apps a Power Automate](platform/logicapp-flow-connector.md) – aktualizováno pro nový konektor Azure Monitoru.

### <a name="metrics"></a>Metriky
- [Disk metriky vyřazení na portálu Azure](https://docs.microsoft.com/azure/azure-monitor/platform/portal-disk-metrics-deprecation) – nový článek.
- [Kurz – vytvoření grafu metrik v Azure Monitor](learn/tutorial-metrics-explorer.md) – přidané video.

### <a name="platform-logs"></a>Protokoly platformy
- [Shromažďujte a analyzujte protokol aktivit Azure v Azure Monitoru](platform/activity-log-collect.md) – přepisujte, abyste lépe vysvětlili shromažďování protokolu aktivit pomocí diagnostických nastavení.

### <a name="virtual-machines"></a>Virtuální počítače
- [Monitorujte virtuální počítače Azure pomocí Azure Monitoru](insights/monitor-vm-azure.md) – nový článek.
- [Úvodní příručka: Monitorování virtuálních počítačů Azure pomocí Azure Monitoru](learn/quick-monitor-azure-vm.md) – aktualizováno tak, aby se přidal Azure Monitor pro virtuální počítače.
- [Výstrahy z Azure Monitor pro virtuální počítače](insights/vminsights-alerts.md) – nový článek
- [Povolit Azure Monitor pro virtuální počítače přehled](insights/vminsights-enable-overview.md) – aktualizované odkazy pro stažení agenta.


Obecné aktualizace obecné dostupnosti Azure Monitoru pro virtuální počítače
- [Co je Azure Monitor pro virtuální počítače?](insights/vminsights-overview.md)
- [Azure Monitor pro virtuální počítače (GA) nejčastější dotazy](insights/vminsights-ga-release-faq.md) 
- [Povolení Azure Monitoru pro virtuální počítače pomocí zásad Azure](insights/vminsights-enable-at-scale-policy.md) 
- [Jak grafovat výkon pomocí Azure Monitoru pro virtuální počítače](insights/vminsights-performance.md)
- [Jak dotazovat protokoly z Azure Monitor pro virtuální počítače](insights/vminsights-log-search.md)
- [Zobrazení závislostí aplikací pomocí Azure Monitoru pro virtuální počítače](insights/vminsights-maps.md) 


### <a name="visualizations"></a>Vizualizace
- [Vizualizace dat z Azure Monitor](visualizations.md) – aktualizována na vědomí plánované vyřazení Návrháře zobrazení.





## <a name="february-2020"></a>Únor 2020

### <a name="agents"></a>Agenti
Více aktualizací jako součást přepsání obsahu rozšíření diagnostiky.

- [Přehled agentů monitorování Azure](platform/agents-overview.md) – restrukturalizované tabulky pro lepší objasnění jedinečných funkcí každého agenta.
- [Přehled rozšíření Diagnostika Azure](platform/diagnostics-extension-overview.md) – dokončení přepsání.
- [Použijte úložiště objektů blob pro službu IIS a úložiště tabulek pro události v Azure Monitoru](platform/diagnostics-extension-logs.md) – obecné přepsání pro aktualizaci a přehlednost.
- [Instalace a konfigurace rozšíření diagnostiky Windows Azure (WAD)](platform/diagnostics-extension-windows-install.md) – nový článek. 
- [Schéma rozšíření diagnostiky systému Windows](platform/diagnostics-extension-schema-windows.md) – reorganizované.
- [Odesílejte data z rozšíření diagnostiky Windows Azure do Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md) – kompletně přepsáno a aktualizováno.
- [Ukládat a zobrazovat diagnostická data ve službě Azure Storage](/azure/cloud-services/diagnostics-extension-to-storage) – zcela přepsáno a aktualizováno.
- [Log Analytics rozšíření virtuálního počítače pro Windows](../virtual-machines/extensions/oms-windows.md) – lepší objasňuje vztah s agentem Log Analytics.
- [Rozšíření virtuálního počítače Azure Monitor pro Linux](../virtual-machines/extensions/oms-linux.md) – lepší objasňuje vztah s agentem Log Analytics.




### <a name="application-insights"></a>Application Insights
- [Připojovací řetězce v Azure Application Insights](app/sdk-connection-string.md) – nový článek.

### <a name="insights-and-solutions"></a>Přehledy a řešení

#### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
- [Integrujte Azure Active Directory se službou Azure Kubernetes](../aks/azure-ad-integration.md) – přidána poznámka pro vytvoření klientské aplikace pro podporu clusteru s podporou RBAC pro podporu Azure Monitoru pro kontejnery.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
- [Azure Monitor pro virtuální počítače (GA) nejčastější dotazy](insights/vminsights-ga-release-faq.md) – změna způsobu ukládání dat výkonu.

#### <a name="office-365"></a>Office 365
- [Řešení pro správu Office 365 v Azure](insights/solution-office-365.md) – aktualizované datum vyřazení.


### <a name="logs"></a>Protokoly
- [Optimalizace dotazů protokolu v Azure Monitor](log-query/query-optimization.md) – nový článek.
- [Správa využití a nákladů pro protokoly monitorování Azure](platform/manage-cost-storage.md) – vylepšené ukázkové dotazy, které vám pomohou pochopit vaše využití.

### <a name="metrics"></a>Metriky
- [Metriky platformy Azure Monitor exportovat pomocí nastavení diagnostiky](platform/metrics-supported-export-diagnostic-settings.md) – přidána část o změně chování pro nulové hodnoty a nulové hodnoty.


### <a name="visualizations"></a>Vizualizace
Několik nových článků pro návrháře zobrazení do průvodce převodem sešitů.

- [Návrhář zobrazení Azure Monitor u průvodce přechodem sešitů](platform/view-designer-conversion-overview.md) – nový článek.
- [Návrhář zobrazení Azure Monitor u možností převodu sešitů](platform/view-designer-conversion-options.md) – nový článek.
- [Návrhář zobrazení Azure Monitor u převodů dlaždic sešitů](platform/view-designer-conversion-tiles.md) – nový článek.
- [Návrhář zobrazení Azure Monitor u souhrnu převodů a přístupu k sešitům](platform/view-designer-conversion-access.md) – nový článek.
- [Návrhář zobrazení Azure Monitor u běžných úloh převodu sešitů](platform/view-designer-conversion-tasks.md) – nový článek.
- [Návrhář zobrazení Azure Monitor u příkladů převodu sešitů](platform/view-designer-conversion-examples.md) – Nový článek.




## <a name="january-2020"></a>Leden 2020

### <a name="general"></a>Obecné
- [Co monitor umonitoruje?](monitor-reference.md) - Nový článek.

### <a name="agents"></a>Agenti
- [Shromažďujte data protokolu pomocí agenta Azure Log Analytics](platform/log-analytics-agent.md) – tabulka požadavků na aktualizovanou síťovou bránu firewall.


### <a name="alerts"></a>Výstrahy
- [Vytvořit a spravovat skupiny akcí na webu Azure Portal](platform/action-groups.md) – nastavení odebrané pro funkce v2, které už nejsou potřeba.
- [Vytvořte upozornění na metriku se šablonou Správce prostředků](platform/alerts-metric-create-templates.md) – byl přidán příklad parametru *ignoreDataBefore.*  Přidána omezení týkající se pravidel s více kritérii.
- [Použití log analytics alert REST API](platform/api-alerts.md) - Příklad JSON opraven.


### <a name="application-insights"></a>Application Insights
- [IP adresy používané application insights a Log Analytics](app/ip-addresses.md) – Aktualizováno část test dostupnosti s tím, jak přidat pravidlo příchozíport povolit provoz pomocí skupiny zabezpečení sítě Azure.
- [Poradce při potížích s Profilerem Přehledů aplikací Azure](app/profiler-troubleshooting.md) – aktualizované obecné řešení potíží.
- [Vzorkování telemetrie v Azure Application Insights](app/sampling.md) – aktualizováno a restrukturalizováno tak, aby se zlepšila čitelnost na základě zpětné vazby od zákazníků.


### <a name="data-security"></a>Zabezpečení dat
- [Azure Monitor konfigurace klíče spravované zákazníkem](platform/customer-managed-keys.md) – nový článek.

### <a name="insights-and-solutions"></a>Přehledy a řešení

#### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
- [Konfigurace Azure Monitor pro sběr dat agenta kontejnerů](insights/container-insights-agent-config.md) – přidány podrobnosti pro upgrade agenta na Azure Red Hat OpenShift a přidány další informace k rozlišení metod pro upgrade agenta.
- [Vytvořte výstrahy výkonu pro Azure Monitor pro kontejnery](insights/container-insights-alerts.md) – revidované informace a aktualizované kroky pro vytvoření výstrahy na data o výkonu uložená v pracovním prostoru pomocí výstrah kontextu pracovního prostoru.
- [Monitorování Kubernetes pomocí Azure Monitor pro kontejnery](insights/container-insights-analyze.md) – Aktualizováno přehled článku a analyzovat článek týkající se podpory clusterů Windows Kubernetes.
- [Konfigurace clusterů Azure Red Hat OpenShift pomocí Azure Monitor pro kontejnery](insights/container-insights-azure-redhat-setup.md) – přidány podrobnosti pro upgrade agenta na Azure Red Hat OpenShift a přidány další informace k rozlišení metod pro upgrade agenta.
- [Konfigurace clusterů Hybridní Kubernetes pomocí Azure Monitoru pro kontejnery](insights/container-insights-hybrid-setup.md) – Aktualizováno tak, aby odráželo přidanou podporu zabezpečeného portu:10250 s cAdvisor kubelet.
- [Jak spravovat Azure Monitor pro kontejnery agent](insights/container-insights-manage-agent.md) – aktualizované podrobnosti týkající se chování a konfigurace metriky scraping s Azure Red Hat OpenShift ve srovnání s jinými typy clusterů Kubernetes.
- [Konfigurace Azure Monitor pro kontejnery Prometheus Integrace](insights/container-insights-prometheus-integration.md) – aktualizované podrobnosti týkající se chování a konfigurace metriky scraping s Azure Red Hat OpenShift ve srovnání s jinými typy clusterů Kubernetes.
- [Jak aktualizovat Azure Monitor pro kontejnery pro metriky](insights/container-insights-update-metrics.md) – aktualizované podrobnosti týkající se chování a konfigurace metriky scraping s Azure Red Hat OpenShift ve srovnání s jinými typy clusterů Kubernetes.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
- [Azure Monitor pro virtuální počítače (GA) nejčastější dotazy](insights/vminsights-ga-release-faq.md) – Přidány informace o upgradu pracovního prostoru a agentů na novou verzi.

#### <a name="office-365"></a>Office 365
- [Řešení pro správu Office 365 v Azure](insights/solution-office-365.md) – přidány podrobnosti a nejčastější dotazy týkající se migrace na řešení Office 365 v Azure Sentinelu. Odstraněna část registrace.



### <a name="logs"></a>Protokoly
- [Správa pracovních prostorů Analýzy protokolů ve službě Azure Monitor](platform/manage-access.md) – aktualizace neakcí.
- [Správa využití a nákladů pro protokoly monitorování Azure](platform/manage-cost-storage.md) – přidáno vysvětlení výpočtu objemu dat v části Cenový model.
- [Pomocí šablon Azure Resource Manager u vytvoření a konfigurace pracovního prostoru Analýzy protokolů](platform/template-workspace-configuration.md) – aktualizovaná šablona s novými cenovými úrovněmi.


### <a name="platform-logs"></a>Protokoly platformy
- [Shromažďujte protokol aktivit Azure s diagnostickým nastavením – Azure Monitor](platform/diagnostic-settings-legacy.md) – další informace o změněných vlastnostech.
- [Export protokolu aktivit Azure](platform/activity-log-export.md) – aktualizováno pro změny v uznané. 





## <a name="december-2019"></a>Prosinec 2019

### <a name="agents"></a>Agenti
- [Připojte počítače S Linuxem k Azure Monitoru](platform/agent-linux.md) – nový článek.

### <a name="alerts"></a>Výstrahy
- [Vytvořte upozornění na metriku pomocí šablony Správce prostředků](platform/alerts-metric-create-templates.md) – byl přidán příklad vlastní metriky.
- [Vytváření výstrah s dynamickými prahovými hodnotami v Azure Monitor](platform/alerts-dynamic-thresholds.md) – přidána část o interpretaci dynamických prahových hodnotových grafů.
- [Přehled monitorování výstrah a oznámení v Azure](platform/alerts-overview.md) – aktualizovaný graf prostředků dotazu.
- [Podporované prostředky pro upozornění metriky v Azure Monitor](platform/alerts-metric-near-real-time.md) – aktualizace na metriky a dimenze podporované.
- [Přepněte ze staršího rozhraní API upozornění služby Log Analytics do nového rozhraní API upozornění Azure](platform/alerts-log-api-switch.md) – přidána poznámka k upravenému názvu výstrahy.
- [Zjistěte, jak fungují upozornění na metriky v Azure Monitoru.](platform/alerts-metric-overview.md) - Přidány podporované typy prostředků pro monitorování ve velkém měřítku.

### <a name="application-insights"></a>Application Insights
- [Přehledy aplikací pro aplikace pracovní služby (aplikace bez HTTP)](app/worker-service.md) – přidána výchozí úroveň protokolování do kódu Jazyka C#. Aktualizovaná referenční verze balíčku.
- [ApplicationInsights.config odkaz - Azure](app/configuration-with-applicationinsights-config.md) - Aktualizovaný ukázkový kód.
- [Automatizujte Přehledy aplikací Azure pomocí PowerShellu](app/powershell.md) – aktualizace na šablonu Správce prostředků.
- [Balíčky NuGet pro Azure Monitor Application Insights](app/nuget.md) – aktualizované verze balíčků.
- [Vytvořte nový prostředek Azure Application Insights](app/create-new-resource.md) – poznámka přidaná ke globálně jedinečnému názvu.
- [Diagnostikujte pomocí živého datového proudu metrik – Přehledy aplikací Azure](app/live-stream.md) – požadavek na aktualizovanou ASP.NET základní verzi sady SDK.
- [Čítače událostí v Application Insights](app/eventcounters.md) – aktualizované kategorie a tabulky na customMetrics.
- [Prozkoumejte protokoly trasování v jazyce Java v Azure Application Insights](app/java-trace-logs.md) – přidána konfigurace pro prahovou hodnotu protokolování agenta Java.
- [IP adresy používané application insights a Log Analytics](app/ip-addresses.md) - aktualizované IP adresy pro Live Metrics Stream.
- [Monitorování výkonu služeb azure app services](app/azure-web-apps.md) – přidána podpora pro ASP.NET Core 3.0. 
- [Monitorování aplikací Pythonu pomocí Azure Monitor (preview)](app/opencensus-python.md) – přidáno vysvětlení pro mapování schématu OpenCensus Python do Azure . Schéma monitoru
- [Poznámky k verzi pro Azure Application Insights](app/release-notes.md) – přidané poznámky pro starší verze.
- [Kanály telemetrie v Azure Application Insights](app/telemetry-channels.md) – aktualizované trvání zahozených dat během delší doby ztraceného připojení.
- [Vzorkování telemetrie v Azure Application Insights](app/sampling.md) – opravený fragment kódu pro vlastní telemetriiInitializer.
- [Poradce při potížích s přehledy aplikací ve webovém projektu Java](app/java-troubleshoot.md) – odebráno prohlášení o nepodpoře kolekce závislostí v JDK 9.

### <a name="insights-and-solutions"></a>Přehledy a řešení
- [Azure Monitor pro kontejnery Nejčastější dotazy](insights/container-insights-faq.md) – přidána otázka na obrázek a název pole.
- [Řešení Azure SQL Analytics v Azure Monitor](insights/azure-sql.md) – aktualizovaná databáze čeká podporu spravované instance.
- [Konfigurace Azure Monitor pro sběr dat agenta kontejnerů](insights/container-insights-agent-config.md) – přidáno nastavení pro enrich_container_logs.
- [Konfigurace clusterů hybridních kubernetes pomocí Azure Monitor pro kontejnery](insights/container-insights-hybrid-setup.md) – přidána část řešení potíží.
- [Sledování stavu replikace služby Active Directory s](insights/ad-replication-status.md) aktualizovaným předpokladem programu Azure Monitor – rozhraní .NET Framework
- [Řešení sledování výkonu sítě v Azure](insights/network-performance-monitor.md) – přidány podporované oblasti.
- [Optimalizujte prostředí služby Active Directory s aktualizovaným](insights/ad-assessment.md) předpokladem Azure Monitor – rozhraní .NET Framework.
- [Optimalizujte prostředí SQL Serveru s aktualizovaným předpokladem Azure Monitor](insights/sql-assessment.md) – rozhraní .NET Framework.
- [Optimalizujte prostředí Operations Manager u system centra s aktualizovaným](insights/scom-assessment.md) předpokladem Azure Log Analytics – rozhraní .NET Framework.
- [Podporovaná připojení s konektorem správy služeb IT v Azure Log Analytics](platform/itsmc-connections.md) – přidáno do New Yorku k požadovanému ID klienta a tajný klíč klienta.

### <a name="logs"></a>Protokoly
- [Odstranění a obnovení pracovního prostoru Azure Log Analytics](platform/delete-workspace.md) – přidaná metoda PowerShellu.
- [Navrhování nasazení protokolů azure monitoru](platform/design-logs-deployment.md) – zvýšena rychlost ingestování pracovního prostoru.

### <a name="metrics"></a>Metriky
- [Metriky platformy Azure Monitor exportovat pomocí nastavení diagnostiky](platform/metrics-supported-export-diagnostic-settings.md) – nový článek.

### <a name="platform-logs"></a>Protokoly platformy
Více článků aktualizovaných jako součást restrukturalizace obsahu pro protokoly platformy na základě nové funkce pro konfiguraci protokolu aktivit pomocí nastavení diagnostiky.

- [Archivace protokolů prostředků Azure do účtu úložiště](platform/resource-logs-collect-storage.md)
- [Schéma událostí protokolu aktivit Azure](platform/activity-log-schema.md)
- [Limity služeb Azure Monitor](service-limits.md)
- [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics](platform/activity-log-collect.md)
- [Shromažďování protokolu aktivit Azure s diagnostickým nastavením (preview) – Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Shromažďování protokolů aktivit Azure do pracovního prostoru Analýzy protokolů napříč tenanty Azure](platform/activity-log-collect-tenants.md)
- [Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics](platform/resource-logs-collect-workspace.md)
- [Vytvoření diagnostického nastavení v Azure pomocí šablony Správce prostředků](platform/diagnostic-settings-template.md)
- [Vytvoření diagnostického nastavení pro shromažďování protokolů a metrik v Azure](platform/diagnostic-settings.md)
- [Export protokolu aktivit Azure](platform/activity-log-export.md)
- [Přehled protokolů platformy Azure](platform/platform-logs-overview.md)
- [Streamování dat monitorování Azure do centra událostí](platform/stream-monitoring-data-event-hubs.md)
- [Streamování protokolů platformy Azure do centra událostí](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Rychlé starty a kurzy

- [Vytvořte graf metrik v Azure Monitor](learn/tutorial-metrics-explorer.md) – nový článek.
- [Shromažďujte protokoly prostředků z prostředku Azure a analyzujte pomocí Azure Monitor](learn/tutorial-resource-logs.md) – nový článek.
- [Monitorujte prostředek Azure pomocí Azure Monitoru](learn/quick-monitor-azure-resource.md) – nový článek.
   
## <a name="next-steps"></a>Další kroky

- Pokud chcete přispět do dokumentace Azure Monitoru, přečtěte si [průvodce přispěvatelem dokumentů](https://docs.microsoft.com/contribute/).