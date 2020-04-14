---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263335"
---
Následující shrnuje omezení klíčů v obecné dostupnosti.

### <a name="sku-ingress-rates-and-capacities"></a>SAZBY A Kapacity příchozích skladových položk

S1 a S2 SKU příchozích přenosů dat sazby a kapacity poskytují flexibilitu při konfiguraci nového prostředí Time Series Insights. Kapacita skladové položky označuje denní rychlost příchozího přenosu dat na základě počtu uložených událostí nebo bajtů podle toho, co nastane dříve. Všimněte si, že příchozí přenos dat se měří *za minutu*a **omezení** se používá pomocí algoritmu token bucket. Příchozí přenos se měří v blocích 1 KB. Například 0,8 KB skutečné události by se měří jako jedna událost a 2,6 KB událost se počítá jako tři události.

| Kapacita skladových míst S1 | Rychlost příchozího přenosu dat | Maximální úložná kapacita
| --- | --- | --- |
| 1 | 1 GB (1 milion událostí) za den | 30 GB (30 milionů událostí) za měsíc |
| 10 | 10 GB (10 milionů událostí) za den | 300 GB (300 milionů událostí) za měsíc |

| Kapacita skladových míst S2 | Rychlost příchozího přenosu dat | Maximální úložná kapacita
| --- | --- | --- |
| 1 | 10 GB (10 milionů událostí) za den | 300 GB (300 milionů událostí) za měsíc |
| 10 | 100 GB (100 milionů událostí) za den | 3 TB (3 miliardy událostí) za měsíc |

> [!NOTE]
> Kapacity škálovat lineárně, takže S1 SKU s kapacitou 2 podporuje 2 GB (2 miliony) událostí za den příchozího přenosu dat a 60 GB (60 milionů událostí) za měsíc.

Prostředí skladových míst s skladem podporují podstatně více událostí za měsíc a mají výrazně vyšší kapacitu příchozího přenosu dat.

| Skladová jednotka (SKU)  | Počet událostí za měsíc  | Počet událostí za minutu | Velikost události za minutu  |
|---------|---------|---------|---------|---------|
| S1     |   30 milionů   |  720    |  720 KB   |
 |S2     |   300 milionů   | 7,200   | 7 200 KB  |

### <a name="property-limits"></a>Omezení vlastností

Omezení vlastností GA závisí na vybraném prostředí sku. Zadané vlastnosti událostí mají odpovídající sloupce JSON, CSV a grafu, které lze zobrazit v průzkumníku [přehledů časových řad](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| Skladová jednotka (SKU) | Maximální vlastnosti |
| --- | --- |
| S1 | 600 vlastností (sloupců) |
| S2 | 800 vlastností (sloupce) |

### <a name="event-sources"></a>Zdroje událostí

Je podporovány maximálně dva zdroje událostí na instanci. 

* Přečtěte si, jak [přidat zdroj centra událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Konfigurace [zdroje centra IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limity rozhraní API

Omezení rozhraní API doby pro obecnou dostupnost časových řad jsou specifikována v [referenční dokumentaci rozhraní REST API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).