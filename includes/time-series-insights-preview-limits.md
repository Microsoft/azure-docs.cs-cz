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
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91287405"
---
### <a name="property-limits"></a>Omezení vlastností

Azure Time Series Insights limity vlastností se zvýšily na 1 000 z maximálního limitu 800 v Gen1. Zadané vlastnosti událostí mají odpovídající sloupce JSON, CSV a grafu, které lze zobrazit v [Azure Time Series Insights Průzkumníku Gen2](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Maximální vlastnosti |
| --- | --- |
| Gen2 (L1) | 1 000 vlastnosti (sloupce) |
| Gen1 (S1) | 600 vlastnosti (sloupce) |
| Gen1 (S2) | 800 vlastnosti (sloupce) |

### <a name="streaming-ingestion"></a>Příjem streamování

* Každé prostředí může mít maximálně dva [zdroje událostí](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) .

* Doporučené postupy a obecné pokyny pro zdroje událostí najdete [tady](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) .

* Ve výchozím nastavení Azure Time Series Insights Gen2 může ingestovat příchozí data rychlostí **až 1 MB za sekundu (MB/s) na Azure Time Series Insights prostředí Gen2**. Existují další omezení [na oddíl centra](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). Sazby až 8 MB/s můžete poskytnout odesláním lístku podpory prostřednictvím Azure Portal. Pokud se chcete dozvědět víc, přečtěte si [omezení propustnosti příjmu streamování](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>Omezení rozhraní API

REST API omezení pro Azure Time Series Insights Gen2 jsou uvedena v [referenční dokumentaci REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
