---
title: Co je nového v dokumentaci k Azure Monitor
description: Důležité aktualizace Azure Monitor dokumentace se každý měsíc aktualizovaly.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: b21e5ea8e25844e3e025915b0b9f15162c642f25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713435"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Co je nového v dokumentaci k Azure Monitor?

Tento článek obsahuje seznam Azure Monitorch článků, které jsou buď nové, nebo byly významně aktualizované. Bude aktualizován první týden v měsíci, aby zahrnoval aktualizace článků z předchozího měsíce.

## <a name="january-2021"></a>Leden 2021 

### <a name="general"></a>Obecné 
- [Azure monitor časté otázky](faq.md) – přidalo se zadání informací o zařízení pro Application Insights.
### <a name="agents"></a>Agenti  
- [Shromažďování událostí trasování událostí pro Windows (ETW) pro analýzu Azure monitor protokoly](./agents/data-sources-event-tracing-windows.md) – nový článek.
- [Pravidla shromažďování dat ve Azure monitor (Preview)](./agents/data-collection-rule-overview.md) – přidalo se odkazy na ukázky PowerShellu a rozhraní příkazového řádku.

### <a name="alerts"></a>Výstrahy  
- [Nakonfigurujte Azure pro připojení nástrojů ITSM pomocí zabezpečeného exportu](./alerts/itsm-connector-secure-webhook-connections-azure-configuration.md) a nového článku.
- [Chyby stavu konektoru na řídicím panelu ITSMC](./alerts/itsmc-dashboard-errors.md) – nový článek
- [Prozkoumejte chyby pomocí řídicího panelu ITSMC](./alerts/itsmc-dashboard.md) – nový článek.
- [Řešení potíží s upozorněními na metriky Azure](./alerts/alerts-troubleshoot-metric.md) – přidané oddíly pro dynamické prahové hodnoty.
- [Řešení problémů v oddělení IT Service Management Connector](./alerts/itsmc-troubleshoot-overview.md) – nový článek

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
- [Azure monitor klíč spravovaný zákazníkem](./logs/customer-managed-keys.md) – zavádí spravovanou identitu přiřazenou uživatelem.
- [Azure monitor protokoluje vyhrazené clustery](./logs/logs-dedicated-clusters.md) – kód aktualizované odezvy.
- [Dotaz na různé služby – Azure monitor a Azure Průzkumník dat (Preview)](/azure/azure-monitor/platform/azure-data-explorer-monitor-cross-service-query) – nový článek

### <a name="metrics"></a>Metriky
- Metrika metrik Azure Monitor metriky [a zobrazení je vysvětleno](./essentials/metrics-aggregation-explained.md) – nový článek.

### <a name="platform-logs"></a>Protokoly platformy
- [Protokoly prostředků Azure monitor podporují služby a kategorie](./essentials/resource-logs-categories.md) – nový článek.

### <a name="visualizations"></a>Vizualizace
- [Zdroje dat Azure monitor sešity](./visualize/workbooks-data-sources.md) – Přidali jsme sloučení a analýzu změn.


## <a name="december-2020"></a>Prosinec 2020

### <a name="general"></a>Obecné
- Azure Monitor chybových zpráv přidaných [zákazníkem spravovaný klíč](logs/customer-managed-keys.md) .
- [Partneři, kteří se integrují s](partners.md) oddílem Azure monitor-added v integraci centra událostí.

### <a name="agents"></a>Agenti
- [Dotazování na více prostředků v Azure Průzkumník dat pomocí Azure monitor](logs/azure-monitor-data-explorer-proxy.md) -New article.
- [Přehled agentů monitorování Azure](agents/agents-overview.md) – přidala se podpora Oracle 8.

### <a name="alerts"></a>Výstrahy
- [Řešení potíží s upozorněními na metriky Azure](alerts/alerts-troubleshoot-metric.md) – přidání potíží pro dynamické prahové hodnoty.
- [IT Service Management Connector v Log Analytics](alerts/itsmc-definition.md) – nový článek.
- [Přehled služby IT Service Management Connector](alerts/itsmc-overview.md) – restrukturované informace pro řešení potíží.
- [Připojení Cherwell ke službě IT Service Management Connector](alerts/itsmc-connections-cherwell.md) – nový článek
- [Připojení k prokázání pomocí konektoru pro správu IT služby](alerts/itsmc-connections-provance.md) – nový článek
- [Připojení SCSM ke službě IT Service Management Connector](alerts/itsmc-connections-scsm.md) – nový článek
- [Připojení ServiceNow ke službě IT Service Management Connector](alerts/itsmc-connections-servicenow.md) – nový článek
- [Ruční oprava problémů s synchronizací ServiceNow](alerts/itsmc-resync-servicenow.md) – restrukturované informace pro řešení potíží




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
- [Sestavy v kontejneru Insights](insights/container-insights-reports.md) – nový článek.

### <a name="logs"></a>Protokoly
- [Azure monitor protokolovat vyhrazené clustery](logs/logs-dedicated-clusters.md) – přidané automatizované příkazy, metody odpojování a odebírání a odstraňování potíží.
- [Dotaz na křížovou službu mezi Azure monitor a Azure Průzkumník dat (Preview)](logs/azure-data-explorer-monitor-cross-service-query.md) – nový článek
- [Export dat pracovního prostoru Log Analytics v Azure monitor (Preview)](logs/logs-data-export.md) – přidány šablony ARM.

### <a name="metrics"></a>Metriky
- [Pokročilé funkce Azure Průzkumník metrik](essentials/metrics-charts.md) – přidané informace o výběru oboru prostředků
- [Zobrazení více prostředků v Průzkumník metrik](essentials/metrics-dynamic-scope.md) – nový článek.

### <a name="networks"></a>Sítě
- [Řešení Azure Networking Analytics v Azure monitor](insights/azure-networking-analytics.md) přibyly informace o sešitu Network Insights.

### <a name="virtual-machines"></a>Virtual Machines
- [Povolí Azure monitor pro hybridní prostředí](vm/vminsights-enable-hybrid.md) – novou verzi agenta závislostí.


### <a name="visualizations"></a>Vizualizace
- [Vizualizace mapy sešitu Azure monitor](visualize/workbooks-map-visualizations.md) – nový článek
- [Azure monitor sešity Přineste si vlastní](visualize/workbooks-bring-your-own-storage.md) článek o novém úložišti.


## <a name="november-2020"></a>Listopad 2020

### <a name="general"></a>Obecné
- [Omezení služby Azure monitor](service-limits.md) – Aktualizováno pro podporu ARC Azure.

### <a name="agents"></a>Agenti
- [Přehled agentů monitorování Azure](agents/agents-overview.md) – aktualizované pro podporu ARC Azure
- [Nainstalujte agenta Azure monitor](agents/azure-monitor-agent-install.md) – nový článek.
- [Přehled agenta Azure monitor](agents/azure-monitor-agent-overview.md) – Aktualizováno pro podporu ARC Azure.
- [Ukázky šablon Správce prostředků pro agenty](agents/resource-manager-agent.md) – aktualizované pro podporu ARC Azure.

### <a name="alerts"></a>Výstrahy
- [Vytváření a Správa skupin akcí ve Azure Portal](alerts/action-groups.md) – byly přidány zdrojové IP adresy pro Webhooky.

### <a name="application-insights"></a>Application Insights
- [Monitorování aplikací s nekódováním kódu Java Azure Monitor Application Insights](app/java-in-process-agent.md) přidány příklad konfigurace.
- [Reakce modulu plug-in pro Application Insights JavaScript SDK](app/javascript-react-plugin.md) – přidání části na používání nereagujících zavěšení
- [Upgrade z Application Insights Java 2. x SDK](app/java-standalone-upgrade-from-2x.md) – nový článek.
- [Poznámky k verzi pro Microsoft. ApplicationInsights. SnapshotCollector](app/snapshot-collector-release-notes.md) -New article

### <a name="autoscale"></a>Automatické škálování
- [Začínáme s automatické škálováním v Azure](autoscale/autoscale-get-started.md) – Přidání oddílu o přesunutí automatického škálování do jiné oblasti.

### <a name="data-collection"></a>Shromažďování dat
- [Konfigurace shromažďování dat pro agenta Azure monitor (Preview)](agents/data-collection-rule-azure-monitor-agent.md) – Aktualizováno pro podporu ARC Azure
- [Pravidla shromažďování dat v Azure monitor (Preview)](agents/data-collection-rule-overview.md) – Aktualizováno pro podporu ARC Azure.
- [Ukázky šablon Správce prostředků pro pravidla shromažďování dat](agents/resource-manager-data-collection-rules.md) – nový článek

### <a name="insights-and-solutions"></a>Přehledy a řešení
- [Připojte Azure k nástrojům ITSM pomocí zabezpečeného exportu, který](alerts/it-service-management-connector-secure-webhook-connections.md) je přidaný v tématu připojení k ServiceNow.

### <a name="logs"></a>Protokoly
- [Integrujte Log Analytics a Excel](logs/log-excel.md) – nový článek.
- [Log Analytics zabezpečení dat](logs/data-security.md) – přidáno do části Další funkce zabezpečení.
- [Log Analytics integrace s Power BI](logs/log-powerbi.md) – nový článek.
- [Standardní sloupce v Azure monitor záznamy protokolu](logs/log-standard-columns.md) – přidáno _SubscriptionId sloupci.

Nové a aktualizované články z změny struktury obsahu dotazů protokolu

- [Kurz Log Analytics](logs/log-analytics-tutorial.md)
- [Dotazy protokolu v Azure Monitor](logs/log-query-overview.md)
- [Přehled Log Analytics v Azure Monitor](logs/log-analytics-overview.md)
- [Ukázky pro dotazy pro Azure Průzkumník dat a Azure Monitor](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Kurz: použití dotazů Kusto v Azure Průzkumník dat a Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Virtuální počítače

- [Povolení přehledu virtuálních počítačů – přehled](vm/vminsights-enable-overview.md) – přidané oblasti

Nové články pro stav hosta virtuálních počítačů Insights (Preview)

- [Stav hosta pro virtuální počítače Insights (Preview)](vm/vminsights-health-overview.md)
- [Upozornění na stav hosta pro virtuální počítače v Insights (Preview)](vm/vminsights-health-alerts.md)
- [Konfigurace monitorování ve stavu hosta virtuálních počítačů Insights (Preview)](vm/vminsights-health-configure.md)
- [Konfigurace monitorování ve stavu hosta virtuálních počítačů Insights pomocí pravidel shromažďování dat (Preview)](vm/vminsights-health-configure-dcr.md)
- [Povolit stav hosta virtuálního počítače Insights (Preview)](vm/vminsights-health-enable.md)
- [Řešení potíží se stavem hosta virtuálních počítačů (Preview)](vm/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>Říjen 2020

### <a name="general"></a>Obecné
- [Azure monitor vyřazení rozhraní API](logs/operationalinsights-api-retirement.md) – nový článek

### <a name="agents"></a>Agenti
- [Co se monitoruje pomocí Azure monitor](monitor-reference.md) -přidáno části v agentech.

### <a name="alerts"></a>Výstrahy
- [Vytváření a Správa skupin akcí v oddílu Azure Portal](alerts/action-groups.md) -added u značky služby.
- [Ukázky šablon Správce prostředků pro výstrahy metrik](alerts/resource-manager-alerts-metric.md) – přidaný parametr shody obsahu a umístění testů.
- [Řešení potíží s upozorněními na metriky Azure](alerts/alerts-troubleshoot-metric.md) – přidání osvědčeného postupu pro konfiguraci pravidel

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
- [Začínáme s automatické škálováním v Azure](autoscale/autoscale-get-started.md) – Přidání oddílu o přesunutí automatického škálování do jiné oblasti.

### <a name="containers"></a>Kontejnery
- [Nakonfigurujte monitorování PV pomocí kontejneru Insights](containers/container-insights-persistent-volumes.md) – nový článek.
- [Jak spravovat agenta služby Container Insights](containers/container-insights-manage-agent.md) – přidala se podpora pro cluster Kubernetes s povoleným ARC Azure.
- [Výstrahy metriky z kontejneru Insights](containers/container-insights-metric-alerts.md) – přidala se podpora pro cluster Kubernetes s podporou ARC Azure.

### <a name="insights-and-solutions"></a>Přehledy a řešení
- [IT Service Management Connector – v ServiceNow části Secure export in Azure monitor](alerts/it-service-management-connector-secure-webhook-connections.md) – added.

### <a name="logs"></a>Protokoly
- [Archivace dat z pracovního prostoru Log Analytics do Azure Storage pomocí aplikace logiky](logs/logs-export-logic-app.md) – nový článek
- [Export dat v pracovním prostoru Log Analytics v Azure monitor (Preview)](logs/logs-data-export.md) – Přidali jsme vzorový text pro žádost REST pro centrum událostí.
- [Spravujte využití a náklady na protokoly Azure monitor](logs/manage-cost-storage.md) – přibyly informace o vztahu mezi protokoly Azure Monitor a Azure Security Center fakturace. Přidání dotazu pro počty uzlů v případě použití cenové úrovně na uzel 
- [Monitoruje stav Log Analytics pracovního prostoru v Azure monitor](logs/monitor-workspace.md) – nový článek.
- [Dotazování dat v Azure monitor pomocí Azure Průzkumník dat (Preview)](logs/azure-data-explorer-monitor-proxy.md) – nový článek
- [Dotaz na exportovaná data z Azure monitor pomocí Azure Průzkumník dat (Preview)](logs/azure-data-explorer-query-storage.md) – nový článek

### <a name="networks"></a>Sítě
- [Azure monitor pro sítě Preview](insights/network-insights-overview.md) – část Poradce při potížích se přidala. Přidání části při připojení

### <a name="platform-logs"></a>Protokoly platformy
- [Schéma událostí protokolu aktivit Azure](essentials/activity-log-schema.md) – Přidal se popis úrovní závažnosti.

### <a name="virtual-machines"></a>Virtuální počítače
- [Změna analýzy v Azure monitor pro virtuální počítače](vm/vminsights-change-analysis.md) – nový článek
- [Povolit Azure monitor pro virtuální počítače přehled](vm/vminsights-enable-overview.md) – přidané oblasti
- [Postup aktualizace služby Container Insights pro metriky](containers/container-insights-update-metrics.md) – přidala se podpora pro cluster Kubernetes s podporou ARC Azure.



## <a name="september-2020"></a>Září 2020

### <a name="general"></a>Obecné
- [Azure monitor časté otázky](faq.md) – přidání části na OpenTelemetry

### <a name="agents"></a>Agenti
- [Přehled Azure Monitorho agenta](agents/azure-monitor-agent-overview.md) – pro přepnutí na nového agenta byly přidány rozhodovací faktory.
- [Přehled agentů monitorování Azure](agents/agents-overview.md) – přidala se podpora pro Windows 10.

### <a name="alerts"></a>Výstrahy
- [Vytvoření upozornění protokolu pomocí Azure Resource Manager šablonu](alerts/alerts-log-create-templates.md) – nový článek.
- [Řešení potíží s výstrahami Azure Metrics](alerts/alerts-troubleshoot-metric.md) – přidaný oddíl k exportu šablony ARM pro pravidlo upozornění metriky.

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
- [Konfigurace clusteru Kubernetes s povoleným kontejnerem Azure s využitím kontejnerových Insights](containers/container-insights-enable-arc-enabled-clusters.md) – Přidání pokynů pro povolení monitorování pomocí instančního objektu
- [Nasazení & hPa metriky s využitím kontejneru Insights](containers/container-insights-deployment-hpa-metrics.md) – nový článek.

### <a name="insights-and-solutions"></a>Přehledy a řešení
- [Azure monitor pro Azure cache pro Redis](insights/redis-cache-insights-overview.md) – odebrání označení verze Preview
- [Azure monitor pro sítě (Preview)](insights/network-insights-overview.md) – přidány části připojení a přenos.
- [IT Service Management Connector – zabezpečený export v Azure monitor](alerts/it-service-management-connector-secure-webhook-connections.md) – nový článek.
- [IT konektor pro IT služby v Azure monitor](alerts/itsmc-connections.md) -Note na ITSM integrace Cherwell a prov.
- [Monitorování Key Vault pomocí Azure monitor pro Key Vault](insights/key-vault-insights-overview.md) – odebrané označení Preview

### <a name="logs"></a>Protokoly
- [Dotazy auditu v Azure monitor dotazy protokolu](logs/query-audit.md) – nový článek.
- Azure Monitor bezpečnostního modulu přidávaného zákazníkem, který je [spravovaný klíčem](logs/customer-managed-keys.md)
- [Azure monitor protokoluje vyhrazené clustery](logs/logs-dedicated-clusters.md) – nový článek.
- [Návrh Azure Monitorch protokolů nasazení](logs/design-logs-deployment.md) – aktualizace škálování a limitu míry přijímání pro ingestování
- [Rozsah dotazu protokolu v Azure Monitor Log Analytics](logs/scope.md) – aktualizace, které zahrnují aplikace založené na pracovních prostorech.
- [Přihlašuje Azure monitor](logs/data-platform-logs.md) – aktualizace, aby zahrnovaly aplikace založené na pracovních prostorech.
- [Standardní sloupce v Azure monitor protokolu záznamů](logs/log-standard-columns.md) – aktualizace, které zahrnují aplikace založené na pracovních prostorech.
- [Omezení služby Azure monitor](service-limits.md) – byla aktualizována omezení pro omezování uživatelských dotazů.
- [Použití účtů úložiště spravovaných zákazníkem ve Azure monitor přepsané log Analyticsm](logs/private-storage.md) článku.
- [Zobrazení a analýza dat v Azure Log Analytics](./logs/data-platform-logs.md) – aktualizace, které zahrnují aplikace založené na pracovních prostorech.


### <a name="platform-logs"></a>Protokoly platformy
- [Schéma událostí protokolu aktivit Azure – Azure monitor](essentials/activity-log-schema.md) – byly přidány úrovně závažnosti.
- [Ukázky šablon Správce prostředků pro nastavení diagnostiky](essentials/resource-manager-diagnostic-settings.md) – přidání ukázky pro účet úložiště Azure.

### <a name="visualizations"></a>Vizualizace
- [Vizualizace grafu Azure Monitorho sešitu](visualize/workbooks-chart-visualizations.md) – nový článek
- [Zobrazovací modul pro vykreslování kompozitních pruhů Azure monitor sešitu](visualize/workbooks-composite-bar.md) – nový článek
- [Vizualizace grafu Azure Monitorho sešitu](visualize/workbooks-graph-visualizations.md) – nový článek
- [Vizualizace mřížky sešitu Azure monitor](visualize/workbooks-grid-visualizations.md) – nový článek
- [Vizualizace v rámečku Azure monitorového sešitu medu](visualize/workbooks-honey-comb.md) – nový článek.
- [Vizualizace textu v sešitu Azure monitor](visualize/workbooks-text-visualizations.md) – nový článek
- [Vizualizace dlaždic Azure monitor sešitu](visualize/workbooks-tile-visualizations.md) – nový článek
- [Vizualizace stromu Azure monitorového sešitu](visualize/workbooks-tree-visualizations.md) – nový článek




## <a name="august-2020"></a>Srpen 2020

### <a name="general"></a>Obecné

- [Co se monitoruje Azure monitor](monitor-reference.md) – aktualizováno tak, aby zahrnovalo Azure monitor agenta.


### <a name="agents"></a>Agenti
- [Přehled agenta Azure monitor](agents/azure-monitor-agent-overview.md) – nový článek.
- [Povolí Azure monitor pro hybridní prostředí](vm/vminsights-enable-hybrid.md) – aktualizovaná verze agenta závislostí.
- [Přehled agentů monitorování Azure](agents/agents-overview.md) – přidání Azure monitor agenta a tabulky podpory konsolidovaného operačního systému


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Nové a aktualizované články z změny struktury obsahu agenta
- [Povolit přehled virtuálních počítačů Insights](vm/vminsights-enable-overview.md)
- [Instalace agenta Log Analytics na počítačích s Linuxem](agents/agent-linux.md)
- [Instalace agenta Log Analytics na počítačích s Windows](agents/agent-windows.md)
- [Přehled agenta Log Analytics](agents/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights pro webové aplikace v JavaScriptu](app/javascript.md) – přidaný oddíl s vysvětlením korelace a konfigurace klientského serveru pro korelaci CORS.
- [Vytvořte novou Azure Monitor Application Insights prostředky založené na pracovním prostoru](app/create-workspace-resource.md) , které nabízí aplikace založené na pracovních prostorech.
- [IP adresy, které používá Application Insights a Log Analytics](app/ip-addresses.md) -aktualizované IP adresy pro datový proud Live Metrics.
- [Monitorujte aplikace v jazyce Java ve všech prostředích Azure monitor tabulce Application Insightsed](app/java-in-process-agent.md) pro podporované vlastní telemetrie.
- [Nativní reakce na modul plug-in pro Application Insights JavaScript SDK](app/javascript-react-native-plugin.md) – nový článek
- [Reakce modulu plug-in pro Application Insights JavaScript SDK](app/javascript-react-plugin.md) – nový článek
- [Ukázka šablony Správce prostředků pro vytváření aplikací služby Azure Functions pomocí Application Insights monitoring](app/resource-manager-function-app.md) – nový článek.
- [Ukázky správce prostředků šablon pro vytváření webových aplikací Azure App Services pomocí Application Insights monitoring](app/resource-manager-web-app.md) – nový článek.
- [Analýza využití pomocí Azure Application Insights](app/usage-overview.md) – přidané video

### <a name="autoscale"></a>Automatické škálování
- [Začínáme s AutoScale v Azure](autoscale/autoscale-get-started.md) – Přidání oddílu o směrování na instance v pořádku pro App Service.

### <a name="data-collection"></a>Shromažďování dat
- [Nakonfigurujte shromažďování dat pro agenta Azure monitor (Preview)](agents/data-collection-rule-azure-monitor-agent.md) – nový článek.
- [Pravidla shromažďování dat v Azure monitor (Preview)](agents/data-collection-rule-overview.md) – nový článek.


### <a name="containers"></a>Kontejnery
- [Nasazení & hPa metriky s využitím kontejneru Insights](containers/container-insights-deployment-hpa-metrics.md) – nový článek.

### <a name="insights"></a>Insights
- [Monitorování řešení v Azure monitor](insights/solutions.md) – Aktualizováno pro nové uživatelské rozhraní.
- [Řešení Network Performance Monitor v Azure](insights/network-performance-monitor.md) – přidání podporovaných oblastí pracovního prostoru


### <a name="logs"></a>Protokoly
- [Azure monitor časté otázky](faq.md) – Přidali jsme položku k odstranění dat z pracovního prostoru. Byla přidána položka pro odpovědi 502 a 503.
  - [Návrh Azure monitor protokolů nasazení](logs/design-logs-deployment.md) – aktualizace a omezení rychlosti pro ingestování
- [Spravujte využití a náklady na protokoly Azure monitor](logs/manage-cost-storage.md) – aktualizované dotazy na použití se aktualizují na efektivnější formát dotazů.
- [Optimalizujte dotazy protokolu v Azure monitor](logs/query-optimization.md) -přidány konkrétní hodnoty k ukazatelům výkonu.
- [Ukázky šablon Správce prostředků pro nastavení diagnostiky](essentials/resource-manager-diagnostic-settings.md) – přidání ukázky pro protokoly auditu dotazů protokolu


### <a name="platform-logs"></a>Protokoly platformy
- [Vytvoření nastavení diagnostiky pro odesílání protokolů a metrik platforem do různých cílů](essentials/diagnostic-settings.md) – přidávají se místní požadavky na nastavení diagnostiky.

### <a name="visualizations"></a>Vizualizace
- [Přehled sešitů Azure monitor](visualize/workbooks-overview.md) – přidáno video
- [Přesuňte šablonu sešitu Azure do jiné oblasti](visualize/workbook-templates-move-region.md) – nový článek.
- [Přesunutí sešitu Azure do jiné oblasti](visualize/workbooks-move-region.md) – nový článek



## <a name="july-2020"></a>Červenec 2020

### <a name="general"></a>Obecné
- [Nasaďte Azure monitor](deploy-scale.md) -restrukturujte obsah připojování virtuálních počítačů s přehledy.
- [Použití privátního odkazu Azure k bezpečnému propojení sítí s Azure Monitormi](logs/private-link-security.md) přidanými částmi na omezeních.

### <a name="alerts"></a>Výstrahy
- [Pravidla akcí pro Azure monitor výstrahy](alerts/alerts-action-rules.md) – přidání procesů CLI.
- [Vytvoření a Správa skupin akcí v Azure Portal](alerts/action-groups.md) – aktualizuje se, aby odrážely změny v uživatelském rozhraní.
- [Příklady dotazů v Azure Monitor Log Analytics](logs/example-queries.md) – nový článek.
- [Vyřešte výstrahy protokolu v části Azure monitor](alerts/alerts-troubleshoot-log.md) -added na kvótě pravidla výstrahy.
- [Řešení potíží s výstrahami Azure metrik](alerts/alerts-troubleshoot-metric.md) – Přidal se oddíl na pravidlo výstrahy na vlastní metriku, která ještě není vysílaná.
- [Pochopte, jak budou výstrahy metriky fungovat v Azure Monitor.](alerts/alerts-metric-overview.md) – Bylo přidáno doporučení pro výběr členitosti agregace.

### <a name="application-insights"></a>Application Insights
- [Poznámky k verzi pro rozšíření webové aplikace Azure – Application Insights](app/web-app-extension-release-notes.md) – nový článek.
- [Příklady šablon Správce prostředků pro prostředky Application Insights](app/resource-manager-app-resource.md) – nový článek.
- [Řešení potíží se službou Azure Application Insights Profiler](app/profiler-troubleshooting.md) – přidání poznámky k chybě běžící v profileru pro ASP.NET Core aplikace na Azure App Service. 

### <a name="containers"></a>Kontejnery
- [Protokolujte výstrahy z kontejneru Insights](containers/container-insights-log-alerts.md) – nový článek.
- [Upozornění na metriky z kontejneru Insights](containers/container-insights-metric-alerts.md) – nový článek

### <a name="logs"></a>Protokoly
- Azure Monitor chybovou zprávu pro přidání [klíčového zákazníka](logs/customer-managed-keys.md) a část konfigurace pro dotazy pro CMK.
- [Azure monitor rozhraní API kolekce dat http](logs/data-collector-api.md) – přidala se ukázka Python 3.
- [Optimalizujte dotazy protokolu v části Azure monitor](logs/query-optimization.md) -added, které vám pomají při použití dílčích dotazů k vyhnutí více prohledávání dat.
- [Kurz: Začínáme s Log Analytics dotazy](./logs/log-analytics-tutorial.md) – přidané video.

### <a name="platform-logs"></a>Protokoly platformy
- [Vytvořte nastavení diagnostiky pro odesílání protokolů a metrik platforem do různých cílů](essentials/diagnostic-settings.md) – přidané video.
- [Ukázky šablon Správce prostředků pro Azure monitor](/resource-manager-samples.md) přidána ukázka ARM pomocí typu cílového cíle. 

### <a name="solutions"></a>Řešení
- [Řešení monitorování v Azure monitor](insights/solutions.md) přidávají procesy CLI.
- [Řešení pro správu Office 365 v Azure](insights/solution-office-365.md) – změněné datum vyřazení

### <a name="virtual-machines"></a>Virtuální počítače

Nové a aktualizované články z změny struktury obsahu pro službu VM Insights

- [Co je to přehledy virtuálních počítačů?](vm/vminsights-overview.md)
- [Konfigurace pracovního prostoru Log Analytics pro virtuální počítač Insights](vm/vminsights-configure-workspace.md)
- [Připojení počítačů se systémem Linux k Azure Monitor](agents/agent-linux.md)
- [Povolení Azure Monitor pro hybridní prostředí](vm/vminsights-enable-hybrid.md)
- [Povolit Azure Monitor pro jeden virtuální počítač nebo sadu škálování virtuálního počítače v Azure Portal](vm/vminsights-enable-portal.md)
- [Povolení přehledů virtuálních počítačů pomocí Azure Policy](./vm/vminsights-enable-policy.md)
- [Povolit přehled virtuálních počítačů Insights](vm/vminsights-enable-overview.md)
- [Povolení přehledů virtuálních počítačů pomocí PowerShellu](vm/vminsights-enable-powershell.md)
- [Povolení přehledů virtuálních počítačů pomocí šablon Správce prostředků](vm/vminsights-enable-resource-manager.md)
- [Povolení přehledů virtuálních počítačů pomocí PowerShellu nebo šablon](./vm/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Vizualizace
- [Upgrade vizualizací řídicího panelu Log Analytics](logs/dashboard-upgrade.md) – aktualizace obnovovací frekvence.
- [Vizualizace dat z Azure monitor](visualizations.md) – přidáno video


## <a name="june-2020"></a>Červen 2020

### <a name="general"></a>Obecné
- [Nasazení Azure monitor](deploy-scale.md) – nový článek
- Azure Monitor vlastnost billingtype se [správou klíčů spravovaných zákazníkem](logs/customer-managed-keys.md) . Byly přidány příkazy prostředí PowerShell.

### <a name="agents"></a>Agenti
- [Přehled agenta Log Analytics](agents/log-analytics-agent.md) – přidání požadavku Pythonu 2

### <a name="alerts"></a>Výstrahy
- [Jak aktualizovat pravidla upozornění nebo pravidla akcí, když se jejich cílový prostředek přesune do jiné oblasti Azure](alerts/alerts-resource-move.md) – nový článek.
- [Řešení potíží s upozorněními Azure Metrics](alerts/alerts-troubleshoot-metric.md) – nový článek
- [Řešení potíží s výstrahami protokolu v Azure monitor](alerts/alerts-troubleshoot-metric.md) – nový článek.
  
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
- [Postup zastavení monitorování hybridního Kubernetes clusteru](containers/container-insights-optout-hybrid.md) – přidáno pro Kubernetes s povoleným obloukem
- Umožňuje [nakonfigurovat cluster Kubernetes s povoleným kontejnerem Azure pomocí kontejneru Insights](containers/container-insights-enable-arc-enabled-clusters.md) – nový článek.
- [Nakonfigurujte Azure Red Hat OpenShift v4. x s kontejnerem Insights](containers/container-insights-azure-redhat4-setup.md) – aktualizované požadavky.
- [Nastavení živých dat ve službě Container Insights (Preview)](containers/container-insights-livedata-setup.md) – odebrání poznámky o funkci, která není dostupná ve vládě Azure USA.

### <a name="insights"></a>Insights
- Nejčastější dotazy [– řešení Network Performance Monitor v Azure](insights/network-performance-monitor-faq.md) – Nejčastější dotazy k monitorování ExpressRoute

### <a name="logs"></a>Protokoly
- [Odstraní a obnoví Azure Log Analytics Workspace](logs/delete-workspace.md) – Přidal se příkaz prostředí PowerShell. Aktualizace řešení potíží.
- [Správa pracovních prostorů Log Analytics v části Azure monitor](logs/manage-access.md) – příklad pro nepovolené tabulky v části Azure RBAC.
- [Spravujte využití a náklady na protokoly Azure monitor](logs/manage-cost-storage.md) – další podrobnosti o výpočtu velikosti dat. Aktualizace konfigurace výstrah datových svazků. Podrobnosti o datech zabezpečení shromažďovaných službou Azure Sentinel Objasnění datového zakončení.
- [Použijte protokoly Azure monitor s Azure Logic Apps a](logs/logicapp-flow-connector.md) pomocí omezení konektoru Power autoadded.

### <a name="metrics"></a>Metriky
- [Azure monitor podporované metriky podle typu prostředku](essentials/metrics-supported.md) – aktualizované metriky SQL Server.


### <a name="platform-logs"></a>Protokoly platformy

- [Správce prostředků ukázky šablon pro nastavení diagnostiky](essentials/resource-manager-diagnostic-settings.md) – oprava pro nastavení diagnostiky protokolu aktivit.
- [Odeslání protokolu aktivit Azure do Log Analytics pracovního prostoru pomocí Azure Portal](essentials/quick-collect-activity-log-portal.md) – nového článku
- [Odeslání protokolu aktivit Azure do Log Analytics pracovního prostoru pomocí Azure Resource Manager šablony](essentials/quick-collect-activity-log-arm.md) – nový článek.

Nové a aktualizované články z restrukturování a konsolidace obsahu protokolu platformy

- [Archivace protokolů prostředků Azure do účtu úložiště](./essentials/resource-logs.md#send-to-azure-storage)
- [Schéma událostí protokolu aktivit Azure](essentials/activity-log-schema.md)
- [Protokol aktivit Azure](essentials/activity-log.md)
- [Ukázky Azure Monitor CLI](/cli-samples.md)
- [Ukázky Azure Monitor PowerShellu](/powershell-samples.md)
- [Průvodce rozhraním REST API pro monitorování Azure](essentials/rest-api-walkthrough.md)
- [Protokoly prostředků Azure podporují služby a schémata.](./essentials/resource-logs-schema.md)
- [Protokoly prostředků Azure](essentials/resource-logs.md)
- [Shromažďování a analýza protokolu aktivit Azure v Azure Monitor](./essentials/activity-log.md)
- [Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Vytvoření nastavení diagnostiky pro odesílání metrik a protokolů platformy do různých cílů](essentials/diagnostic-settings.md)
- [Exportovat protokol aktivit Azure](./essentials/activity-log.md#legacy-collection-methods)
- [Přehled protokolů platformy Azure](essentials/platform-logs-overview.md)
- [Streamování protokolů platformy Azure do centra událostí](./essentials/resource-logs.md#send-to-azure-event-hubs)
- [Zobrazení událostí protokolu aktivit Azure v Azure Monitor](./essentials/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Virtuální počítače
- [Povolit přehledy o virtuálních počítačích v Azure Portal](./vm/vminsights-enable-portal.md) -aktualizováno tak, aby zahrnovaly Azure ARC.
- [Povolit přehled služby VM Insights](vm/vminsights-enable-overview.md) – aktualizováno tak, aby zahrnovalo Azure ARC.
- [Co je to přehledy virtuálních počítačů?](vm/vminsights-overview.md) – Aktualizováno tak, aby zahrnovalo Azure ARC.


### <a name="visualizations"></a>Vizualizace
- [Azure monitor sešity zdrojů dat](visualize/workbooks-data-sources.md) – přidány výstrahy a vlastní koncové body.
- [Řešení potíží s Azure monitor přehledy na základě sešitu](insights/troubleshoot-workbooks.md) – nový článek.
- [Upgrade vizualizací řídicího panelu Log Analytics](logs/dashboard-upgrade.md) – nový článek.



## <a name="may-2020"></a>Květen 2020

### <a name="general"></a>Obecné

- [Azure monitor Nejčastější dotazy](faq.md) – přidané oddíly pro metriky.
- [Azure monitor uživatelsky řízených](logs/customer-managed-keys.md) změn v přípravě na obecnou dostupnost.
- [Předdefinované definice zásad pro Azure monitor](./policy-reference.md) – nový článek.
- [Účty úložiště vlastněné zákazníkem pro](logs/private-storage.md) ingestování protokolů – nový článek
- [Správa využití a nákladů na protokoly Azure monitor](logs/manage-cost-storage.md) – přidaní poměrná fakturace clusteru
- [Pomocí privátního propojení Azure můžete bezpečně propojit sítě a Azure monitor](logs/private-link-security.md) – nový článek.


#### <a name="new-resource-manager-template-samples"></a>Nové ukázky šablon Správce prostředků 
- [Ukázky šablon Správce prostředků pro Azure Monitor](/resource-manager-samples.md)
- [Ukázky šablon Správce prostředků pro skupiny akcí](alerts/resource-manager-action-groups.md)
- [Ukázky šablon Správce prostředků pro agenty](agents/resource-manager-agent.md)
- [Ukázky šablon Správce prostředků pro službu Container Insights](containers/resource-manager-container-insights.md)
- [Ukázky šablon Správce prostředků pro virtuální počítač Insights](vm/resource-manager-vminsights.md)
- [Ukázky šablon Správce prostředků pro nastavení diagnostiky](essentials/resource-manager-diagnostic-settings.md)
- [Ukázky šablon Správce prostředků pro pracovní prostory Log Analytics](logs/resource-manager-workspace.md)
- [Ukázky šablon Správce prostředků pro dotazy protokolů](logs/resource-manager-log-queries.md)
- [Ukázky šablon Správce prostředků pro pravidla výstrah dotazů protokolu](alerts/resource-manager-alerts-log.md)
- [Ukázky šablon Správce prostředků pro pravidla upozornění metrik](alerts/resource-manager-alerts-metric.md)
- [Ukázky šablon Správce prostředků pro sešity](visualize/resource-manager-workbooks.md)

### <a name="agents"></a>Agenti
- [Instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](agents/diagnostics-extension-windows-install.md) – Přidání podrobností o konfiguraci diagnostiky.
- [Přehled agenta Log Analytics](agents/log-analytics-agent.md) – byly přidány podporované verze systému Linux.

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
- [výraz App () v Azure Monitorch dotazech protokolu](logs/app-expression.md)
- [Rozsah dotazu protokolu v Azure Monitor Log Analytics](logs/scope.md)
- [Dotazování napříč prostředky pomocí Azure Monitor](logs/cross-workspace-query.md)
- [Standardní vlastnosti v Azure Monitor záznamů protokolu](./logs/log-standard-columns.md)
- [Struktura protokolů Azure Monitor](./logs/data-platform-logs.md)





### <a name="containers"></a>Kontejnery
- [Jak povolit službu Container Insights](containers/container-insights-onboard.md) – aktualizovaná konfigurační tabulka brány firewall.
- [Postup aktualizace služby Container Insights pro metriky](containers/container-insights-update-metrics.md) – aktualizace pro shromažďování metrik pomocí spravovaných identit
- [Náklady na monitorování pro službu Container Insights](containers/container-insights-cost.md) – nový článek.
- [Nastavte dynamická data služby Container Insights (Preview)](containers/container-insights-livedata-setup.md) – podpora nové vazby role clusteru.

### <a name="insights"></a>Insights
- [Azure monitor pro Azure cache pro Redis (Preview)](insights/redis-cache-insights-overview.md) – nový článek.
- [Monitorování Key Vault pomocí Azure monitor pro Key Vault (Preview)](./insights/key-vault-insights-overview.md) – nový článek

### <a name="logs"></a>Protokoly
- [Vytvoření & konfigurace Log Analytics pomocí prostředí PowerShell](logs/powershell-workspace-configuration.md) – přidání části Poradce při potížích.
- V části věnované odstraňování potíží s [Azure Portal vytvořte Log Analytics pracovní prostor](logs/quick-create-workspace.md) .
- [Vytvoření pracovního prostoru Log Analytics pomocí Azure CLI](logs/quick-create-workspace-cli.md) – Přidání oddílu pro odstraňování potíží
- [Odstraní a obnoví Azure Log Analytics Workspace](logs/delete-workspace.md) – aktualizované informace o obnovení odstraněného pracovního prostoru.
- [Funkce v Azure Monitorch dotazech protokolu](logs/functions.md) – odebraná Poznámka o funkcích, které neobsahují jiné funkce.
- [Struktura Azure monitor v protokolech](./logs/data-platform-logs.md) – popis vlastností pro Application Insights tabulku
- [Použijte protokol Azure monitor s Azure Logic Apps a](logs/logicapp-flow-connector.md) omezením automatizace, které jste přidali v části.
- [Pomocí PowerShellu můžete vytvořit a nakonfigurovat Log Analytics pracovní prostor](logs/powershell-workspace-configuration.md) – přidání části řešení potíží.


### <a name="metrics"></a>Metriky
- [Azure monitor podporované metriky podle typu prostředku](essentials/metrics-supported.md) – vyjasněné metriky hostů a směrování metrik. 

### <a name="solutions"></a>Řešení
- [Optimalizujte prostředí Active Directory pomocí Azure Monitored](insights/ad-assessment.md) Windows serveru 2019 na podporované verze.
- [Optimalizujte SQL Server prostředí pomocí Azure monitor](insights/sql-assessment.md) přidaných do podporovaných verzí SQL Server.


### <a name="virtual-machines"></a>Virtuální počítače
- [Povolit přehled virtuálních počítačů v Insights](vm/vminsights-enable-overview.md) – přidáno k podporovaným verzím serveru Ubuntu Přidání podporovaných oblastí pro pracovní prostor Log Analytics
- [Postup vytvoření grafu výkonu s omezeními pro virtuální počítače s](vm/vminsights-performance.md) přidanými informacemi pro nedostupné metriky

### <a name="visualizations"></a>Vizualizace
- [Azure monitor sešity a šablony Azure Resource Manager](visualize/workbooks-automate.md) přidány správce prostředků aktualizace pro nasazení šablony sešitu.
- [Azure monitor sešity skupiny](./visualize/workbooks-groups.md) – nový článek.
- [Sešity Azure monitor – transformují data JSON pomocí JSONPath](visualize/workbooks-jsonpath.md) – nový článek.


## <a name="april-2020"></a>Duben 2020

### <a name="general"></a>Obecné

- Azure Monitor oddílem přidaných uživatelem [spravovaný klíč](logs/customer-managed-keys.md) na asynchronních operacích
- [Správa pracovních prostorů Log Analytics v](logs/manage-access.md) oddílech s aktualizovanými vlastními protokoly Azure monitor.

### <a name="alerts"></a>Výstrahy

- [Pravidla akcí pro výstrahy Azure monitor](alerts/alerts-action-rules.md) – přidané video
- [Přehled upozorňování a monitorování oznámení v Azure](alerts/alerts-overview.md) – přidané video

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

- [Konfigurace Azure Red Hat OpenShift v4. x s kontejnerem Insights](containers/container-insights-azure-redhat4-setup.md) – nový článek
- [Ruční oprava problémů s synchronizací ServiceNow](alerts/itsmc-resync-servicenow.md) – nový článek
- [Postup zastavení monitorování clusteru Azure a Red Hat OpenShift v4](containers/container-insights-optout-openshift-v4.md) – nový článek
- [Postup zastavení monitorování clusteru Azure Red Hat OpenShift V3](containers/container-insights-optout-openshift-v3.md) – nový článek
- [Postup zastavení monitorování hybridního Kubernetes clusteru](containers/container-insights-optout-hybrid.md) – nový článek

### <a name="insights"></a>Insights

- [Monitorujte trezory klíčů Azure pomocí Azure monitor pro trezory klíčů (Preview)](insights/key-vault-insights-overview.md) – nový článek.

### <a name="logs"></a>Protokoly

- [Omezení služby Azure monitor](service-limits.md) – přidaná uživatelská omezení uživatelských dotazů.
- [Správa využití a nákladů na protokoly Azure monitor](logs/manage-cost-storage.md) – pro clustery se přidávají poplatky za protokoly. Přidaný dotaz Kusto, který umožňuje zákazníkům se staršími cenovými úrovněmi na základě počtu uzlů určit, jestli se mají přesunout na úroveň rezervace za GB nebo kapacitu.

### <a name="metrics"></a>Metriky

- [Pokročilé funkce](essentials/metrics-charts.md) oddílu agregace služby Azure Průzkumník metrik – přidáno

### <a name="workbooks"></a>Workbooks

- [Azure monitor sešitů a šablon Azure Resource Manager](visualize/workbooks-automate.md) přidány šablony Správce prostředků pro nasazení šablony sešitu.

## <a name="march-2020"></a>Březen 2020

### <a name="general"></a>Obecné

- [Přehled Azure monitor](overview.md) – přidání Azure monitor přehledu videa
- [Azure monitor konfiguraci klíče spravovaného zákazníkem](logs/customer-managed-keys.md) – obecné aktualizace.
- [Referenční informace o Azure monitor dat](/azure/azure-monitor/reference/) – nový web

### <a name="alerts"></a>Výstrahy

- [Umožňuje vytvářet, zobrazovat a spravovat výstrahy protokolu aktivit v Azure monitor](alerts/alerts-activity-log.md) – další vysvětlení správce prostředků šablony.
- [Pochopte, jak budou výstrahy metriky fungovat v Azure Monitor.](alerts/alerts-metric-overview.md) – Aktualizováno pro státní podporu.
- [Řešení potíží s Azure monitor výstrahami a oznámeními](alerts/alerts-troubleshoot.md) – nový článek

### <a name="application-insights"></a>Application Insights

- [Automatizace Azure Application Insights pomocí prostředí PowerShell](app/powershell.md) – přidané příklady ARMClient
- [Průběžný export telemetrie z Application Insights](app/export-telemetry.md) – přidejte tabulku s podrobnostmi o exportní struktuře.
- [Povolí Snapshot debugger pro aplikace .NET v](app/snapshot-debugger-appservice.md) příkladu přidaných Azure App Service šablony Správce prostředků.
- [Spravujte využití a náklady na Azure Application Insights](app/pricing.md) – přibyly informace o upozornění na data Cap.
- [Monitorujte aplikace v Pythonu pomocí Azure monitor (Preview)](app/opencensus-python.md) – přidané standardní metriky.
- [Podpora zdrojového mapování pro aplikace JavaScriptu – Azure Monitor Application Insights](app/source-map-support.md) – nový článek

### <a name="containers"></a>Kontejnery

- [Nejčastější dotazy k Azure monitor](faq.md) – aktualizace pro službu Container Insights.
- [Konfigurace monitorování GPU pomocí kontejneru Insights](containers/container-insights-gpu-monitoring.md) – nový článek

### <a name="insights"></a>Insights

- [Řešení pro správu Office 365 v Azure](insights/solution-office-365.md) – aktualizované datum vyřazení

### <a name="logs"></a>Protokoly

- [Optimalizujte dotazy protokolu v Azure monitor](logs/query-optimization.md) stav procesoru pro analýzu XML a JSON.
- [Odstranění a obnovení Azure Log Analytics pracovního prostoru](logs/delete-workspace.md) – přidání řešení potíží
- [Použijte protokoly Azure monitor s využitím Azure Logic Apps a automatizace](logs/logicapp-flow-connector.md) – Aktualizováno pro nové konektory Azure monitor.

### <a name="metrics"></a>Metriky

- [Nepoužívané metriky disku v Azure Portal](essentials/portal-disk-metrics-deprecation.md) – nový článek.
- [Kurz – vytvoření grafu metrik ve Azure monitor](essentials/tutorial-metrics-explorer.md) – přidáno video

### <a name="platform-logs"></a>Protokoly platformy

- [Shromažďování a analýza protokolu aktivit Azure v Azure monitor](./essentials/activity-log.md) přepsání pro lepší vysvětlení shromažďování protokolu aktivit s nastavením diagnostiky.

### <a name="virtual-machines"></a>Virtuální počítače

- [Monitorujte virtuální počítače Azure pomocí Azure monitor](vm/monitor-vm-azure.md) – nový článek.
- [Rychlý Start: monitorování virtuálních počítačů Azure pomocí Azure monitor](vm/quick-monitor-azure-vm.md) -aktualizováno, aby se přidaly přehledy o virtuálních počítačích.
- [Výstrahy z VM Insights](vm/vminsights-alerts.md) – nový článek
- [Povolit přehled pro virtuální počítače](vm/vminsights-enable-overview.md) – aktualizované odkazy na stažení agenta

Obecné aktualizace pro obecnou dostupnost služby VM Insights

- [Co je to přehledy virtuálních počítačů?](vm/vminsights-overview.md)
- [Nejčastější dotazy k VIRTUÁLNÍm počítačům v Insights (GA)](vm/vminsights-ga-release-faq.md) 
- [Povolení přehledů virtuálních počítačů pomocí Azure Policy](./vm/vminsights-enable-policy.md) 
- [Postup vytvoření grafu výkonu pomocí přehledů VM](vm/vminsights-performance.md)
- [Dotazování protokolů z virtuálních počítačů Insights](vm/vminsights-log-search.md)
- [Zobrazení závislostí aplikace s využitím virtuálních počítačů Insights](vm/vminsights-maps.md) 

### <a name="visualizations"></a>Vizualizace

- [Vizualizace dat z Azure monitor](visualizations.md) -aktualizováno na poznámku plánované vyřazení návrháře zobrazení.

## <a name="february-2020"></a>Únor 2020

### <a name="agents"></a>Agenti

Více aktualizací jako součást přepisu obsahu diagnostického rozšíření.

- [Přehled agentů monitorování Azure](agents/agents-overview.md) – restrukturované tabulky pro lepší objasnění jedinečných funkcí každého agenta.
- [Přehled rozšíření Azure Diagnostics](agents/diagnostics-extension-overview.md) – dokončení přepisu
- [Použijte úložiště objektů BLOB pro službu IIS a úložiště tabulek pro události v Azure monitor](essentials/diagnostics-extension-logs.md) – obecné přepsání pro aktualizace a přehlednost.
- [Nainstalujte a nakonfigurujte rozšíření Windows Azure Diagnostics (WAD)](agents/diagnostics-extension-windows-install.md) – nový článek. 
- [Schéma rozšíření Windows Diagnostics](agents/diagnostics-extension-schema-windows.md) – změna uspořádání.
- [Posílání dat z rozšíření Windows Azure Diagnostics do Azure Event Hubs](agents/diagnostics-extension-stream-event-hubs.md) – kompletně přepsáno a aktualizováno.
- [Ukládání a zobrazení diagnostických dat v Azure Storage](../cloud-services/diagnostics-extension-to-storage.md) – kompletně přepsáno a aktualizováno.
- [Log Analytics rozšíření pro virtuální počítače pro Windows](../virtual-machines/extensions/oms-windows.md) – lépe upřesňuje vztahy s agentem Log Analytics.
- [Azure monitor rozšíření pro virtuální počítače pro Linux](../virtual-machines/extensions/oms-linux.md) – lépe upřesňuje vztahy s agentem Log Analytics.

### <a name="application-insights"></a>Application Insights

- [Připojovací řetězce v Azure Application Insights](app/sdk-connection-string.md) – nový článek.

### <a name="insights-and-solutions"></a>Přehledy a řešení

#### <a name="container-insights"></a>Přehledy kontejnerů

- [Integrace Azure Active Directory se službou Azure Kubernetes](../aks/azure-ad-integration-cli.md) – přidání poznámky k vytvoření klientské aplikace, která podporuje Kubernetes cluster s podporou RBAC pro podporu služby Container Insights.

#### <a name="vm-insights"></a>Přehledy virtuálních počítačů

- [Nejčastější dotazy k virtuálním počítačům (GA)](vm/vminsights-ga-release-faq.md) – Změna způsobu ukládání dat výkonu.

#### <a name="office-365"></a>Office 365

- [Řešení pro správu Office 365 v Azure](insights/solution-office-365.md) – aktualizované datum vyřazení


### <a name="logs"></a>Protokoly

- [Optimalizujte dotazy protokolu v Azure monitor](logs/query-optimization.md) – nový článek.
- [Spravujte využití a náklady na protokoly Azure monitor](logs/manage-cost-storage.md) – vylepšené Ukázkové dotazy vám pomůžou pochopit vaše využití.

### <a name="metrics"></a>Metriky

- [Azure monitor metriky platformy exportovatelné prostřednictvím nastavení diagnostiky](essentials/metrics-supported-export-diagnostic-settings.md) – Přidání oddílu při změně chování pro hodnoty null a nulové hodnoty.

### <a name="visualizations"></a>Vizualizace

Několik nových článků pro Průvodce převodem návrháře zobrazení na sešity.

- [Průvodce přechodem návrháře Azure monitor do sešitů](visualize/view-designer-conversion-overview.md) – nový článek.
- [Možnosti převodu Azure monitor návrháře zobrazení na sešity](visualize/view-designer-conversion-options.md) – nový článek
- [Azure monitor návrháře zobrazení v sešitech převod dlaždic](visualize/view-designer-conversion-tiles.md) – nový článek
- [Souhrn převodu a přístup k novému článku pro návrháře zobrazení Azure monitor na sešity](visualize/view-designer-conversion-access.md)
- [Azure monitor návrháře zobrazení na sešity převod běžných úkolů](visualize/view-designer-conversion-tasks.md) – nový článek
- [Příklady převodu Azure monitor návrháře zobrazení na sešity](visualize/view-designer-conversion-examples.md) – nový článek.

## <a name="january-2020"></a>Leden 2020

### <a name="general"></a>Obecné

- [Co je monitorované pomocí Azure Monitor?](monitor-reference.md) – Nový článek.

### <a name="agents"></a>Agenti

- [Shromažďovat data protokolu v](agents/log-analytics-agent.md) tabulce požadavků brány firewall pro službu Azure Log Analytics agent – aktualizované

### <a name="alerts"></a>Výstrahy

- [Vytváření a Správa skupin akcí ve Azure Portal](alerts/action-groups.md) – nastavení odebrané pro funkce v2, které už nejsou potřeba.
- [Vytvořte upozornění na metriku správce prostředků s](alerts/alerts-metric-create-templates.md) příkladem přidaným šablonou pro parametr *ignoreDataBefore* .  Přidali jsme omezení pro pravidla s více kritérii.
- [Použití Log Analytics výstrah REST API](alerts/api-alerts.md) – ukázka formátu JSON byla opravena.

### <a name="application-insights"></a>Application Insights

- [IP adresy, které používá Application Insights a Log Analytics](app/ip-addresses.md) – aktualizuje část test dostupnosti s postupem, jak přidat pravidlo příchozího portu pro povolení provozu pomocí skupin zabezpečení sítě Azure.
- [Řešení potíží se službou Azure Application Insights Profiler](app/profiler-troubleshooting.md) – aktualizace obecného řešení potíží
- [Vzorkování telemetrie v Azure Application Insights](app/sampling.md) – aktualizováno a restrukturalizace, aby se zlepšila čitelnost na základě zpětné vazby od zákazníků.

### <a name="data-security"></a>Zabezpečení dat

- [Azure monitor konfiguraci klíče spravovaného zákazníkem](logs/customer-managed-keys.md) – nový článek.

### <a name="insights-and-solutions"></a>Přehledy a řešení

#### <a name="container-insights"></a>Přehledy kontejnerů

- [Konfigurace shromažďování dat agenta Container Insights](containers/container-insights-agent-config.md) – přidaly se podrobnosti o upgradu agenta v Azure Red Hat OpenShift a přidaly se další informace k rozlišení metod pro upgrade agenta.
- [Vytváření výstrah výkonu pro službu Container Insights](./containers/container-insights-log-alerts.md) – revidované informace a aktualizované kroky pro vytvoření výstrahy týkající se dat výkonu uložených v pracovním prostoru pomocí výstrah kontextu v pracovním prostoru.
- [Kubernetes monitorování s využitím kontejnerů](containers/container-insights-analyze.md) – informace o podpoře clusterů Windows Kubernetes najdete v článku Přehled i v článku s informacemi o analýze.
- [Konfigurace clusterů Azure Red Hat OpenShift s využitím kontejnerů](containers/container-insights-azure-redhat-setup.md) – Přidání podrobností pro upgrade agenta na Azure Red Hat OpenShift a přidání dalších informací k odlišení metod pro upgrade agenta.
- [Konfigurace hybridních clusterů Kubernetes s](containers/container-insights-hybrid-setup.md) využitím služby Container Insights – aktualizováno tak, aby odrážely přidanou podporu pro zabezpečený port: 10250 s cAdvisor Kubelet.
- [Jak spravovat agenta služby Container Insights](containers/container-insights-manage-agent.md) – aktualizované podrobnosti související s chováním a konfigurací vyřazení metriky pomocí Azure Red Hat OpenShift ve srovnání s jinými typy clusterů Kubernetes.
- [Konfigurace integrace Prometheus Insights](containers/container-insights-prometheus-integration.md) – aktualizované podrobnosti související s chováním a konfigurací vyřazení metriky s využitím Azure Red Hat OpenShift v porovnání s jinými typy clusterů Kubernetes.
- [Postup aktualizace služby Container Insights pro metriky](containers/container-insights-update-metrics.md) – aktualizované podrobnosti související s chováním a konfigurací vyřazení metriky pomocí Azure Red Hat OpenShift ve srovnání s jinými typy clusterů Kubernetes.

#### <a name="vm-insights"></a>Přehledy virtuálních počítačů

- [Nejčastější dotazy k virtuálním počítačům (GA)](vm/vminsights-ga-release-faq.md) – přidané informace o tom, jak upgradovat pracovní prostor a agenty na novou verzi.

#### <a name="office-365"></a>Office 365

- [Řešení pro správu office 365 v Azure](insights/solution-office-365.md) – podrobnosti a nejčastější dotazy k migraci na řešení Office 365 v Azure Sentinel. Oddíl připojování se odebral.

### <a name="logs"></a>Protokoly

- [Správa pracovních prostorů Log Analytics v Azure monitor](logs/manage-access.md) – aktualizace akcí.
- [Spravujte využití a náklady na protokoly Azure monitor](logs/manage-cost-storage.md) – Přidali jsme do oddílu cenového modelu objasnění při výpočtu objemu dat.
- [Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat šablonu Log Analytics pracovní prostor](./logs/resource-manager-workspace.md) – aktualizovaná šablona s novými cenovými úrovněmi.

### <a name="platform-logs"></a>Protokoly platformy

- [Shromažďování protokolů aktivit Azure s nastavením diagnostiky – Azure monitor](./essentials/activity-log.md) – Další informace o změněných vlastnostech.
- [Exportujte protokol aktivit Azure](./essentials/activity-log.md#legacy-collection-methods) – Aktualizováno pro změny uživatelského rozhraní. 

## <a name="december-2019"></a>Prosinec 2019

### <a name="agents"></a>Agenti

- [Připojte počítače se systémem Linux k Azure monitor](agents/agent-linux.md) – nový článek.

### <a name="alerts"></a>Výstrahy

- [Vytvoření upozornění na metriku s](alerts/alerts-metric-create-templates.md) příkladem pro vlastní metriku správce prostředků přidány šablony
- [Vytváření výstrah s dynamickými mezními hodnotami v sekci Azure monitor](alerts/alerts-dynamic-thresholds.md) -added při interpretaci dynamických mezních grafů.
- [Přehled upozorňování a monitorování oznámení v Azure](alerts/alerts-overview.md) – aktualizovaný dotaz na diagram prostředků
- [Podporované prostředky pro výstrahy metriky v Azure monitor](alerts/alerts-metric-near-real-time.md) – podporuje se aktualizace metrik a dimenzí.
- [Přepne z rozhraní API upozornění starší verze Log Analytics do nového rozhraní API Azure Alerts](alerts/alerts-log-api-switch.md) – přidání poznámky k názvu změněné výstrahy.
- [Pochopte, jak budou výstrahy metriky fungovat v Azure Monitor.](alerts/alerts-metric-overview.md) – Byly přidány podporované typy prostředků pro monitorování ve velkém měřítku.

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

- V [kontejneru Insights byly často kladené otázky](./faq.md) – Přidali jsme otázku do polí obrázek a název.
- [Řešení Azure SQL Analytics v Azure monitor](insights/azure-sql.md) -aktualizované databázi čekají na podporu spravované instance.
- [Konfigurace shromažďování dat agenta Container Insights](containers/container-insights-agent-config.md) – přidání nastavení pro enrich_container_logs.
- [Konfigurace hybridních clusterů Kubernetes s využitím kontejneru Insights](containers/container-insights-hybrid-setup.md) – přidání potíží do oddílu pro odstraňování potíží
- [Monitorovat stav replikace služby Active Directory s aktualizací Azure monitor](insights/ad-replication-status.md) -.NET Framework.
- [Řešení Network Performance Monitor v Azure](insights/network-performance-monitor.md) – přidání podporovaných oblastí
- [Optimalizujte prostředí Active Directory pomocí Azure Monitor](insights/ad-assessment.md) .NET Framework aktualizace požadovaných součástí.
- [Optimalizujte SQL Server prostředí pomocí](insights/sql-assessment.md) aktualizovaného předpokladu Azure monitor .NET Framework.
- [Optimalizujte System Center Operations Manager prostředí pomocí](insights/scom-assessment.md) aktualizace požadovaných součástí Azure Log Analytics .NET Framework.
- [Podporovaná připojení ke službě IT Service Management Connector v Azure Log Analytics](alerts/itsmc-connections.md) – přidání nového York k požadovanému ID klienta a tajnému kódu klienta.

### <a name="logs"></a>Protokoly

- [Odstraní a obnoví Azure Log Analytics Workspace](logs/delete-workspace.md) – přidala se metoda PowerShellu.
- [Návrh Azure monitor protokolů nasazení](logs/design-logs-deployment.md) – míra ingestování pro pracovní prostor se zvýšila.

### <a name="metrics"></a>Metriky

- [Azure monitor metriky platforem exportovatelné prostřednictvím nastavení diagnostiky](essentials/metrics-supported-export-diagnostic-settings.md) – nový článek

### <a name="platform-logs"></a>Protokoly platformy

Několik článků bylo aktualizováno v rámci změny struktury obsahu pro protokoly platformy na základě nové funkce pro konfiguraci protokolu aktivit pomocí nastavení diagnostiky.

- [Archivace protokolů prostředků Azure do účtu úložiště](./essentials/resource-logs.md#send-to-azure-storage)
- [Schéma událostí protokolu aktivit Azure](essentials/activity-log-schema.md)
- [Omezení služby Azure Monitor](service-limits.md)
- [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics](./essentials/activity-log.md)
- [Shromažďování protokolů aktivit Azure s nastavením diagnostiky (Preview) – Azure Monitor](./essentials/activity-log.md)
- [Shromažďování protokolů aktivit Azure do pracovního prostoru Log Analytics napříč klienty Azure](./essentials/activity-log.md)
- [Shromažďování protokolů prostředků Azure v pracovním prostoru Log Analytics](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Vytvoření nastavení diagnostiky v Azure pomocí šablony Správce prostředků](./essentials/resource-manager-diagnostic-settings.md)
- [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik v Azure](essentials/diagnostic-settings.md)
- [Exportovat protokol aktivit Azure](./essentials/activity-log.md#legacy-collection-methods)
- [Přehled protokolů platformy Azure](essentials/platform-logs-overview.md)
- [Streamování dat monitorování Azure do centra událostí](essentials/stream-monitoring-data-event-hubs.md)
- [Streamování protokolů platformy Azure do centra událostí](./essentials/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Rychlé starty a kurzy

- [Vytvoření grafu metrik v Azure monitor](essentials/tutorial-metrics-explorer.md) – nový článek
- [Shromažďovat protokoly prostředků z prostředku Azure a analyzovat je pomocí Azure monitor](essentials/tutorial-resource-logs.md) – nový článek.
- [Monitorujte prostředek Azure pomocí Azure monitor](essentials/quick-monitor-azure-resource.md) – nový článek.
   
## <a name="next-steps"></a>Další kroky

- Pokud se chcete přispívat k dokumentaci Azure Monitor, přečtěte si [příručku pro přispěvatele docs](/contribute/).