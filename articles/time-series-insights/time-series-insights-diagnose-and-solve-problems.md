---
title: Diagnostika, řešení potíží a řešení problémů – Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak diagnostikovat, řešit problémy a řešit běžné problémy v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: ca38ebb015552042591fb4cc6b7edfe99527e79f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007051"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnostikujte a řešte problémy v prostředí Time Series Insights.

Tento článek popisuje některé problémy, se kterými se můžete setkat v prostředí Azure Time Series Insights. Tento článek nabízí možné příčiny a řešení pro řešení.

## <a name="video"></a>Video

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Přečtěte si o běžných Time Series Insightsch zákaznických výzev a zmírnění problémů.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problém: nejsou zobrazena žádná data.

V [průzkumníkovi Azure Time Series Insights](https://insights.timeseries.azure.com) se nemůžou vyskytovat žádná data z několika běžných důvodů:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Příčina: zdrojová data události nejsou ve formátu JSON.

Azure Time Series Insights podporuje pouze data JSON. Ukázky JSON najdete v tématu [tvary JSON nepodporuje](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Příčina B: ve zdrojovém klíči události chybí požadovaná oprávnění.

* Pro službu IoT Hub v Azure IoT Hub musíte zadat klíč, který má oprávnění **služby Connect** . Obě zásady **iothubowner** nebo **služby** budou fungovat, protože obě mají oprávnění **k připojení ke službě** .

   [oprávnění k připojení služby ![IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* V případě centra událostí v Azure Event Hubs musíte zadat klíč, který má oprávnění k **naslouchání** . Obě zásady **pro čtení** nebo **správu** budou fungovat, protože obě mají oprávnění k **naslouchání** .

   [![oprávnění k naslouchání centra událostí](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Příčina C: zadaná skupina příjemců není výhradně Time Series Insights

Když zaregistrujete centrum IoT nebo centrum událostí, je důležité nastavit skupinu uživatelů, kterou chcete použít ke čtení dat. Tuto skupinu příjemců *nelze sdílet*. Pokud je skupina uživatelů sdílená, příslušné centrum IoT nebo centrum událostí automaticky a náhodně odpojí jedno z čtecích zařízení. Poskytněte jedinečnou skupinu uživatelů, ze které se má Time Series Insights číst.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problém: zobrazují se některá data, ale chybí data.

Když se data zobrazí jenom částečně a data se budou odmyslet, měli byste zvážit několik možností.

### <a name="cause-a-your-environment-is-being-throttled"></a>Příčina: vaše prostředí se omezuje

Omezování je běžný problém, když se prostředí zřídí po vytvoření zdroje událostí, který obsahuje data. Azure IoT Hub a centra událostí Azure uchovávají data po dobu až sedmi dnů. Time Series Insights vždy začínat nejstarší událostí ve zdroji událostí (First-in, First-out nebo *FIFO*).

Například pokud máte ve zdroji událostí 5 000 000 události, když se připojíte k prostředí Time Series Insights S1, s jednou jednotkou, Time Series Insights načte přibližně 1 000 000 událostí za den. Může to vypadat jako u Time Series Insights trvá 5 dní od latence. Ale co se děje, je to omezení prostředí.

Pokud máte ve zdroji událostí staré události, můžete k omezování přístupu přistupovat jedním ze dvou způsobů:

- Změňte limity uchování zdroje událostí, aby bylo možné odebrat staré události, které nechcete zobrazit v Time Series Insights.
- Zřídit větší velikost prostředí (počet jednotek), aby se zvýšila propustnost starých událostí. Pokud v předchozím příkladu zvýšíte stejné prostředí S1 na pět jednotek za jeden den, prostředí by mělo být během jednoho dne zachytávání. Pokud má vaše výroba události ustálených stavů hodnotu 1 000 000 nebo méně událostí za den, můžete snížit kapacitu události na jednu jednotku po jejím zachycení.

Omezení omezování se vynutilo na základě typu SKU a kapacity prostředí. Všechny zdroje událostí v prostředí sdílejí tuto kapacitu. Pokud zdroj událostí služby IoT Hub nebo centra událostí odesílá data nad rámec vynuceného limitu, uvidíte omezení a prodlevu.

Následující obrázek ukazuje Time Series Insights prostředí, které má SKU S1 a kapacitu 3. Může to mít až 3 000 000 událostí za den.

[aktuální kapacita SKU ![prostředí](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Předpokládejme například, že prostředí přijímá zprávy z centra událostí. Frekvence denních přenosů je ~ 67 000 zpráv. Tato rychlost překládá na přibližně 46 zpráv každou minutu. 

* Pokud se každá zpráva centra událostí sloučí do jedné Time Series Insights události, neproběhne omezení. 
* Pokud se každá zpráva centra událostí sloučí do 100 Time Series Insightsch událostí, měly by se každých minut ingestovat události 4 600. 

Prostředí SKU S1, které má kapacitu 3, může při každé minutě přijímat jenom 2 100 událostí (1 000 000 událostí za den = 700 událostí za minutu se třemi jednotkami = 2 100 událostí za minutu). 

Podrobné informace o tom, jak funguje shrnutá logika, najdete v tématu [podporované tvary JSON](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Doporučená řešení pro nadměrné omezení

Pro vyřešení prodlevy Zvyšte kapacitu SKU vašeho prostředí. Další informace najdete v tématu [škálování Time Series Insightsho prostředí](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Příčina B: počáteční přijímání historických dat zpomaluje příchozí data

Pokud připojíte existující zdroj události, je možné, že vaše centrum IoT nebo centrum událostí již obsahuje data. Prostředí začíná vymezit načítání dat od začátku období uchování zprávy zdroje událostí. Toto výchozí zpracování nelze přepsat. Můžete se zapojit do omezování. Omezení může chvíli trvat, než bude ingestovat historická data.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Doporučená řešení pro velké počáteční přijímání

Postup při opravě prodlevy:

1. Zvyšte kapacitu SKU na maximální povolenou hodnotu (10, v tomto případě). Po zvýšení kapacity se proces příchozího přenosu začne zachytit mnohem rychleji. Účtuje se vám zvýšená kapacita. Chcete-li vizualizovat, jak rychle pracujete, můžete zobrazit graf dostupnosti v [průzkumníkovi Time Series Insights](https://insights.timeseries.azure.com).

2. Po započetí prodlevy snižte kapacitu SKU na normální míru příchozího přenosu dat.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problém: nastavení názvu vlastnosti časového razítka zdroje události nefunguje

Ujistěte se, že název a hodnota vlastnosti časového razítka odpovídají následujícím pravidlům:

* V názvu vlastnosti časového razítka se rozlišují malá a velká písmena.
* Hodnota vlastnosti časového razítka, která pochází ze zdroje události jako řetězec JSON, by měla mít formát _RRRR-MM-DDThh: mm: ss. FFFFFFFK_. Příklad je **2008-04-12T12:53Z**.

Nejjednodušší způsob, jak zajistit, aby byl název vlastnosti časového razítka zachycen a správně fungoval, je použití Průzkumníka Time Series Insights. V Průzkumníku Time Series Insights pomocí grafu vyberte časový úsek po zadání názvu vlastnosti časového razítka. Klikněte pravým tlačítkem na výběr a potom vyberte možnost **prozkoumat události** .

První záhlaví sloupce by mělo být název vlastnosti časového razítka. Vedle **časového razítka**slova by se měla zobrazit **($TS)** .

Neměly by se zobrazovat tyto hodnoty:

- *(ABC)* : označuje, že Time Series Insights čte datové hodnoty jako řetězce.
- *Ikona kalendáře*: označuje, že Time Series Insights čte datovou hodnotu jako *DateTime*.
- *#* : označuje, že Time Series Insights čte hodnoty dat jako celé číslo.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [tom, jak zmírnit latenci v Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Naučte [se škálovat Time Series Insights prostředí](time-series-insights-how-to-scale-your-environment.md).