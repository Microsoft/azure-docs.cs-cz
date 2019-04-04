---
title: Metriky na platformě Azure Monitor | Dokumentace Microsoftu
description: Popisuje metriky ve službě Azure Monitor, které jsou zjednodušené data schopný zajistit podporu téměř v reálném čase scénáře monitorování.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 1027398a1a7f790adedf6c7eebed44a8db501b8a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905031"
---
# <a name="metrics-in-azure-monitor"></a>Metriky na platformě Azure Monitor

> [!NOTE]
> Datová Platforma Azure Monitor je založena na dva základní datové typy: Metriky a protokoly. Tento článek popisuje metriky. Odkazovat na [protokolů ve službě Azure Monitor](data-platform-logs.md) protokolů a získat podrobný popis [platforn data Azure Monitor](data-platform.md) porovnání obou.


Metriky ve službě Azure Monitor jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře, díky kterým jsou zvláště užitečná pro výstrahy a rychlé zjišťování problémů. Tento článek popisuje, jak metriky jsou strukturovaná, co můžete dělat s nimi a identifikuje různých zdrojů dat, které ukládají data v metrikách.

## <a name="what-are-metrics"></a>Co jsou metriky?
Metriky jsou číselné hodnoty, které popisují některé aspekty systému v určitou dobu. Metriky se budou shromažďovat v pravidelných intervalech a jsou užitečné pro výstrahy, protože můžou být často Vzorkovaná a upozornění můžete aktivuje rychle s relativně jednoduché logikou.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Co můžete dělat s metrikami Azure monitoru?
Následující tabulka uvádí různé způsoby, které můžete data metriky ve službě Azure Monitor.

|  |  |
|:---|:---|
| Analýza | Použití [Průzkumníka metrik](metrics-charts.md) k analýze shromážděných metrik v grafu a porovnat metriky z různých zdrojů. |
| Vizualizace | Připnutí grafu z Průzkumníku metrik na [řídicí panel Azure](../learn/tutorial-app-dashboards.md).<br>Vytvoření [sešitu](../app/usage-workbooks.md) zkombinovat s více sadami dat v interaktivní sestavy. Export výsledků dotazu na [Grafana](grafana-plugin.md) využít jeho mnoha a zkombinovat s jinými zdroji dat. |
| Výstrahy | Konfigurace [metriky pravidlo upozornění](alerts-metric.md) , který odešle oznámení, nebo má [automatizované akce](action-groups.md) když hodnota metriky překročí mezní hodnotu. |
| Automatizace |  Použití [automatického škálování](autoscale-overview.md) zvýšení nebo snížení prostředků na základě metrik hodnoty překročení prahové hodnoty. |
| Export | [Metriky směrovat protokoly](diagnostic-logs-stream-log-store.md) k analýze dat v Azure Monitor Metrics spolu s daty v protokoly Azure monitoru a k uložení hodnoty metrik déle než 93 dní.<br>Stream metrik [centra událostí](stream-monitoring-data-event-hubs.md) směrovat do externích systémů. |
| Načíst | Přístup k hodnoty metrik z příkazového řádku pomocí [rutin prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Přístup k hodnoty metrik z vlastní aplikace s využitím [rozhraní REST API](rest-api-walkthrough.md).<br>Přístup k hodnoty metrik z příkazového řádku pomocí [CLI](/azure/monitor/metrics). |
| Archiv | [Archiv](..//learn/tutorial-archive-data.md) historii výkon nebo stav prostředku pro dodržování předpisů, auditování, nebo v režimu offline pro účely vykazování. |


## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Jak se data v Azure Monitor Metrics strukturované?
Data shromážděná službou Azure Monitor Metrics je uložen v databázi časových řad, který je optimalizovaný pro analýzu dat s časovým razítkem. Každá sada hodnoty metrik je časové řady s následujícími vlastnostmi:

* Čas, kdy byl shromážděn hodnota
* Je přidružený prostředek hodnota
* Obor názvů, který se chová jako kategorie metriky
* Název metriky
* Vlastní hodnota
* Některé metriky může mít více rozměrů, jak je popsáno v [vícedimenzionálních metrik](#multi-dimensional-metrics). Vlastní metriky můžete mít až 10 dimenze.

Metriky Azure jsou uloženy 93 dní. Je možné [odeslat platformy metriky pro prostředky Azure Monitor k pracovnímu prostoru Log Analytics](diagnostic-logs-stream-log-store.md) pro dlouhodobé trendy.

## <a name="multi-dimensional-metrics"></a>Vícedimenzionálních metrik
Jedním z problémů na data metriky je, že je často má omezené informace, které poskytují kontext pro shromážděné hodnoty. Azure Monitor řeší tento problém s vícedimenzionálních metrik. Dimenze metriky jsou páry název hodnota, které přenášejí další data k popisu hodnota metriky. Například metrika _volného místa na disku_ může mít dimenzi volá _jednotka_ hodnotami _C:_, _D:_, který by umožnil zobrazení buď volného místa na disku ve všech jednotek nebo jednotlivě pro každý disk.

Následující příklad ukazuje dvě datové sady pro hypotetické metriku s názvem _propustnost sítě_. První datová sada nemá žádné dimenze. Druhý objekt dataset zobrazuje hodnoty dvourozměrné, _IP adresu_ a _směr_:

### <a name="network-throughput"></a>Propustnost sítě

| Timestamp     | Hodnota metriky |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 kb/s |
| 8/9/2017 8:15 | 1,141.4 kb/s |
| 8/9/2017 8:16 | 1,110.2 kb/s |

Tato metrika-rozměrné lze pouze jako odpověď základní otázky "jaké byly Moje propustnost sítě v daném okamžiku?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Propustnost sítě a dvě dimenze ("IP" a "Směr")

| Timestamp     | Dimenze "IP"   | Dimenze "Směr" | Hodnota metriky|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Směr = "Odeslat"    | 646.5 kb/s |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Směr = "Zobrazit" | 420.1 kB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Směr = "Odeslat"    | 150.0 kb/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Směr = "Zobrazit" | 115,2 kb/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Směr = "Odeslat"    | 515.2 kb/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Směr = "Zobrazit" | 371.1 kB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Směr = "Odeslat"    | 155.0 kb/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Směr = "Zobrazit" | 100.1 kB/s |

Tato metrika může odpovídat na otázky, jako je například "jaké byly propustnost sítě pro každou IP adresu?" a "a byla odeslána jak velký objem dat přijatých?" Vícedimenzionálních metrik provádět další analýzy a diagnostiku hodnotu ve srovnání s-jednodimenzionální metriky.

## <a name="interacting-with-azure-monitor-metrics"></a>Interakce s metrikami Azure monitoru
Použití [Průzkumníka metrik](metrics-charts.md) pro interaktivní analýzu dat metriky databáze a grafu hodnoty několika metrik v čase. Můžete připnout graf na řídicí panel k zobrazení se ostatní vizualizace. Můžete také načíst metriky pomocí [Azure, rozhraní REST API pro monitorování](rest-api-walkthrough.md).

![Průzkumník metrik](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Zdroje metrik Azure monitoru
Existují tři základní zdroje z metrik shromážděných službou Azure Monitor. Jakmile tyto metriky se budou shromažďovat metriky databáze Azure Monitor, lze vyhodnotit jejich společně bez ohledu na jejich zdroj.

**Platforma metriky** vytvářejí prostředky Azure a získáte přehled o jejich stav a výkon. Vytvoří jednotlivých typů prostředků [odlišnou sadu metriky](metrics-supported.md) bez veškeré požadované konfigurace. Platforma metriky se shromažďují z prostředků Azure v minutových intervalech, pokud není určeno jinak v definici tuto metriku. 

**Hostovaný operační systém metriky** jsou shromážděná z hostovaného operačního systému virtuálního počítače. Povolení metrik operačního systému hosta pro virtuální počítače s Windows s [Windows diagnostické rozšíření (WAD)](../platform/diagnostics-extension-overview.md) a pro virtuální počítače s Linuxem pomocí [InfluxData Telegraf agenta](https://www.influxdata.com/time-series-platform/telegraf/).

**Metriky aplikací** jsou vytvořeny pomocí Application Insights pro monitorované aplikace a pomáhají detekovat problémy s výkonem a sledování trendů ve využití vaší aplikace. To zahrnuje tyto hodnoty jako _doba odezvy serveru_ a _výjimky prohlížeče_.

**Vlastní metriky** jsou metriky, které definujete kromě standardních metrik, které jsou automaticky dostupné. Je možné [definujte vlastní metriky v aplikaci](../app/api-custom-events-metrics.md) , který je monitorován pomocí Application Insights nebo vytvořit vlastní metriky použití služby Azure [vlastní metriky rozhraní API](metrics-store-custom-rest-api.md).


## <a name="next-steps"></a>Další postup

- Další informace o [datová Platforma Azure Monitor](data-platform.md).
- Další informace o [protokolování dat ve službě Azure Monitor](data-platform-logs.md).
- Další informace o [data k dispozici monitorování](data-sources.md) pro různé prostředky v Azure.
