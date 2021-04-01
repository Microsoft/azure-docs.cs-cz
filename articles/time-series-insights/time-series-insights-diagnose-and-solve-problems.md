---
title: Diagnostika, řešení potíží a řešení problémů – Azure Time Series Insights
description: Tento článek popisuje, jak diagnostikovat, řešit problémy a řešit běžné problémy v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 15f2ff5aaa1d731c13125d0a3ab4ac32acb9276c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023271"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>Diagnostika a řešení problémů v prostředí Azure Time Series Insights Gen1

> [!CAUTION]
> Toto je Gen1 článek.

Tento článek popisuje problémy, se kterými se můžete setkat v prostředí Azure Time Series Insights. Tento článek nabízí možné příčiny a řešení pro řešení.

## <a name="video"></a>Video

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>Přečtěte si o běžných problémech Azure Time Series Insights a zmírnění problémů.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problém: nejsou zobrazena žádná data.

Pokud se v [Azure Time Series Insights Exploreru](https://insights.timeseries.azure.com)neobjeví žádná data, zvažte tyto běžné příčiny.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Příčina: zdrojová data události nejsou ve formátu JSON.

Azure Time Series Insights podporuje pouze data JSON. Ukázky JSON najdete v tématu [podporované tvary JSON](./concepts-json-flattening-escaping-rules.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Příčina B: ve zdrojovém klíči události chybí požadovaná oprávnění.

* Pro službu IoT Hub v Azure IoT Hub musíte zadat klíč, který má oprávnění služby Connect. Vyberte buď zásadu **iothubowner** , nebo zásady **služby** . Obě služby mají oprávnění k připojení.

   [![Oprávnění služby IoT Hub Connect](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* V případě centra událostí v Azure Event Hubs musíte zadat klíč, který má oprávnění k naslouchání. Zásady **číst** a **Spravovat** budou fungovat, protože obě mají oprávnění k naslouchání.

   [![Oprávnění k naslouchání centra událostí](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>Příčina C: zadaná skupina příjemců není výhradně Azure Time Series Insights

Když zaregistrujete centrum IoT nebo centrum událostí, je důležité nastavit skupinu uživatelů, kterou chcete použít ke čtení dat. Tuto skupinu příjemců *nelze sdílet*. Pokud je skupina uživatelů sdílená, příslušné centrum IoT nebo centrum událostí automaticky a náhodně odpojí jedno z čtecích zařízení. Poskytněte jedinečnou skupinu uživatelů, ze které se má Azure Time Series Insights číst.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Příčina D: prostředí se právě zřídilo.

Data se zobrazí v aplikaci Azure Time Series Insights Explorer během několika minut po prvním vytvoření prostředí a jeho dat.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problém: zobrazují se některá data, ale chybí data.

Když se data zobrazují jenom částečně a data se zdají být odložení, zvažte tyto možné problémy.

### <a name="cause-a-your-environment-is-being-throttled"></a>Příčina: vaše prostředí se omezuje

[Omezování](time-series-insights-environment-mitigate-latency.md) je běžný problém, když se prostředí zřídí po vytvoření zdroje událostí, který obsahuje data. Azure IoT Hub a centra událostí Azure uchovávají data po dobu až sedmi dnů. Azure Time Series Insights vždy začíná nejstarší událostí ve zdroji událostí (poprvé, First-out nebo *FIFO*).

Například pokud máte ve zdroji událostí 5 000 000 události, když se připojíte k prostředí Azure Time Series Insights S1, s jednou jednotkou, Azure Time Series Insights načte přibližně 1 000 000 událostí za den. Může to vypadat jako u Azure Time Series Insights trvá 5 dní od latence. Ale co se děje, znamená to, že prostředí je omezené.

Pokud máte ve zdroji událostí staré události, můžete k omezování přístupu přistupovat jedním ze dvou způsobů:

* Změňte limity uchování zdroje událostí, aby bylo možné odebrat staré události, které nechcete zobrazit v Azure Time Series Insights.
* Zřídit větší velikost prostředí (počet jednotek), aby se zvýšila propustnost starých událostí. Pokud v předchozím příkladu narostete stejné prostředí S1 na pět jednotek po dobu jednoho dne, prostředí by mělo zachytit za jeden den. Pokud má vaše výroba události ustálených stavů hodnotu 1 000 000 nebo méně událostí za den, můžete kapacitu události snížit na jednu jednotku po Azure Time Series Insights úlovky.

Limit pro vymáhání omezení vychází z typu SKU a kapacity prostředí. Všechny zdroje událostí v prostředí sdílejí tuto kapacitu. Pokud zdroj událostí pro Centrum IoT nebo centrum událostí přenáší data nad rámec vynuceného limitu, dojde k omezení a prodlevě.

Následující obrázek ukazuje Azure Time Series Insights prostředí, které má SKU S1 a kapacitu 3. Může to mít až 3 000 000 událostí za den.

[![Kapacita prostředí](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Představte si prostředí, které ingestuje zprávy z centra událostí. Má denní rychlost příchozího přenosu informací o zprávách 67 000. Tato rychlost překládá na přibližně 46 zpráv každou minutu.

* Pokud se každá zpráva centra událostí sloučí do jedné Azure Time Series Insights události, neproběhne omezení.
* Pokud se každá zpráva centra událostí sloučí do 100 Azure Time Series Insightsch událostí, měly by se každých minut ingestovat události 4 600.

Prostředí SKU S1, které má kapacitu 3, může při každé minutě přijímat jenom 2 100 událostí (1 000 000 událostí za den = 700 událostí za minutu se třemi jednotkami = 2 100 událostí za minutu).

Základní informace o tom, jak funguje shrnutá logika, najdete v tématu [podporované tvary JSON](./concepts-json-flattening-escaping-rules.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Doporučená řešení pro nadměrné omezení

Pro vyřešení prodlevy Zvyšte kapacitu SKU vašeho prostředí. Další informace najdete v tématu [škálování Azure Time Series Insightsho prostředí](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Příčina B: počáteční přijímání historických dat zpomaluje příchozí data

Pokud připojíte existující zdroj události, je možné, že vaše centrum IoT nebo centrum událostí již obsahuje data. Prostředí začíná vymezit načítání dat od začátku období uchování zprávy zdroje událostí. Toto výchozí zpracování nelze přepsat. Můžete se zapojit do omezování. Omezení může chvíli trvat, než bude ingestovat historická data.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Doporučená řešení pro velké počáteční přijímání

Postup při opravě prodlevy:

1. Zvyšte kapacitu SKU na maximální povolenou hodnotu (10, v tomto případě). Po zvýšení kapacity se proces příchozího přenosu začne zachytit mnohem rychleji. Účtuje se vám zvýšená kapacita. Chcete-li vizualizovat, jak rychle pracujete, můžete zobrazit graf dostupnosti v [průzkumníkovi Azure Time Series Insights](https://insights.timeseries.azure.com).

2. Po započetí prodlevy snižte kapacitu SKU na normální míru příchozího přenosu dat.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problém: data se zobrazila dřív, ale už se nezobrazují.

Pokud Azure Time Series Insights nadále nepřijímá data, ale události se pořád streamují do centra IoT nebo centra událostí, zvažte tuto potenciální příčinu.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>Příčina: váš přístupový klíč rozbočovače se znovu vygeneroval a vaše prostředí je potřeba aktualizovat.

K tomuto problému dochází, pokud klíč poskytnutý při vytváření zdroje událostí již není platný. V Azure Time Series Insights se zobrazila telemetrie, ale nepřišly žádné příchozí zprávy. Pokud si nejste jistí, jestli se klíč znovu vygeneroval, můžete v protokolu aktivit centra událostí vyhledat autorizační pravidla pro vytvoření nebo aktualizaci oboru názvů. V případě služby IoT Hub vyhledejte "vytvořit nebo aktualizovat prostředek IotHub".

Pokud chcete Azure Time Series Insights prostředí aktualizovat novým klíčem, otevřete prostředek centra v Azure Portal a zkopírujte nový klíč. Přejít na prostředek Azure Time Series Insights a vyberte **zdroje událostí**:

   [![Vybrat zdroje událostí](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Vyberte zdroj události nebo zdroje, ze kterých se zastavilo přijímání, vložte do nového klíče a pak vyberte **Uložit**:

   [![Vložit do nového klíče](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problém: nastavení názvu vlastnosti časového razítka zdroje události nefunguje

Zajistěte, aby hodnota vlastnosti časového razítka, která pochází ze zdroje události jako řetězec JSON, byla ve formátu _RRRR-MM-DDThh: mm: ss. FFFFFFFK_. Tady je příklad: **2008-04-12T12:53Z**.

Pamatujte, že v názvu vlastnosti časového razítka se rozlišují malá a velká písmena.

Nejjednodušší způsob, jak zajistit, aby byl název vlastnosti časového razítka zachycen a správně fungoval, je použití Průzkumníka Azure Time Series Insights. V Průzkumníku Azure Time Series Insights pomocí grafu vyberte časový úsek po zadání názvu vlastnosti časového razítka. Klikněte pravým tlačítkem na výběr a pak vyberte **prozkoumat události**.

První záhlaví sloupce by mělo být název vlastnosti časového razítka. Vedle **časového razítka** slova **($TS)** se zobrazí.

Následující hodnoty nebudou zobrazeny:

* *(ABC)*: označuje, že Azure Time Series Insights čte datové hodnoty jako řetězce.
* *Ikona kalendáře*: označuje, že Azure Time Series Insights čte hodnoty dat jako hodnoty DateTime.
* *#*: Označuje, že Azure Time Series Insights čte hodnoty dat jako celá čísla.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [tom, jak zmírnit latenci v Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

* Naučte [se škálovat Azure Time Series Insights prostředí](time-series-insights-how-to-scale-your-environment.md).