---
title: Nadcházející změny přijímání a sloučení pravidel v Azure Time Series Insights | Microsoft Docs
description: Změny pravidel pro přijímání
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: lyhughes
ms.openlocfilehash: 067244aa40256e3cc76239343790974bc3c06481
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85919039"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Nadcházející změny pravidel sloučení a uvozovacích znaků JSON pro nová prostředí

Tyto změny se použijí jenom pro *nová* Azure Time Series Insights prostředí s průběžnými platbami (PAYG). Tyto změny neplatí pro prostředí Standard (S) SKU.

Vaše Azure Time Series Insights prostředí dynamicky vytváří sloupce úložiště podle konkrétní sady zásad vytváření názvů. Když je událost ingestovaná, použije se pro datovou část a názvy vlastností JSON sada pravidel. Změny v tom, jak se data JSON sloučí a ukládají, začnou platit pro nová Azure Time Series Insights prostředí s průběžnými platbami v červenci 2020. Tato změna se týká následujících případů:

* Pokud vaše datová část JSON obsahuje vnořené objekty
*  Pokud vaše datová část JSON obsahuje pole
*  Pokud použijete některé z následujících čtyř speciálních znaků v názvu vlastnosti JSON: [\. '
*  Pokud jsou některé z vlastností ID TS v rámci vnořeného objektu.

Pokud vytvoříte nové prostředí a jeden nebo více výše uvedených případů platí pro datovou část události, uvidíte, že vaše data jsou shrnutá a uložená jinak. Níže je uveden souhrn změn:

| Aktuální pravidlo | Nové pravidlo | Ukázkový kód JSON | Název předchozího sloupce | Nový název sloupce
|---|---| ---| ---|  ---|
| Vnořený kód JSON se sloučí s použitím podtržítka jako oddělovače. |Vnořený kód JSON se sloučí za použití tečky jako oddělovače.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Speciální znaky nejsou uvozeny řídicími znaky. | Názvy vlastností JSON, které obsahují speciální znaky. [\ a ' jsou uvozeny pomocí znaků [a]. V rámci [a] Existují další uvozovací znaky jednoduchých uvozovek a zpětná lomítka. Zapíše se jedna uvozovka \' a zpětné lomítko se zapíše jako\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` | 
| Pole primitivních hodnot se ukládají jako řetězec. | Pole primitivních typů se ukládají jako dynamický typ.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Pole objektů jsou vždycky shrnutá a vytvářejí několik událostí. | Pokud objekty v poli nemají buď ID TS, nebo časové razítko (y), pole objektů je uloženo jako dynamický typ. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Doporučené změny pro nová prostředí

#### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Pokud je vaše ID TS a/nebo vlastnost časového razítka vnořená do objektu:

*  Všechna nová nasazení budou muset odpovídat novým pravidlům pro přijímání. Pokud třeba vaše ID TS máte, budete `telemetry_tagId` muset aktualizovat všechny šablony ARM nebo skripty pro automatické nasazení, které se nakonfigurují `telemetry.tagId` jako ID služby TS pro prostředí. Tato změna je nutná také pro časová razítka zdroje událostí ve vnořeném formátu JSON.

 #### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Pokud vaše datová část obsahuje vnořené JSON nebo speciální znaky a automatizujte vytváření výrazů s proměnnými [modelu časové řady](.\time-series-insights-update-tsm.md)

*  Aktualizujte kód klienta, který spouští [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch#typesbatchput) , aby odpovídal novým pravidlům pro přijímání. Například [výraz předchozí časové řady](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) `"value": {"tsx": "$event.series_value.Double"}` by měl být aktualizován na jednu z níže uvedených možností:
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`



## <a name="next-steps"></a>Další kroky

- Čtení [Přidání podpory pro datový typ Long](./time-series-insights-long-data-type.md).

- Přečtěte si [Azure Time Series Insights a příchozí úložiště ve verzi Preview](./time-series-insights-update-storage-ingress.md).

