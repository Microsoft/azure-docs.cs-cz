---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/16/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: e8c040bc186932680ce7eb4418a787dc061d02ce
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991143"
---
V následující části jsou shrnuta klíčová omezení obecně dostupné.

### <a name="sku-ingress-rates-and-capacities"></a>Míry příchozího přenosu SKU a kapacity

Frekvence a míry příchozího přenosu SKU S1 a S2 poskytují flexibilitu při konfiguraci nového prostředí Time Series Insights.

| Kapacita SKU S1 | Míra příchozího přenosu dat | Maximální kapacita úložiště
| --- | --- | --- |
| 1\. místo | 1 GB (1 000 000 událostí) | 30 GB (30 000 000 událostí) za měsíc |
| 10 | 10 GB (10 000 000 událostí) | 300 GB (300 000 000 událostí) za měsíc |

| Kapacita SKU S2 | Míra příchozího přenosu dat | Maximální kapacita úložiště
| --- | --- | --- |
| 1\. místo | 10 GB (10 000 000 událostí) | 300 GB (300 000 000 událostí) za měsíc |
| 10 | 100 GB (100 000 000 událostí) | 3 TB (3 000 000 000 událostí) za měsíc |

> [!NOTE]
> Kapacity se lineárně škálují, takže SKU S1 s kapacitou 2 podporuje 2 GB (2 000 000) událostí za den a 60 GB (60 000 000 událostí) za měsíc.

Prostředí S2 SKU podporují podstatně více událostí za měsíc a mají výrazně vyšší kapacitu příchozího přenosu dat.

| Skladová položka  | Počet událostí za měsíc  | Velikost události za měsíc  | Počet událostí za minutu | Velikost události za minutu  |
|---------|---------|---------|---------|---------|
| S1     |   30 000 000     |  30 GB     |  720    |  720 KB   |
 |S2     |   300 000 000    |   300 GB   | 7 200   | 7 200 KB  |

### <a name="property-limits"></a>Omezení vlastností

Omezení vlastností GA závisí na vybraném prostředí SKU. Zadané vlastnosti události mají odpovídající sloupce JSON, CSV a grafu, které se dají zobrazit v [Time Series Insights Exploreru](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| Skladová položka | Maximální vlastnosti |
| --- | --- |
| S1 | 600 vlastnosti (sloupce) |
| S2 | 800 vlastnosti (sloupce) |

### <a name="event-sources"></a>Zdroje událostí

Podporuje se maximálně dva zdroje událostí na instanci. 

* Naučte se [Přidat zdroj centra událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Nakonfigurujte [Zdroj služby IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Omezení rozhraní API

Omezení REST API pro Time Series Insights Obecná dostupnost jsou uvedena v [referenční dokumentaci k REST API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).