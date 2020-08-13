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
ms.date: 08/12/2020
ms.custom: lyhughes
ms.openlocfilehash: cdf90614e1f766fc37e04a1416081bd35e19b74e
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168197"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Nadcházející změny pravidel sloučení a uvozovacích znaků JSON pro nová prostředí

**Tyto změny se použijí jenom pro *nově vytvořená* Azure Time Series Insights prostředí Gen2. Tyto změny se nevztahují na Gen1 prostředí.**

Prostředí Azure Time Series Insights Gen2 dynamicky vytváří sloupce úložiště, a to podle konkrétní sady zásad vytváření názvů. Když je událost ingestovaná, použije se pro datovou část a názvy vlastností JSON sada pravidel. Změny v tom, jak se data JSON sloučí a ukládají, vstoupí v platnost pro nová Azure Time Series Insights Gen2 prostředí v červenci 2020. Tato změna se týká následujících případů:

* Pokud vaše datová část JSON obsahuje vnořené objekty
* Pokud vaše datová část JSON obsahuje pole
* Pokud použijete některé z následujících čtyř speciálních znaků v názvu vlastnosti JSON: [\. '
* Pokud jsou některé z vlastností ID TS v rámci vnořeného objektu.

Pokud vytvoříte nové prostředí a jeden nebo více výše uvedených případů platí pro datovou část události, uvidíte, že vaše data jsou shrnutá a uložená jinak. Níže je uveden souhrn změn:

| Aktuální pravidlo | Nové pravidlo | Ukázkový kód JSON | Název předchozího sloupce | Nový název sloupce
|---|---| ---| ---|  ---|
| Vnořený kód JSON se sloučí s použitím podtržítka jako oddělovače. |Vnořený kód JSON se sloučí za použití tečky jako oddělovače.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Speciální znaky nejsou uvozeny řídicími znaky. | Názvy vlastností JSON, které obsahují speciální znaky. [\ a ' jsou uvozeny pomocí znaků [a]. V rámci [a] Existují další uvozovací znaky jednoduchých uvozovek a zpětná lomítka. Zapíše se jedna uvozovka \' a zpětné lomítko se zapíše jako\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Pole primitivních hodnot se ukládají jako řetězec. | Pole primitivních typů se ukládají jako dynamický typ.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Pole objektů jsou vždycky shrnutá a vytvářejí několik událostí. | Pokud objekty v poli nemají buď ID TS, nebo časové razítko (y), pole objektů je uloženo jako dynamický typ. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Doporučené změny pro nová prostředí

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Pokud je vaše ID TS a/nebo vlastnost časového razítka vnořená do objektu

* Všechna nová nasazení budou muset odpovídat novým pravidlům pro přijímání. Například pokud máte ID TS, `telemetry_tagId` budete muset aktualizovat šablony Azure Resource Manager (ARM) nebo automatizované skripty pro nasazení, které se konfigurují `telemetry.tagId` jako ID služby TS. Tato změna je nutná také pro časová razítka zdroje událostí ve vnořeném formátu JSON.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Pokud vaše datová část obsahuje vnořené JSON nebo speciální znaky a automatizujte vytváření výrazů s proměnnými [modelu časové řady](.\time-series-insights-update-tsm.md)

* Aktualizujte kód klienta, který spouští [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) , aby odpovídal novým pravidlům pro přijímání. Například [výraz předchozí časové řady](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) `"value": {"tsx": "$event.series_value.Double"}` by měl být aktualizován na jednu z níže uvedených možností:
  * `"value": {"tsx": "$event.series.value.Double"}`
  * `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Další kroky

* Přečtěte si [Azure Time Series Insights Gen2 a příchozí úložiště](./time-series-insights-update-storage-ingress.md).

* Přečtěte si další informace o dotazování dat pomocí [rozhraní API pro dotazování časové řady](./concepts-query-overview.md).

* Přečtěte si další informace o [nové syntaxi výrazu časové řady](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).
