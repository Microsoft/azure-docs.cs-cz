---
title: Model Time Series ve službě Azure Time Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Principy Azure Time Series Insights časové řady modelu.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: a6eb142cf607c286ccce1282bb5c67a30c040c62
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53716578"
---
# <a name="time-series-model"></a>Model služby Time Series

Tento článek popisuje Model časové řady součástí Azure čas Series Insights ve verzi Preview. Popisuje samotného modelu, své schopnosti a jak začít vytvářet a aktualizuje se vlastní model.

Tradičně chybí data, která se shromažďuje ze zařízení IoT kontextové informace, které je obtížné najít a rychle analyzovat senzory. Zjednodušuje vyhledávání a analýza dat IoT je hlavní motivace pro Model časové řady. Povolením kurátorování, údržby a obohacení dat časových řad vám pomůže při přípravě datové sady spotřebiteli dosahuje tohoto cíle. 

Čas řady modely hrát zásadní roli v dotazech a navigace vzhledem k tomu, že contextualize zařízení a jiných zařízení entity. Data, která obsahuje trvalé v modelu časové řady využívají výpočty dotazu časových řad s využitím vzorce, které jsou v nich uložené.

![tsm][1]

## <a name="key-capabilities"></a>Klíčové funkce

Model časové řady s cílem usnadnit jednoduchá zábavná a nijak namáhavá ke správě contextualization řady čas umožňuje následující funkce v čase Series Insights ve verzi Preview. To vám pomůže:

* Vytváření a správa výpočtů nebo vzorce, transformovat data využití skalární funkce, agregace operací a podobně.

* Definování vztahů nadřazenosti a podřízenosti k povolení navigace a odkaz a poskytují kontext k času řady telemetrii.

* Definovat vlastnosti, které jsou spojeny s instancí součástí *instance pole* a jejich používání při vytváření hierarchie.

## <a name="times-series-model-key-components"></a>Časy klíčové komponenty modelu řady

Model časové řady má tři hlavní komponenty:

* Time Series modelu *typy*
* Time Series modelu *hierarchie*
* Time Series modelu *instancí*

## <a name="time-series-model-types"></a>Čas modelu řady typů

Time Series modelu *typy* umožňují definovat proměnné nebo vzorce pro provádění výpočtů. Typy jsou spojeny s konkrétní instanci služby Time Series Insights. Typ může mít jednu nebo více proměnných. Například může být instance služby Time Series Insights typu *teplotní snímač*, který se skládá z proměnné *průměrná teplota*, *minimální teploty*a *max. teploty*. Výchozí typ vytvoříme při spuštění data přenášejí do služby Time Series Insights. Výchozí typ lze načíst a aktualizovat z nastavení modelu. Výchozí typy mají proměnná, která vrátí počet událostí.

## <a name="time-series-model-type-json-example"></a>Časové řady Model typu JSON – příklad

Ukázka:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
``````

Další informace o typech modelu časové řady, najdete v článku [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

## <a name="variables"></a>Proměnné

Čas Series Insights typy mají proměnné, které jsou pojmenované výpočty hodnot z události. Definice proměnných Series Insights čas obsahují pravidla vzorec a výpočty. Definice proměnných patří *druh*, *hodnotu*, *filtr*, *snížení*, a *hranice*. Proměnné jsou uložené v definici typu v modelu časové řady a je možné poskytnout vložené prostřednictvím API pro dotazy přepsat uloženou definici.

Následující matice funguje jako legendu pro definice proměnné:

![tabulka][2]

### <a name="variable-kind"></a>Typ proměnné

Jsou podporovány následující typy proměnných:

* *Číselné*
* *Agregace*

### <a name="variable-filter"></a>Proměnné filtru

Proměnné filtry zadat klauzuli volitelné filtru k omezení počtu řádků pro výpočty na základě podmínek považují za.

### <a name="variable-value"></a>Hodnota proměnné

Hodnoty proměnných jsou a byste měli použít ve výpočtu. Toto je v události, které jsme by měla odkazovat na sloupec.

### <a name="variable-aggregation"></a>Proměnné agregace

Agregační funkce proměnné umožňuje součást výpočtu. Time Series Insights podporuje pravidelné agregace (konkrétně *min*, *maximální*, *avg*, *součet*, a *počet*).

## <a name="time-series-model-hierarchies"></a>Časové řady modelu hierarchie

Hierarchie uspořádání instance tak, že zadáte názvy vlastností a jejich vztahy. Můžete mít jednu hierarchii nebo více hierarchií. Nemusí být aktuální část dat, ale každá instance by měla být mapována do hierarchie. Instance modelu časové řady můžete namapovat na hierarchii jeden nebo více hierarchií.

Hierarchie, které jsou definovány pomocí *ID hierarchie*, *název*, a *zdroj*. Hierarchie mít cesta, která je v pořadí shora dolů. nadřazený podřízený hierarchie, která uživatelé chtějí vytvořit. Mapování vlastnosti nadřazenosti a podřízenosti *instance pole*.

### <a name="time-series-model-hierarchy-json-example"></a>Hierarchie časové řady modelu – příklad JSON

Ukázka:

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

Další informace o modelu časové řady hierarchii, najdete v článku [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Definice chování hierarchie

Zvažte následující příklad, kdy má hierarchii H1 *vytváření*, *floor*, a *místnosti* jako část definice:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

V závislosti na tom *instance pole*, hierarchie atributy a hodnoty se zobrazí, jak je znázorněno v následující tabulce:

| ID časové řady | Pole instance |
| --- | --- |
| ID 1 | "sestavení" = "1000", "floor" = "10", "místo" = "55"  |
| S ID 2 | "sestavení" = "1000", "místo" = "55" |
| ID3 | "floor" = "10" |
| U ID 4 | "sestavení" = "1000", "floor" = "10"  |
| ID5 | "Sestavení", "floor" nebo "místo" je Nenastaveno |

V předchozím příkladu ID1 a u ID 4 zobrazuje jako součást hierarchie H1 v Průzkumníku služby Azure Time Series Insights a zbývající jsou zařazeny do *bez nadřazených položek instance* vzhledem k tomu, že jsou není v souladu s hierarchii zadaná data.

## <a name="time-series-model-instances"></a>Instance řady modelu času

Instance jsou časové řady sami. Ve většině případů *deviceId* nebo *assetId* je jedinečný identifikátor prostředku v prostředí. Instance mít popisné informace k nim má přiřazené názvem vlastnosti instance. Minimálně instance vlastnosti zahrnují informace o hierarchii. Také mohou obsahovat užitečné a popisný data, jako jsou výrobce, operátor nebo datum posledního služby.

Instance, které jsou definovány pomocí *timeSeriesId*, *typeId*, *hierarchyId*, a *instanceFields*. Každá instance se mapuje na jediný *typ*a jeden nebo více hierarchií. Instance dědí všechny vlastnosti z hierarchie a další *instanceFields* lze přidat další instanci vlastnosti definice.

*instanceFields* jsou vlastnosti instance a statická data, která definuje instanci. Hodnoty vlastností hierarchie nebo jiné hierarchie definují současně také podporuje indexování provádět operace vyhledávání.

## <a name="time-series-model-instance-json-example"></a>Časové řady Model instance JSON – příklad

Ukázka:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

Další informace o modelu časové řady instancí, najdete v článku [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-settings-example"></a>Příklad nastavení modelu řady čas

Ukázka:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

Další informace o nastavení modelu časové řady, najdete v článku [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Další postup

Zobrazit [Azure čas Series Insights ve verzi Preview úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

Podívejte se na novou [modelu časové řady](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
