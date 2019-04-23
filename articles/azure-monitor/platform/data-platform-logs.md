---
title: Protokoly ve službě Azure Monitor | Dokumentace Microsoftu
description: Popisuje protokolů ve službě Azure Monitor, které se používají pro pokročilé analýzy dat monitorování.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: 59213c5391b5b652eeead05c4a5af761571fcece
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798942"
---
# <a name="logs-in-azure-monitor"></a>Protokoly v Azure Monitoru

> [!NOTE]
> Všechna data shromážděná službou Azure Monitor se vejde do jednoho ze dvou základních typů, metriky a protokoly. Tento článek popisuje protokoly. Odkazovat na [metriky ve službě Azure Monitor](data-platform-metrics.md) metrik a získat podrobný popis [monitorování dat shromážděných službou Azure Monitor](data-platform.md) porovnání obou.

Protokoly ve službě Azure Monitor jsou zvláště užitečné pro provádění komplexní analýzu dat z různých zdrojů. Tento článek popisuje, jak protokoly jsou strukturované ve službě Azure Monitor, co můžete dělat s daty a identifikuje různých zdrojů dat, které ukládají data v protokolech.

> [!NOTE]
> Je důležité rozlišovat mezi protokoly Azure monitoru a zdroje dat protokolu v Azure. Například událostí na úrovni předplatného v Azure se zapisují do [protokolu aktivit](activity-logs-overview.md) , který se zobrazí v nabídce Azure Monitor. Většinu prostředků se zapisuje provozní informace do [diagnostický protokol](diagnostic-logs-overview.md) předávání do různých umístění. Protokoly služby Azure Monitor je platforma dat protokolu, který shromažďuje protokoly aktivit a diagnostické protokoly spolu s dalšími daty monitorování poskytovat hloubkovou analýzu napříč celou sadu prostředků.

## <a name="what-are-azure-monitor-logs"></a>Co jsou Azure Monitor protokoly?

Protokoly ve službě Azure Monitor obsahovat různé druhy dat uspořádaných do s různými sadami vlastností pro jednotlivé typy záznamů. Protokoly mohou obsahovat číselných hodnot, jako je Azure Monitor metriky, ale obvykle obsahují textová data se podrobný popis. Se dále liší od data metriky se liší v jejich strukturu a často nejsou shromážděny v pravidelných intervalech. Telemetrická data, jako je například událostmi a sledováním se ukládají protokoly Azure monitoru Kromě údaje o výkonu tak, aby se všechny možné kombinovat pro analýzu.

Běžný typ položky protokolu je událost, která se shromažďují nedojde. Události jsou vytvořené aplikace nebo služby a obvykle zahrnují dostatek informací, které poskytují úplný kontext na své vlastní. Například událost může znamenat, že určitý prostředek vytvořil nebo změnil, nový hostitel spuštěna v reakci na zvýšení provozu, nebo v aplikaci došlo k chybě.

 Vzhledem k tomu, že formát dat se může lišit, aplikace můžete vytvořit vlastní protokoly pomocí struktura, která potřebují. Data metriky mohou být uloženy i v protokolech kombinovat s dalšími daty monitorování pro vytvoření trendu a ostatní data analýzy.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Co můžete dělat s protokoly Azure monitoru?
Následující tabulka uvádí různé způsoby protokoly můžete použít ve službě Azure Monitor.


|  |  |
|:---|:---|
| Analýza | Použití [Log Analytics](../log-query/get-started-portal.md) na webu Azure Portal k zápisu [protokolu dotazy](../log-query/log-query-overview.md) a interaktivní analýzy dat protokolu pomocí výkonný stroj analýzy Průzkumník dat.<br>Použití [konzola Application Insights analytics](../app/analytics.md) na webu Azure Portal k vytváření dotazů protokolu a interaktivně analyzuje data protokolů ze služby Application Insights. |
| Vizualizace | Připnout výsledky dotazu se vykresluje jako tabulky nebo grafy [řídicí panel Azure](../../azure-portal/azure-portal-dashboards.md).<br>Vytvoření [sešitu](../app/usage-workbooks.md) zkombinovat s více sadami dat v interaktivní sestavy. <br>Export výsledků dotazu na [Power BI](powerbi.md) a použít různé vizualizace a sdílet s uživateli mimo Azure.<br>Export výsledků dotazu na [Grafana](grafana-plugin.md) využít jeho mnoha a zkombinovat s jinými zdroji dat.|
| Výstrahy | Konfigurace [pravidel upozornění protokolů](alerts-log.md) , který odešle oznámení, nebo má [automatizované akce](action-groups.md) při výsledky dotazu splňují konkrétní výsledek.<br>Konfigurace [metriky pravidlo upozornění](alerts-metric-logs.md) na některé protokoly dat protokolu extrahovat jako metriky. |
| Načíst | Přístup k protokolu výsledků dotazu z příkazového řádku pomocí [rozhraní příkazového řádku Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Přístup k protokolu výsledků dotazu z příkazového řádku pomocí [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Přístup k protokolu výsledků dotazu z vlastní aplikaci pomocí [rozhraní REST API](https://dev.loganalytics.io/). |
| Export | Vytvořit pracovní postup k načtení dat protokolu a zkopírujte ho do externího umístění pomocí [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Jak se data v protokolech sledování Azure strukturována?
Data shromážděná službou Azure Monitor protokolů je uložené v [pracovní prostor Log Analytics](../platform/manage-access.md). Je možné [vytvořit víc pracovních prostorů](manage-access.md#determine-the-number-of-workspaces-you-need) ve vašem předplatném ke správě různých sad dat protokolu. Každý pracovní prostor obsahuje více tabulek, že každý ukládání dat z určitého zdroje. Při sdílení všechny tabulky [některé běžné vlastnosti](log-standard-properties.md), každá má jedinečnou sadu vlastností v závislosti na tom, jaká data se ukládá. Nový pracovní prostor bude mít standardní sadu tabulek a přidá se více tabulek v různých řešeních pro monitorování a dalším službám, které zapisují do pracovního prostoru.

Data protokolu z Application Insights používá stejný stroj Log Analytics jako pracovní prostory, ale jsou uloženy odděleně pro každou monitorovanou aplikaci. Každá aplikace má standardní sadu tabulek pro uchovávání dat, jako jsou žádosti o aplikace, výjimky a zobrazení stránek.

Protokol dotazů se buď pomocí dat z pracovního prostoru Log Analytics nebo z některé aplikace Application Insights. Můžete použít [napříč prostředky dotazu](../log-query/cross-workspace-query.md) k analýze dat aplikací společně s další data protokolu nebo k vytvoření dotazů, včetně víc pracovních prostorů nebo aplikací.

![Pracovní prostory](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Dotazy na protokoly
Data v protokolech sledování Azure jsou načítány s použitím [dotaz protokolu](../log-query/log-query-overview.md) napsané pomocí [Kusto dotazovací jazyk](../log-query/get-started-queries.md), která umožňuje rychle načítat, konsolidovat a analýzy shromážděných dat. Použití [Log Analytics](../log-query/portals.md) k psaní a testování dotazů na protokoly na webu Azure Portal. Umožňuje interaktivně pracovat s výsledky nebo je připnout na řídicí panel k zobrazení se ostatní vizualizace.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Otevřít [Log Analytics ze služby Application Insights](../app/analytics.md) k analýze dat Application Insights.

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

Můžete také načíst data protokolu pomocí [rozhraní API služby Log Analytics](https://dev.loganalytics.io/documentation/overview) a [REST API pro Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Zdroje protokoly Azure monitoru
Azure Monitor může shromažďovat data protokolu z různých zdrojů v rámci Azure a z místních prostředků. V následujících tabulkách jsou k dispozici různé prostředky, které zápis dat do Azure monitoru protokoly různých datových zdrojů. Každý má odkaz na podrobnosti na veškeré požadované konfigurace.

### <a name="azure-tenant-and-subscription"></a>Azure tenant a předplatné

| Data | Popis |
|:---|:---|
| Protokoly auditování Azure Active Directory | Nakonfigurovat přes nastavení diagnostiky pro každý adresář. Zobrazit [protokoly integrace Azure AD s protokoly Azure monitoru](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Protokoly aktivit | Ve výchozím nastavení uloženy odděleně a lze použít pro téměř upozornění v reálném čase. Nainstalujte řešení Activity Log Analytics k zápisu do pracovního prostoru Log Analytics. Zobrazit [shromažďovat a analyzovat protokoly aktivit Azure do Log Analytics](collect-activity-logs.md). |

### <a name="azure-resources"></a>Prostředky Azure

| Data | Popis |
|:---|:---|
| Diagnostika prostředků | Konfigurace nastavení diagnostiky pro zápis do diagnostická data, včetně metrik do pracovního prostoru Log Analytics. Zobrazit [Stream diagnostické protokoly Azure do Log Analytics](diagnostic-logs-stream-log-store.md). |
| Řešení monitorování | Řešení monitorování zápis dat, že které shromažďují do svého pracovního prostoru Log Analytics. Zobrazit [podrobnosti shromažďování dat pro řešení pro správu v Azure](../insights/solutions-inventory.md) seznam řešení. Zobrazit [monitorování řešení ve službě Azure Monitor](../insights/solutions.md) podrobnosti o instalaci a používání řešení. |
| Metriky | Odeslání metrik platformu pro prostředky Azure Monitor k pracovnímu prostoru Log Analytics, pokud chcete zachovat data protokolu pro delší dobu a provádět komplexní analýzy s jinými datovými typy pomocí [Kusto dotazovací jazyk](/azure/kusto/query/). Zobrazit [Stream diagnostické protokoly Azure do Log Analytics](diagnostic-logs-stream-log-store.md). |
| Azure Table Storage | Shromažďování dat ze služby Azure storage, kde některé prostředky Azure zapisovat data monitorování. Zobrazit [úložiště objektů blob v Azure použít pro službu IIS a Azure table storage pro události s využitím Log Analytics](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Virtuální počítače

| Data | Popis |
|:---|:---|
|  Zdroje dat agenta | Zdroje dat shromážděných z [Windows](agent-windows.md) a [Linux](../learn/quick-collect-linux-computer.md) agentů zahrnují události, údaje o výkonu a vlastní protokoly. Zobrazit [agenta zdrojů dat ve službě Azure Monitor](data-sources.md) seznam zdrojů dat a podrobnosti o konfiguraci. |
| Řešení monitorování | Řešení monitorování zápisu dat že do jejich pracovního prostoru Log Analytics shromažďovat od agentů. Zobrazit [podrobnosti shromažďování dat pro řešení pro správu v Azure](../insights/solutions-inventory.md) seznam řešení. Zobrazit [monitorování řešení ve službě Azure Monitor](../insights/solutions.md) podrobnosti o instalaci a používání řešení. |
| System Center Operations Manager | Připojení skupiny pro správu Operations Manageru do Azure monitoru ke shromažďování dat událostí a výkonu z agentů v místním do protokolů. Zobrazit [připojení Operations Manageru k Log Analytics](om-agents.md) podrobnosti o této konfiguraci. |


### <a name="applications"></a>Aplikace

| Data | Popis |
|:---|:---|
| Žádosti a výjimky | Podrobné údaje o aplikaci žádosti a výjimky jsou v _požadavky_, _zobrazení stránky_, a _výjimky_ tabulky. Volání [externí komponenty](../app/asp-net-dependencies.md) v _závislosti_ tabulky. |
| Využití a výkonu | Výkon pro aplikace je k dispozici v _požadavky_, _browserTimings_ a _čítače výkonu_ tabulky. Data pro [vlastní metriky](../app/api-custom-events-metrics.md#trackevent) probíhá _customMetrics_ tabulky.|
| Data trasování | Výsledkem [distribuované trasování](../app/distributed-tracing.md) jsou uloženy v _trasy_ tabulky. |
| Testy dostupnosti | Souhrnná data z [testy dostupnosti](../app/monitor-web-app-availability.md) je uložen v _availabilityResults_ tabulky. Podrobná data z těchto testů v samostatné úložiště a k němu přistupovat z Application Insights na webu Azure Portal. |

### <a name="insights"></a>Insights

| Data | Popis |
|:---|:---|
| Azure Monitor for Containers | Data inventáře a výkonu shromážděné [monitorování Azure pro kontejnery](../insights/container-insights-overview.md). Zobrazit [podrobnosti o kontejneru shromažďování dat](../insights/container-insights-analyze.md#container-data-collection-details) seznam tabulek. |
| Azure Monitor pro virtuální počítače | Data mapy a výkonu shromážděné [monitorování Azure pro virtuální počítače](../insights/vminsights-overview.md). Zobrazit [jak provádět dotazy protokolů ze služby Azure Monitor pro virtuální počítače](../insights/vminsights-log-search.md) podrobnosti o dotazování na tato data. |

### <a name="custom"></a>Vlastní 

| Data | Popis |
|:---|:---|
| REST API | Zápis dat do pracovního prostoru Log Analytics z jakéhokoli klienta REST. Zobrazit [odeslání dat protokolů do Azure monitoru pomocí rozhraní API kolekce dat HTTP](data-collector-api.md) podrobnosti.
| Aplikace logiky | Zapisovat data do pracovního prostoru Log Analytics z pracovního postupu aplikace logiky s **kolekce dat Azure Log Analytics** akce. |

### <a name="security"></a>Zabezpečení

| Data | Popis |
|:---|:---|
| Azure Security Center | [Azure Security Center](/azure/security-center/) ukládá data, která shromažďuje v pracovním prostoru Log Analytics, kde můžou být analyzována s ostatními daty protokolu. Zobrazit [shromažďování dat ve službě Azure Security Center](../../security-center/security-center-enable-data-collection.md) podrobnosti o konfiguraci pracovního prostoru. |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) ukládá data ze zdrojů dat do pracovního prostoru Log Analytics. Zobrazit [připojení zdroje dat](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Další postup

- Další informace o [datová Platforma Azure Monitor](data-platform.md).
- Další informace o [metriky ve službě Azure Monitor](data-platform-metrics.md).
- Další informace o [data k dispozici monitorování](data-sources.md) pro různé prostředky v Azure.
