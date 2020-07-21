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
ms.openlocfilehash: cd6ea6d4967e024ddf88fb9572d5efae8b7a7815
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495328"
---
### <a name="property-limits"></a>Omezení vlastností

Azure Time Series Insights limity vlastností se zvýšily na 1 000 z maximálního limitu 800 v Gen1. Zadané vlastnosti událostí mají odpovídající sloupce JSON, CSV a grafu, které lze zobrazit v [Azure Time Series Insights Průzkumníku Gen2](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| Skladová položka | Maximální vlastnosti |
| --- | --- |
| Gen2 (L1) | 1 000 vlastnosti (sloupce) |
| Gen1 (S1) | 600 vlastnosti (sloupce) |
| Gen1 (S2) | 800 vlastnosti (sloupce) |

### <a name="event-sources"></a>Zdroje událostí

Podporuje se maximálně dva zdroje událostí na instanci.

* Naučte se [Přidat zdroj centra událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Nakonfigurujte [Zdroj služby IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Ve výchozím nastavení [prostředí Gen2 podporují](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-throughput-limitations) příchozí přenosy až o **1 megabajt za sekundu (MB/s) na prostředí**. V případě potřeby můžou zákazníci škálovat jejich prostředí až o **16 MB/s** . K dispozici je také omezení počtu **0,5 MB/s**na oddíly.

### <a name="api-limits"></a>Omezení rozhraní API

REST API omezení pro Azure Time Series Insights Gen2 jsou uvedena v [referenční dokumentaci REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
