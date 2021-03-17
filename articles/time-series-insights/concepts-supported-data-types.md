---
title: Podporované datové typy – Azure Time Series Insights Gen2 | Microsoft Docs
description: Přečtěte si o podporovaných datových typech v Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: b0536a152797d17cba0930b3a142a7fb92eaf5ea
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685153"
---
# <a name="supported-data-types"></a>Podporované datové typy

Následující tabulka uvádí typy dat podporované nástrojem Azure Time Series Insights Gen2

| Datový typ | Popis | Příklad | [Syntaxe výrazů časové řady](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Název sloupce vlastnosti v Parquet
|---|---|---|---|---|
| **bool** | Datový typ, který má jeden ze dvou stavů: `true` nebo `false` . | `"isQuestionable" : true` | `$event.isQuestionable.Bool` nebo `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | Představuje okamžitý čas, obvykle vyjádřený jako datum a denní dobu. Vyjádřeno ve formátu [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) . Vlastnosti DateTime jsou vždy uloženy ve formátu UTC. Posuny časového pásma, jsou-li správně formátovány, budou aplikovány a pak vracející hodnotu uloženou v UTC. V [této](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) části najdete další informace o vlastnosti časového razítka prostředí a posunech data a času. | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  Pokud je "eventProcessedLocalTime" časové razítko zdroje událostí: `$event.$ts` . Pokud se jedná o jinou vlastnost JSON: `$event.eventProcessedLocalTime.DateTime` nebo `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | 64-bit číslo s dvojitou přesností  | `"value": 31.0482941` | `$event.value.Double` nebo `$event['value'].Double` |  `value_double`
| **long** | Podepsané 64 celé číslo se znaménkem  | `"value" : 31` | `$event.value.Long` nebo `$event['value'].Long` |  `value_long`
| **řetezce** | Textové hodnoty musí obsahovat platnou znakovou sadu UTF-8. Hodnoty null a prázdné řetězce jsou ošetřeny stejným způsobem. |  `"site": "DIM_MLGGG"`| `$event.site.String` nebo `$event['site'].String`| `site_string`
| **dynamic** | Komplexní (neprimitivní) typ tvořený buď polem, nebo kontejnerem objektů a objektů (Dictionary). V současné době se jako dynamická budou ukládat jenom pole dokument JSON primitivních hodnot nebo polí objektů, které neobsahují ID TS nebo vlastnost Timestamp (y). Přečtěte si tento [článek](./concepts-json-flattening-escaping-rules.md) , abyste pochopili, jak budou objekty shrnuty, a pole může být nekumulativní. Vlastnosti datové části uložené jako tento typ jsou přístupné jenom výběrem `Explore Events` v průzkumníkovi Time Series Insights k zobrazení nezpracovaných událostí nebo prostřednictvím [`GetEvents`](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) rozhraní API pro analýzu na straně klienta. |  `"values": "[197, 194, 189, 188]"` | Odkazování na dynamické typy ve výrazu časové řady ještě není podporováno. | `values_dynamic`

> [!NOTE]
> 64 bitové celočíselné hodnoty jsou podporovány, ale největší číslo, které může aplikace Azure Time Series Insights Explorer bezpečně vyjádřit, je 9 007 199 254 740 991 (2 ^ 53-1) z důvodu omezení jazyka JavaScript. Pokud pracujete s čísly v datovém modelu výše, můžete velikost zmenšit tak, že vytvoříte [proměnnou modelu časové řady](./concepts-variables.md#numeric-variables) a [převedete](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) tuto hodnotu.

> [!NOTE]
> Typ **řetězce** nemůže mít hodnotu null:
>
> * [Výraz časové řady (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax) vyjádřený v [dotazu časové řady](/rest/api/time-series-insights/reference-query-apis) , který porovnává hodnotu prázdného řetězce ('**'**) s **hodnotou null** , se chová stejným způsobem: `$event.siteid.String = NULL` je ekvivalentem `$event.siteid.String = ''` .
> * Rozhraní API může vracet hodnoty **null** i v případě, že původní události obsahovaly prázdné řetězce.
> * Nepoužívejte závislost na hodnotách **null** ve sloupcích **řetězců** k provedení porovnání nebo vyhodnocení, považovat je za stejným způsobem jako prázdné řetězce.

## <a name="sending-mixed-data-types"></a>Posílání smíšených datových typů

Vaše prostředí Azure Time Series Insights Gen2 je silného typu. Pokud zařízení nebo značky odesílají data různých typů pro vlastnost zařízení, budou hodnoty uloženy ve dvou oddělených sloupcích a [funkce COALESCE ()](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) by měla být použita při definování výrazů proměnných modelu časové řady v VOLÁNÍCH rozhraní API.

Nástroj Azure Time Series Insights Explorer nabízí způsob, jak automaticky připínat samostatné sloupce stejné vlastnosti zařízení. V následujícím příkladu senzor odesílá `PresentValue` vlastnost, která může být dlouhá i dvojitá. Pokud chcete zadat dotaz na všechny uložené hodnoty (bez ohledu na datový typ) `PresentValue` vlastnosti, vyberte `PresentValue (Double | Long)` a sloučených se na ně.

[![Automatické sloučení v Průzkumníkovi](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objekty a pole

Komplexní typy, jako jsou objekty a pole, můžete posílat jako součást datové části události. Vnořené objekty budou shrnuty a pole budou uložena buď jako `dynamic` nebo shrnutá, aby se vytvořilo více událostí v závislosti na konfiguraci prostředí a tvaru JSON. Další informace o [pravidlech pro sloučení a uvozovací znaky JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si [pravidla pro sloučení a uvozovací znaky JSON](./concepts-json-flattening-escaping-rules.md) , abyste pochopili, jak budou události uložené.

* Princip [omezení propustnosti](./concepts-streaming-ingress-throughput-limits.md) vašeho prostředí

* Seznamte se se [zdroji událostí](concepts-streaming-ingestion-event-sources.md) a ingestujte streamovaná data.
