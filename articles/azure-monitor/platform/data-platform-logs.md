---
title: Přihlásí se Azure Monitor | Microsoft Docs
description: Popisuje protokoly v Azure Monitor, které se používají pro pokročilou analýzu dat monitorování.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 413616034dfe7d1f13612ba12ba86014af62c704
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325623"
---
# <a name="logs-in-azure-monitor"></a>Protokoly v Azure Monitoru

> [!NOTE]
> Všechna data shromážděná Azure Monitor se vejdou do jednoho ze dvou základních typů, metrik a protokolů. Tento článek popisuje protokoly. Podrobné informace o metrikách a [monitorování dat shromažďovaných pomocí Azure monitor](data-platform.md) pro porovnání těchto dvou informací najdete v tématu věnovaném [metrikám v Azure monitor](data-platform-metrics.md) .

Protokoly v Azure Monitor jsou zvláště užitečné pro provádění složitých analýz napříč daty z nejrůznějších zdrojů. Tento článek popisuje, jak jsou v Azure Monitor strukturované protokoly, co můžete s daty dělat, a identifikuje různé zdroje dat, které ukládají data v protokolech.

> [!NOTE]
> Je důležité rozlišovat mezi protokoly Azure Monitor a zdroji dat protokolů v Azure. Například události na úrovni předplatného v Azure se zapisují do [protokolu aktivit](platform-logs-overview.md) , který můžete zobrazit v nabídce Azure monitor. Většina prostředků zapisuje provozní informace do [protokolu prostředků](platform-logs-overview.md) , který můžete přemístit do různých umístění. Protokoly Azure Monitor jsou datovou platformou protokolu, která shromažďuje protokoly aktivit a protokoly prostředků spolu s dalšími daty monitorování a zajišťuje tak hloubkovou analýzu v celé sadě prostředků.

## <a name="what-are-azure-monitor-logs"></a>Co jsou protokoly Azure Monitor?

Protokoly v Azure Monitor obsahují různé druhy dat uspořádané do záznamů s různými sadami vlastností pro každý typ. Protokoly mohou obsahovat číselné hodnoty, například Azure Monitor metriky, ale obvykle obsahují textová data s podrobnými popisy. Dále se liší od dat metrik v tom, že se liší ve své struktuře a často nejsou shromažďovány v pravidelných intervalech. Telemetrie, jako jsou události a trasování, se ukládají Azure Monitor společně s daty o výkonu, aby se mohla kombinovat pro účely analýzy.

Běžným typem položky protokolu je událost, která je shromažďována občas. Události vytváří aplikace nebo služba a obvykle obsahují dostatek informací, které poskytují úplný kontext vlastnímu kontextu. Například událost může znamenat, že se určitý prostředek vytvořil nebo změnil, aby byl nový hostitel spuštěný v reakci na zvýšení provozu nebo aby v aplikaci byla zjištěna chyba.

 Vzhledem k tomu, že se může změnit formát dat, aplikace mohou vytvořit vlastní protokoly pomocí struktury, kterou potřebují. Data metriky je dokonce možné ukládat v protokolech a kombinovat je s dalšími daty monitorování pro účely trendů a dalších analýz dat.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Co se dá dělat s protokoly Azure Monitor?
V následující tabulce jsou uvedeny různé způsoby, jak můžete použít protokoly v Azure Monitor.


|  | Popis |
|:---|:---|
| **Analýza** | Pomocí [Log Analytics](../log-query/get-started-portal.md) v Azure Portal můžete zapisovat [dotazy protokolu](../log-query/log-query-overview.md) a interaktivně analyzovat data protokolu pomocí výkonného analytického modulu Průzkumník dat.<br>Pomocí [konzoly Application Insights Analytics](../log-query/log-query-overview.md) v Azure Portal můžete zapisovat dotazy protokolu a interaktivně analyzovat data protokolu z Application Insights. |
| **Vizualizace** | Připněte výsledky dotazu vykreslené jako tabulky nebo grafy na [řídicí panel Azure](../../azure-portal/azure-portal-dashboards.md).<br>Vytvoří [sešit](./workbooks-overview.md) pro kombinování s více sadami dat v interaktivní sestavě. <br>Exportujte výsledky dotazu do [Power BI](powerbi.md) , abyste mohli používat různé vizualizace a sdílet s uživateli mimo Azure.<br>Exportujte výsledky dotazu do [Grafana](grafana-plugin.md) a využijte jeho řídicí panely a kombinaci s jinými zdroji dat.|
| **Výstraha** | Nakonfigurujte [pravidlo upozornění protokolu](alerts-log.md) , které pošle oznámení, nebo provede [automatizovanou akci](action-groups.md) , když výsledky dotazu odpovídají konkrétnímu výsledku.<br>Nakonfigurujte [pravidlo upozornění metriky](alerts-metric-logs.md) pro některé protokoly dat protokolů extrahované jako metriky. |
| **Stahovat** | Přístup k výsledkům dotazu protokolu z příkazového řádku pomocí [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Přístup k výsledkům dotazu protokolu z příkazového řádku pomocí [rutin prostředí PowerShell](/powershell/module/az.operationalinsights).<br>Přístup k výsledkům dotazu protokolu z vlastní aplikace pomocí [REST API](https://dev.loganalytics.io/). |
| **Export** | Sestavte pracovní postup, který načte data protokolu a zkopíruje ho do externího umístění pomocí [Logic Apps](../../logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Jak se strukturují data v Azure Monitor strukturovaných protokolů?
Data shromážděná protokolem Azure Monitor se ukládají do [pracovního prostoru Log Analytics](./design-logs-deployment.md). Každý pracovní prostor obsahuje několik tabulek, z nichž každý ukládá data z určitého zdroje. Zatímco všechny tabulky sdílejí [některé společné vlastnosti](log-standard-properties.md), má každá z nich jedinečnou sadu vlastností v závislosti na druhu dat, která ukládá. Nový pracovní prostor bude mít standardní sadu tabulek a další tabulky, které budou zapisovat do pracovního prostoru, budou přidány do různých řešení monitorování a dalších služeb.

Data protokolu z Application Insights používají stejný modul Log Analytics jako pracovní prostory, ale ukládají se samostatně pro každou monitorovanou aplikaci. Každá aplikace má standardní sadu tabulek pro uchovávání dat, jako jsou například žádosti o aplikace, výjimky a zobrazení stránek.

Dotazy protokolu budou buď používat data z Log Analyticsho pracovního prostoru nebo Application Insights aplikace. [Dotaz na více prostředků](../log-query/cross-workspace-query.md) můžete použít k analýze dat aplikace společně s ostatními daty protokolu nebo k vytváření dotazů, včetně několika pracovních prostorů nebo aplikací.

![Pracovní prostory](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Dotazy na protokoly
Data v Azure Monitor protokoly se načítají pomocí [dotazu protokolu](../log-query/log-query-overview.md) napsaného pomocí [dotazovacího jazyka Kusto](../log-query/get-started-queries.md), který umožňuje rychlé načítání, konsolidaci a analýzu shromážděných dat. Použijte [Log Analytics](../log-query/log-query-overview.md) k zápisu a testování dotazů protokolu v Azure Portal. Umožňuje interaktivní práci s výsledky nebo jejich připnutí na řídicí panel, aby je bylo možné zobrazit s ostatními vizualizacemi.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Chcete-li analyzovat Application Insights data, otevřete [Log Analytics z Application Insights](../log-query/log-query-overview.md) .

![Analýzy Application Insights](media/data-platform-logs/app-insights-analytics.png)

Data protokolu můžete také načíst pomocí [rozhraní Log Analytics API](https://dev.loganalytics.io/documentation/overview) a [REST API Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Zdroje protokolů Azure Monitor
Azure Monitor může shromažďovat data protokolu z nejrůznějších zdrojů v rámci Azure i z místních prostředků. V následujících tabulkách jsou uvedeny různé zdroje dat, které jsou k dispozici z různých prostředků, které zapisují data do protokolů Azure Monitor. Každá z nich má odkaz na podrobnosti o jakékoli požadované konfiguraci.

### <a name="azure-tenant-and-subscription"></a>Tenant a předplatné Azure

| Data | Popis |
|:---|:---|
| Protokoly auditu Azure Active Directory | Nakonfigurováno prostřednictvím nastavení diagnostiky pro každý adresář. Viz [integrace protokolů služby Azure AD s protokoly Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Protokoly aktivit | Ukládá se samostatně ve výchozím nastavení a dá se použít pro výstrahy téměř v reálném čase. Nainstalujte řešení aktivity Log Analytics pro zápis do pracovního prostoru Log Analytics. Viz [shromáždění a analýza protokolů aktivit Azure v Log Analytics](./activity-log.md). |

### <a name="azure-resources"></a>Prostředky Azure

| Data | Popis |
|:---|:---|
| Diagnostika prostředků | Nakonfigurujte nastavení diagnostiky pro zápis do diagnostických dat, včetně metriky do pracovního prostoru Log Analytics. Další Log Analytics najdete v tématu [streamování protokolů prostředků Azure](./resource-logs.md#send-to-log-analytics-workspace). |
| Řešení monitorování | Řešení monitorování zapisují data, která shromažďuje do svého Log Analytics pracovního prostoru. Seznam řešení najdete v tématu [Podrobnosti shromažďování dat pro řešení pro správu v Azure](../monitor-reference.md) . Podrobnosti o instalaci a používání řešení najdete v tématu [monitorování řešení v Azure monitor](../insights/solutions.md) . |
| Metriky | Pošlete metriky platforem pro Azure Monitor prostředky do pracovního prostoru Log Analytics, abyste zachovali data protokolu pro delší období a prováděli složitou analýzu s jinými datovými typy pomocí [dotazovacího jazyka Kusto](/azure/kusto/query/). Další Log Analytics najdete v tématu [streamování protokolů prostředků Azure](./resource-logs.md#send-to-azure-storage). |
| Azure Table Storage | Shromažďovat data ze služby Azure Storage, kde některé prostředky Azure napisují data monitorování. Další informace najdete v tématu [použití úložiště objektů BLOB v Azure pro službu IIS a úložiště tabulek Azure pro události s Log Analytics](diagnostics-extension-logs.md). |

### <a name="virtual-machines"></a>Virtuální počítače

| Data | Popis |
|:---|:---|
|  Zdroje dat agenta | Zdroje dat shromažďované z agentů [Windows](agent-windows.md) a [Linux](../learn/quick-collect-linux-computer.md) zahrnují události, údaje o výkonu a vlastní protokoly. Seznam zdrojů dat a podrobnosti o konfiguraci najdete [v tématu zdroje dat agenta v Azure monitor](data-sources.md) . |
| Řešení monitorování | Řešení monitorování zapisují data, která shromažďují z agentů do svého pracovního prostoru Log Analytics. Seznam řešení najdete v tématu [Podrobnosti shromažďování dat pro řešení pro správu v Azure](../monitor-reference.md) . Podrobnosti o instalaci a používání řešení najdete v tématu [monitorování řešení v Azure monitor](../insights/solutions.md) . |
| System Center Operations Manager | Připojte Operations Manager skupinu pro správu, abyste Azure Monitor shromažďovat data o událostech a výkonu od místních agentů do protokolů. Podrobnosti o této konfiguraci najdete v tématu věnovaném [připojení Operations Manager k Log Analytics](om-agents.md) . |


### <a name="applications"></a>Aplikace

| Data | Popis |
|:---|:---|
| Žádosti a výjimky | Podrobná data o požadavcích aplikace a výjimkách jsou v tabulkách _požadavků_, _pageViews_a _Exceptions_ . Volání [externích komponent](../app/asp-net-dependencies.md) jsou v tabulce _závislosti_ . |
| Využití a výkon | Výkon aplikace je k dispozici v tabulkách _požadavky_, _browserTimings_ a _čítače výkonu_ . Data pro [vlastní metriky](../app/api-custom-events-metrics.md#trackevent) jsou v tabulce _customMetrics_ .|
| Trasovat data | Výsledky z [distribuovaného trasování](../app/distributed-tracing.md) jsou uloženy v tabulce _trasování_ . |
| Testy dostupnosti | Souhrnná data z [testů dostupnosti](../app/monitor-web-app-availability.md) se ukládají do tabulky _availabilityResults_ . Podrobná data z těchto testů jsou v odděleném úložišti a jsou dostupná z Application Insights v Azure Portal. |

### <a name="insights"></a>Insights

| Data | Popis |
|:---|:---|
| Azure Monitor pro kontejnery | Data inventáře a výkonu shromážděná [Azure monitor pro kontejnery](../insights/container-insights-overview.md). Seznam tabulek najdete v tématu [Podrobnosti o kolekci dat kontejneru](../insights/container-insights-log-search.md#container-records) . |
| Azure Monitor pro virtuální počítače | Data o mapě a výkonu shromažďovaná nástrojem [Azure monitor pro virtuální počítače](../insights/vminsights-overview.md). Podrobnosti o dotazování na tato data najdete v tématu [Postup dotazování protokolů z Azure monitor pro virtuální počítače](../insights/vminsights-log-search.md) . |

### <a name="custom"></a>Vlastní 

| Data | Popis |
|:---|:---|
| REST API | Zápis dat do Log Analytics pracovního prostoru z libovolného klienta REST. Podrobnosti najdete v tématu [odeslání dat protokolu do Azure monitor pomocí rozhraní API kolekce dat http](data-collector-api.md) .
| Aplikace logiky | Pomocí akce **shromažďování dat pro Azure Log Analytics** zapište do Log Analytics pracovního prostoru jakákoli data z pracovního postupu aplikace logiky. |

### <a name="security"></a>Zabezpečení

| Data | Popis |
|:---|:---|
| Azure Security Center | [Azure Security Center](../../security-center/index.yml) ukládá data, která shromažďuje v pracovním prostoru Log Analytics, kde je lze analyzovat pomocí jiných dat protokolu. Podrobnosti o konfiguraci pracovního prostoru najdete [v tématu shromažďování dat v Azure Security Center](../../security-center/security-center-enable-data-collection.md) . |
| Azure Sentinel | [Sentinel v Azure](../../sentinel/index.yml) ukládá data ze zdrojů dat do pracovního prostoru Log Analytics. Viz [propojení zdrojů dat](../../sentinel/connect-data-sources.md).  |


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure monitor datovou platformu](data-platform.md).
- Seznamte [se s metrikami v Azure monitor](data-platform-metrics.md).
- Přečtěte si o [dostupných datech monitorování](data-sources.md) různých prostředků v Azure.

