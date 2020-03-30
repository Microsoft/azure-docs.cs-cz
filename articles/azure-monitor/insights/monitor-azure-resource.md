---
title: Monitorování prostředků Azure pomocí Azure Monitoru | Dokumenty společnosti Microsoft
description: Popisuje, jak shromažďovat a analyzovat data monitorování z prostředků v Azure pomocí Azure Monitoru.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249266"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorování prostředků Azure pomocí Azure Monitoru
Pokud máte důležité aplikace a obchodní procesy, které jsou závislé na prostředcích Azure, chcete tyto prostředky sledovat z důvodu jejich dostupnosti, výkonu a provozu. Tento článek popisuje data monitorování generovaná prostředky Azure a jak můžete pomocí funkcí Azure Monitoru analyzovat a upozorňovat na tato data.

> [!IMPORTANT]
> Tento článek se vztahuje na všechny služby v Azure, které používají Azure Monitor. Výpočetní prostředky, včetně virtuálních počítačů a služby App Service, generují stejná data monitorování, která jsou popsána zde, ale mají také hostovaný operační systém, který může také generovat protokoly a metriky. Podrobnosti o shromažďování a analýze těchto dat naleznete v dokumentaci k monitorování těchto služeb.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
Azure Monitor je služba monitorování celého zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování vašich prostředků Azure kromě prostředků v jiných cloudech a místních. [Datová platforma Azure Monitor](../platform/data-platform.md) shromažďuje data do [protokolů](../platform/data-platform-logs.md) a [metrik,](../platform/data-platform-metrics.md) kde je lze analyzovat společně pomocí kompletní sady nástrojů pro monitorování, jak je popsáno v následujících částech.

- [Co můžete dělat s metrikami Azure Monitor?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Co můžete dělat s protokoly monitorování Azure?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Jakmile vytvoříte prostředek Azure, Azure Monitor je povolený a začne shromažďovat metriky a protokoly aktivit, které můžete [zobrazit a analyzovat na webu Azure Portal](#monitoring-in-the-azure-portal). V některých konfiguracích můžete shromažďovat další data monitorování a povolit další funkce. Podrobnosti o požadavcích na konfiguraci naleznete níže v [části Data monitorování.](#monitoring-data)


## <a name="costs-associated-with-monitoring"></a>Náklady spojené s monitorováním
Neexistuje žádné náklady na analýzu dat monitorování, která jsou shromažďována ve výchozím nastavení. Ta zahrnují následující:

- Shromažďování metrik platformy a jejich analýza pomocí průzkumníka metrik.
- Shromažďování protokolu aktivit a jeho analýza na webu Azure Portal.
- Vytvoření pravidla výstrahy protokolu aktivit.

Neexistují žádné náklady azure monitoru pro shromažďování a export protokolů a metrik, ale může být související náklady spojené s cílem:

- Náklady spojené s přihlašováním a uchováváním dat při shromažďování protokolů a metrik v pracovním prostoru Log Analytics. Viz [Ceny Azure Monitor pro Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Náklady spojené s úložištěm dat při shromažďování protokolů a metrik do účtu úložiště Azure. Viz [Ceny azure storage pro úložiště objektů blob](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Náklady spojené s streamováním centra událostí při předávání protokolů a metrik do Azure Event Hubs. Podívejte se na [ceny Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Může být Azure Monitor náklady spojené s následující. Viz [Ceny Azure Monitoru](https://azure.microsoft.com/pricing/details/monitor/):

- Spuštění dotazu protokolu.
- Vytvoření pravidla výstrahy metriky nebo dotazu protokolu.
- Odeslání oznámení z libovolného pravidla výstrahy.
- Přístup k metriky prostřednictvím rozhraní API.

## <a name="monitoring-data"></a>Data monitorování
Prostředky v Azure generovat [protokoly](../platform/data-platform-logs.md) a [metriky zobrazené](../platform/data-platform-metrics.md) v následujícím diagramu. Konkrétní data, která generují, a všechna další řešení nebo přehledy, které poskytují, najdete v dokumentaci ke každé službě Azure.

![Přehled](media/monitor-azure-resource/logs-metrics.png)



- [Metriky platformy](../platform/data-platform-metrics.md) – číselné hodnoty, které jsou automaticky shromažďovány v pravidelných intervalech a popisují některé aspekty prostředku v určitém čase. 
- [Protokoly prostředků](../platform/platform-logs-overview.md) – poskytují přehled o operacích, které byly provedeny v rámci prostředku Azure (rovina dat), například získání tajného klíče z trezoru klíčů nebo podání požadavku do databáze. Obsah a struktura protokolů prostředků se liší podle služby Azure a typu prostředků.
- [Protokol aktivit](../platform/platform-logs-overview.md) – poskytuje přehled o operacích na každém prostředku Azure v předplatném z vnějšku (rovina správy), například vytvoření nového prostředku nebo spuštění virtuálního počítače. Toto je informace o co, kdo a kdy pro všechny operace zápisu (PUT, POST, DELETE) přijata na prostředky ve vašem předplatném.


## <a name="configuration-requirements"></a>Požadavky na konfiguraci

### <a name="configure-monitoring"></a>Konfigurace sledování
Některá data monitorování jsou shromažďována automaticky, ale v závislosti na požadavcích může být nutné provést určitou konfiguraci. Konkrétní informace pro každý typ údajů z monitorování naleznete níže.

- [Metriky platformy](../platform/data-platform-metrics.md) – metriky platformy se shromažďují automaticky do [metrik Azure Monitor bez](../platform/data-platform-metrics.md) nutnosti konfigurace. Vytvořte diagnostické nastavení pro odesílání položek do protokolů monitorování Azure nebo jejich předávání mimo Azure.
- [Protokoly prostředků](../platform/platform-logs-overview.md) – protokoly prostředků jsou automaticky generovány prostředky Azure, ale nejsou shromažďovány bez nastavení diagnostiky.  Vytvořte diagnostické nastavení pro odesílání položek do protokolů monitorování Azure nebo jejich předávání mimo Azure.
- [Protokol aktivit](../platform/platform-logs-overview.md) – Protokol aktivit se shromažďuje automaticky bez nutnosti konfigurace a lze zobrazit na webu Azure Portal. Vytvořte diagnostické nastavení, které je zkopíruje do protokolů monitorování Azure nebo je přepošlete mimo Azure.

### <a name="log-analytics-workspace"></a>Pracovní prostor služby Log Analytics
Shromažďování dat do protokolů monitorování Azure vyžaduje pracovní prostor Analýzy protokolů. Službu můžete rychle spustit vytvořením nového pracovního prostoru, ale může být hodnota v použití pracovního prostoru, který shromažďuje data z jiných služeb. Podrobnosti o vytvoření pracovního prostoru pracovního prostoru Azure [Portal najdete v tématu Vytvoření pracovního prostoru analýzy protokolů](../learn/quick-create-workspace.md) na webu Azure Portal, kde najdete podrobnosti o vytvoření pracovního prostoru a [návrhu nasazení protokolů azure monitoru,](../platform/design-logs-deployment.md) které vám pomůžou určit nejlepší návrh pracovního prostoru pro vaše požadavky. Pokud používáte existující pracovní prostor ve vaší organizaci, budete potřebovat příslušná oprávnění, jak je popsáno v [části Správa přístupu k datům protokolu a pracovním prostorům ve službě Azure Monitor](../platform/manage-access.md). 





## <a name="diagnostic-settings"></a>Nastavení diagnostiky
Diagnostická nastavení definují, kam mají být odesílány protokoly prostředků a metriky pro konkrétní prostředek. Možné destinace jsou:

- [Pracovní prostor Log Analytics,](../platform/resource-logs-collect-workspace.md) který umožňuje analyzovat data s dalšími daty monitorování shromážděnými službou Azure Monitor pomocí výkonných dotazů protokolu a také využít další funkce Azure Monitoru, jako jsou výstrahy protokolů a vizualizace. 
- [Centra událostí](../platform/resource-logs-stream-event-hubs.md) pro streamování dat do externích systémů, jako jsou siem třetí strany a další řešení analýzy protokolů. 
- [Účet úložiště Azure,](../platform/resource-logs-collect-storage.md) který je užitečný pro auditování, statickou analýzu nebo zálohování.

Postupujte podle postupu v [části Nastavení vytváření diagnostiky, abyste shromažďovali protokoly platformy a metriky v Azure](../platform/diagnostic-settings.md) a vytvářeli a spravovali diagnostická nastavení prostřednictvím portálu Azure. Viz [Vytvoření nastavení diagnostiky v Azure pomocí šablony Správce prostředků](../platform/diagnostic-settings-template.md) k jejich definování v šabloně a povolení úplného monitorování prostředku při jeho vytvoření.


## <a name="monitoring-in-the-azure-portal"></a>Monitorování na webu Azure Portal
 Přístup k datům monitorování pro většinu prostředků Azure můžete přistupovat z nabídky prostředků na webu Azure Portal. To vám umožní přístup k datům jednoho prostředku pomocí standardních nástrojů Azure Monitor. Některé služby Azure budou poskytovat různé možnosti, takže byste měli odkazovat na dokumentaci pro tuto službu pro další informace. Nabídka **Azure Monitor** slouží k analýze dat ze všech monitorovaných prostředků. 

### <a name="overview"></a>Přehled
Mnoho služeb bude zahrnovat data monitorování na stránce **Přehled** jako rychlý pohled na jejich provoz. To bude obvykle založené na podmnožině metrik platformy uložené v metrikách Azure Monitor. Další možnosti monitorování budou obvykle k dispozici v části **monitorování** služeb. .

![Stránka s přehledem](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Přehledy a řešení 
Některé služby budou poskytovat nástroje nad rámec standardních funkcí Azure Monitoru. [Přehledy](../insights/insights-overview.md) poskytují přizpůsobené monitorování integrované na datové platformě Azure Monitora a standardních funkcích. [Řešení](../insights/solutions.md) poskytují předdefinovanou logiku monitorování postavenou na protokolech monitorování Azure. 

Pokud má služba přehled Azure Monitor, můžete k ní přistupovat z **monitorování** v nabídce každého prostředku. Získejte přístup ke všem přehledům a řešením z nabídky **Azure Monitor.**

![Insights](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metriky
Analyzujte metriky na webu Azure Portal pomocí [průzkumníka metrik,](../platform/metrics-getting-started.md) který je k dispozici v položce nabídky **Metriky** pro většinu služeb. Tento nástroj umožňuje pracovat s jednotlivými metrikami nebo kombinovat více k identifikaci korelací a trendů. 

- Základní informace o používání průzkumníka metrik najdete v tématu [Začínáme s Průzkumníkem metrik Azure.](../platform/metrics-getting-started.md)
- V [tématu Pokročilé funkce Průzkumníka metrik Azure](../platform/metrics-charts.md) pro pokročilé funkce průzkumníka metrik, jako je použití více metrik a použití filtrů a rozdělení.

![Metriky](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Protokol aktivit 
Zobrazení položek v protokolu aktivit na portálu Azure s počátečním filtrem nastaveným na aktuální prostředek. Zkopírujte protokol aktivit do pracovního prostoru Log Analytics, abyste k němu měli přístup a používali jej v dotazech protokolu a sešitech. 

- Viz [Zobrazení a načtení událostí protokolu aktivit Azure](../platform/activity-log-view.md) podrobnosti o zobrazení protokolu aktivit a načítání položek pomocí různých metod.
- Konkrétní události, které se zaznamenávají, najdete v dokumentaci ke službě Azure.

![Protokol aktivit](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor
Protokoly monitorování Azure konsoliduje protokoly a metriky z více služeb a dalších zdrojů dat pro analýzu pomocí výkonného nástroje pro dotazy. Jak je popsáno výše, vytvořte diagnostické nastavení pro shromažďování metrik platformy, protokolu aktivit a protokolů prostředků do pracovního prostoru Log Analytics v Azure Monitoru.

[Log Analytics](../log-query/get-started-portal.md) umožňuje pracovat s [dotazy protokolu](../log-query/log-query-overview.md), což je výkonná funkce Azure Monitor, který umožňuje provádět pokročilou analýzu dat protokolu pomocí plně vybavený dotazovací jazyk. Sem otevřete Analýzu **protokolů** z protokolů v nabídce **Monitorování** pro prostředek Azure pro práci s dotazy protokolu pomocí prostředku jako [oboru dotazu](../log-query/scope.md#query-scope). To vám umožní analyzovat data ve více tabulkách pouze pro tento prostředek. Pomocí **protokolů** z nabídky Azure Monitor získat přístup k protokolům pro všechny prostředky. 

- Viz [Začínáme s dotazy protokolu v Azure Monitoru](../log-query/get-started-queries.md) pro kurz o použití dotazovacího jazyka používaného k zápisu dotazů protokolu.
- Informace o tom, jak se protokoly prostředků shromažďují v protokolech Azure v Azure Monitoru, najdete v [tématu Shromažďování protokolů prostředků Azure v pracovním prostoru Analýzy protokolů v Azure Monitoru](../platform/resource-logs-collect-workspace.md) a podrobnosti o tom, jak k nim přistupovat v dotazu.
- Vysvětlení, jak jsou data protokolu prostředků strukturována v protokolech monitorování Azure, najdete v [tématu režim shromažďování](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) informací.
- Podrobnosti o každé službě Azure najdete v dokumentaci k její tabulce v protokolech azure monitoru.

![Protokoly](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitorování z příkazového řádku
K datům monitorování shromážděným z vašeho prostředku můžete přistupovat z příkazového řádku nebo zahrnout do skriptu pomocí [Azure PowerShellu](/powershell/azure/) nebo [Rozhraní příkazového řádku Azure](/cli/azure/). 

- Viz [odkaz na metriky vykreslování spojení se](/cli/azure/monitor/metrics) spojením se metrikami pro přístup k datům metrikz funkce cli.
- Viz [odkaz CLI Log Analytics](/cli/azure/ext/log-analytics/monitor/log-analytics) pro přístup k datům protokolů monitorování Azure pomocí dotazu protokolu z příkazového příkazového příkazu.
- Viz [odkaz na metriky Azure PowerShellu](/powershell/module/azurerm.insights/get-azurermmetric) pro přístup k datům metrik z Azure PowerShellu.
- Viz [Odkaz na dotaz protokolu Azure PowerShellu](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) pro přístup k datům protokolů azure monitoru pomocí dotazu protokolu z Azure PowerShellu.

## <a name="monitoring-from-rest-api"></a>Monitorování z rozhraní REST API
Zahrnout data monitorování shromážděná z vašeho prostředku ve vlastní aplikaci pomocí rozhraní REST API.

- Podrobnosti o přístupu k metrikám z rozhraní AZURE Monitor REST API najdete v tématu Azure [Monitoring REST API.](../platform/rest-api-walkthrough.md)
- Informace o přístupu k datům protokolů Azure Monitor logs pomocí dotazu protokolu z Azure PowerShellu najdete v tématu Rozhraní REST API Azure Log [Analytics.](https://dev.loganalytics.io/)

## <a name="alerts"></a>Výstrahy
[Výstrahy](../platform/alerts-overview.md) vás aktivně upozorní a potenciálně podniknou kroky, pokud jsou v datech monitorování nalezeny důležité podmínky. Vytvoříte pravidlo výstrahy, které definuje cíl pro výstrahu, podmínky pro vytvoření výstrahy a všechny akce, které mají být přijaty v reakci.

Různé druhy dat monitorování se používají pro různé druhy pravidel výstrah.

- [Výstraha protokolu aktivit](../platform/alerts-activity-log.md) – vytvoří výstrahu při vytvoření položky v protokolu aktivit, která odpovídá určitým kritériím. To vám umožní být upozorněni například při vytvoření určitého typu prostředku nebo pokud se změna konfigurace nezdaří.
- [Upozornění na metriku](../platform/alerts-metric.md) – vytvořte výstrahu, když hodnota metriky překročí určitou prahovou hodnotu. Upozornění na metriky jsou citlivější než ostatní výstrahy a lze je automaticky vyřešit při opravě problému.
- [Výstraha dotazu protokolu](../platform/alerts-log.md) – spustí dotaz protokolu v pravidelných intervalech a vytvoří výstrahu, pokud je nalezena určitá podmínka. To umožňuje provádět komplexní analýzy napříč více sadami dat a .

Pomocí **výstrah** z nabídky prostředku můžete zobrazit výstrahy a spravovat pravidla výstrah pro tento prostředek. Jenom výstrahy protokolu aktivit a upozornění metriky používají jako cíl jednotlivé prostředky Azure. Výstrahy dotazů protokolu používají pracovní prostor Log Analytics jako cíl a jsou založeny na dotazu, který má přístup k protokolům uloženým v tomto pracovním prostoru. Pomocí nabídky Azure Monitor můžete zobrazit a spravovat výstrahy pro všechny prostředky a pravidla výstrah se správou dotazů protokolu.

- Podrobnosti o vytváření pravidel výstrah najdete v článcích pro různé druhy výstrah výše.
- Podrobnosti o vytvoření skupiny akcí na webu Azure Portal najdete v tématu [Vytváření a správa skupin akcí,](../platform/action-groups.md) které umožňují spravovat odpovědi na výstrahy.



## <a name="next-steps"></a>Další kroky

* Podrobnosti o protokolech prostředků pro různé služby Azure najdete v tématu [Podporované služby, schémata a kategorie pro protokoly prostředků Azure.](../platform/diagnostic-logs-schema.md)  
