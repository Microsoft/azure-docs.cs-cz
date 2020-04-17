---
title: Datová platforma Azure Monitor | Dokumenty společnosti Microsoft
description: Data monitorování shromážděná službou Azure Monitor jsou rozdělena do metrik, které jsou zjednodušené a schopné podporovat scénáře a protokoly téměř v reálném čase, které se používají pro pokročilou analýzu.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 58f542238c952088777ed9809b57dae3cdb9cf12
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457259"
---
# <a name="azure-monitor-data-platform"></a>Datová platforma Azure Monitor

Povolení pozorovatelnosti v dnešních složitých výpočetních prostředích s pouštějících distribuované aplikace, které jsou závislé na cloudových i místních službách, vyžaduje sběr provozních dat z každé vrstvy a všech součástí distribuovaného systému. Musíte být schopni provádět podrobné přehledy o těchto datech a konsolidovat je do jednoho podokna skla s různými perspektivami, abyste podpořili velké množství zúčastněných stran ve vaší organizaci.

[Azure Monitor](../overview.md) shromažďuje a agreguje data z různých zdrojů do společné datové platformy, kde ji lze použít pro analýzu, vizualizaci a výstrahy. Poskytuje konzistentní prostředí nad daty z více zdrojů, což vám poskytuje podrobné přehledy napříč všemi sledovanými prostředky a dokonce i s daty z jiných služeb, které ukládají svá data ve službě Azure Monitor.


![Přehled služby Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Data pozorovatelnosti ve službě Azure Monitor
Metriky, protokoly a distribuované trasování se běžně označují jako tři pilíře pozorovatelnosti. Jedná se o různé druhy dat, které musí monitorovací nástroj shromažďovat a analyzovat, aby byla zajištěna dostatečná pozorovatelnost monitorovaného systému. Pozorovatelnosti lze dosáhnout korelace dat z více pilířů a agregace dat v celé sadě sledovaných prostředků. Vzhledem k tomu, že Azure Monitor ukládá data z více zdrojů dohromady, data mohou korelovat a analyzovat pomocí společné sady nástrojů. Také koreluje data mezi více předplatnými Azure a tenanty, kromě hostingových dat pro jiné služby.

Prostředky Azure generují značné množství dat monitorování. Azure Monitor konsoliduje tato data spolu s daty monitorování z jiných zdrojů do platformy Metriky nebo protokoly. Každý je optimalizovaný pro konkrétní scénáře monitorování a každý podporuje různé funkce ve službě Azure Monitor. Funkce, jako je analýza dat, vizualizace nebo výstrahy, vyžadují, abyste pochopili rozdíly, abyste mohli implementovat požadovaný scénář co nejefektivnějším a nákladově nejefektivnějším způsobem. Přehledy v Azure Monitor, jako jsou [Application Insights](../app/app-insights-overview.md) nebo Azure Monitor pro [virtuální počítače](../insights/vminsights-overview.md) mají analytické nástroje, které vám umožní soustředit se na konkrétní scénář monitorování bez nutnosti pochopit rozdíly mezi dvěma typy dat. 


### <a name="metrics"></a>Metriky
[Metriky](data-platform-metrics.md) jsou číselné hodnoty, které popisují některé aspekty systému v určitém okamžiku v čase. Jsou shromažďovány v pravidelných intervalech a jsou označeny časovým razítkem, názvem, hodnotou a jedním nebo více definujícími popisky. Metriky lze agregovat pomocí různých algoritmů ve srovnání s jinými metrikami a analyzovat trendy v průběhu času. 

Metriky ve službě Azure Monitor jsou uloženy v databázi časových řad, která je optimalizovaná pro analýzu dat s časovým razítkem. Díky tomu metriky jsou obzvláště vhodné pro upozorňování a rychlé zjišťování problémů. Mohou vám říct, jak váš systém funguje, ale obvykle je třeba kombinovat s protokoly k identifikaci hlavní příčinu problémů.

Metriky jsou k dispozici pro interaktivní analýzu na webu Azure Portal s [Průzkumníkem metrik Azure](../platform/metrics-getting-started.md). Lze je přidat do [řídicího panelu Azure](../learn/tutorial-app-dashboards.md) pro vizualizaci v kombinaci s jinými daty a použít pro [výstrahy](alerts-metric.md)téměř v reálném čase .

Přečtěte si další informace o metrikách Azure Monitor, včetně jejich zdrojů dat v [metrikách v Azure Monitoru](data-platform-metrics.md).

### <a name="logs"></a>Protokoly
[Protokoly](data-platform-logs.md) jsou události, ke kterým došlo v rámci systému. Mohou obsahovat různé druhy dat a mohou být strukturované nebo volné formě textu s časovým razítkem. Mohou být vytvořeny sporadicky jako události v prostředí generovat položky protokolu a systém pod velkým zatížením obvykle generovat více svazku protokolu.

Protokoly v Azure Monitoru jsou uložené v pracovním prostoru Log Analytics, který je založený na [Azure Data Explorer,](/azure/data-explorer/) který poskytuje výkonný modul analýzy a [bohatý dotazovací jazyk](/azure/kusto/query/). Protokoly obvykle poskytují dostatek informací poskytnout úplný kontext problému, který je identifikován a jsou cenné pro identifikaci kořenového případu problémů.

> [!NOTE]
> Je důležité rozlišovat mezi protokoly monitorování Azure a zdroji dat protokolu v Azure. Například události na úrovni předplatného v Azure se zapisují do [protokolu aktivit,](platform-logs-overview.md) který můžete zobrazit z nabídky Azure Monitor. Většina prostředků bude zapisovat provozní informace do [protokolu prostředků,](platform-logs-overview.md) který můžete předat do různých umístění. Azure Monitor Logs je datová platforma protokolu, která shromažďuje protokoly aktivit a protokoly prostředků spolu s dalšími daty monitorování, aby poskytovala hloubkovou analýzu napříč celou sadou prostředků.


 Můžete pracovat s [dotazy protokolu](../log-query/log-query-overview.md) interaktivně s [Log Analytics](../log-query/portals.md) na webu Azure portal nebo přidat výsledky na [řídicí panel Azure](../learn/tutorial-app-dashboards.md) pro vizualizaci v kombinaci s jinými daty. Můžete také vytvořit [výstrahy protokolu,](alerts-log.md) které spustí výstrahu na základě výsledků dotazu plánu.

Přečtěte si další informace o protokolech monitorování Azure, včetně jejich zdrojů dat v [protokolech v Azure Monitoru](data-platform-logs.md).

### <a name="distributed-traces"></a>Distribuované stopy
Trasování jsou řada souvisejících událostí, které následují požadavek uživatele prostřednictvím distribuovaného systému. Lze je použít k určení chování kódu aplikace a výkonu různých transakcí. Zatímco protokoly budou často vytvářeny jednotlivými součástmi distribuovaného systému, trasování měří provoz a výkon aplikace v celé sadě součástí.

Distribuované trasování v Azure Monitoru je povoleno pomocí [sady Application Insights SDK](../app/distributed-tracing.md)a data trasování se ukládají s dalšími daty protokolu aplikací shromážděnými application insights. Díky tomu je k dispozici pro stejné nástroje analýzy jako ostatní data protokolu, včetně dotazů protokolu, řídicích panelů a výstrah.

Přečtěte si další informace o distribuovaném trasování na adrese [Co je distribuované trasování?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Porovnání metrik a protokolů azure monitoru

Následující tabulka porovnává metriky a protokoly v Azure Monitoru.

| Atribut  | Metriky | Protokoly |
|:---|:---|:---|
| Výhody | Lehký a schopný téměř v reálném čase scénáře, jako je například upozornění. Ideální pro rychlou detekci problémů. | Analyzováno s rozšířeným dotazovacím jazykem. Ideální pro hlubokou analýzu a identifikaci hlavní příčiny. |
| Data | Pouze číselné hodnoty | Textová nebo číselná data |
| Struktura | Standardní sada vlastností včetně ukázkového času, sledovaného prostředku, číselné hodnoty. Některé metriky obsahují více dimenzí pro další definici. | Jedinečná sada vlastností v závislosti na typu protokolu. |
| Kolekce | Shromažďovány v pravidelných intervalech. | Mohou být shromažďovány sporadicky jako události aktivovat záznam, který má být vytvořen. |
| Zobrazení na webu Azure Portal | Průzkumník metrik | Log Analytics |
| Zdroje dat zahrnují | Metriky platformy shromážděné z prostředků Azure.<br>Aplikace monitorované application insights.<br>Vlastní definované aplikací nebo rozhraním API. | Protokoly aplikací a prostředků.<br>Monitorovací řešení.<br>Agenti a rozšíření virtuálních min.<br>Žádosti o aplikaci a výjimky.<br>Azure Security Center.<br>Rozhraní API kolektoru dat. |

## <a name="collect-monitoring-data"></a>Shromažďování dat monitorování
Různé [zdroje dat pro Azure Monitor](data-sources.md) bude zapisovat do pracovního prostoru Log Analytics (protokoly) nebo databáze metrik Azure Monitor (metriky) nebo obojí. Některé zdroje budou zapisovat přímo do těchto úložišť dat, zatímco jiné mohou zapisovat do jiného umístění, jako je úložiště Azure a vyžadovat určitou konfiguraci k naplnění protokolů nebo metrik. 

Viz [metriky v Azure Monitor](data-platform-metrics.md) a [protokoly v Azure Monitor](data-platform-logs.md) u výpisu různých zdrojů dat, které naplňují každý typ.


## <a name="stream-data-to-external-systems"></a>Streamování dat do externích systémů
Kromě použití nástrojů v Azure k analýze dat monitorování můžete mít požadavek na jejich předání externímu nástroji, jako je produkt pro správu informací o zabezpečení a událostí (SIEM). Toto předávání se obvykle provádí přímo z monitorovaných prostředků prostřednictvím [Azure Event Hubs](/azure/event-hubs/). Některé zdroje lze nakonfigurovat tak, aby odesílaly data přímo do centra událostí, zatímco k načtení požadovaných dat můžete použít jiný proces, například aplikaci logiky. Podrobnosti najdete v [tématu Stream Azure monitoring data do centra událostí pro spotřebu pomocí externího nástroje.](stream-monitoring-data-event-hubs.md)



## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [metriky v Azure Monitor](data-platform-metrics.md).
- Přečtěte si další informace o [protokolech v Azure Monitoru](data-platform-logs.md).
- Přečtěte si o [datech monitorování dostupných](data-sources.md) pro různé prostředky v Azure.
