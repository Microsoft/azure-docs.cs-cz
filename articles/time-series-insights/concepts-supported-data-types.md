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
ms.date: 08/12/2020
ms.openlocfilehash: e6fd405d1969a2f40a5f0c3466a57fbec60723e9
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141155"
---
# <a name="supported-data-types"></a>Podporované datové typy

Následující tabulka uvádí typy dat podporované nástrojem Azure Time Series Insights Gen2

| Datový typ | Popis | Příklad | Název sloupce vlastnosti v Parquet
|---|---|---|---|
| **bool** | Datový typ, který má jeden ze dvou stavů: `true` nebo `false` . | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | Představuje okamžitý čas, obvykle vyjádřený jako datum a denní dobu. Vyjádřeno ve formátu [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) . Vlastnosti DateTime jsou vždy uloženy ve formátu UTC. Posuny časového pásma, jsou-li správně formátovány, budou aplikovány a pak vracející hodnotu uloženou v UTC. V [této](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) části najdete další informace o vlastnosti časového razítka prostředí a posunech data a času. | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **double** | 64-bit číslo s dvojitou přesností  | `"value": 31.0482941` | value_double
| **long** | Podepsané 64 celé číslo se znaménkem  | `"value" : 31` | value_long
| **řetězec** | Textové hodnoty musí obsahovat platnou znakovou sadu UTF-8. Hodnoty null a prázdné řetězce jsou ošetřeny stejným způsobem. |  `"site": "DIM_MLGGG"` | site_string
| **dynamic** | Komplexní (neprimitivní) typ tvořený buď polem, nebo kontejnerem objektů a objektů (Dictionary). V současné době se jako dynamická budou ukládat jenom pole dokument JSON primitivních hodnot nebo polí objektů, které neobsahují ID TS nebo vlastnost Timestamp (y). Přečtěte si tento [článek](./concepts-json-flattening-escaping-rules.md) , abyste pochopili, jak budou objekty shrnuty, a pole může být nekumulativní. Vlastnosti datové části uložené jako tento typ jsou přístupné prostřednictvím Azure Time Series Insights Průzkumníku Gen2 a `GetEvents`   rozhraní API pro dotazování. |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>Posílání smíšených datových typů

Vaše prostředí Azure Time Series Insights Gen2 je silného typu. Pokud zařízení nebo značky odesílají data různých typů pro vlastnost zařízení, budou hodnoty uloženy ve dvou oddělených sloupcích a [funkce COALESCE ()](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) by měla být použita při definování výrazů proměnných modelu časové řady v VOLÁNÍCH rozhraní API.

Nástroj Azure Time Series Insights Explorer nabízí způsob, jak automaticky připínat samostatné sloupce stejné vlastnosti zařízení. V následujícím příkladu senzor odesílá `PresentValue` vlastnost, která může být dlouhá i dvojitá. Pokud chcete zadat dotaz na všechny uložené hodnoty (bez ohledu na datový typ) `PresentValue` vlastnosti, vyberte `PresentValue (Double | Long)` a sloučených se na ně.

[![Automatické sloučení v Průzkumníkovi](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objekty a pole

Komplexní typy, jako jsou objekty a pole, můžete posílat jako součást datové části události. Vnořené objekty budou shrnuty a pole budou uložena buď jako `dynamic` nebo shrnutá, aby se vytvořilo více událostí v závislosti na konfiguraci prostředí a tvaru JSON. Další informace o [pravidlech pro sloučení a uvozovací znaky JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si [pravidla pro sloučení a uvozovací znaky JSON](./concepts-json-flattening-escaping-rules.md) , abyste pochopili, jak budou události uložené.

* Princip [omezení propustnosti](./concepts-streaming-ingress-throughput-limits.md) vašeho prostředí

* Seznamte se se [zdroji událostí](concepts-streaming-ingestion-event-sources.md) a ingestujte streamovaná data.
