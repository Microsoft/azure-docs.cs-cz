---
title: Diagnostika, řešení problémů a řešení problémů – Azure Time Series Insights | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak diagnostikovat, řešit a řešit běžné problémy v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 209df97169c71d910677ffdb2e2b12593882445b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152572"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnostikujte a řešte problémy v prostředí Time Series Insights

Tento článek popisuje některé problémy, se kterými se můžete setkat v prostředí Azure Time Series Insights. Článek nabízí potenciální příčiny a řešení pro řešení.

## <a name="video"></a>Video

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Seznamte se s běžnými výzvami a zmírněními rizik time series insights.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problém: nejsou zobrazena žádná data

Žádná data v [průzkumníku Azure Time Series Insights](https://insights.timeseries.azure.com) může dojít z několika běžných důvodů:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Příčina A: data zdroje událostí nejsou ve formátu JSON

Azure Time Series Insights podporuje jenom data JSON. Ukázky JSON načtu [podporované obrazce JSON](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Příčina B: Ve zdrojovém klíči události chybí požadované oprávnění.

* Pro službu IoT hub v Azure IoT Hub, musíte poskytnout klíč, který má oprávnění **připojení služby.** Vyberte zásady **iothubowner** nebo **service,** protože obě mají oprávnění **pro připojení služby.**

   [![Oprávnění připojení služby IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Pro centrum událostí v Azure Event Hubs musíte zadat klíč, který má oprávnění **k naslouchání.** Buď **číst** nebo **spravovat** zásady bude fungovat, protože oba mají **oprávnění naslouchat.**

   [![Oprávnění k poslechu centra událostí](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Příčina C: poskytnutá skupina spotřebitelů není výhradní pro Time Series Insights

Když zaregistrujete centrum IoT hub nebo centrum událostí, je důležité nastavit skupinu spotřebitelů, kterou chcete použít ke čtení dat. Tuto skupinu spotřebitelů *nelze sdílet*. Pokud je skupina spotřebitelů sdílena, základní centrum IoT nebo centrum událostí automaticky a náhodně odpojí jeden z čtenářů. Poskytněte jedinečnou skupinu spotřebitelů, ze které si můžete číst přehledy time series.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Příčina D: prostředí bylo právě zřízeno

Data se zobrazí v průzkumníku Time Series Insights během několika minut po prvním vytvoření prostředí a jeho dat.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problém: některá data jsou zobrazena, ale chybí data

Pokud se data zobrazí pouze částečně a data se zdají být zaostávající, měli byste zvážit několik možností.

### <a name="cause-a-your-environment-is-being-throttled"></a>Příčina A: Vaše prostředí je omezeno

[Omezení](time-series-insights-environment-mitigate-latency.md) je běžný problém při vytváření prostředí po vytvoření zdroje událostí, který má data. Azure IoT Hub a Centra událostí Azure ukládají data až sedm dní. Time Series Insights vždy začínají nejstarší událostí ve zdroji událostí (first-in, first-out nebo *FIFO).*

Například pokud máte 5 milionů událostí ve zdroji událostí při připojení k Prostředí S1, s jednou jednotkou Time Series Insights, Time Series Insights přečte přibližně 1 milion událostí za den. Může to vypadat, že Time Series Insights zažívá pět dní latence. Nicméně, co se děje, je, že prostředí je omezen.

Pokud máte staré události ve zdroji událostí, můžete přistupovat k omezení jedním ze dvou způsobů:

- Změňte limity uchovávání informací zdroje událostí, abyste odstranili staré události, které nechcete zobrazovat v přehledech time series.
- Zřídit větší velikost prostředí (počet jednotek) pro zvýšení propustnost starých událostí. Pomocí předchozího příkladu pokud zvýšíte stejné prostředí S1 na pět jednotek pro jeden den, prostředí by měl dohnat během jednoho dne. Pokud je vaše výroba událostí v ustáleném stavu 1 milion nebo méně událostí za den, můžete snížit kapacitu události na jednu jednotku poté, co ji dožene.

Omezení limit je vynuceno na základě typu sku prostředí a kapacity. Tuto kapacitu sdílejí všechny zdroje událostí v prostředí. Pokud zdroj událostí pro centrum IoT nebo centrum událostí odesílá data za vynucená omezení, dojde k omezení a zpoždění.

Následující obrázek znázorňuje prostředí Time Series Insights, které má skladovou položku S1 a kapacitu 3. Může příchozí 3 miliony událostí za den.

[![Aktuální kapacita skladové položky prostředí](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Jako příklad předpokládejme, že prostředí ingestuje zprávy z centra událostí. Denní rychlost příchozího přenosu dat je ~67 000 zpráv. Tato rychlost se promítá do přibližně 46 zpráv každou minutu. 

* Pokud je každá zpráva centra událostí sloučí na jednu událost Time Series Insights, k omezení nedojde. 
* Pokud je každá zpráva centra událostí sloučí na 100 událostí Time Series Insights, 4 600 událostí by měla být ingestována každou minutu. 

Prostředí S1 SKU, které má kapacitu 3 může příchozí přenos dat pouze 2 100 událostí každou minutu (1 milion událostí za den = 700 událostí za minutu na tři jednotky = 2 100 událostí za minutu). 

Pro vysoké úrovni pochopení, jak funguje logika sloučení, přečtěte si [podporované obrazce JSON](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Doporučená rozlišení pro nadměrné omezení

Chcete-li opravit zpoždění, zvyšte kapacitu skladové položky vašeho prostředí. Další informace najdete v [článek Škálování prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Příčina B: počáteční požití historických dat zpomaluje příchozí přístup

Pokud připojíte existující zdroj událostí, je pravděpodobné, že vaše centrum IoT hub nebo centrum událostí již obsahuje data. Prostředí začne vytahovat data od začátku doby uchovávání zpráv zdroje události. Toto výchozí zpracování nelze přepsat. Můžete zapojit škrcení. Omezení může chvíli trvat, než se dožene, protože ingestuje historická data.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Doporučená rozlišení pro velké počáteční požití

Chcete-li opravit zpoždění:

1. Zvyšte kapacitu skladové položky na maximální povolenou hodnotu (v tomto případě 10). Po zvýšení kapacity proces příchozího přenosu dat začne dohánět mnohem rychleji. Za zvýšenou kapacitu vám bude účtována zvýšená kapacita. Chcete-li vizualizovat, jak rychle doháníte, můžete zobrazit graf dostupnosti v [průzkumníku Přehledy časových řad](https://insights.timeseries.azure.com).

2. Když je zpoždění zachyceno, snižte kapacitu skladové položky na normální rychlost příchozího přenosu dat.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problém: data se zobrazovala dříve, ale již se nezobrazují

TSI již neingestuje data, ale události jsou stále streamování do služby Iot Hub nebo Event Hub

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-updating"></a>Příčina A: Přístupový klíč rozbočovače byl obnoven a vaše prostředí potřebuje aktualizaci

K tomuto problému dochází, když klíč poskytnutý při vytváření zdroje událostí již není platný. Ve vašem centru se zobrazí telemetrická data, ale žádné příchozí přenosy přijatých zpráv v přehledech časové řady. Pokud si nejste jisti, zda byl klíč znovu vygenerován, můžete v protokolu aktivit centra událostí vyhledat "Vytvořit nebo aktualizovat autorizační pravidla oboru názvů" nebo hledat "Vytvořit nebo aktualizovat prostředek IotHub" pro službu IoT hub.

Chcete-li aktualizovat prostředí Time Series Insights pomocí nového klíče, otevřete svůj prostředek centra na webu Azure Portal a zkopírujte nový klíč. Přejděte na zdroj TSI a klikněte na Zdroje událostí. 

   [![Aktualizovat klíč.](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Vyberte zdroj událostí, které mají, ze kterého bylo požití zastaveno, vložte nový klíč a klikněte na Uložit.

   [![Aktualizovat klíč.](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problém: Nastavení názvu vlastnosti časového razítka zdroje události nefunguje

Ujistěte se, že název a hodnota vlastnosti časového razítka odpovídají následujícím pravidlům:

* Název vlastnosti časového razítka rozlišuje malá a velká písmena.
* Hodnota vlastnosti časového razítka, která pochází ze zdroje událostí jako řetězec JSON, by měla mít formát _yyyy-MM-ddTHH:mm:ss. FFFFFFFK_. Příkladem je **2008-04-12T12:53Z**.

Nejjednodušší způsob, jak zajistit, že název vlastnosti časového razítka je zachycen a funguje správně, je použití průzkumníka Time Series Insights. V průzkumníku Time Series Insights vyberte pomocí grafu časové období po zadání názvu vlastnosti časového razítka. Klikněte pravým tlačítkem myši na výběr a vyberte možnost **Prozkoumat události.**

Záhlaví prvního sloupce by mělo být název vlastnosti časového razítka. Vedle slova **Časové razítko** **($ts)** se zobrazí.

Následující hodnoty se nezobrazí:

- *(abc)*: Označuje, že Time Series Insights čte hodnoty dat jako řetězce.
- *Ikona kalendáře*: Označuje, že funkce Time Series Insights čte hodnotu dat jako *datetime*.
- *#*: Označuje, že Time Series Insights čte hodnoty dat jako celé číslo.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [tom, jak zmírnit latenci v Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Přečtěte [si, jak škálovat prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md).