---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 1e07f56bc2e820b325414e124c7825c1d356ca26
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046402"
---
Následující shrnuje omezení klíčů v Azure Time Series Insights Gen1.

### <a name="sku-ingress-rates-and-capacities"></a>Míry příchozího přenosu SKU a kapacity

Frekvence a míry příchozího přenosu SKU S1 a S2 poskytují flexibilitu při konfiguraci nového prostředí Azure Time Series Insights. Kapacita SKU indikuje denní míru příchozího přenosu dat na základě počtu uložených událostí nebo bajtů, podle toho, co nastane dřív. Všimněte si, že příchozí přenos dat se měří *za minutu*a **omezování** se aplikuje pomocí algoritmu kontejneru tokenů. Příchozí přenos dat se měří v blocích po 1 KB. Například skutečná událost 0,8-KB bude měřena jako jedna událost a událost 2,6-KB se počítá jako tři události.

| Kapacita SKU S1 | Míra příchozího přenosu dat | Maximální kapacita úložiště
| --- | --- | --- |
| 1 | 1 GB (1 000 000 událostí) za den | 30 GB (30 000 000 událostí) za měsíc |
| 10 | 10 GB (10 000 000 událostí) za den | 300 GB (300 000 000 událostí) za měsíc |

| Kapacita SKU S2 | Míra příchozího přenosu dat | Maximální kapacita úložiště
| --- | --- | --- |
| 1 | 10 GB (10 000 000 událostí) za den | 300 GB (300 000 000 událostí) za měsíc |
| 10 | 100 GB (100 000 000 událostí) za den | 3 TB (3 000 000 000 událostí) za měsíc |

> [!NOTE]
> Kapacity se lineárně škálují, takže SKU S1 s kapacitou 2 podporuje 2 GB (2 000 000) událostí za den a 60 GB (60 000 000 událostí) za měsíc.

Prostředí S2 SKU podporují podstatně více událostí za měsíc a mají výrazně vyšší kapacitu příchozího přenosu dat.

| Skladová položka  | Počet událostí za měsíc  | Počet událostí za minutu | Velikost události za minutu  |
|---------|---------|---------|---------|---------|
| S1     |   30 000 000   |  720    |  720 KB   |
 |S2     |   300 000 000   | 7 200   | 7 200 KB  |

### <a name="property-limits"></a>Omezení vlastností

Omezení vlastností Gen1 závisí na vybraném prostředí SKU. Zadané vlastnosti události mají odpovídající sloupce JSON, CSV a grafu, které se dají zobrazit v [Azure Time Series Insights Exploreru](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| Skladová položka | Maximální vlastnosti |
| --- | --- |
| S1 | 600 vlastnosti (sloupce) |
| S2 | 800 vlastnosti (sloupce) |

### <a name="event-sources"></a>Zdroje událostí

Podporuje se maximálně dva zdroje událostí na instanci.

* Naučte se [Přidat zdroj centra událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Nakonfigurujte [Zdroj služby IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Omezení rozhraní API

REST API omezení pro Azure Time Series Insights Gen1 jsou uvedena v [referenční dokumentaci REST API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).