---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 5ec1f71d13d5d2fb3af86c1c63d5e61ce13d2c82
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553184"
---
### <a name="property-limits"></a>Omezení vlastností

Omezení Azure Time Series Insights vlastností se zvýšila na 1 000 pro teplé úložiště a žádné omezení vlastností pro studené úložiště. Zadané vlastnosti událostí mají odpovídající sloupce JSON, CSV a grafu, které lze zobrazit v [Azure Time Series Insights Průzkumníku Gen2](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| Skladová položka | Maximální vlastnosti |
| --- | --- |
| Gen2 (L1) | 1 000 vlastnosti (sloupce) pro teplé úložiště a neomezené pro studené úložiště|
| Gen1 (S1) | 600 vlastnosti (sloupce) |
| Gen1 (S2) | 800 vlastnosti (sloupce) |

### <a name="streaming-ingestion"></a>Příjem streamování

* Každé prostředí může mít maximálně dva [zdroje událostí](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) .

* Doporučené postupy a obecné pokyny pro zdroje událostí najdete [tady](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) .

* Ve výchozím nastavení Azure Time Series Insights Gen2 může ingestovat příchozí data rychlostí **až 1 MB za sekundu (MB/s) na Azure Time Series Insights prostředí Gen2**. Existují další omezení [na oddíl centra](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). Sazby až 8 MB/s můžete poskytnout odesláním lístku podpory prostřednictvím Azure Portal. Pokud se chcete dozvědět víc, přečtěte si [omezení propustnosti příjmu streamování](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>Omezení rozhraní API

REST API omezení pro Azure Time Series Insights Gen2 jsou uvedena v [referenční dokumentaci REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
