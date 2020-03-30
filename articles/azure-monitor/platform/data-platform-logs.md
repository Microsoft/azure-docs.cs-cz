---
title: Protokoly ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Popisuje protokoly v Azure Monitoru, které se používají pro pokročilou analýzu dat monitorování.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: dd499a82ba1011d96772d6722e25a434d43a6bf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480194"
---
# <a name="logs-in-azure-monitor"></a>Protokoly v Azure Monitoru

> [!NOTE]
> Všechna data shromážděná službou Azure Monitor se vejdou do jednoho ze dvou základních typů, metriky a protokoly. Tento článek popisuje protokoly. Podrobný popis metrik a [data monitorování shromážděná službou Azure Monitor](data-platform.md) najdete v části Metriky v Azure [Monitoru,](data-platform-metrics.md) kde najdete porovnání těchto dvou metrik.

Protokoly ve službě Azure Monitor jsou užitečné zejména pro provádění komplexní analýzy napříč daty z různých zdrojů. Tento článek popisuje, jak jsou protokoly strukturovány v Azure Monitoru, co můžete dělat s daty a identifikuje různé zdroje dat, které ukládají data v protokolech.

> [!NOTE]
> Je důležité rozlišovat mezi protokoly monitorování Azure a zdroji dat protokolu v Azure. Například události na úrovni předplatného v Azure se zapisují do [protokolu aktivit,](platform-logs-overview.md) který můžete zobrazit z nabídky Azure Monitor. Většina prostředků bude zapisovat provozní informace do [protokolu prostředků,](platform-logs-overview.md) který můžete předat do různých umístění. Azure Monitor Logs je datová platforma protokolu, která shromažďuje protokoly aktivit a protokoly prostředků spolu s dalšími daty monitorování, aby poskytovala hloubkovou analýzu napříč celou sadou prostředků.

## <a name="what-are-azure-monitor-logs"></a>Co jsou protokoly monitorování Azure?

Protokoly ve službě Azure Monitor obsahují různé druhy dat uspořádaných do záznamů s různými sadami vlastností pro každý typ. Protokoly mohou obsahovat číselné hodnoty, jako jsou metriky Azure Monitor, ale obvykle obsahují textová data s podrobnými popisy. Dále se liší od metrických dat v tom, že se liší ve své struktuře a často nejsou shromažďovány v pravidelných intervalech. Telemetrie, jako jsou události a trasování jsou uloženy protokoly monitorování Azure kromě dat o výkonu tak, aby všechny lze kombinovat pro analýzu.

Běžným typem položky protokolu je událost, která je shromažďována sporadicky. Události jsou vytvářeny aplikací nebo službou a obvykle obsahují dostatek informací, které poskytují úplný kontext samy o sobě. Událost může například znamenat, že konkrétní prostředek byl vytvořen nebo změněn, nový hostitel byl spuštěn v reakci na zvýšený provoz nebo byla zjištěna chyba v aplikaci.

 Vzhledem k tomu, že formát dat se může lišit, aplikace můžete vytvořit vlastní protokoly pomocí struktury, které potřebují. Metrická data mohou být dokonce uložena v protokolech a kombinovat je s dalšími daty monitorování pro sledování a další analýzu dat.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Co můžete dělat s protokoly monitorování Azure?
V následující tabulce jsou uvedeny různé způsoby, jak můžete protokoly používat v Azure Monitoru.


|  |  |
|:---|:---|
| Analýza | Pomocí [analýzy protokolů](../log-query/get-started-portal.md) na webu Azure Portal můžete zapisovat [dotazy protokolů](../log-query/log-query-overview.md) a interaktivně analyzovat data protokolu pomocí výkonného modulu analýzy Průzkumníka dat.<br>Pomocí [analytické konzoly Application Insights](../app/analytics.md) na webu Azure Portal můžete psát dotazy protokolů a interaktivně analyzovat data protokolu z Application Insights. |
| Vizualizace | Připnutí výsledků dotazu vykreslených jako tabulky nebo grafy na [řídicí panel Azure](../../azure-portal/azure-portal-dashboards.md).<br>Vytvořte [sešit,](../app/usage-workbooks.md) který chcete kombinovat s více sadami dat v interaktivní sestavě. <br>Exportujte výsledky dotazu do [Power BI,](powerbi.md) abyste používali různé vizualizace a sdíleli je s uživateli mimo Azure.<br>Exportujte výsledky dotazu do [Grafany,](grafana-plugin.md) abyste využili jeho řídicí panel a zkombinovali je s jinými zdroji dat.|
| Výstrahy | Nakonfigurujte [pravidlo výstrahprotokolu,](alerts-log.md) které odešle oznámení nebo provede [automatickou akci,](action-groups.md) pokud výsledky dotazu odpovídají určitému výsledku.<br>Nakonfigurujte [pravidlo upozornění metriky](alerts-metric-logs.md) pro určité protokoly dat protokolu extrahované jako metriky. |
| Načíst | Přístup k výsledkům dotazu protokolu z příkazového řádku pomocí [azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Přístup k dotazu protokolu výsledky z příkazového řádku pomocí [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Přístup k dotazu protokolu výsledky z vlastní aplikace pomocí [rozhraní REST API](https://dev.loganalytics.io/). |
| Export | Vytvořte pracovní postup pro načtení dat protokolu a zkopírujte je do externího umístění pomocí [logic apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Jak jsou data v protokolech monitorování Azure strukturovaná?
Data shromážděná protokoly monitorování Azure se ukládají v [pracovním prostoru Analýzy protokolů](../platform/design-logs-deployment.md). Každý pracovní prostor obsahuje více tabulek, které každý ukládá data z určitého zdroje. Zatímco všechny tabulky sdílejí [některé společné vlastnosti](log-standard-properties.md), každá má jedinečnou sadu vlastností v závislosti na druhu dat, které ukládá. Nový pracovní prostor bude mít standardní sadu tabulek a další tabulky budou přidány různými řešeními monitorování a dalšími službami, které zapisují do pracovního prostoru.

Data protokolu z Application Insights používá stejný modul Log Analytics jako pracovní prostory, ale jsou uložena samostatně pro každou monitorovnou aplikaci. Každá aplikace má standardní sadu tabulek pro ukládání dat, jako jsou požadavky aplikací, výjimky a zobrazení stránek.

Dotazy protokolu budou používat data z pracovního prostoru Log Analytics nebo z aplikace Application Insights. [Pomocí dotazu napříč prostředky](../log-query/cross-workspace-query.md) můžete analyzovat data aplikace společně s dalšími daty protokolu nebo vytvářet dotazy včetně více pracovních prostorů nebo aplikací.

![Pracovní prostory](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Dotazy na protokoly
Data v protokolech monitorování Azure se načítají pomocí [dotazu protokolu](../log-query/log-query-overview.md) napsaného [pomocí dotazovacího jazyka Kusto](../log-query/get-started-queries.md), který umožňuje rychle načítat, konsolidovat a analyzovat shromážděná data. Pomocí [analýzy protokolů zapisujte](../log-query/portals.md) a otestujte dotazy protokolu na webu Azure Portal. Umožňuje interaktivně pracovat s výsledky nebo je připnout na řídicí panel a zobrazit je pomocí jiných vizualizací.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Otevřete [analýzu protokolů z Application Insights](../app/analytics.md) a analyzujte data Application Insights.

![Analýza přehledů aplikací](media/data-platform-logs/app-insights-analytics.png)

Data protokolu můžete také načíst pomocí [rozhraní API analýzy protokolů](https://dev.loganalytics.io/documentation/overview) a [rozhraní REST API pro přehledy aplikací](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Zdroje protokolů monitorování Azure
Azure Monitor může shromažďovat data protokolu z různých zdrojů v rámci Azure i z místních prostředků. V následujících tabulkách jsou uvedeny různé zdroje dat, které jsou k dispozici z různých prostředků, které zapisují data do protokolů monitorování Azure. Každý z nich má odkaz na podrobnosti o požadované konfiguraci.

### <a name="azure-tenant-and-subscription"></a>Tenant azure a předplatné

| Data | Popis |
|:---|:---|
| Protokoly auditu služby Azure Active Directory | Konfigurováno prostřednictvím nastavení diagnostiky pro každý adresář. Viz [Integrace protokolů Azure AD s protokoly Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Protokoly aktivit | Uloženy samostatně ve výchozím nastavení a mohou být použity pro téměř v reálném čase výstrahy. Nainstalujte řešení Analytics protokolu aktivit a zapište do pracovního prostoru Log Analytics. Viz [Shromažďování a analýza protokolů aktivit Azure v Log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Prostředky Azure

| Data | Popis |
|:---|:---|
| Diagnostika zdrojů | Nakonfigurujte nastavení diagnostiky pro zápis do diagnostických dat, včetně metrik do pracovního prostoru Analýzy protokolů. Viz [Stream ování protokolů prostředků Azure do analýzy protokolů](resource-logs-collect-workspace.md). |
| Řešení monitorování | Řešení monitorování zapisují data, která shromažďují, do pracovního prostoru Log Analytics. Seznam řešení najdete [v podrobnostech o shromažďování dat pro řešení pro správu v Azure.](../insights/solutions-inventory.md) Podrobnosti o instalaci a používání řešení najdete v tématu [Monitorování řešení v Azure Monitoru.](../insights/solutions.md) |
| Metriky | Odesílat metriky platformy pro prostředky Azure Monitor u zaznamenávat protokol analytics pracovního prostoru uchovávat data protokolu po delší dobu a provádět komplexní analýzy s jinými typy dat pomocí [dotazovacího jazyka Kusto](/azure/kusto/query/). Viz [Stream ování protokolů prostředků Azure do analýzy protokolů](resource-logs-collect-storage.md). |
| Azure Table Storage | Shromažďujte data z úložiště Azure, kde některé prostředky Azure zapisují data monitorování. Najdete [v tématu Použití úložiště objektů blob Azure pro Službu IIS a úložiště tabulek Azure pro události pomocí Analýzy protokolů](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Virtuální počítače

| Data | Popis |
|:---|:---|
|  Zdroje dat agenta | Zdroje dat shromážděné od agentů [Windows](agent-windows.md) a [Linuxu](../learn/quick-collect-linux-computer.md) zahrnují události, data o výkonu a vlastní protokoly. Seznam zdrojů dat a podrobnosti o konfiguraci najdete v tématu [Zdroje dat agenta v Azure Monitoru.](data-sources.md) |
| Řešení monitorování | Řešení monitorování zapisují data, která shromažďují od agentů, do pracovního prostoru Log Analytics. Seznam řešení najdete [v podrobnostech o shromažďování dat pro řešení pro správu v Azure.](../insights/solutions-inventory.md) Podrobnosti o instalaci a používání řešení najdete v tématu [Monitorování řešení v Azure Monitoru.](../insights/solutions.md) |
| System Center Operations Manager | Připojte skupinu pro správu Operations Manageru k Azure Monitoru a shromažďujte data událostí a výkonu od místních agentů do protokolů. Podrobnosti o této konfiguraci najdete v [tématu Připojení nástroje Operations Manager k analýze protokolů.](om-agents.md) |


### <a name="applications"></a>Aplikace

| Data | Popis |
|:---|:---|
| Žádosti a výjimky | Podrobné údaje o požadavcích a výjimkách aplikací jsou uvedeny v _tabulkách požadavků_, _pageViews_a _výjimek._ Volání [externích součástí](../app/asp-net-dependencies.md) jsou v tabulce _závislostí._ |
| Využití a výkon | Výkon aplikace je k dispozici v _požadavcích_, _browserTimings_ a _performanceCounters_ tabulkách. Data pro [vlastní metriky](../app/api-custom-events-metrics.md#trackevent) jsou v tabulce _customMetrics._|
| Trasovací data | Výsledky [distribuovaného trasování](../app/distributed-tracing.md) jsou uloženy v tabulce _trasování._ |
| Testy dostupnosti | Souhrnná data z [testů dostupnosti](../app/monitor-web-app-availability.md) jsou uložena v tabulce _availabilityResults._ Podrobná data z těchto testů jsou v samostatném úložišti a přístupná z Application Insights na webu Azure Portal. |

### <a name="insights"></a>Insights

| Data | Popis |
|:---|:---|
| Azure Monitor pro kontejnery | Údaje o inventáři a výkonu shromažďované [službou Azure Monitor pro kontejnery](../insights/container-insights-overview.md). Seznam tabulek najdete v [podrobnostech o kolekci dat kontejneru.](../insights/container-insights-log-search.md#container-records) |
| Azure Monitor pro virtuální počítače | Mapovaná a výkonová data shromážděná [službou Azure Monitor pro virtuální počítače](../insights/vminsights-overview.md). Podrobnosti o dotazování na tato data najdete v tématu [Jak dotazovat protokoly z Azure Monitoru pro virtuální počítače.](../insights/vminsights-log-search.md) |

### <a name="custom"></a>Vlastní 

| Data | Popis |
|:---|:---|
| REST API | Zapište data do pracovního prostoru Analýzy protokolů z libovolného klienta REST. Podrobnosti najdete v tématu [Odesílání dat protokolu do Azure Monitoru pomocí rozhraní API pro shromažďování dat HTTP.](data-collector-api.md)
| Aplikace logiky | Zapište všechna data do pracovního prostoru Analýzy protokolů z pracovního postupu aplikace logiky pomocí akce **Shromažďování dat Azure Log Analytics.** |

### <a name="security"></a>Zabezpečení

| Data | Popis |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) ukládá data, která shromažďuje v pracovním prostoru Log Analytics, kde je možné analyzovat s jinými daty protokolu. Podrobnosti o konfiguraci pracovního prostoru najdete v tématu [Shromažďování dat v Centru zabezpečení Azure.](../../security-center/security-center-enable-data-collection.md) |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) ukládá data ze zdrojů dat do pracovního prostoru Analýzy protokolů. Viz [Připojení zdrojů dat](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Další kroky

- Další informace o [datové platformě Azure Monitor](data-platform.md).
- Další informace o [metrikách v Azure Monitoru](data-platform-metrics.md).
- Přečtěte si o [datech monitorování dostupných](data-sources.md) pro různé prostředky v Azure.
