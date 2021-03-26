---
title: Metriky v Azure Monitor | Microsoft Docs
description: Popisuje metriky v Azure Monitor, což jsou odlehčená data monitorování schopná podporovat scénáře téměř v reálném čase.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 68e947a6e13ba5195815fe966ec69ec6a2f4b8e1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562966"
---
# <a name="azure-monitor-metrics-overview"></a>Přehled metrik Azure Monitor
Metriky Azure Monitor jsou funkcí Azure Monitor, která shromažďuje číselná data z [monitorovaných prostředků](../monitor-reference.md) do databáze časových řad. Metriky jsou číselné hodnoty, které jsou shromažďovány v pravidelných intervalech a popisují určitý aspekt systému v určitou dobu. Metriky v Azure Monitor jsou odlehčené a umožňují podpoře scénářů téměř v reálném čase, které jsou zvláště užitečné pro upozorňování a rychlé zjišťování problémů. Můžete je interaktivně analyzovat pomocí Průzkumníka metrik, pokud je hodnota překročena prahovou hodnotou nebo je vizualizovat v sešitu nebo řídicím panelu, bude aktivní upozornění.


> [!NOTE]
> Azure Monitor metriky jsou jednou ze polovině datové platformy, která podporuje Azure Monitor. Druhý je [Azure monitor protokolů](../logs/data-platform-logs.md) , které shromažďují a organizují data protokolů a výkonu a umožňují jejich analýzu pomocí bohatých dotazovacích jazyků. Metriky jsou jednodušší než data v Azure Monitor protokoly a umožňují podpoře scénářů téměř v reálném čase, které jsou zvláště užitečné pro upozorňování a rychlé zjišťování problémů. Metriky, i když můžou ukládat jenom číselná data do konkrétní struktury, zatímco protokoly můžou ukládat různé datové typy z každého s vlastní strukturou. Pomocí dotazů protokolu, které se nedají použít k analýze dat metrik, můžete také provádět komplexní analýzy dat protokolů.


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Co se dá dělat s Azure Monitor metrikami?
V následující tabulce jsou uvedeny různé způsoby, jak můžete použít metriky v Azure Monitor.

|  | Description |
|:---|:---|
| **Analyzovat** | Pomocí [Průzkumníka metrik](metrics-charts.md) můžete analyzovat shromážděné metriky v grafu a porovnat metriky z různých prostředků. |
| **Výstraha** | Nakonfigurujte [pravidlo upozornění metriky](../alerts/alerts-metric.md) , které pošle oznámení nebo provede [automatizovanou akci](../alerts/action-groups.md) , když hodnota metriky přechází ze prahové hodnoty. |
| **Vizualizace** | Připněte graf z Průzkumníka metrik na [řídicí panel Azure](../app/tutorial-app-dashboards.md).<br>Vytvoří [sešit](../visualize/workbooks-overview.md) pro kombinování s více sadami dat v interaktivní sestavě. Exportujte výsledky dotazu do [Grafana](../visualize/grafana-plugin.md) a využijte jeho řídicí panely a kombinaci s jinými zdroji dat. |
| **Automatizace** |  Pomocí [automatického škálování](../autoscale/autoscale-overview.md) můžete zvýšit nebo snížit prostředky na základě hodnoty metriky, která překračuje prahovou hodnotu. |
| **Stahovat** | Přístup k hodnotám metrik z příkazového řádku pomocí  [rutin PowerShellu](/powershell/module/az.applicationinsights)<br>Přístup k hodnotám metrik z vlastní aplikace pomocí [REST API](./rest-api-walkthrough.md).<br>Přístup k hodnotám metrik z příkazového řádku pomocí  [CLI](/cli/azure/monitor/metrics). |
| **Export** | [Směrování metriky do protokolů](./resource-logs.md#send-to-azure-storage) pro analýzu dat v Azure monitor metriky spolu s daty v protokolech Azure monitor a ukládání hodnot metriky po dobu delší než 93 dní.<br>Streamujte metriky do [centra událostí](./stream-monitoring-data-event-hubs.md) , abyste je mohli směrovat do externích systémů. |
| **Archiv** | [Archivujte](./platform-logs-overview.md) historii výkonu nebo stavu prostředku pro účely dodržování předpisů, auditování nebo offline generování sestav. |

![Přehled metrik](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>Shromažďování dat
Existují tři základní zdroje metrik, které shromažďuje Azure Monitor. Jakmile se tyto metriky shromažďují v databázi metrik Azure Monitor, dají se vyhodnotit společně bez ohledu na jejich zdroj.

**Prostředky Azure**. Prostředky Azure vytvářejí metriky platforem a poskytují přehled o stavu a výkonu. Každý typ prostředku vytvoří [jedinečnou sadu metrik](./metrics-supported.md) bez nutnosti jakékoli konfigurace. Metriky platformy se shromažďují z prostředků Azure v četnosti jedné minuty, pokud není uvedeno jinak v definici metriky. 

**Aplikace**. Metriky jsou vytvářeny Application Insights pro vaše monitorované aplikace, které vám pomůžou detekovat problémy s výkonem a sledovat trendy v tom, jak se vaše aplikace používá. To zahrnuje tyto hodnoty jako _dobu odezvy serveru_ a _výjimky prohlížeče_.

**Agenti virtuálních počítačů**. Metriky se shromažďují z hostovaného operačního systému virtuálního počítače. Povolte metriky hostovaného operačního systému pro virtuální počítače s Windows s nástrojem [Diagnostika systému Windows (WAD)](../agents/diagnostics-extension-overview.md) a pro virtuální počítače se systémem Linux pomocí [agenta InfluxData telegraf](https://www.influxdata.com/time-series-platform/telegraf/).

**Vlastní metriky**. Kromě standardních metrik, které jsou k dispozici automaticky, můžete definovat metriky. [Ve své aplikaci můžete definovat vlastní metriky](../app/api-custom-events-metrics.md) , které jsou monitorované nástrojem Application Insights nebo vytvářet vlastní metriky pro službu Azure pomocí [rozhraní API pro vlastní metriky](./metrics-store-custom-rest-api.md).

- Úplný seznam zdrojů dat, které mohou odesílat data do Azure Monitor metriky, najdete v tématu [co je monitorováno pomocí Azure monitor?](../monitor-reference.md) .

## <a name="metrics-explorer"></a>Průzkumník metrik
Pomocí [Průzkumník metrik](metrics-charts.md) můžete interaktivně analyzovat data v databázi metrik a v grafu hodnoty různých metrik v průběhu času. Grafy můžete připnout na řídicí panel a zobrazit je s ostatními vizualizacemi. Metriky můžete také načíst pomocí [REST API monitorování Azure](./rest-api-walkthrough.md).

![Průzkumník metrik](media/data-platform-metrics/metrics-explorer.png)

- V tématu [Začínáme s průzkumníkem Azure monitor metriky](./metrics-getting-started.md) můžete začít používat Průzkumníka metrik.

## <a name="data-structure"></a>Struktura dat
Data shromážděná pomocí Azure Monitor metriky se ukládají do databáze časových řad, která je optimalizovaná pro analýzu časových údajů s časovým razítkem. Každá sada hodnot metrik je časová řada s následujícími vlastnostmi:

* Čas, kdy byla hodnota shromažďována
* Prostředek, ke kterému je přiřazena hodnota
* Obor názvů, který funguje jako kategorie pro metriku
* Název metriky
* Samotná hodnota
* Některé metriky mohou mít více dimenzí, jak je popsáno v multidimenzionálních [metrikách](#multi-dimensional-metrics). Vlastní metriky můžou mít až 10 dimenzí.

## <a name="multi-dimensional-metrics"></a>Multidimenzionální metriky
Jedním z výzev k datům metriky je, že často obsahují omezené informace, aby poskytovaly kontext pro shromážděné hodnoty. Azure Monitor řeší tuto výzvu pomocí multidimenzionální metriky. Dimenze metriky jsou páry název-hodnota, které obsahují další data pro popis hodnoty metriky. Například _dostupné místo na disku_ může mít dimenzi s názvem _jednotka_ s hodnotami _C:_, _D:_, což by umožnilo zobrazení dostupného místa na disku ve všech jednotkách nebo pro každou jednotku zvlášť.

Následující příklad znázorňuje dvě datové sady pro hypotetickou metriku nazvanou _propustnost sítě_. První datová sada nemá žádné dimenze. Druhá datová sada zobrazuje hodnoty se dvěma dimenzemi, _IP adresou_ a _směrem_:

### <a name="network-throughput"></a>Propustnost sítě

| Timestamp     | Hodnota metriky |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1 331,8 KB/s |
| 8/9/2017 8:15 | 1 141,4 KB/s |
| 8/9/2017 8:16 | 1 110,2 KB/s |

Tato nedimenzionální metrika může odpovědět jenom na základní otázku, jako je například propustnost sítě v daném čase?

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Propustnost sítě + dvě dimenze ("IP" a "směr")

| Timestamp     | Dimenze "IP"   | Rozměr "Direction" | Hodnota metriky|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direction = Send    | 646,5 KB/s |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direction = Receive | 420,1 KB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direction = Send    | 150,0 KB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direction = Receive | 115,2 KB/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direction = Send    | 515,2 KB/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direction = Receive | 371,1 KB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direction = Send    | 155,0 KB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direction = Receive | 100,1 KB/s |

Tato metrika může odpovídat na otázky, jako je třeba propustnost sítě pro každou IP adresu? "a" kolik dat bylo odesláno a přijato? " Multidimenzionální metriky mají v porovnání s nedimenzionálními metrikami další analytickou a diagnostickou hodnotu.

## <a name="retention-of-metrics"></a>Uchování metrik
Pro většinu prostředků v Azure se metriky ukládají po dobu 93 dnů. Existují některé výjimky:

**Metriky hostovaného operačního systému**
-   **Klasické metriky hostovaného operačního systému**. Jedná se o čítače výkonu shromážděné [diagnostickým rozšířením systému Windows (WAD)](../agents/diagnostics-extension-overview.md) nebo [Linux DIAGNOSTIC Extension (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) a směrované na účet služby Azure Storage. Uchovávání těchto metrik je 14 dní.
-   **Metriky hostovaného operačního systému odeslané do Azure monitor metriky**. Jedná se o čítače výkonu shromážděné [diagnostickým rozšířením systému Windows (WAD)](../agents/diagnostics-extension-overview.md) , které se odesílají do [Azure monitor dat](../agents/diagnostics-extension-overview.md#data-destinations)nebo prostřednictvím [agenta InfluxData telegraf](https://www.influxdata.com/time-series-platform/telegraf/) v počítačích se systémem Linux. Uchovávání těchto metrik je 93 dnů.
-   **Metriky hostovaného operačního systému shromážděné agentem Log Analytics**. Jedná se o čítače výkonu shromažďované agentem Log Analytics a odeslané do pracovního prostoru Log Analytics. Uchovávání těchto metrik je 31 dní a dá se prodloužit na 2 roky.

**Application Insights metriky založené na protokolu**. 
- [Metriky založené na protokolu](../app/pre-aggregated-metrics-log-metrics.md) se za scénou převádějí do dotazů protokolu. Jejich uchovávání odpovídá uchovávání událostí v základních protokolech. U Application Insightsch prostředků se protokoly ukládají po dobu 90 dnů.


> [!NOTE]
> [Metriky platformy pro Azure monitor prostředky můžete odeslat do Log Analytics pracovního prostoru](./resource-logs.md#send-to-azure-storage) pro dlouhodobé trendy.





## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure monitor datovou platformu](../data-platform.md).
- Přečtěte si o [datech protokolu v Azure monitor](../logs/data-platform-logs.md).
- Přečtěte si o [dostupných datech monitorování](../agents/data-sources.md) různých prostředků v Azure.