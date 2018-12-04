---
title: Diagnostikovat a řešit problémy v Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak diagnostikovat, řešení a vyřešit běžné problémy, které můžete narazit ve vašem prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: ef06e7b1abd66a2204ef982943fe24354bd7f122
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837439"
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnostikovat a řešit problémy ve vašem prostředí Time Series Insights

Tento článek popisuje některé problémy, které se můžou objevovat ve vašem prostředí Time Series Insights. Nabízí možné příčiny a řešení pro řešení.

## <a name="video"></a>Video: 

### <a name="in-this-video-we-cover-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>V tomto videu se budeme zabývat běžné problémy zákazníků služby Time Series Insights a způsoby zmírnění rizik.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-1-no-data-is-shown"></a>Problém č. 1: Žádná data se zobrazí.
Několik běžných důvodů, proč se nemusí zobrazovat data v [Průzkumník služby Azure Time Series Insights](https://insights.timeseries.azure.com):

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Možnou příčinou A: události zdroje dat není ve formátu JSON
Azure Time Series Insights podporuje jenom data JSON. Ukázky JSON najdete v tématu [tvary JSON nepodporuje](./how-to-shape-query-json.md).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Klíč zdroje událostí B: možnou příčinou je chybějící požadované oprávnění
* Pro službu IoT Hub, je potřeba zadat klíč, který má **služba připojit** oprávnění.

   ![Povolení pro připojení služby Azure IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Jak je znázorněno na předchozím obrázku, buď zásady **iothubowner** a **služby** bude fungovat, protože obě mají **služba připojit** oprávnění.

* Pro Centrum událostí, je potřeba zadat klíč, který má **naslouchání** oprávnění.

   ![Oprávnění naslouchat centra událostí](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Jak je znázorněno na předchozím obrázku, buď zásady **čtení** a **spravovat** bude fungovat, protože obě mají **naslouchání** oprávnění.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Možná příčina C: se skupina uživatelů k dispozici není výhradně pro Time Series Insights
Během registrace am služby IoT Hub nebo centra událostí zadejte skupinu příjemců, který se má použít pro načtení dat. Tato skupina uživatelů musí **není** sdílet. Pokud se skupina uživatelů se sdílí, základní centra událostí automaticky odpojí jeden čtecích zařízení náhodně. Zadejte skupinu příjemců jedinečný pro čtení ze služby Time Series Insights.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>Problém č. 2: Některá data se zobrazí, ale chybí některé
Pokud vidíte data částečně, ale data zaostává, existuje několik možností vzít v úvahu:

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>Získávání omezený možnou příčinou A: prostředí
Toto je běžný problém při zřizování prostředí po vytvoření zdroje událostí s daty.  Azure IoT Hubs a centra událostí uložte data až sedm dní.  TSI se vždycky spustí z nejstarší události (FIFO), do zdroje událostí.  Takže pokud máte pět milionů událostí ve zdroji událostí při připojování k S1, prostředí TSI jedné jednotky, načte TSI přibližně 1 000 událostí za den.  To může se zdát, že dochází TSI pět dní čekací doba na první pohled.  Co se skutečně děje je, že je omezovaná prostředí.  Pokud máte staré události ve zdroji událostí, můžete přistupovat jedním ze dvou způsobů:

- Změnit limity uchování váš zdroj událostí umožňující vyřadit staré události, které nechcete zobrazit ve službě TSI
- Pokud chcete zvýšit propustnost staré události zřízení větší velikost prostředí (z hlediska počtu jednotek).  Použití v příkladu výše, je-li zvýšit stejném prostředí do pěti jednotek S1 za jeden den, prostředí by měl zachytávání do tohoto okamžiku v rámci dne.  Pokud stabilního stavu události v produkčním prostředí je 1 milion nebo méně událostí za den, můžete po ji zachytila snížit objem událostí zase dolů na jednu jednotku.  

Limit omezení se vynucuje na základě typ SKU prostředí a kapacity. Všechny zdroje událostí do prostředí sdílet tuto kapacitu. Pokud zdroj události pro službu IoT Hub nebo v Centru událostí je předání dat nad rámec vynucené omezení, uvidíte omezení šířky pásma a s menší prodlevou.

Následující diagram znázorňuje prostředí Time Series Insights, který má skladovou Položku S1 a kapacitou 3. Může 3 miliony událostí příchozího přenosu dat za den.

![Aktuální kapacita skladové položky prostředí](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Předpokládejme například, že toto prostředí je ingestovat zprávy z centra událostí. Sledujte rychlost příchozího přenosu dat je znázorněno v následujícím diagramu:

![Příklad rychlost příchozího přenosu dat pro Centrum událostí](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Jak je znázorněno v diagramu, denní rychlost příchozího přenosu dat se ~ 67,000 zprávy. Tato sazba se přeloží přibližně na 46 zprávy každou minutu. Pokud každá zpráva centra událostí se sloučí do jedné události Time Series Insights, zobrazí toto prostředí, nedojde k omezování. Pokud každá zpráva centra událostí se sloučí do 100 událostí Time Series Insights, pak 4,600 události by měly ingestování každou minutu. S1 SKU prostředí, které má kapacitu 3 můžete pouze 2100 událostí příchozího přenosu dat každou minutu (1 milion událostí za den = 700 událostí za minutu za 3 jednotky = 2100 událostí za minutu). Proto se zobrazí s menší prodlevou kvůli omezování. 

Přehled, jak sloučení logiky funguje, najdete v části [tvary JSON nepodporuje](./how-to-shape-query-json.md).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Doporučené řešení kroky pro omezení nadměrné
K vyřešení je zpoždění, zvýšit kapacitu skladové položky vašeho prostředí. Další informace najdete v tématu [jak škálovat vaše prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>Možnou příčinou B: počáteční ingestování historických dat způsobuje pomalé příchozího přenosu dat
Pokud se chcete připojit stávající zdroj události, je pravděpodobné, že služby IoT Hub nebo event hub již obsahuje data v něm. Prostředí spustí načítání dat ze začátku období uchování zpráv zdroje událostí.

Toto chování je výchozí chování a nemůže být přepsána. Můžete zapojit, omezení a že může trvat nějakou neunikly ingestovat historická data.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>Kroky doporučené řešení pro velké počáteční příjmu
Chcete-li vyřešit je zpoždění, proveďte následující kroky:
1. Zvýšit kapacitu skladové položky na maximální povolenou hodnotu (v tomto případě 10). Po kapacitu se zvýší, spustí se proces příchozího přenosu dat, zachytávání se mnohem rychleji. Můžete sledovat, jak rychle jste už zachytávání pomocí grafu dostupnosti v [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com). Bude vám účtována zvýšené kapacity.
2. Poté, co je zpoždění je zachycena, snížit kapacita skladové položky zpět na vaše rychlost příchozího přenosu normální dat.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Potíže s 3: Moje zdroje událostí *názvu vlastnosti časového razítka* nastavení nebude fungovat.
Ujistěte se, že název a hodnotu v souladu s těmito pravidly:
* Název vlastnosti časového razítka je _malá a velká písmena_.
* Hodnota vlastnosti časového razítka, který pochází ze zdroje událostí, jako řetězec formátu JSON by měl mít formát _rrrr-MM-ddTHH. FFFFFFFK_. Příkladem takových řetězec je "2008-04-12T12:53Z".

Nejjednodušší způsob, jak zajistit, aby vaše *názvu vlastnosti časového razítka* jsou zachyceny a správně funguje, je použít v Průzkumníku TSI.  V Průzkumníku TSI, pomocí grafu a vybrat určitou dobu, po jste zadali *názvu vlastnosti časového razítka*.  Klikněte pravým tlačítkem na výběr a zvolte *zkoumat události* možnost.  První záhlaví sloupce musí být váš *názvu vlastnosti časového razítka* a měl by mít *($ts)* vedle slovo *časové razítko*, spíše než:
- *(abc)* , které naznačují TSI čte hodnoty dat jako řetězce
- *Ikonu kalendáře*, které naznačují TSI čte data hodnoty jako *data a času*
- *#*, což by označoval TSI čte hodnoty dat jako celé číslo


## <a name="next-steps"></a>Další postup
- Další pomoc, zahájit konverzaci na [fórum na webu MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Můžete také použít [podpory Azure](https://azure.microsoft.com/support/options/) pro možnosti podpory s asistencí.
