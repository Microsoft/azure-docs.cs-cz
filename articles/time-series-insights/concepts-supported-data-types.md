---
title: Podporované datové typy – Azure Time Series Insights | Microsoft Docs
description: Přečtěte si o podporovaných datových typech v Azure Time Series Insights Preview.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 1a1ab9fa19956341e930c85d13d7067be4449cea
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049834"
---
# <a name="supported-data-types"></a>Podporované datové typy

Následující tabulka uvádí typy dat podporované nástrojem Time Series Insights

| Datový typ | Popis | Příklad | Název sloupce vlastnosti v Parquet
|---|---|---|---|
| **bool** | Datový typ, který má jeden ze dvou stavů: `true` nebo `false` . | "" s otázkou ": true | isQuestionable_bool
| **datetime** | Představuje okamžitý čas, obvykle vyjádřený jako datum a denní dobu. Vyjádřeno ve formátu [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) . Vlastnosti DateTime jsou vždy uloženy ve formátu UTC. Posuny časového pásma, jsou-li správně formátovány, budou aplikovány a pak vracející hodnotu uloženou v UTC. V [této](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) části najdete další informace o vlastnosti časového razítka prostředí a posunech data a času. | "eventProcessedLocalTime": "2020-03-2016T09:03:32.8301668 Z" | eventProcessedLocalTime_datetime 
| **double** | 64-bit číslo s dvojitou přesností  | "value": 31,0482941 | value_double
| **long** | Podepsané 64 celé číslo se znaménkem  | "value": 31 | value_long
| **řetězec** | Textové hodnoty musí obsahovat platnou znakovou sadu UTF-8. |  "Web": "DIM_MLGGG" | site_string
| **dynamic** | Komplexní (neprimitivní) typ tvořený buď polem, nebo kontejnerem objektů a objektů (Dictionary). V současné době se pouze dokument pole JSON primitivních polí nebo polí objektů, které neobsahují ID TS nebo které jsou v časovém razítku (y). Přečtěte si tento [článek](./concepts-json-flattening-escaping-rules.md) , abyste pochopili, jak budou objekty shrnuté a že je možné zrušit zahrnutí polí. |  "hodnoty": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * Vaše prostředí TSI je silného typu. Pokud zařízení nebo značky odesílají integrální i neintegrální data, hodnoty vlastností zařízení se uloží ve dvou oddělených sloupcích a dlouhém sloupci a [funkce COALESCE ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) by se měla použít při volání rozhraní API a definování výrazů proměnných modelu časové řady.

#### <a name="objects-and-arrays"></a>Objekty a pole

Komplexní typy, jako jsou objekty a pole, můžete posílat jako součást datové části události. Vnořené objekty budou shrnuty a pole budou uložena buď jako `dynamic` nebo shrnutá, aby se vytvořilo více událostí v závislosti na konfiguraci prostředí a tvaru JSON. Další informace o [pravidlech pro sloučení a uvozovací znaky JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Další kroky

* Přečtěte si [pravidla pro sloučení a uvozovací znaky JSON](./concepts-json-flattening-escaping-rules.md) , abyste pochopili, jak budou události uložené. 

* Princip [omezení propustnosti](concepts-streaming-throughput-limitations.md) vašeho prostředí

* Seznamte se se [zdroji událostí](concepts-streaming-ingestion-event-sources.md) a ingestujte streamovaná data.
