---
title: Metriky ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Popisuje metriky v Azure Monitoru, které jsou zjednodušené data monitorování schopné podporovat scénáře téměř v reálném čase.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a02b3df02d455db8a7dfd21f63d659f75a66e446
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457310"
---
# <a name="metrics-in-azure-monitor"></a>Metriky na platformě Azure Monitor

> [!NOTE]
> Datová platforma Azure Monitor je založená na dvou základních datových typech: metriky a protokoly. Tento článek popisuje metriky. Podrobné informace o protokolech a [na datové platformě Azure Monitor](data-platform.md) usrovnávacíte pro porovnání těchto dvou protokolů najdete v [protokolech v Azure Monitoru.](data-platform-logs.md)

Metriky ve službě Azure Monitor jsou lehké a schopné podporovat scénáře téměř v reálném čase, takže jsou obzvláště užitečné pro upozorňování a rychlé zjišťování problémů. Tento článek popisuje, jak jsou metriky strukturovány, co s nimi můžete dělat, a identifikuje různé zdroje dat, které ukládají data v metrikách.

## <a name="what-are-metrics"></a>Co jsou metriky?
Metriky jsou číselné hodnoty, které popisují určité aspekty systému v konkrétním čase. Metriky jsou shromažďovány v pravidelných intervalech a jsou užitečné pro výstrahy, protože mohou být vzorkovány často a výstraha může být aktivována rychle s relativně jednoduchou logikou.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Co můžete dělat s metrikami Azure Monitor?
V následující tabulce jsou uvedeny různé způsoby, jak můžete použít metrická data v Azure Monitoru.

|  |  |
|:---|:---|
| Analýza | Pomocí [průzkumníka metrik](metrics-charts.md) můžete analyzovat shromážděné metriky v grafu a porovnávat metriky z různých zdrojů. |
| Vizualizace | Připněte graf z průzkumníka metrik na [řídicí panel Azure](../learn/tutorial-app-dashboards.md).<br>Vytvořte [sešit,](../app/usage-workbooks.md) který chcete kombinovat s více sadami dat v interaktivní sestavě. Exportujte výsledky dotazu do [Grafany,](grafana-plugin.md) abyste využili jeho řídicí panel a zkombinovali je s jinými zdroji dat. |
| Výstrahy | Nakonfigurujte [pravidlo upozornění metriky,](alerts-metric.md) které odešle oznámení nebo provede [automatickou akci,](action-groups.md) když hodnota metriky překročí prahovou hodnotu. |
| Automatizace |  Automatické [škálování](autoscale-overview.md) slouží ke zvýšení nebo snížení zdrojů na základě hodnoty metriky překračující prahovou hodnotu. |
| Export | [Směrovat metriky do protokolů](resource-logs-collect-storage.md) k analýze dat v metrikách Azure Monitor spolu s daty v protokolech monitorování Azure a ukládat hodnoty metrik y déle než 93 dní.<br>Streamujte metriky do [centra událostí](stream-monitoring-data-event-hubs.md) a směrujte je do externích systémů. |
| Načíst | Přístup k hodnotám metrik z příkazového řádku pomocí [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Přístup k hodnotám metrik z vlastní aplikace pomocí [rozhraní REST API](rest-api-walkthrough.md).<br>Přístup k hodnotám metrik z příkazového řádku pomocí [příkazového příkazového řádku](/cli/azure/monitor/metrics). |
| Archiv | [Archivujte](..//learn/tutorial-archive-data.md) výkon nebo historii stavu prostředku z důvodu dodržování předpisů, auditování nebo offline vykazování. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Jak jsou data v metrikách Azure Monitor strukturovaná?
Data shromážděná metrikami Azure Monitor ukládají v databázi časových řad, která je optimalizovaná pro analýzu dat s časovým razítkem. Každá sada hodnot metrik je časová řada s následujícími vlastnostmi:

* Čas, kdy byla hodnota shromážděna
* Zdroj, ke kterým je hodnota přidružena
* Obor názvů, který funguje jako kategorie pro metriku
* Název metriky
* Samotná hodnota
* Některé metriky mohou mít více dimenzí, jak je popsáno v [multidimenzionální metriky](#multi-dimensional-metrics). Vlastní metriky mohou mít až 10 dimenzí.

## <a name="multi-dimensional-metrics"></a>Vícerozměrné metriky
Jednou z výzev pro metrická data je, že často obsahuje omezené informace, které poskytují kontext pro shromážděné hodnoty. Azure Monitor řeší tuto výzvu pomocí vícerozměrných metrik. Dimenze metriky jsou dvojice název-hodnota, které nesou další data k popisu hodnoty metriky. Například metrika _Dostupné místo_ na disku může mít dimenzi nazvanou _Jednotka_ s hodnotami _C:_, _D:_, která by umožnila zobrazit dostupné místo na disku na všech jednotkách nebo pro každou jednotku jednotlivě.

Následující příklad ilustruje dvě datové sady pro hypotetickou metriku nazvanou _Propustnost sítě_. První datová sada nemá žádné dimenze. Druhá datová sada zobrazuje hodnoty se dvěma dimenzemi, _IP adresa_ a _směr_:

### <a name="network-throughput"></a>Propustnost sítě

| Časové razítko     | Hodnota metriky |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1 110,2 kb/s |

Tato nedimenzionální metrika může odpovědět pouze na základní otázku jako "jaká byla moje propustnost sítě v daném čase?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Propustnost sítě + dvě dimenze ("IP" a "Direction")

| Časové razítko     | Dimenze "IP"   | Rozměr "Směr" | Hodnota metriky|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Směr="Odeslat"    | 646,5 kb/s |
| 8/9/2017 8:14 | IP="192.168.5.2" | Směr="Přijmout" | 420,1 kb/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Směr="Odeslat"    | 150,0 kb/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Směr="Přijmout" | 115,2 kb/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Směr="Odeslat"    | 515,2 kb/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Směr="Přijmout" | 371,1 kb/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Směr="Odeslat"    | 155,0 kb/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Směr="Přijmout" | 100,1 kb/s |

Tato metrika může odpovědět na otázky jako "jaká byla propustnost sítě pro každou ADRESU IP?" a "kolik dat bylo odesláno oproti přijatým?" Vícerozměrné metriky nesou další analytickou a diagnostickou hodnotu ve srovnání s nedimenzionálními metrikami.

## <a name="interacting-with-azure-monitor-metrics"></a>Práce s metrikami služby Azure Monitor
Pomocí [Průzkumníka metrik](metrics-charts.md) můžete interaktivně analyzovat data v databázi metrik a zmapovat hodnoty více metrik v čase. Grafy můžete připnout k řídicímu panelu a zobrazit je pomocí jiných vizualizací. Metriky můžete také načíst pomocí [rozhraní REST API monitorování Azure](rest-api-walkthrough.md).

![Průzkumník metrik](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Zdroje metrik Azure Monitor
Existují tři základní zdroje metriky shromážděné Azure Monitor. Jakmile se tyto metriky shromažďují v databázi metrik Azure Monitoru, lze je vyhodnotit společně bez ohledu na jejich zdroj.

**Metriky platformy** jsou vytvářeny prostředky Azure a poskytují přehled o jejich stavu a výkonu. Každý typ prostředku vytvoří [odlišnou sadu metrik](metrics-supported.md) bez jakékoli požadované konfigurace. Metriky platformy se shromažďují z prostředků Azure s frekvencí jedné minuty, pokud není v definici metriky uvedeno jinak. 

**Metriky hostovaného operačního systému** jsou shromažďovány z hostovaného operačního systému virtuálního počítače. Povolte metriky hostovaného operačního systému pro virtuální počítače s Windows pomocí [windows diagnostic extension (WAD)](../platform/diagnostics-extension-overview.md) a pro virtuální počítače s Linuxem s [AgentEm InfluxData Telegraf .](https://www.influxdata.com/time-series-platform/telegraf/)

**Metriky aplikací** jsou vytvářeny application insights pro monitorované aplikace a pomáhají vám zjistit problémy s výkonem a sledovat trendy ve způsobu, jakým se vaše aplikace používá. To zahrnuje například hodnoty jako _doba odezvy serveru_ a _výjimky prohlížeče_.

**Vlastní metriky** jsou metriky, které definujete kromě standardních metrik, které jsou automaticky k dispozici. Můžete [definovat vlastní metriky ve vaší aplikaci,](../app/api-custom-events-metrics.md) která je monitorovaná Application Insights nebo vytvořit vlastní metriky pro službu Azure pomocí [vlastního rozhraní API metriky](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Uchovávání metrik
U většiny prostředků v Azure se metriky ukládají po dobu 93 dnů. Existují některé výjimky:

**Metriky hostovaného operačního spoje**
-   **Klasické metriky hostovaného operačního systému**. Jedná se o čítače výkonu shromážděné [pomocí rozšíření Windows Diagnostic Extension (WAD)](../platform/diagnostics-extension-overview.md) nebo Linux Diagnostic Extension [(LAD)](../../virtual-machines/extensions/diagnostics-linux.md) a směrované na účet úložiště Azure. Uchovávání těchto metrik je 14 dní.
-   **Metriky hostovaného operačního serveru odeslané do metrik Azure Monitor .** Jedná se o čítače výkonu shromážděné [pomocí rozšíření Windows Diagnostic Extension (WAD)](diagnostics-extension-overview.md) a odeslané do [jímky dat Azure Monitor](diagnostics-extension-overview.md#data-destinations)nebo prostřednictvím [agenta InfluxData Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) na počítačích s Linuxem. Uchovávání těchto metrik je 93 dní.
-   **Metriky hostovaného operačního operačního služby shromažďované agentem Log Analytics**. Jedná se o čítače výkonu shromážděné agentem Analýzy protokolů a odeslané do pracovního prostoru Analýzy protokolů. Uchovávání těchto metrik je 31 dní a může být prodlouženo až na 2 roky.

**Metriky založené na protokolu Application Insights**. 
- Za scénou [metriky založené na protokolu](../app/pre-aggregated-metrics-log-metrics.md) přeložit do dotazů protokolu. Jejich uchovávání odpovídá uchovávání událostí v podkladových protokolech. Pro prostředky Application Insights protokoly jsou uloženy po dobu 90 dnů.


> [!NOTE]
> [Metriky platformy pro prostředky Azure Monitoru můžete odeslat do pracovního prostoru Log Analytics](resource-logs-collect-storage.md) pro dlouhodobé trendy.





## <a name="next-steps"></a>Další kroky

- Další informace o [datové platformě Azure Monitor](data-platform.md).
- Další informace o [datech protokolu v Azure Monitoru](data-platform-logs.md).
- Přečtěte si o [datech monitorování dostupných](data-sources.md) pro různé prostředky v Azure.
