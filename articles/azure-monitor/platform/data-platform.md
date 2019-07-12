---
title: Azure Monitor datové platformy | Dokumentace Microsoftu
description: Monitorování data shromážděná službou Azure Monitor je rozdělené na protokoly, které se používají pro pokročilé analýzy a metriky, které jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 00c0fea9d8ca7ee299a9a19473917eba90edd675
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606977"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor datová platforma

Povolení observability napříč dnešní komplexních výpočetních prostředí spouštění distribuovaných aplikací, které závisí na cloudových a místních služeb vyžaduje shromažďování provozních dat z každé vrstvě a všechny komponenty distribuovaného systému. Musíte být schopni provést podrobné informace na těchto datech a konsolidovat do jediného najednou pomocí různých perspektiv pro podporu velkého množství zúčastněné strany ve vaší organizaci.

[Azure Monitor](../overview.md) shromažďuje a agreguje data z různých zdrojů do common data platformy, kde je možné pro analýzu, vizualizaci a upozorňování. Poskytuje konzistentní prostředí na data z víc zdrojů, která poskytuje podrobný přehled napříč všechny sledované prostředky a dokonce i s daty z jiných služeb, které ukládají data ve službě Azure Monitor.


![Přehled služby Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Observability data ve službě Azure Monitor
Metriky, protokoly a distribuované trasování se běžně označují jako tři pilíře observability. Toto jsou různé druhy dat, která jako nástroj pro sledování musí shromažďovat a analyzovat a poskytovat dostatečné observability monitorovaný systém. Observability lze dosáhnout korelaci dat z více pilíře a agregace dat napříč celou sadu prostředků, které jsou monitorovány. Protože Azure Monitor uchovává data z víc zdrojů společně, data mohou být přiřazeny a analyzovat pomocí společnou sadu nástrojů. Také koreluje data napříč více tenantů, kromě dat pro ostatní služby hostování a předplatných Azure.

Prostředky Azure generují značné množství dat monitorování. Azure Monitor konsoliduje tato data společně s monitorování dat z jiných zdrojů do jedné metriky nebo protokoly platformy. Každá je optimalizovaná pro konkrétní scénáře monitorování, a podporují různé funkce ve službě Azure Monitor. Funkce, jako jsou analýzy dat, vizualizace nebo výstrahy vyžadují, abyste porozumět rozdílům tak, aby vámi požadovaného scénáře můžete implementovat způsobem nejvíce efektivnější a cenově výhodnější. Insights ve službě Azure Monitor, jako [Application Insights](../app/app-insights-overview.md) nebo [monitorování Azure pro virtuální počítače](../insights/vminsights-overview.md) mají analytické nástroje, které vám umožní zaměřit na konkrétní scénář monitorování bez nutnosti znát rozdíly mezi těmito dvěma typy data. 


### <a name="metrics"></a>Metriky
[Metriky](data-platform-metrics.md) jsou číselné hodnoty, které popisují některé aspekty systému v určitém místě v čase. Jsou shromážděné v pravidelných intervalech a jsou označeny časové razítko, název, hodnotu a jeden nebo více štítků definující. Metriky se dají agregovat pomocí různých algoritmů, ve srovnání s dalšími metrikami a analyzovat trendy v čase. 

Metriky ve službě Azure Monitor ukládají do databáze časových řad, který je optimalizovaný pro analýzu dat s časovým razítkem. Tomu metriky je obzvláště vhodný pro generování výstrah a rychlé zjišťování chyb. Osoba může zjistit, jaký je výkon vašeho systému, ale většinou potřebují a nelze jej zkombinovat s protokoly a identifikovat hlavní příčinu problémů.

Metriky jsou k dispozici pro interaktivní analýzu na webu Azure Portal s [Průzkumníka metrik](../app/metrics-explorer.md). Je možné je přidat do [řídicí panel Azure](../learn/tutorial-app-dashboards.md) za účelem vizualizace v kombinaci s ostatními daty a použít pro téměř v reálném čase [upozorňování](alerts-metric.md).

Další informace o monitorování metrik Azure, včetně jejich zdroje dat v [metriky ve službě Azure Monitor](data-platform-metrics.md).

### <a name="logs"></a>Logs
[Protokoly](data-platform-logs.md) jsou události, ke kterým došlo v rámci systému. Jsou může obsahovat různé druhy dat a může být strukturovaná nebo volnotextové formuláře s časovým razítkem. Je možné se vytvořil nedojde a generovat položky protokolu událostí v prostředí systému v případě velkého zatížení obvykle vygeneruje další svazek protokolu.

Protokoly ve službě Azure Monitor se ukládají v pracovním prostoru Log Analytics, která je založena na [Průzkumník dat Azure](/azure/data-explorer/) poskytující modulu výkonné analýzy a [Bohatý dotazovací jazyk](/azure/kusto/query/). Protokoly obvykle poskytnout dostatek informací, které poskytují úplný kontext se zjištěného problému a jsou užitečné pro určení kořenové případ problémy.

> [!NOTE]
> Je důležité rozlišovat mezi protokoly Azure monitoru a zdroje dat protokolu v Azure. Například událostí na úrovni předplatného v Azure se zapisují do [protokolu aktivit](activity-logs-overview.md) , který se zobrazí v nabídce Azure Monitor. Většinu prostředků se zapisuje provozní informace do [diagnostický protokol](diagnostic-logs-overview.md) předávání do různých umístění. Protokoly služby Azure Monitor je platforma dat protokolu, který shromažďuje protokoly aktivit a diagnostické protokoly spolu s dalšími daty monitorování poskytovat hloubkovou analýzu napříč celou sadu prostředků.


 Můžete pracovat s [protokolu dotazy](../log-query/log-query-overview.md) interaktivně pomocí [Log Analytics](../log-query/portals.md) na webu Azure Portal, nebo přidejte výsledky a [řídicí panel Azure](../learn/tutorial-app-dashboards.md) za účelem vizualizace v kombinaci s Další data. Můžete také vytvořit [upozornění protokolů](alerts-log.md) která aktivuje upozornění na základě výsledků dotazu plánu.

Další informace o monitorování protokolů Azure, včetně jejich zdroje dat v [protokolů ve službě Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Distribuované trasování
Trasování jsou řadu souvisejících událostí, které následují žádost uživatele prostřednictvím distribuovaného systému. Jejich slouží k určení chování kódu aplikace a výkonu různých transakcí. Když protokoly se často vytvářejí z jednotlivých součástí distribuovaný systém, trasování měří provoz a výkon vaší aplikace napříč celou sadu komponent.

Distribuované trasování ve službě Azure Monitor je povolená s [Application Insights SDK](../app/distributed-tracing.md), a data trasování je uložen s ostatními daty protokolu aplikace shromážděných službou Application Insights. Díky tomu je k dispozici stejné analytické nástroje, jako ostatní data protokolu, včetně dotazů na protokoly, řídicích panelů a výstrah.

Další informace o distribuované trasování na [co je distribuované trasování?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Porovnání Azure monitorování metrik a protokolů

Následující tabulka porovnává metrik a protokolů ve službě Azure Monitor.

| Atribut  | Metriky | Logs |
|:---|:---|:---|
| Výhody | Odlehčená a podporuje téměř v reálném čase scénářů, jako je třeba výstrahy. Ideální pro rychlé zjišťování chyb. | Analýza s bohatou dotazovací jazyk. Ideální pro hloubkovou analýzu a identifikovat hlavní příčinu. |
| Data | Pouze číselné hodnoty | Textová nebo číselná data |
| Struktura | Standardní sadu vlastností, včetně doba vzorku, monitorovány zdroj, číselnou hodnotu. Některé metriky zahrnují více dimenzí pro další definice. | Jedinečnou sadu vlastností v závislosti na typu protokolu. |
| Collection | Shromážďovány v pravidelných intervalech. | Mohou být shromažďovány nedojde, jak aktivovat události záznam, který se má vytvořit. |
| Zobrazit na webu Azure portal | Průzkumník metrik | Log Analytics |
| Zahrnout zdroje dat | Platforma metrik shromážděných z prostředků Azure.<br>Služba Application Insights monitorovat aplikace.<br>Vlastní aplikace nebo API definována. | Aplikace a diagnostické protokoly.<br>Řešení monitorování.<br>Agenti a rozšíření virtuálních počítačů.<br>Požadavky na aplikaci a výjimky.<br>Azure Security Center.<br>Rozhraní API kolekce dat |

## <a name="collect-monitoring-data"></a>Shromažďování dat monitorování
Různé [zdroje dat pro monitorování Azure](data-sources.md) bude zapisovat do pracovního prostoru Log Analytics (protokoly) nebo Azure Monitor metriky databáze (metriky) nebo obě. Některé zdroje budou zapisovat přímo do těchto úložišť dat, zatímco jiné můžou zapisovat do jiného umístění, jako je Azure storage a vyžadovat určitou konfiguraci k naplnění protokolů nebo metrik. 

Zobrazit [metriky ve službě Azure Monitor](data-platform-metrics.md) a [protokolů ve službě Azure Monitor](data-platform-logs.md) seznam různých zdrojů dat, která naplní jednotlivých typů.


## <a name="stream-data-to-external-systems"></a>Datový Stream k externím systémům
Kromě použití nástroje pro analýzu dat monitorování v Azure, můžete mít povinnost předat externího nástroje, jako jsou informace o zabezpečení a událostí produktů pro správu (SIEM). Toto přesměrování se obvykle provádí přímo z monitorovaných prostředků prostřednictvím [Azure Event Hubs](/azure/event-hubs/). K odesílání dat přímo do centra událostí, zatímco jiný proces, jako je například aplikace logiky můžete použít k načtení požadovaných dat můžete nakonfigurovat některé zdroje. Zobrazit [pomocí externího nástroje pro monitorování data do centra událostí pro používání Azure Stream](stream-monitoring-data-event-hubs.md) podrobnosti.



## <a name="next-steps"></a>Další postup

- Další informace o [metriky ve službě Azure Monitor](data-platform-metrics.md).
- Další informace o [protokolů ve službě Azure Monitor](data-platform-logs.md).
- Další informace o [data k dispozici monitorování](data-sources.md) pro různé prostředky v Azure.
