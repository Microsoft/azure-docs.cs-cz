---
title: Řešení potíží s Azure Time Series Insights – diagnostikovat a řešit problémy v Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak Diagnostika a řešení potíží s, řešit běžné problémy, které můžete narazit ve vašem prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.custom: seodec18
ms.openlocfilehash: 8e9a2bc8378f71e510d11b3e28438489d620ff6f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322324"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnostikovat a řešit problémy ve vašem prostředí Time Series Insights

Tento článek popisuje některé problémy, které můžete narazit ve vašem prostředí Azure Time Series Insights. Tento článek nabízí možné příčiny a řešení pro řešení.

## <a name="video"></a>Video: 

V tomto videu se budeme zabývat běžné problémy zákazníků služby Time Series Insights a způsoby zmírnění rizik:</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-1-no-data-is-shown"></a>Problém č. 1: Žádná data se zobrazí.

Žádná data v [Průzkumníka služby Azure Time Series Insights](https://insights.timeseries.azure.com) může dojít k několika běžných důvodů:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Příčina A: Události zdroje dat není ve formátu JSON

Azure Time Series Insights podporuje jenom JSON data. Ukázky JSON najdete v tématu [tvary JSON nepodporuje](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Příčina B: Klíč zdroje událostí chybí požadovaná oprávnění

* Pro službu IoT hub ve službě Azure IoT Hub, je nutné zadat klíč, který má **služba připojit** oprávnění. Buď z **iothubowner** nebo **služby** zásad bude fungovat, protože obě mají **připojení služby** oprávnění.

   ![Oprávnění k připojení služby Azure IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)


* Pro Centrum událostí ve službě Azure Event Hubs, je nutné zadat klíč, který má **naslouchání** oprávnění. Některé z **čtení** nebo **spravovat** zásad bude fungovat, protože obě mají **naslouchání** oprávnění.

   ![Oprávnění naslouchat centra událostí](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Příčina C: Skupina uživatelů, které jsou k dispozici není výhradně pro Time Series Insights

Při registraci služby IoT hub nebo centra událostí, je třeba nastavit skupinu příjemců, který chcete použít k načtení dat. Tuto skupinu příjemců *nemohou být sdíleny*. Pokud se skupina uživatelů se sdílí, základní služby IoT hub nebo event hub automaticky a náhodně Odpojí jeden čtecích zařízení. Zadejte skupinu příjemců jedinečný pro čtení ze služby Time Series Insights.

## <a name="problem-2-some-data-is-shown-but-data-is-missing"></a>Problém č. 2: Některá data se zobrazí, ale chybí data

Když se zobrazí data jenom částečně a data jsou pravděpodobně se vzhledem, měli byste zvážit několik možností.

### <a name="cause-a-your-environment-is-being-throttled"></a>Příčina A: Prostředí je omezovaná

Omezení šířky pásma je běžný problém při zřizování prostředí po vytvoření zdroje událostí, který obsahuje data. Azure IoT Hub a centra událostí Azure ukládat data po dobu až sedmi dnů. Time Series Insights vždy začínají nejstarší událost v případě zdroje (první dovnitř, první ven nebo *FIFO*). 

Například pokud máte 5 milionů událostí ve zdroji událostí při připojování k S1, přečte jedné jednotky Time Series Insights prostředí Time Series Insights přibližně 1 milion událostí za den. Může vypadat jako Time Series Insights dochází k pět dní latence. Co se děje se ale, že je omezovaná prostředí. 

Pokud máte staré události ve zdroji událostí, můžete přistupovat, omezení šířky pásma v jednom ze dvou způsobů:

- Změňte limity uchování váš zdroj událostí k odebrání staré události, které nechcete zobrazit v Time Series Insights.
- Pokud chcete zvýšit propustnost staré události zřízení větší velikost prostředí (počet jednotek). Použijeme předchozí příklad, pokud zvýšíte stejné prostředí do pěti jednotek S1 za jeden den, prostředí by měl dohnat starší než jeden den. Pokud pravidelným událost v produkčním prostředí je 1 milion nebo menším počtem událostí za den, můžete zkrátit objemu událostí do jedné jednotky po zachycuje.

Limit omezení se vynucuje na základě typ SKU prostředí a kapacity. Všechny zdroje událostí do prostředí sdílet tuto kapacitu. Pokud zdroj události pro službu IoT hub nebo event hub odesílá data nad rámec vynucené omezení, uvidíte omezení šířky pásma a s menší prodlevou.

Následující obrázek znázorňuje prostředí Time Series Insights, který má skladovou Položku S1 a kapacitou 3. Může 3 miliony událostí příchozího přenosu dat za den.

![Aktuální kapacita skladové položky prostředí](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Jako příklad předpokládejme, že toto prostředí přijímá zprávy z centra událostí. Následující obrázek znázorňuje rychlost příchozího přenosu dat:

![Příklad rychlost příchozího přenosu dat pro Centrum událostí](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Denní rychlost příchozího přenosu dat se ~ 67,000 zprávy. Tato sazba se přeloží na přibližně 46 zprávy každou minutu. Pokud každá zpráva centra událostí se sloučí do jedné události Time Series Insights, omezování nedojde. Pokud každá zpráva centra událostí se sloučí do 100 událostí Time Series Insights, by měl třeba 4,600 události přijímat každou minutu. S1 SKU prostředí, které má kapacitu 3 můžete pouze 2100 událostí příchozího přenosu dat každou minutu (1 milion událostí za den = 700 událostí za minutu za tři jednotky = 2100 událostí za minutu). Pro toto nastavení se zobrazí s menší prodlevou kvůli omezování. 

Přehled, jak sloučení logiky funguje, najdete v části [tvary JSON nepodporuje](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Doporučené řešení pro nadměrné omezení

K vyřešení je zpoždění, zvýšit kapacitu skladové položky vašeho prostředí. Další informace najdete v tématu [škálování prostředí Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Příčina B: Počáteční ingestování historických dat zpomaluje příchozího přenosu dat

Pokud se připojíte existujícímu zdroji událostí, je pravděpodobné, že službě IoT hub nebo event hub již obsahuje data. Prostředí spustí načítání dat ze začátku období uchování zpráv zdroje událostí. Toto je výchozí zpracování a nedají se přepsat. Můžete zapojit, omezení šířky pásma. Omezení využití sítě může chvíli trvat dohnat ingestuje historická data.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Doporučené řešení pro velké počáteční příjmu

Chcete-li vyřešit je zpoždění:

1. Zvýšit kapacitu skladové položky na maximální povolenou hodnotu (v tomto případě 10). Jakmile zvýšíte kapacitu, spustí se proces příchozího přenosu dat mnohem rychleji dohnat. Bude vám účtována zvýšené kapacity. K vizualizaci, jak rychle jste zachycování, až se zobrazí na graf dostupnosti v [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com). 

2. Pokud je zpoždění je zachycena, snížit kapacita skladové položky na vaše rychlost příchozího přenosu normální dat.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Potíže s 3: Nastavení názvu vlastnosti časového razítka zdroj události nefunguje

Ujistěte se, že název vlastnosti časového razítka a hodnotu v souladu s těmito pravidly:
* Název vlastnosti časového razítka je velká a malá písmena.
* Hodnota vlastnosti časového razítka, která se dodává ze zdroje událostí jako řetězec formátu JSON by měl mít formát _rrrr-MM-ddTHH. FFFFFFFK_. Příkladem je **2008-04-12T12:53Z**.

Nejjednodušší způsob, jak zajistit, že váš název vlastnosti časového razítka se zaznamenávají a funguje správně, je použít Průzkumníka služby Time Series Insights. V Průzkumníku Time Series Insights pomocí grafu vyberte určité době po zadání názvu vlastnosti časového razítka. Klikněte pravým tlačítkem na výběr a potom vyberte **zkoumat události** možnost. 

První záhlaví sloupce by měl být název vlastnosti časového razítka. Vedle slovo **časové razítko**, měli byste vidět **($ts)**. 

Neměli vidět následující hodnoty:
- *(abc)* : Označuje, že Time Series Insights čte hodnoty dat jako řetězce.
- *Ikonu kalendáře*: Označuje, že Time Series Insights je čtení data hodnoty jako *data a času*.
- *#*: Označuje, že Time Series Insights čte hodnoty dat jako celé číslo.


## <a name="next-steps"></a>Další postup

- Potřebujete pomoc, zahájit konverzaci [fórum na webu MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Možnosti podpory s asistencí, použijte [podpory Azure](https://azure.microsoft.com/support/options/).
