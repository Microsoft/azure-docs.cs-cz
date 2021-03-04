---
title: Datová platforma Azure Monitor | Microsoft Docs
description: Data monitorování shromážděná pomocí Azure Monitor jsou rozdělená na metriky, které jsou odlehčené a schopné podpořit podporu scénářů a protokolů v reálném čase, které se používají pro pokročilou analýzu.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 7356b9bb814f8bca5465fe74d48409b9dbca6d3b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731693"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor datovou platformu

Díky tomu, že v dnešních složitých výpočetních prostředích využívajících distribuované aplikace, které spoléhají na cloudové i místní služby, je potřeba mít na výběr provozní data z každé vrstvy a všechny součásti distribuovaného systému. Musíte být schopni provádět podrobné přehledy o těchto datech a konsolidovat je do jednoho podokna skla s různými perspektivami, aby podporovaly velké množství zúčastněných stran ve vaší organizaci.

[Azure monitor](overview.md) shromažďuje a agreguje data z nejrůznějších zdrojů do společné datové platformy, kde se dá použít k analýze, vizualizaci a upozorňování. Nabízí konzistentní prostředí nad daty z více zdrojů, což poskytuje podrobné přehledy napříč všemi monitorovanými prostředky a dokonce i s daty z jiných služeb, které ukládají svá data v Azure Monitor.


![Přehled služby Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Data o pozorování v Azure Monitor
Metriky, protokoly a distribuovaná trasování se běžně označují jako tři pilíře, které je možné pozorovat. Jedná se o různé druhy dat, které nástroj pro monitorování musí shromažďovat a analyzovat, aby poskytoval dostatečnou pozorovateli monitorovaného systému. Možnost pozorování se dá dosáhnout korelacemi dat z několika pilířů a agregací dat napříč celou sadou monitorovaných prostředků. Vzhledem k tomu, že Azure Monitor ukládá data z více zdrojů dohromady, lze data sladit a analyzovat pomocí společné sady nástrojů. Také koreluje data mezi několika předplatnými Azure a klienty, kromě hostování dat pro jiné služby.

Prostředky Azure generují významné množství dat monitorování. Azure Monitor konsoliduje tato data společně s daty monitorování z jiných zdrojů do metriky nebo na platformu protokolů. Každé je optimalizováno pro konkrétní scénáře monitorování a každá podporuje různé funkce v Azure Monitor. Funkce, jako jsou analýza dat, vizualizace nebo upozorňování, vyžadují, abyste porozuměli rozdílům, abyste mohli požadovaný scénář implementovat nejúčinnějším a cenově nejefektivnějším způsobem. Přehledy v Azure Monitor, jako je například [Application Insights](app/app-insights-overview.md) nebo [virtuální počítač Insights](vm/vminsights-overview.md) , mají analytické nástroje, které vám umožní zaměřit se na konkrétní scénář monitorování, aniž by bylo nutné porozumět rozdílům mezi dvěma typy dat. 


### <a name="metrics"></a>Metriky
[Metriky](essentials/data-platform-metrics.md) jsou číselné hodnoty, které popisují určitý aspekt systému v určitém časovém okamžiku. Jsou shromažďovány v pravidelných intervalech a jsou označeny časovým razítkem, názvem, hodnotou a jedním nebo více definujícími popisky. Metriky se dají agregovat pomocí nejrůznějších algoritmů, srovnávat s jinými metrikami a analyzovat z hlediska trendů v čase. 

Metriky v Azure Monitor jsou uloženy v databázi časových řad, která je optimalizována pro analýzu časových údajů s časovým razítkem. Díky tomu jsou metriky zvláště vhodné pro upozorňování a rychlé zjišťování problémů. Můžou vám sdělit, jak systém funguje, ale obvykle se musí kombinovat s protokoly, aby identifikoval hlavní příčinu problémů.

Metriky jsou k dispozici pro interaktivní analýzu v Azure Portal s využitím [Azure Průzkumník metrik](essentials/metrics-getting-started.md). Můžete je přidat do [řídicího panelu Azure](app/tutorial-app-dashboards.md) pro vizualizaci v kombinaci s ostatními daty a používat pro [výstrahy](alerts/alerts-metric.md)téměř v reálném čase.

Přečtěte si další informace o Azure Monitor metrikách, včetně jejich zdrojů dat v [metrikách v Azure monitor](essentials/data-platform-metrics.md).

### <a name="logs"></a>Protokoly
[Protokoly](logs/data-platform-logs.md) jsou události, ke kterým došlo v rámci systému. Můžou obsahovat různé druhy dat a můžou být strukturované nebo volné textové zprávy s časovým razítkem. Můžou být vytvářené občas, jak události v prostředí generují položky protokolu. Systém při velkém zatížení obvykle vygeneruje objemnější protokol.

Protokoly v Azure Monitor jsou uložené v pracovním prostoru Log Analytics, který je založený na [službě Azure Průzkumník dat](/azure/data-explorer/) , která poskytuje výkonný analytický modul a [bohatý dotazovací jazyk](/azure/kusto/query/). Protokoly obvykle poskytují dostatek informací, které poskytují úplný kontext zjištěného problému a jsou důležité pro identifikaci kořenového případu problémů.

> [!NOTE]
> Je důležité rozlišovat mezi protokoly Azure Monitor a zdroji dat protokolů v Azure. Například události na úrovni předplatného v Azure se zapisují do [protokolu aktivit](essentials/platform-logs-overview.md) , který můžete zobrazit v nabídce Azure monitor. Většina prostředků zapisuje provozní informace do [protokolu prostředků](essentials/platform-logs-overview.md) , který můžete přemístit do různých umístění. Protokoly Azure Monitor jsou datovou platformou protokolu, která shromažďuje protokoly aktivit a protokoly prostředků spolu s dalšími daty monitorování a zajišťuje tak hloubkovou analýzu v celé sadě prostředků.


 S [dotazy protokolu](logs/log-query-overview.md) můžete pracovat interaktivně pomocí [Log Analytics](logs/log-query-overview.md) v Azure Portal nebo přidat výsledky do [řídicího panelu Azure](app/tutorial-app-dashboards.md) pro vizualizaci v kombinaci s jinými daty. Můžete také vytvořit [výstrahy protokolu](alerts/alerts-log.md) , které aktivují výstrahu na základě výsledků dotazu plánu.

Přečtěte si další informace o protokolech Azure Monitor, včetně jejich zdrojů dat, v části [protokoly v Azure monitor](logs/data-platform-logs.md).

### <a name="distributed-traces"></a>Distribuované trasování
Trasování jsou série souvisejících událostí, které následují požadavky uživatele prostřednictvím distribuovaného systému. Lze je použít k určení chování kódu aplikace a výkonu různých transakcí. I když budou protokoly často vytvářeny jednotlivými součástmi distribuovaného systému, trasování měří provoz aplikace a výkon aplikace napříč celou sadou komponent.

Distribuované trasování v Azure Monitor je povolené pomocí [sady Application Insights SDK](app/distributed-tracing.md)a data trasování se ukládají s dalšími daty protokolů aplikací shromážděnými pomocí Application Insights. Díky tomu je k dispozici pro stejné analytické nástroje jako jiná data protokolu, včetně dotazů protokolu, řídicích panelů a výstrah.

Přečtěte si další informace o distribuovaném trasování v tématu [co je distribuované trasování?](app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Porovnat Azure Monitor metriky a protokoly

Následující tabulka porovnává metriky a protokoly v Azure Monitor.

| Atribut  | Metriky | Protokoly |
|:---|:---|:---|
| Výhody | Odlehčené a schopné scénáře téměř v reálném čase, jako jsou například výstrahy. Ideální pro rychlé zjišťování problémů. | Analyzováno pomocí bohatých dotazovacích jazyků. Ideální pro hloubkovou analýzu a identifikaci hlavní příčiny. |
| Data | Pouze číselné hodnoty | Textová nebo číselná data |
| Struktura | Standardní sada vlastností včetně času vzorkování, monitorovaného prostředku a číselné hodnoty Některé metriky obsahují více dimenzí pro další definice. | Jedinečná sada vlastností v závislosti na typu protokolu. |
| Kolekce | Shromažďováno v pravidelných intervalech. | Může být shromažďována občas, protože události spouštějí záznam, který se má vytvořit. |
| Zobrazení na webu Azure Portal | Průzkumník metrik | Log Analytics |
| Zdroje dat zahrnují | Metriky platformy shromážděné z prostředků Azure.<br>Aplikace monitorované pomocí Application Insights.<br>Vlastní definuje aplikace nebo rozhraní API. | Protokoly aplikací a prostředků.<br>Monitorování řešení.<br>Agenti a rozšíření virtuálních počítačů.<br>Žádosti a výjimky aplikace<br>Azure Security Center<br>Rozhraní API kolekce dat. |

## <a name="collect-monitoring-data"></a>Shromažďování dat monitorování
Různé [zdroje dat pro Azure monitor](agents/data-sources.md) budou zapisovat do pracovního prostoru Log Analytics (protokoly) nebo do databáze metriky Azure monitor (metriky) nebo do obou možností. Některé zdroje budou zapisovat přímo do těchto úložišť dat, zatímco ostatní můžou zapisovat do jiného umístění, jako je Azure Storage, a vyžadují, aby některá konfigurace naplnila protokoly nebo metriky. 

Seznam různých zdrojů dat, které naplňují jednotlivé typy, najdete v tématu [metriky v Azure monitor](essentials/data-platform-metrics.md) a [v Azure monitor protokolů](logs/data-platform-logs.md) .


## <a name="stream-data-to-external-systems"></a>Streamování dat do externích systémů
Kromě používání nástrojů v Azure k analýze dat monitorování je možné, že budete mít požadavek na jejich přeposlání na externí nástroj, jako je například produkt pro správu událostí a SIEM (Security Information and Event Management). Toto přesměrování se obvykle provádí přímo ze sledovaných prostředků prostřednictvím [Azure Event Hubs](../event-hubs/index.yml). Některé zdroje je možné nakonfigurovat tak, aby odesílaly data přímo do centra událostí, zatímco můžete k získání požadovaných dat použít jiný proces, jako je například aplikace logiky. Podrobnosti najdete v tématu [streamování dat monitorování Azure do centra událostí pro využití externím nástrojem](essentials/stream-monitoring-data-event-hubs.md) .



## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [metrikách v Azure monitor](essentials/data-platform-metrics.md).
- Přečtěte si další informace o [protokolech v Azure monitor](logs/data-platform-logs.md).
- Přečtěte si o [dostupných datech monitorování](agents/data-sources.md) různých prostředků v Azure.

