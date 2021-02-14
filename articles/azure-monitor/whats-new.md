---
title: Co je nového v dokumentaci k Azure Monitor
description: Důležité aktualizace Azure Monitor dokumentace se každý měsíc aktualizovaly.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: 35bfc9c752243a69912b0732c4105712fa4a6086
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377665"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Co je nového v dokumentaci k Azure Monitor?

Tento článek obsahuje seznam Azure Monitorch článků, které jsou buď nové, nebo byly významně aktualizované. Bude aktualizován první týden v měsíci, aby zahrnoval aktualizace článků z předchozího měsíce.

## <a name="january-2021"></a>Leden 2021 

### <a name="general"></a>Obecné 
- [Azure monitor časté otázky](faq.md) – přidalo se zadání informací o zařízení pro Application Insights.
### <a name="agents"></a>Agenti  
- [Shromažďování událostí trasování událostí pro Windows (ETW) pro analýzu Azure monitor protokoly](platform/data-sources-event-tracing-windows.md) – nový článek.
- [Pravidla shromažďování dat ve Azure monitor (Preview)](platform/data-collection-rule-overview.md) – přidalo se odkazy na ukázky PowerShellu a rozhraní příkazového řádku.

### <a name="alerts"></a>Výstrahy  
- [Nakonfigurujte Azure pro připojení nástrojů ITSM pomocí zabezpečeného exportu](platform/itsm-connector-secure-webhook-connections-azure-configuration.md) a nového článku.
- [Chyby stavu konektoru na řídicím panelu ITSMC](platform/itsmc-dashboard-errors.md) – nový článek
- [Prozkoumejte chyby pomocí řídicího panelu ITSMC](platform/itsmc-dashboard.md) – nový článek.
- [Řešení potíží s upozorněními na metriky Azure](platform/alerts-troubleshoot-metric.md) – přidané oddíly pro dynamické prahové hodnoty.
- [Řešení problémů v oddělení IT Service Management Connector](platform/itsmc-troubleshoot-overview.md) – nový článek

### <a name="application-insights"></a>Application Insights
- [Korelace telemetrie Azure Application Insights](app/correlation.md) – přidala se korelace trasování, když jeden modul volá OpenCensus Python jinou.
- [Application Insights pro webové stránky](app/javascript.md) – nový článek.
- [Klikněte na analýza Automatická kolekce modul plug-in pro Application Insights JavaScript SDK](app/javascript-click-analytics-plugin.md) – nový článek.
- [Monitorujte své aplikace bez změny kódu – automatické instrumentace pro Azure monitor](app/codeless-overview.md) sloupce pythonu Application Insights přidáno.
- [Reakce modulu plug-in pro Application Insights JavaScript SDK](app/javascript-react-plugin.md) – nový článek
- [Procesory telemetrie (Preview) – Azure Monitor Application Insights pro přepis Java](app/java-standalone-telemetry-processors.md) .
- [Analýza využití pomocí Azure Application Insights](app/usage-overview.md) – nový článek.
- [Použití analýzy změn aplikace v Azure monitor k nalezení problémů s webovou aplikací](app/change-analysis.md) – přidání chyby messges


### <a name="insights"></a>Insights    
- [Azure monitor pro Azure Průzkumník dat (Preview)](insights/data-explorer.md) – nový článek

### <a name="logs"></a>Protokoly    
- [Azure monitor klíč spravovaný zákazníkem](platform/customer-managed-keys.md) – zavádí spravovanou identitu přiřazenou uživatelem.
- [Azure monitor protokoluje vyhrazené clustery](log-query/logs-dedicated-clusters.md) – kód aktualizované odezvy.
- [Dotaz na různé služby – Azure monitor a Azure Průzkumník dat (Preview)](platform/azure-monitor-troubleshooting-logs.md) – nový článek

### <a name="metrics"></a>Metriky
- Metrika metrik Azure Monitor metriky [a zobrazení je vysvětleno](platform/metrics-aggregation-explained.md) – nový článek.

### <a name="platform-logs"></a>Protokoly platformy
- [Protokoly prostředků Azure monitor podporují služby a kategorie](platform/resource-logs-categories.md) – nový článek.

### <a name="visualizations"></a>Vizualizace
- [Zdroje dat Azure monitor sešity](platform/workbooks-data-sources.md) – Přidali jsme sloučení a analýzu změn.


## <a name="december-2020"></a>Prosinec 2020

### <a name="general"></a>Obecné
- Azure Monitor chybových zpráv přidaných [zákazníkem spravovaný klíč](platform/customer-managed-keys.md) .
- [Partneři, kteří se integrují s](platform/partners.md) oddílem Azure monitor-added v integraci centra událostí.

### <a name="agents"></a>Agenti
- [Dotazování na více prostředků v Azure Průzkumník dat pomocí Azure monitor](platform/azure-monitor-data-explorer-proxy.md) -New article.
- [Přehled agentů monitorování Azure](platform/agents-overview.md) – přidala se podpora Oracle 8.

### <a name="alerts"></a>Výstrahy
- [Řešení potíží s upozorněními na metriky Azure](platform/alerts-troubleshoot-metric.md) – přidání potíží pro dynamické prahové hodnoty.
- [IT Service Management Connector v Log Analytics](platform/itsmc-definition.md) – nový článek.
- [Přehled služby IT Service Management Connector](platform/itsmc-overview.md) – restrukturované informace pro řešení potíží.
- [Připojení Cherwell ke službě IT Service Management Connector](platform/itsmc-connections-cherwell.md) – nový článek
- [Připojení k prokázání pomocí konektoru pro správu IT služby](platform/itsmc-connections-provance.md) – nový článek
- [Připojení SCSM ke službě IT Service Management Connector](platform/itsmc-connections-scsm.md) – nový článek
- [Připojení ServiceNow ke službě IT Service Management Connector](platform/itsmc-connections-servicenow.md) – nový článek
- [Ruční oprava problémů s synchronizací ServiceNow](platform/itsmc-resync-servicenow.md) – restrukturované informace pro řešení potíží




### <a name="application-insights"></a>Application Insights
- [Azure Application Insights pro webové aplikace v JavaScriptu](app/javascript.md) – nastavení připojovacího řetězce se přidalo.
- [Metrika Azure Application Insights Standard](app/standard-metrics.md) – nový článek
- [Azure Monitor Application Insights Java](app/java-in-process-agent.md) – Další informace o posílání vlastní telemetrie z vaší aplikace.
- [Průběžný export telemetrie z Application Insights](app/export-telemetry.md) – bylo přidáno export na základě nastavení diagnostiky.
- [Povolte Snapshot debugger pro aplikace .NET a .NET Core v Azure Functions](app/snapshot-debugger-function-app.md) – nový článek.
- [IP adresy, které používá Application Insights a Log Analytics](app/ip-addresses.md) -přidané IP adresy pro Azure Government.
- [Řešení potíží se službou Azure Application Insights Profiler](app/profiler-troubleshooting.md) – přidané informace na stránce stav rozšíření webu diagnostické služby.
- [Řešení potíží s testy dostupnosti služby Azure Application Insights](app/troubleshoot-availability.md) – aktualizace pro řešení potíží s testy pro testování příkazů.
- [Řešení potíží s Azure Monitor Application Insights pro jazyk Java](app/java-standalone-troubleshoot.md) – nový článek.

### <a name="containers"></a>Kontejnery
- [Sestavy v Azure monitor for Containers](insights/container-insights-reports.md) – nový článek.

### <a name="logs"></a>Protokoly
- [Azure monitor protokolovat vyhrazené clustery](log-query/logs-dedicated-clusters.md) – přidané automatizované příkazy, metody odpojování a odebírání a odstraňování potíží.
- [Dotaz na křížovou službu mezi Azure monitor a Azure Průzkumník dat (Preview)](platform/azure-data-explorer-monitor-cross-service-query.md) – nový článek
- [Export dat pracovního prostoru Log Analytics v Azure monitor (Preview)](platform/logs-data-export.md) – přidány šablony ARM.

### <a name="metrics"></a>Metriky
- [Pokročilé funkce Azure Průzkumník metrik](platform/metrics-charts.md) – přidané informace o výběru oboru prostředků
- [Zobrazení více prostředků v Průzkumník metrik](platform/metrics-dynamic-scope.md) – nový článek.

### <a name="networks"></a>Sítě
- [Řešení Azure Networking Analytics v Azure monitor](insights/azure-networking-analytics.md) přibyly informace o sešitu Network Insights.

### <a name="virtual-machines"></a>Virtual Machines
- [Povolí Azure monitor pro hybridní prostředí](insights/vminsights-enable-hybrid.md) – novou verzi agenta závislostí.


### <a name="visualizations"></a>Vizualizace
- [Vizualizace mapy sešitu Azure monitor](platform/workbooks-map-visualizations.md) – nový článek
- [Azure monitor sešity Přineste si vlastní](platform/workbooks-bring-your-own-storage.md) článek o novém úložišti.


## <a name="november-2020"></a>Listopad 2020

### <a name="general"></a>Obecné
- [Omezení služby Azure monitor](service-limits.md) – Aktualizováno pro podporu ARC Azure.

### <a name="agents"></a>Agenti
- [Přehled agentů monitorování Azure](platform/agents-overview.md) – aktualizované pro podporu ARC Azure
- [Nainstalujte agenta Azure monitor](platform/azure-monitor-agent-install.md) – nový článek.
- [Přehled agenta Azure monitor](platform/azure-monitor-agent-overview.md) – Aktualizováno pro podporu ARC Azure.
- [Ukázky šablon Správce prostředků pro agenty](samples/resource-manager-agent.md) – aktualizované pro podporu ARC Azure.

### <a name="alerts"></a>Výstrahy
- [Vytváření a Správa skupin akcí ve Azure Portal](platform/action-groups.md) – byly přidány zdrojové IP adresy pro Webhooky.

### <a name="application-insights"></a>Application Insights
- [Monitorování aplikací s nekódováním kódu Java Azure Monitor Application Insights](app/java-in-process-agent.md) přidány příklad konfigurace.
- [Reakce modulu plug-in pro Application Insights JavaScript SDK](app/javascript-react-plugin.md) – přidání části na používání nereagujících zavěšení
- [Upgrade z Application Insights Java 2. x SDK](app/java-standalone-upgrade-from-2x.md) – nový článek.
- [Poznámky k verzi pro Microsoft. ApplicationInsights. SnapshotCollector](app/snapshot-collector-release-notes.md) -New article

### <a name="autoscale"></a>Automatické škálování
- [Začínáme s automatické škálováním v Azure](platform/autoscale-get-started.md) – Přidání oddílu o přesunutí automatického škálování do jiné oblasti.

### <a name="data-collection"></a>Shromažďování dat
- [Konfigurace shromažďování dat pro agenta Azure monitor (Preview)](platform/data-collection-rule-azure-monitor-agent.md) – Aktualizováno pro podporu ARC Azure
- [Pravidla shromažďování dat v Azure monitor (Preview)](platform/data-collection-rule-overview.md) – Aktualizováno pro podporu ARC Azure.
- [Ukázky šablon Správce prostředků pro pravidla shromažďování dat](samples/resource-manager-data-collection-rules.md) – nový článek

### <a name="insights-and-solutions"></a>Přehledy a řešení
- [Připojte Azure k nástrojům ITSM pomocí zabezpečeného exportu, který](platform/it-service-management-connector-secure-webhook-connections.md) je přidaný v tématu připojení k ServiceNow.

### <a name="logs"></a>Protokoly
- [Integrujte Log Analytics a Excel](log-query/log-excel.md) – nový článek.
- [Log Analytics zabezpečení dat](platform/data-security.md) – přidáno do části Další funkce zabezpečení.
- [Log Analytics integrace s Power BI](log-query/log-powerbi.md) – nový článek.
- [Standardní sloupce v Azure monitor záznamy protokolu](platform/log-standard-columns.md) – přidáno _SubscriptionId sloupci.

Nové a aktualizované články z změny struktury obsahu dotazů protokolu

- [Kurz Log Analytics](log-query/log-analytics-tutorial.md)
- [Dotazy protokolu v Azure Monitor](log-query/log-query-overview.md)
- [Přehled Log Analytics v Azure Monitor](log-query/log-analytics-overview.md)
- [Ukázky pro dotazy pro Azure Průzkumník dat a Azure Monitor](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Kurz: použití dotazů Kusto v Azure Průzkumník dat a Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Virtuální počítače

- [Povolit Azure monitor pro virtuální počítače přehled](insights/vminsights-enable-overview.md) – přidané oblasti

Nové články pro stav hosta Azure Monitor pro virtuální počítače (Preview)

- [Stav hosta Azure Monitor pro virtuální počítače (Preview)](insights/vminsights-health-overview.md)
- [Azure Monitor pro virtuální počítače upozornění na stav hosta (Preview)](insights/vminsights-health-alerts.md)
- [Konfigurace monitorování v Azure Monitor pro virtuální počítače stav hosta (Preview)](insights/vminsights-health-configure.md)
- [Konfigurace monitorování v Azure Monitor pro virtuální počítače stav hosta pomocí pravidel shromažďování dat (Preview)](insights/vminsights-health-configure-dcr.md)
- [Povolit stav hosta Azure Monitor pro virtuální počítače (Preview)](insights/vminsights-health-enable.md)
- [Řešení potíží s Azure Monitor pro virtuální počítačem stavem hosta (Preview)](insights/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>Říjen 2020

### <a name="general"></a>Obecné
- [Azure monitor vyřazení rozhraní API](platform/operationalinsights-api-retirement.md) – nový článek

### <a name="agents"></a>Agenti
- [Co se monitoruje pomocí Azure monitor](monitor-reference.md) -přidáno části v agentech.

### <a name="alerts"></a>Výstrahy
- [Vytváření a Správa skupin akcí v oddílu Azure Portal](platform/action-groups.md) -added u značky služby.
- [Ukázky šablon Správce prostředků pro výstrahy metrik](samples/resource-manager-alerts-metric.md) – přidaný parametr shody obsahu a umístění testů.
- [Řešení potíží s upozorněními na metriky Azure](platform/alerts-troubleshoot-metric.md) – přidání osvědčeného postupu pro konfiguraci pravidel

### <a name="application-insights"></a>Application Insights
- [Úhlový modul plug-in pro Application Insights JavaScript SDK](app/javascript-angular-plugin.md) – nový článek
- [Azure Application Insights pro ASP.NET Core aplikace](app/asp-net-core.md) – Nejčastější dotazy týkající se protokolů ILogger.
- [Nakonfigurujte monitorování pro ASP.NET pomocí](app/asp-net.md) přepsaného článku Azure Application Insights –.
- [Založené na protokolech a předem agregované metriky ve službě Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) – přidané tabulky s předem agregovanými metrikami.
- [Sledujte dostupnost a odezvu všech](app/monitor-web-app-availability.md) oddílů přidaných webem na Tagy naplnění na pozici webu.
- [Monitorovat aplikace Java kdekoli – Azure Monitor Application Insights](app/java-standalone-config.md) přidat příklad konfigurace.
- [Monitorujte aplikace Java kdekoli – Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md) – nový článek.
- [Použití analýzy změn aplikace v Azure monitor k nalezení problémů s webovými aplikacemi](app/change-analysis.md) – přidané oddíly na virtuálních počítačích a protokolu aktivit
  
### <a name="autoscale"></a>Automatické škálování
- [Začínáme s automatické škálováním v Azure](platform/autoscale-get-started.md) – Přidání oddílu o přesunutí automatického škálování do jiné oblasti.

### <a name="containers"></a>Kontejnery
- [Nakonfigurujte monitorování PV pomocí Azure monitor for Containers](insights/container-insights-persistent-volumes.md) – nový článek.
- [Jak spravovat Azure monitor pro kontejnery agent](insights/container-insights-manage-agent.md) – přidala se podpora pro cluster Kubernetes s podporou ARC Azure.
- [Výstrahy metriky od Azure monitor pro kontejnery](insights/container-insights-metric-alerts.md) – přidala se podpora pro cluster Kubernetes s podporou Azure ARC.

### <a name="insights-and-solutions"></a>Přehledy a řešení
- [IT Service Management Connector – v ServiceNow části Secure export in Azure monitor](platform/it-service-management-connector-secure-webhook-connections.md) – added.

### <a name="logs"></a>Protokoly
- [Archivace dat z pracovního prostoru Log Analytics do Azure Storage pomocí aplikace logiky](platform/logs-export-logic-app.md) – nový článek
- [Export dat v pracovním prostoru Log Analytics v Azure monitor (Preview)](platform/logs-data-export.md) – Přidali jsme vzorový text pro žádost REST pro centrum událostí.
- [Spravujte využití a náklady na protokoly Azure monitor](platform/manage-cost-storage.md) – přibyly informace o vztahu mezi protokoly Azure Monitor a Azure Security Center fakturace. Přidání dotazu pro počty uzlů v případě použití cenové úrovně na uzel 
- [Monitoruje stav Log Analytics pracovního prostoru v Azure monitor](platform/monitor-workspace.md) – nový článek.
- [Dotazování dat v Azure monitor pomocí Azure Průzkumník dat (Preview)](platform/azure-data-explorer-monitor-proxy.md) – nový článek
- [Dotaz na exportovaná data z Azure monitor pomocí Azure Průzkumník dat (Preview)](platform/azure-data-explorer-query-storage.md) – nový článek

### <a name="networks"></a>Sítě
- [Azure monitor pro sítě Preview](insights/network-insights-overview.md) – část Poradce při potížích se přidala. Přidání části při připojení

### <a name="platform-logs"></a>Protokoly platformy
- [Schéma událostí protokolu aktivit Azure](platform/activity-log-schema.md) – Přidal se popis úrovní závažnosti.

### <a name="virtual-machines"></a>Virtuální počítače
- [Změna analýzy v Azure monitor pro virtuální počítače](insights/vminsights-change-analysis.md) – nový článek
- [Povolit Azure monitor pro virtuální počítače přehled](insights/vminsights-enable-overview.md) – přidané oblasti
- [Jak aktualizovat Azure monitor pro kontejnery pro metriky](insights/container-insights-update-metrics.md) – přidala se podpora pro cluster Kubernetes s podporou ARC Azure.



## <a name="september-2020"></a>Září 2020

### <a name="general"></a>Obecné
- [Azure monitor časté otázky](faq.md) – přidání části na OpenTelemetry

### <a name="agents"></a>Agenti
- [Přehled Azure Monitorho agenta](platform/azure-monitor-agent-overview.md) – pro přepnutí na nového agenta byly přidány rozhodovací faktory.
- [Přehled agentů monitorování Azure](platform/agents-overview.md) – přidala se podpora pro Windows 10.

### <a name="alerts"></a>Výstrahy
- [Vytvoření upozornění protokolu pomocí Azure Resource Manager šablonu](platform/alerts-log-create-templates.md) – nový článek.
- [Řešení potíží s výstrahami Azure Metrics](platform/alerts-troubleshoot-metric.md) – přidaný oddíl k exportu šablony ARM pro pravidlo upozornění metriky.

### <a name="application-insights"></a>Application Insights
- [Vytvořit nový Azure Monitor Application Insights na základě pracovního prostoru, který](app/create-workspace-resource.md) odebral označení verze Preview.
- [Uchovávání dat a ukládání v Azure Application Insights](app/data-retention-privacy.md) – byly přidány podrobnosti o nové podpoře ochrany před únikem informací pro Mac a Linux.
- [Čítače událostí v Application Insights](app/eventcounters.md) přidávají poznámku k čítačům shromážděným ve výchozím nastavení.
- [Protokolová a předem agregovaná metrika v Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) – odebrané označení Preview
- [Migrace klasického prostředku Azure Monitor Application Insights do prostředku založeného na pracovním prostoru](app/convert-classic-resource.md) – nový článek.
- [Monitorujte aplikace v jazyce Java v jakémkoli prostředí – Azure Monitor Application Insights](app/java-in-process-agent.md) – Aktualizováno pro novou verzi Preview agenta.
- [Nastavení analýzy webových aplikací pro ASP.NET pomocí](app/asp-net.md) přepsaného článku Azure Application Insights –.
- [Kanály telemetrie v Azure Application Insights](app/telemetry-channels.md) – byly přidány podrobnosti o nové podpoře ochrany před únikem informací pro Mac a Linux.
- [Poradce při](app/snapshot-debugger-troubleshoot.md) potížích s Snapshot Debugger můžete vyřešit v části Snapshot Debugger protokolu SSL pro Azure Application Insights.
- [Použití analýzy změn aplikace v Azure monitor k vyhledání problémů s webovými aplikacemi](app/change-analysis.md) – přidání virtuálního počítače a protokolu aktivit.


### <a name="containers"></a>Kontejnery
- [Nakonfigurujte cluster Kubernetes s povoleným ARC Azure pomocí Azure monitor pro kontejnery](insights/container-insights-enable-arc-enabled-clusters.md) – doprovodné materiály k povolení monitorování pomocí instančního objektu.
- [Nasazení & metriky hPa s Azure monitor pro kontejnery](insights/container-insights-deployment-hpa-metrics.md) – nový článek.

### <a name="insights-and-solutions"></a>Přehledy a řešení
- [Azure monitor pro Azure cache pro Redis](insights/redis-cache-insights-overview.md) – odebrání označení verze Preview
- [Azure monitor pro sítě (Preview)](insights/network-insights-overview.md) – přidány části připojení a přenos.
- [IT Service Management Connector – zabezpečený export v Azure monitor](platform/it-service-management-connector-secure-webhook-connections.md) – nový článek.
- [IT konektor pro IT služby v Azure monitor](platform/itsmc-connections.md) -Note na ITSM integrace Cherwell a prov.
- [Monitorování Key Vault pomocí Azure monitor pro Key Vault](insights/key-vault-insights-overview.md) – odebrané označení Preview

### <a name="logs"></a>Protokoly
- [Dotazy auditu v Azure monitor dotazy protokolu](log-query/query-audit.md) – nový článek.
- Azure Monitor bezpečnostního modulu přidávaného zákazníkem, který je [spravovaný klíčem](platform/customer-managed-keys.md)
- [Azure monitor protokoluje vyhrazené clustery](log-query/logs-dedicated-clusters.md) – nový článek.
- [Návrh Azure Monitorch protokolů nasazení](platform/design-logs-deployment.md) – aktualizace škálování a limitu míry přijímání pro ingestování
- [Rozsah dotazu protokolu v Azure Monitor Log Analytics](log-query/scope.md) – aktualizace, které zahrnují aplikace založené na pracovních prostorech.
- [Přihlašuje Azure monitor](platform/data-platform-logs.md) – aktualizace, aby zahrnovaly aplikace založené na pracovních prostorech.
- [Standardní sloupce v Azure monitor protokolu záznamů](platform/log-standard-columns.md) – aktualizace, které zahrnují aplikace založené na pracovních prostorech.
- [Omezení služby Azure monitor](service-limits.md) – byla aktualizována omezení pro omezování uživatelských dotazů.
- [Použití účtů úložiště spravovaných zákazníkem ve Azure monitor přepsané log Analyticsm](platform/private-storage.md) článku.
- [Zobrazení a analýza dat v Azure Log Analytics](./platform/data-platform-logs.md) – aktualizace, které zahrnují aplikace založené na pracovních prostorech.


### <a name="platform-logs"></a>Protokoly platformy
- [Schéma událostí protokolu aktivit Azure – Azure monitor](platform/activity-log-schema.md) – byly přidány úrovně závažnosti.
- [Ukázky šablon Správce prostředků pro nastavení diagnostiky](samples/resource-manager-diagnostic-settings.md) – přidání ukázky pro účet úložiště Azure.

### <a name="visualizations"></a>Vizualizace
- [Vizualizace grafu Azure Monitorho sešitu](platform/workbooks-chart-visualizations.md) – nový článek
- [Zobrazovací modul pro vykreslování kompozitních pruhů Azure monitor sešitu](platform/workbooks-composite-bar.md) – nový článek
- [Vizualizace grafu Azure Monitorho sešitu](platform/workbooks-graph-visualizations.md) – nový článek
- [Vizualizace mřížky sešitu Azure monitor](platform/workbooks-grid-visualizations.md) – nový článek
- [Vizualizace v rámečku Azure monitorového sešitu medu](platform/workbooks-honey-comb.md) – nový článek.
- [Vizualizace textu v sešitu Azure monitor](platform/workbooks-text-visualizations.md) – nový článek
- [Vizualizace dlaždic Azure monitor sešitu](platform/workbooks-tile-visualizations.md) – nový článek
- [Vizualizace stromu Azure monitorového sešitu](platform/workbooks-tree-visualizations.md) – nový článek




## <a name="august-2020"></a>Srpen 2020

### <a name="general"></a>Obecné

- [Co se monitoruje Azure monitor](monitor-reference.md) – aktualizováno tak, aby zahrnovalo Azure monitor agenta.


### <a name="agents"></a>Agenti
- [Přehled agenta Azure monitor](platform/azure-monitor-agent-overview.md) – nový článek.
- [Povolí Azure monitor pro hybridní prostředí](insights/vminsights-enable-hybrid.md) – aktualizovaná verze agenta závislostí.
- [Přehled agentů monitorování Azure](platform/agents-overview.md) – přidání Azure monitor agenta a tabulky podpory konsolidovaného operačního systému


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Nové a aktualizované články z změny struktury obsahu agenta
- [Povolit Azure Monitor pro virtuální počítače – přehled](insights/vminsights-enable-overview.md)
- [Instalace agenta Log Analytics na počítačích s Linuxem](platform/agent-linux.md)
- [Instalace agenta Log Analytics na počítačích s Windows](platform/agent-windows.md)
- [Přehled agenta Log Analytics](platform/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights pro webové aplikace v JavaScriptu](app/javascript.md) – přidaný oddíl s vysvětlením korelace a konfigurace klientského serveru pro korelaci CORS.
- [Vytvořte novou Azure Monitor Application Insights prostředky založené na pracovním prostoru](app/create-workspace-resource.md) , které nabízí aplikace založené na pracovních prostorech.
- [IP adresy, které používá Application Insights a Log Analytics](app/ip-addresses.md) -aktualizované IP adresy pro datový proud Live Metrics.
- [Monitorujte aplikace v jazyce Java ve všech prostředích Azure monitor tabulce Application Insightsed](app/java-in-process-agent.md) pro podporované vlastní telemetrie.
- [Nativní reakce na modul plug-in pro Application Insights JavaScript SDK](app/javascript-react-native-plugin.md) – nový článek
- [Reakce modulu plug-in pro Application Insights JavaScript SDK](app/javascript-react-plugin.md) – nový článek
- [Ukázka šablony Správce prostředků pro vytváření aplikací služby Azure Functions pomocí Application Insights monitoring](samples/resource-manager-function-app.md) – nový článek.
- [Ukázky správce prostředků šablon pro vytváření webových aplikací Azure App Services pomocí Application Insights monitoring](samples/resource-manager-web-app.md) – nový článek.
- [Analýza využití pomocí Azure Application Insights](app/usage-overview.md) – přidané video

### <a name="autoscale"></a>Automatické škálování
- [Začínáme s AutoScale v Azure](platform/autoscale-get-started.md) – Přidání oddílu o směrování na instance v pořádku pro App Service.

### <a name="data-collection"></a>Shromažďování dat
- [Nakonfigurujte shromažďování dat pro agenta Azure monitor (Preview)](platform/data-collection-rule-azure-monitor-agent.md) – nový článek.
- [Pravidla shromažďování dat v Azure monitor (Preview)](platform/data-collection-rule-overview.md) – nový článek.


### <a name="containers"></a>Kontejnery
- [Nasazení & metriky hPa s Azure monitor pro kontejnery](insights/container-insights-deployment-hpa-metrics.md) – nový článek.

### <a name="insights"></a>Insights
- [Monitorování řešení v Azure monitor](insights/solutions.md) – Aktualizováno pro nové uživatelské rozhraní.
- [Řešení Network Performance Monitor v Azure](insights/network-performance-monitor.md) – přidání podporovaných oblastí pracovního prostoru


### <a name="logs"></a>Protokoly
- [Azure monitor časté otázky](faq.md) – Přidali jsme položku k odstranění dat z pracovního prostoru. Byla přidána položka pro odpovědi 502 a 503.
  - [Návrh Azure monitor protokolů nasazení](platform/design-logs-deployment.md) – aktualizace a omezení rychlosti pro ingestování
- [Spravujte využití a náklady na protokoly Azure monitor](platform/manage-cost-storage.md) – aktualizované dotazy na použití se aktualizují na efektivnější formát dotazů.
- [Optimalizujte dotazy protokolu v Azure monitor](log-query/query-optimization.md) -přidány konkrétní hodnoty k ukazatelům výkonu.
- [Ukázky šablon Správce prostředků pro nastavení diagnostiky](samples/resource-manager-diagnostic-settings.md) – přidání ukázky pro protokoly auditu dotazů protokolu


### <a name="platform-logs"></a>Protokoly platformy
- [Vytvoření nastavení diagnostiky pro odesílání protokolů a metrik platforem do různých cílů](platform/diagnostic-settings.md) – přidávají se místní požadavky na nastavení diagnostiky.

### <a name="visualizations"></a>Vizualizace
- [Přehled sešitů Azure monitor](platform/workbooks-overview.md) – přidáno video
- [Přesuňte šablonu sešitu Azure do jiné oblasti](platform/workbook-templates-move-region.md) – nový článek.
- [Přesunutí sešitu Azure do jiné oblasti](platform/workbooks-move-region.md) – nový článek



## <a name="july-2020"></a>Červenec 2020

### <a name="general"></a>Obecné
- [Nasazení Azure monitor](deploy-scale.md) -restrukturování obsahu Azure monitor pro virtuální počítače registraci.
- [Použití privátního odkazu Azure k bezpečnému propojení sítí s Azure Monitormi](platform/private-link-security.md) přidanými částmi na omezeních.

### <a name="alerts"></a>Výstrahy
- [Pravidla akcí pro Azure monitor výstrahy](platform/alerts-action-rules.md) – přidání procesů CLI.
- [Vytvoření a Správa skupin akcí v Azure Portal](platform/action-groups.md) – aktualizuje se, aby odrážely změny v uživatelském rozhraní.
- [Příklady dotazů v Azure Monitor Log Analytics](log-query/example-queries.md) – nový článek.
- [Vyřešte výstrahy protokolu v části Azure monitor](platform/alerts-troubleshoot-log.md) -added na kvótě pravidla výstrahy.
- [Řešení potíží s výstrahami Azure metrik](platform/alerts-troubleshoot-metric.md) – Přidal se oddíl na pravidlo výstrahy na vlastní metriku, která ještě není vysílaná.
- [Pochopte, jak budou výstrahy metriky fungovat v Azure Monitor.](platform/alerts-metric-overview.md) – Bylo přidáno doporučení pro výběr členitosti agregace.

### <a name="application-insights"></a>Application Insights
- [Poznámky k verzi pro rozšíření webové aplikace Azure – Application Insights](app/web-app-extension-release-notes.md) – nový článek.
- [Příklady šablon Správce prostředků pro prostředky Application Insights](samples/resource-manager-app-resource.md) – nový článek.
- [Řešení potíží se službou Azure Application Insights Profiler](app/profiler-troubleshooting.md) – přidání poznámky k chybě běžící v profileru pro ASP.NET Core aplikace na Azure App Service. 

### <a name="containers"></a>Kontejnery
- [Protokoluje výstrahy z Azure monitor pro kontejnery](insights/container-insights-log-alerts.md) – nový článek.
- [Výstrahy metriky od Azure monitor pro kontejnery](insights/container-insights-metric-alerts.md) – nový článek.

### <a name="logs"></a>Protokoly
- Azure Monitor chybovou zprávu pro přidání [klíčového zákazníka](platform/customer-managed-keys.md) a část konfigurace pro dotazy pro CMK.
- [Azure monitor rozhraní API kolekce dat http](platform/data-collector-api.md) – přidala se ukázka Python 3.
- [Optimalizujte dotazy protokolu v části Azure monitor](log-query/query-optimization.md) -added, které vám pomají při použití dílčích dotazů k vyhnutí více prohledávání dat.
- [Kurz: Začínáme s Log Analytics dotazy](./log-query/log-analytics-tutorial.md) – přidané video.

### <a name="platform-logs"></a>Protokoly platformy
- [Vytvořte nastavení diagnostiky pro odesílání protokolů a metrik platforem do různých cílů](platform/diagnostic-settings.md) – přidané video.
- [Ukázky šablon Správce prostředků pro Azure monitor](samples/resource-manager-samples.md) přidána ukázka ARM pomocí typu cílového cíle. 

### <a name="solutions"></a>Řešení
- [Řešení monitorování v Azure monitor](insights/solutions.md) přidávají procesy CLI.
- [Řešení pro správu Office 365 v Azure](insights/solution-office-365.md) – změněné datum vyřazení

### <a name="virtual-machines"></a>Virtuální počítače

Nové a aktualizované články z změny struktury Azure Monitor pro virtuální počítače obsahu

- [Co je Azure Monitor pro virtuální počítače?](insights/vminsights-overview.md)
- [Konfigurace pracovního prostoru Log Analytics pro Azure Monitor pro virtuální počítače](insights/vminsights-configure-workspace.md)
- [Připojení počítačů se systémem Linux k Azure Monitor](platform/agent-linux.md)
- [Povolení Azure Monitor pro hybridní prostředí](insights/vminsights-enable-hybrid.md)
- [Povolit Azure Monitor pro jeden virtuální počítač nebo sadu škálování virtuálního počítače v Azure Portal](insights/vminsights-enable-portal.md)
- [Povolení Azure Monitor pro virtuální počítače pomocí Azure Policy](./insights/vminsights-enable-policy.md)
- [Povolit Azure Monitor pro virtuální počítače – přehled](insights/vminsights-enable-overview.md)
- [Povolení Azure Monitor pro virtuální počítače pomocí prostředí PowerShell](insights/vminsights-enable-powershell.md)
- [Povolení Azure Monitor pro virtuální počítače pomocí šablon Správce prostředků](insights/vminsights-enable-resource-manager.md)
- [Povolení Azure Monitor pro virtuální počítače s využitím PowerShellu nebo šablon](./insights/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Vizualizace
- [Upgrade vizualizací řídicího panelu Log Analytics](log-query/dashboard-upgrade.md) – aktualizace obnovovací frekvence.
- [Vizualizace dat z Azure monitor](visualizations.md) – přidáno video


## <a name="june-2020"></a>Červen 2020

### <a name="general"></a>Obecné
- [Nasazení Azure monitor](deploy-scale.md) – nový článek
- Azure Monitor vlastnost billingtype se [správou klíčů spravovaných zákazníkem](platform/customer-managed-keys.md) . Byly přidány příkazy prostředí PowerShell.

### <a name="agents"></a>Agenti
- [Přehled agenta Log Analytics](platform/log-analytics-agent.md) – přidání požadavku Pythonu 2

### <a name="alerts"></a>Výstrahy
- [Jak aktualizovat pravidla upozornění nebo pravidla akcí, když se jejich cílový prostředek přesune do jiné oblasti Azure](platform/alerts-resource-move.md) – nový článek.
- [Řešení potíží s upozorněními Azure Metrics](platform/alerts-troubleshoot-metric.md) – nový článek
- [Řešení potíží s výstrahami protokolu v Azure monitor](platform/alerts-troubleshoot-metric.md) – nový článek.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights pro Web Apps v JavaScriptu](app/javascript.md) – aktualizace na JavaScript SDK Byl aktualizován fragment na selhání načítání sestavy.
- [NAKONFIGURUJTE BYOS (Přineste si vlastní úložiště) pro Profiler & Snapshot Debugger](app/profiler-bring-your-own-storage.md) – nový článek.
- [Příchozí sledování žádostí v Azure Application Insights s OpenCensus Pythonem](app/opencensus-python-request.md) – aktualizované protokolování a konfiguraci pro OpenCensus.
- [Monitorování živé webové aplikace v ASP.NET s využitím Azure Application Insights](app/monitor-performance-live-website-now.md) – aktualizované datum vyřazení pro monitorování stavu v1.
- [Monitorování Node.js Services s využitím Azure Application Insights](app/nodejs.md) – několik aktualizací včetně migrace z verzí zkontrolují a konfigurace sady SDK
- [Monitorování aplikací Pythonu pomocí Azure monitor (Preview)](app/opencensus-python.md) – přidané části týkající se konfigurace Azure monitor vývozců
- [Monitorujte své aplikace bez změn kódu – automatické instrumentace pro Azure Monitor Application Insights](app/codeless-overview.md) – nový článek.
- [Řešení potíží se selháním načtení sady SDK pro webové aplikace v jazyce JavaScript](app/javascript-sdk-load-failure.md) – nový článek

### <a name="containers"></a>Kontejnery
- [Postup zastavení monitorování hybridního Kubernetes clusteru](insights/container-insights-optout-hybrid.md) – přidáno pro Kubernetes s povoleným obloukem
- [Nakonfigurujte cluster Kubernetes s povoleným obloukem Azure pomocí Azure monitor for Containers](insights/container-insights-enable-arc-enabled-clusters.md) – nový článek.
- [Nakonfigurujte Azure Red Hat OpenShift v4. x s Azure monitor pro kontejnery](insights/container-insights-azure-redhat4-setup.md) – aktualizované požadavky.
- [Nastavení Azure monitor pro kontejnery živá data (Preview)](insights/container-insights-livedata-setup.md) – odebrání poznámky o funkci, která není dostupná ve službě Azure USA pro státní správu.

### <a name="insights"></a>Insights
- Nejčastější dotazy [– řešení Network Performance Monitor v Azure](insights/network-performance-monitor-faq.md) – Nejčastější dotazy k monitorování ExpressRoute

### <a name="logs"></a>Protokoly
- [Odstraní a obnoví Azure Log Analytics Workspace](platform/delete-workspace.md) – Přidal se příkaz prostředí PowerShell. Aktualizace řešení potíží.
- [Správa pracovních prostorů Log Analytics v části Azure monitor](platform/manage-access.md) – příklad pro nepovolené tabulky v části Azure RBAC.
- [Spravujte využití a náklady na protokoly Azure monitor](platform/manage-cost-storage.md) – další podrobnosti o výpočtu velikosti dat. Aktualizace konfigurace výstrah datových svazků. Podrobnosti o datech zabezpečení shromažďovaných službou Azure Sentinel Objasnění datového zakončení.
- [Použijte protokoly Azure monitor s Azure Logic Apps a](platform/logicapp-flow-connector.md) pomocí omezení konektoru Power autoadded.

### <a name="metrics"></a>Metriky
- [Azure monitor podporované metriky podle typu prostředku](platform/metrics-supported.md) – aktualizované metriky SQL Server.


### <a name="platform-logs"></a>Protokoly platformy

- [Správce prostředků ukázky šablon pro nastavení diagnostiky](samples/resource-manager-diagnostic-settings.md) – oprava pro nastavení diagnostiky protokolu aktivit.
- [Odeslání protokolu aktivit Azure do Log Analytics pracovního prostoru pomocí Azure Portal](learn/quick-collect-activity-log-portal.md) – nového článku
- [Odeslání protokolu aktivit Azure do Log Analytics pracovního prostoru pomocí Azure Resource Manager šablony](learn/quick-collect-activity-log-arm.md) – nový článek.

Nové a aktualizované články z restrukturování a konsolidace obsahu protokolu platformy

- [Archivace protokolů prostředků Azure do účtu úložiště](./platform/resource-logs.md#send-to-azure-storage)
- [Schéma událostí protokolu aktivit Azure](platform/activity-log-schema.md)
- [Protokol aktivit Azure](platform/activity-log.md)
- [Ukázky Azure Monitor CLI](samples/cli-samples.md)
- [Ukázky Azure Monitor PowerShellu](samples/powershell-samples.md)
- [Průvodce rozhraním REST API pro monitorování Azure](platform/rest-api-walkthrough.md)
- [Protokoly prostředků Azure podporují služby a schémata.](./platform/resource-logs-schema.md)
- [Protokoly prostředků Azure](platform/resource-logs.md)
- [Shromažďování a analýza protokolu aktivit Azure v Azure Monitor](./platform/activity-log.md)
- [Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Vytvoření nastavení diagnostiky pro odesílání metrik a protokolů platformy do různých cílů](platform/diagnostic-settings.md)
- [Exportovat protokol aktivit Azure](./platform/activity-log.md#legacy-collection-methods)
- [Přehled protokolů platformy Azure](platform/platform-logs-overview.md)
- [Streamování protokolů platformy Azure do centra událostí](./platform/resource-logs.md#send-to-azure-event-hubs)
- [Zobrazení událostí protokolu aktivit Azure v Azure Monitor](./platform/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Virtuální počítače
- [Povolit Azure monitor pro virtuální počítače v Azure Portal](./insights/vminsights-enable-portal.md) -aktualizováno tak, aby zahrnovalo Azure ARC.
- [Povolit Azure monitor pro virtuální počítače přehled](insights/vminsights-enable-overview.md) – aktualizováno tak, aby zahrnovalo Azure ARC.
- [Co je Azure Monitor pro virtuální počítače?](insights/vminsights-overview.md) – Aktualizováno tak, aby zahrnovalo Azure ARC.


### <a name="visualizations"></a>Vizualizace
- [Azure monitor sešity zdrojů dat](platform/workbooks-data-sources.md) – přidány výstrahy a vlastní koncové body.
- [Řešení potíží s Azure monitor přehledy na základě sešitu](insights/troubleshoot-workbooks.md) – nový článek.
- [Upgrade vizualizací řídicího panelu Log Analytics](log-query/dashboard-upgrade.md) – nový článek.



## <a name="may-2020"></a>Květen 2020

### <a name="general"></a>Obecné

- [Azure monitor Nejčastější dotazy](faq.md) – přidané oddíly pro metriky.
- [Azure monitor uživatelsky řízených](platform/customer-managed-keys.md) změn v přípravě na obecnou dostupnost.
- [Předdefinované definice zásad pro Azure monitor](./samples/policy-reference.md) – nový článek.
- [Účty úložiště vlastněné zákazníkem pro](platform/private-storage.md) ingestování protokolů – nový článek
- [Správa využití a nákladů na protokoly Azure monitor](platform/manage-cost-storage.md) – přidaní poměrná fakturace clusteru
- [Pomocí privátního propojení Azure můžete bezpečně propojit sítě a Azure monitor](platform/private-link-security.md) – nový článek.


#### <a name="new-resource-manager-template-samples"></a>Nové ukázky šablon Správce prostředků 
- [Ukázky šablon Správce prostředků pro Azure Monitor](samples/resource-manager-samples.md)
- [Ukázky šablon Správce prostředků pro skupiny akcí](samples/resource-manager-action-groups.md)
- [Ukázky šablon Správce prostředků pro agenty](samples/resource-manager-agent.md)
- [Ukázky šablon Správce prostředků pro Azure Monitor pro kontejnery](samples/resource-manager-container-insights.md)
- [Ukázky šablon Správce prostředků pro Azure Monitor pro virtuální počítače](samples/resource-manager-vminsights.md)
- [Ukázky šablon Správce prostředků pro nastavení diagnostiky](samples/resource-manager-diagnostic-settings.md)
- [Ukázky šablon Správce prostředků pro pracovní prostory Log Analytics](samples/resource-manager-workspace.md)
- [Ukázky šablon Správce prostředků pro dotazy protokolů](samples/resource-manager-log-queries.md)
- [Ukázky šablon Správce prostředků pro pravidla výstrah dotazů protokolu](samples/resource-manager-alerts-log.md)
- [Ukázky šablon Správce prostředků pro pravidla upozornění metrik](samples/resource-manager-alerts-metric.md)
- [Ukázky šablon Správce prostředků pro sešity](samples/resource-manager-workbooks.md)

### <a name="agents"></a>Agenti
- [Instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](platform/diagnostics-extension-windows-install.md) – Přidání podrobností o konfiguraci diagnostiky.
- [Přehled agenta Log Analytics](platform/log-analytics-agent.md) – byly přidány podporované verze systému Linux.

### <a name="application-insights"></a>Application Insights

- [Monitorujte aplikace běžící na Azure Functions s Application Insights-Azure monitor](app/monitor-functions.md) – nový článek.
- [Monitorujte Node.js služby pomocí Azure Application Insights](app/nodejs.md) – obecné aktualizace, včetně nových oddílů migrace z předchozích verzí.
- [IP adresy, které používá Application Insights a Log Analytics](app/ip-addresses.md) – přidané IP adresy pro Webhooky a pro státní správu USA.
- [Monitorování aplikací ve službě Azure Kubernetes Service (AKS) s využitím Application Insights-Azure monitor](app/kubernetes-codeless.md) – nový článek.
- [Řešení potíží s daty – Application Insights pro rozhraní .NET](app/asp-net-troubleshoot-no-data.md) přidaných do shromažďování protokolů pomocí příkazu dotnet-Trace.
- [Použití analýzy změn aplikace v Azure monitor k vyhledání problémů s webovými aplikacemi](app/change-analysis.md) – více aktualizací ve funkci pro změnu analýzy.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Nové a aktualizované články pro verzi Preview pro aplikace založené na pracovních prostorech
- [Schéma prostředků založené na pracovním prostoru Azure Monitor Application Insights](app/apm-tables.md)
- [Vytvoření nového prostředku založeného na pracovním prostoru Azure Monitor Application Insights](app/create-workspace-resource.md)
- [výraz App () v Azure Monitorch dotazech protokolu](log-query/app-expression.md)
- [Rozsah dotazu protokolu v Azure Monitor Log Analytics](log-query/scope.md)
- [Dotazování napříč prostředky pomocí Azure Monitor](log-query/cross-workspace-query.md)
- [Standardní vlastnosti v Azure Monitor záznamů protokolu](./platform/log-standard-columns.md)
- [Struktura protokolů Azure Monitor](./platform/data-platform-logs.md)





### <a name="containers"></a>Kontejnery
- [Postup povolení Azure monitor pro kontejnery](insights/container-insights-onboard.md) – aktualizovaná konfigurační tabulka brány firewall
- [Postup aktualizace Azure monitor pro kontejnery pro metriky](insights/container-insights-update-metrics.md) – aktualizace pro shromažďování metrik pomocí spravovaných identit
- [Náklady na monitorování Azure monitor pro kontejnery](insights/container-insights-cost.md) – nový článek.
- [Nastavte Azure monitor pro kontejnery živá data (Preview)](insights/container-insights-livedata-setup.md) – podpora nové vazby role clusteru.

### <a name="insights"></a>Insights
- [Azure monitor pro Azure cache pro Redis (Preview)](insights/redis-cache-insights-overview.md) – nový článek.
- [Monitorování Key Vault pomocí Azure monitor pro Key Vault (Preview)](./insights/key-vault-insights-overview.md) – nový článek

### <a name="logs"></a>Protokoly
- [Vytvoření & konfigurace Log Analytics pomocí prostředí PowerShell](platform/powershell-workspace-configuration.md) – přidání části Poradce při potížích.
- V části věnované odstraňování potíží s [Azure Portal vytvořte Log Analytics pracovní prostor](learn/quick-create-workspace.md) .
- [Vytvoření pracovního prostoru Log Analytics pomocí Azure CLI](learn/quick-create-workspace-cli.md) – Přidání oddílu pro odstraňování potíží
- [Odstraní a obnoví Azure Log Analytics Workspace](platform/delete-workspace.md) – aktualizované informace o obnovení odstraněného pracovního prostoru.
- [Funkce v Azure Monitorch dotazech protokolu](log-query/functions.md) – odebraná Poznámka o funkcích, které neobsahují jiné funkce.
- [Struktura Azure monitor v protokolech](./platform/data-platform-logs.md) – popis vlastností pro Application Insights tabulku
- [Použijte protokol Azure monitor s Azure Logic Apps a](platform/logicapp-flow-connector.md) omezením automatizace, které jste přidali v části.
- [Pomocí PowerShellu můžete vytvořit a nakonfigurovat Log Analytics pracovní prostor](platform/powershell-workspace-configuration.md) – přidání části řešení potíží.


### <a name="metrics"></a>Metriky
- [Azure monitor podporované metriky podle typu prostředku](platform/metrics-supported.md) – vyjasněné metriky hostů a směrování metrik. 

### <a name="solutions"></a>Řešení
- [Optimalizujte prostředí Active Directory pomocí Azure Monitored](insights/ad-assessment.md) Windows serveru 2019 na podporované verze.
- [Optimalizujte SQL Server prostředí pomocí Azure monitor](insights/sql-assessment.md) přidaných do podporovaných verzí SQL Server.


### <a name="virtual-machines"></a>Virtuální počítače
- [Povolit Azure monitor pro virtuální počítače přehled](insights/vminsights-enable-overview.md) – přidáno do podporovaných verzí serveru Ubuntu Přidání podporovaných oblastí pro pracovní prostor Log Analytics
- [Jak vynést výkon grafu pomocí Azure monitor pro virtuální počítače](insights/vminsights-performance.md) – omezení pro nedostupné metriky.

### <a name="visualizations"></a>Vizualizace
- [Azure monitor sešity a šablony Azure Resource Manager](platform/workbooks-automate.md) přidány správce prostředků aktualizace pro nasazení šablony sešitu.
- [Azure monitor sešity skupiny](platform/workbooks-groups.md) – nový článek.
- [Sešity Azure monitor – transformují data JSON pomocí JSONPath](platform/workbooks-jsonpath.md) – nový článek.


## <a name="april-2020"></a>Duben 2020

### <a name="general"></a>Obecné

- Azure Monitor oddílem přidaných uživatelem [spravovaný klíč](platform/customer-managed-keys.md) na asynchronních operacích
- [Správa pracovních prostorů Log Analytics v](platform/manage-access.md) oddílech s aktualizovanými vlastními protokoly Azure monitor.

### <a name="alerts"></a>Výstrahy

- [Pravidla akcí pro výstrahy Azure monitor](platform/alerts-action-rules.md) – přidané video
- [Přehled upozorňování a monitorování oznámení v Azure](platform/alerts-overview.md) – přidané video

### <a name="application-insights"></a>Application Insights

- [Mapa aplikace v Azure Application Insights](app/app-map.md) – přidala se konfigurace názvů cloudových rolí pro agenta Java.
- [Reference k rozhraní API agenta Azure Application Insights .NET](app/status-monitor-v2-api-reference.md) – konsolidovaná Reference k rozhraní API.
- [IP adresy, které používá Application Insights a Log Analytics](app/ip-addresses.md) -aktualizované IP adresy pro rozhraní API pro App Insights a Log Analytics, Webhooky skupin akcí, Azure USA státní správy.
- [Sledujte aplikace Java kdekoli](app/java-standalone-config.md) – nový článek.
- [Monitorujte aplikace Java v jakémkoli prostředí](app/java-in-process-agent.md) – nový článek.
- [Monitorujte aplikace Java spuštěné v jakémkoli prostředí](app/java-standalone-arguments.md) – nový článek.
- [Monitorujte aplikace Java běžící na místním](app/java-on-premises.md) a novém článku.
- [Odebrání Application Insights v aplikaci Visual Studio](app/remove-application-insights.md) – nový článek.
- [Vzorkování telemetrie v Azure Application Insights](app/sampling.md) – oprava v ukázce s pevnou sazbou v Pythonu.

### <a name="containers"></a>Kontejnery

- [Nakonfigurujte Azure Red Hat OpenShift v4. x s Azure monitor for Containers](insights/container-insights-azure-redhat4-setup.md) – nový článek.
- [Ruční oprava problémů s synchronizací ServiceNow](platform/itsmc-resync-servicenow.md) – nový článek
- [Postup zastavení monitorování clusteru Azure a Red Hat OpenShift v4](insights/container-insights-optout-openshift-v4.md) – nový článek
- [Postup zastavení monitorování clusteru Azure Red Hat OpenShift V3](insights/container-insights-optout-openshift-v3.md) – nový článek
- [Postup zastavení monitorování hybridního Kubernetes clusteru](insights/container-insights-optout-hybrid.md) – nový článek

### <a name="insights"></a>Insights

- [Monitorujte trezory klíčů Azure pomocí Azure monitor pro trezory klíčů (Preview)](insights/key-vault-insights-overview.md) – nový článek.

### <a name="logs"></a>Protokoly

- [Omezení služby Azure monitor](service-limits.md) – přidaná uživatelská omezení uživatelských dotazů.
- [Správa využití a nákladů na protokoly Azure monitor](platform/manage-cost-storage.md) – pro clustery se přidávají poplatky za protokoly. Přidaný dotaz Kusto, který umožňuje zákazníkům se staršími cenovými úrovněmi na základě počtu uzlů určit, jestli se mají přesunout na úroveň rezervace za GB nebo kapacitu.

### <a name="metrics"></a>Metriky

- [Pokročilé funkce](platform/metrics-charts.md) oddílu agregace služby Azure Průzkumník metrik – přidáno

### <a name="workbooks"></a>Workbooks

- [Azure monitor sešitů a šablon Azure Resource Manager](platform/workbooks-automate.md) přidány šablony Správce prostředků pro nasazení šablony sešitu.

## <a name="march-2020"></a>Březen 2020

### <a name="general"></a>Obecné

- [Přehled Azure monitor](overview.md) – přidání Azure monitor přehledu videa
- [Azure monitor konfiguraci klíče spravovaného zákazníkem](platform/customer-managed-keys.md) – obecné aktualizace.
- [Referenční informace o Azure monitor dat](/azure/azure-monitor/reference/) – nový web

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
- [Podpora zdrojového mapování pro aplikace JavaScriptu – Azure Monitor Application Insights](app/source-map-support.md) – nový článek

### <a name="containers"></a>Kontejnery

- [Nejčastější dotazy Azure monitor](faq.md) – aktualizace pro Azure monitor pro kontejnery.
- [Nakonfigurujte monitorování GPU pomocí Azure monitor for Containers](insights/container-insights-gpu-monitoring.md) – nový článek.

### <a name="insights"></a>Insights

- [Řešení pro správu Office 365 v Azure](insights/solution-office-365.md) – aktualizované datum vyřazení

### <a name="logs"></a>Protokoly

- [Optimalizujte dotazy protokolu v Azure monitor](log-query/query-optimization.md) stav procesoru pro analýzu XML a JSON.
- [Odstranění a obnovení Azure Log Analytics pracovního prostoru](platform/delete-workspace.md) – přidání řešení potíží
- [Použijte protokoly Azure monitor s využitím Azure Logic Apps a automatizace](platform/logicapp-flow-connector.md) – Aktualizováno pro nové konektory Azure monitor.

### <a name="metrics"></a>Metriky

- [Nepoužívané metriky disku v Azure Portal](platform/portal-disk-metrics-deprecation.md) – nový článek.
- [Kurz – vytvoření grafu metrik ve Azure monitor](learn/tutorial-metrics-explorer.md) – přidáno video

### <a name="platform-logs"></a>Protokoly platformy

- [Shromažďování a analýza protokolu aktivit Azure v Azure monitor](./platform/activity-log.md) přepsání pro lepší vysvětlení shromažďování protokolu aktivit s nastavením diagnostiky.

### <a name="virtual-machines"></a>Virtuální počítače

- [Monitorujte virtuální počítače Azure pomocí Azure monitor](insights/monitor-vm-azure.md) – nový článek.
- [Rychlý Start: monitorování virtuálních počítačů Azure pomocí Azure monitor](learn/quick-monitor-azure-vm.md) -aktualizovaných k přidání Azure monitor pro virtuální počítače
- [Výstrahy z Azure monitor pro virtuální počítače](insights/vminsights-alerts.md) – nový článek.
- [Povolit Azure monitor pro virtuální počítače přehled](insights/vminsights-enable-overview.md) – aktualizované odkazy na stažení agenta.

Obecné aktualizace pro obecnou dostupnost Azure Monitor pro virtuální počítače

- [Co je Azure Monitor pro virtuální počítače?](insights/vminsights-overview.md)
- [Nejčastější dotazy k Azure Monitor pro virtuální počítače (GA)](insights/vminsights-ga-release-faq.md) 
- [Povolení Azure Monitor pro virtuální počítače pomocí Azure Policy](./insights/vminsights-enable-policy.md) 
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
- [Ukládání a zobrazení diagnostických dat v Azure Storage](../cloud-services/diagnostics-extension-to-storage.md) – kompletně přepsáno a aktualizováno.
- [Log Analytics rozšíření pro virtuální počítače pro Windows](../virtual-machines/extensions/oms-windows.md) – lépe upřesňuje vztahy s agentem Log Analytics.
- [Azure monitor rozšíření pro virtuální počítače pro Linux](../virtual-machines/extensions/oms-linux.md) – lépe upřesňuje vztahy s agentem Log Analytics.

### <a name="application-insights"></a>Application Insights

- [Připojovací řetězce v Azure Application Insights](app/sdk-connection-string.md) – nový článek.

### <a name="insights-and-solutions"></a>Přehledy a řešení

#### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery

- [Integrace Azure Active Directory se službou Azure Kubernetes](../aks/azure-ad-integration-cli.md) – přidání poznámky k vytvoření klientské aplikace pro podporu Kubernetes clusteru s POVOLENým RBAC pro podporu Azure monitor pro kontejnery.

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
- [Vytvořte výstrahy výkonu pro Azure monitor pro kontejnery](./insights/container-insights-log-alerts.md) – revidované informace a aktualizované kroky pro vytvoření výstrahy pro data o výkonu uložené v pracovním prostoru pomocí výstrah kontextu v pracovním prostoru.
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
- [Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat šablonu Log Analytics pracovní prostor](./samples/resource-manager-workspace.md) – aktualizovaná šablona s novými cenovými úrovněmi.

### <a name="platform-logs"></a>Protokoly platformy

- [Shromažďování protokolů aktivit Azure s nastavením diagnostiky – Azure monitor](./platform/activity-log.md) – Další informace o změněných vlastnostech.
- [Exportujte protokol aktivit Azure](./platform/activity-log.md#legacy-collection-methods) – Aktualizováno pro změny uživatelského rozhraní. 

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
- [ Odkaz naApplicationInsights.config –](app/configuration-with-applicationinsights-config.md) ukázkový kód aktualizovaný službou Azure.
- [Automatizace Application Insights Azure pomocí PowerShellu](app/powershell.md) – aktualizace šablony Správce prostředků
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

- [Azure monitor pro kontejnery často kladené otázky](./faq.md) – přidání otázky do polí obrázek a název.
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

- [Archivace protokolů prostředků Azure do účtu úložiště](./platform/resource-logs.md#send-to-azure-storage)
- [Schéma událostí protokolu aktivit Azure](platform/activity-log-schema.md)
- [Omezení služby Azure Monitor](service-limits.md)
- [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics](./platform/activity-log.md)
- [Shromažďování protokolů aktivit Azure s nastavením diagnostiky (Preview) – Azure Monitor](./platform/activity-log.md)
- [Shromažďování protokolů aktivit Azure do pracovního prostoru Log Analytics napříč klienty Azure](./platform/activity-log.md)
- [Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Vytvoření nastavení diagnostiky v Azure pomocí šablony Správce prostředků](./samples/resource-manager-diagnostic-settings.md)
- [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik v Azure](platform/diagnostic-settings.md)
- [Exportovat protokol aktivit Azure](./platform/activity-log.md#legacy-collection-methods)
- [Přehled protokolů platformy Azure](platform/platform-logs-overview.md)
- [Streamování dat monitorování Azure do centra událostí](platform/stream-monitoring-data-event-hubs.md)
- [Streamování protokolů platformy Azure do centra událostí](./platform/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Rychlé starty a kurzy

- [Vytvoření grafu metrik v Azure monitor](learn/tutorial-metrics-explorer.md) – nový článek
- [Shromažďovat protokoly prostředků z prostředku Azure a analyzovat je pomocí Azure monitor](learn/tutorial-resource-logs.md) – nový článek.
- [Monitorujte prostředek Azure pomocí Azure monitor](learn/quick-monitor-azure-resource.md) – nový článek.
   
## <a name="next-steps"></a>Další kroky

- Pokud se chcete přispívat k dokumentaci Azure Monitor, přečtěte si [příručku pro přispěvatele docs](/contribute/).