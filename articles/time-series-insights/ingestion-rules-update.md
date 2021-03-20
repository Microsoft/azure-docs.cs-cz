---
title: Nadcházející změny přijímání a sloučení pravidel v Azure Time Series Insights Gen2 | Microsoft Docs
description: Změny pravidel pro přijímání
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 56a1d5aab2f665f9c5bd8f6fa322f35e55483c7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995229"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Nadcházející změny v pravidlech sloučení a uvozovacích znaků JSON pro nová prostředí

> [!IMPORTANT]
> Tyto změny budou aplikovány na *nově vytvořená* Microsoft Azure Time Series Insights prostředí Gen2. Změny se nevztahují na Gen1 prostředí.

Prostředí Azure Time Series Insights Gen2 dynamicky vytváří sloupce úložiště, a to podle konkrétní sady zásad vytváření názvů. Když se událost ingestuje, Time Series Insights použije sadu pravidel pro datové části JSON a názvy vlastností. Změny v tom, jak se data JSON sloučí a ukládají, se projeví pro nová Azure Time Series Insights Gen2 prostředí v červenci 2020. Tato změna se týká následujících případů:

* Datová část JSON obsahuje vnořené objekty.
* Datová část JSON obsahuje pole.
* V názvu vlastnosti JSON použijete některý z následujících čtyř speciálních znaků: `[` `\` `.``'`
* Jedna nebo více vlastností ID vaší časové řady (TS) jsou v rámci vnořeného objektu.

Pokud vytvoříte nové prostředí a jeden nebo více těchto případů platí pro datovou část události, budou vaše data shrnutá a uložená jinak. Následující tabulka shrnuje změny:

| Aktuální pravidlo | Nové pravidlo | Ukázkový kód JSON | Název předchozího sloupce | Nový název sloupce
|---|---| ---| ---|  ---|
| Vnořený kód JSON je sloučený pomocí podtržítka jako oddělovače. |Vnořený kód JSON je sloučený pomocí tečky jako oddělovače.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Speciální znaky nejsou uvozeny řídicími znaky. | Názvy vlastností JSON, které obsahují speciální znaky `.` `[`   `\` a `'` jsou uvozeny pomocí `['` a `']` . V `['` a `']` existují další uvozovací znaky jednoduchých uvozovek a zpětná lomítka. Jedna uvozovka se zapíše jako `\'` a zpětné lomítko jako `\\` .  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Pole primitivních hodnot jsou uložena jako řetězec. | Pole primitivních typů jsou uložena jako dynamický typ.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Pole objektů jsou vždycky shrnutá a vytvářejí několik událostí. | Pokud objekty v poli nemají buď ID TS, nebo vlastnosti časového razítka, pole objektů je uloženo jako dynamický typ. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Doporučené změny pro nová prostředí

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Pokud je vaše ID TS a/nebo vlastnost časového razítka vnořená do objektu

Všechna nová nasazení musí odpovídat novým pravidlům pro přijímání. Pokud máte například ID TS `telemetry_tagId` , budete muset aktualizovat jakékoli šablony Azure Resource Manager nebo skripty pro automatické nasazení, které se konfigurují `telemetry.tagId` jako ID služby TS. Tuto změnu také budete potřebovat pro časová razítka zdroje událostí ve vnořeném formátu JSON.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Pokud vaše datová část obsahuje vnořené JSON nebo speciální znaky a automatizujte vytváření výrazů s proměnnými [modelu časové řady](./concepts-model-overview.md)

Aktualizujte kód klienta, který spustí [TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) , aby odpovídal novým pravidlům pro přijímání. Například byste měli aktualizovat [výraz předchozí časové řady](/rest/api/time-series-insights/reference-time-series-expression-syntax) `"value": {"tsx": "$event.series_value.Double"}` na jednu z následujících možností:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [Azure Time Series Insights Gen2 úložiště a příchozí přenosy](./concepts-ingestion-overview.md).

* Naučte se, jak zadávat dotazy na data pomocí [rozhraní API pro dotazování časových řad](./concepts-query-overview.md).

* Přečtěte si další informace o [nové syntaxi výrazu časové řady](/rest/api/time-series-insights/reference-time-series-expression-syntax).