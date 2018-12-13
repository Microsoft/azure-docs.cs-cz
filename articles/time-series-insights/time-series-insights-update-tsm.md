---
title: Time Series modelu | Dokumentace Microsoftu
description: Principy časové řady modelu
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 5d5f94aebcd55474385e903246ce7945586456dd
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890411"
---
# <a name="time-series-model"></a>Model služby Time Series

Tento dokument podrobně popisuje **modelu časové řady** (TSM) v rámci aktualizace Azure Time Series Insights (TSI). Popisuje samotného modelu, své schopnosti a jak začít vytvářet a aktualizuje se vlastní model.

Data shromážděná ze zařízení IoT tradičně chybí kontextové informace kvůli tomu obtížné najít a senzory rychle analyzovat. Hlavní motivace pro TSM je zjednodušuje vyhledávání a analýza dat IoT tím, že kurátorování, údržby a obohacení dat časových řad vám pomůže při přípravě spotřebiteli datových sad. TSMs hrát zásadní roli v dotazech a navigace, protože jejich contextualize zařízení a jiných zařízení entity. Data se ukládají v TSM využívá čas řady dotazů výpočty s využitím vzorce, které jsou v nich uložené.

![tsm][1]

## <a name="key-capabilities"></a>Klíčové funkce

S cílem usnadnit jednoduchá zábavná a nijak namáhavá ke správě času řady contextualization TSM umožňuje ve službě TSI Azure (Preview):

* Možnost vytvářet a spravovat výpočty nebo vzorce, který umožňuje transformovat data využití skalární funkce agregační operace, např.
* Definování nadřazené vztahy nadřazenosti a podřízenosti umožňující navigaci a referenční informace k zajištění kontextu pro čas řady telemetrie.
* Definujte vlastnosti související s instancí část pole instancí a tyto použít k vytvoření hierarchie.

## <a name="times-series-model-key-components"></a>Časy klíčové komponenty modelu řady

Existují tři hlavní komponenty TSM:

* **Time Series modelu** *typy*
* **Time Series modelu** *hierarchie*
* **Time Series modelu** *instancí*

## <a name="time-series-model-types"></a>Čas modelu řady typů

**Time Series modelu** *typy* povolit definující proměnné nebo vzorce pro provádění výpočtů a jsou přidruženy k dané instanci služby TSI. Typ může mít jednu nebo více proměnných. Například může být TSI instance typu **teplotní snímač**, který se skládá z proměnné: *průměrná teplota*, *minimální teploty*, a *max teplota*. Výchozí typ vytvoříme při spuštění data přenášejí do TSI. Dají se načíst a aktualizovat z nastavení modelu. Proměnná, která vrátí počet událostí, které budou mít výchozí typy.

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

Další informace o modelu časové řady typů z [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

## <a name="variables"></a>Proměnné

Azure TSI typy mají proměnné, které jsou pojmenované výpočty hodnot z události. Definice proměnných TSI obsahují pravidla vzorec a výpočty. Definice proměnných zahrnují typ, hodnoty, filtr, snížení a hranice. Proměnné jsou uložené v definici typu v TSM a je možné poskytnout vložené prostřednictvím API pro dotazy přepsat uloženou definici.

Matice níže funguje jako legendu pro definice proměnné:

![tabulka][2]

### <a name="variable-kind"></a>Typ proměnné

Jsou podporovány následující typy proměnných:

* Čísla
* Agregovaná hodnota

### <a name="variable-filter"></a>Proměnné filtru

Proměnné filtry zadat klauzuli volitelné filtru k omezení počtu řádků pro výpočty na základě podmínek považují za.

### <a name="variable-value"></a>Hodnota proměnné

Hodnoty proměnných jsou a byste měli použít ve výpočtu. Toto je v události, které jsme by měla odkazovat na sloupec.

### <a name="variable-aggregation"></a>Proměnné agregace

Agregační funkce proměnné umožňuje součást výpočtu. TSI bude podporovat regulární agregace (konkrétně **min**, **maximální**, **avg**, **součet**, a **počet**).

## <a name="time-series-model-hierarchies"></a>Časové řady modelu hierarchie

Hierarchie uspořádání instance tak, že zadáte názvy vlastností a jejich vztahy. Můžete mít jednu hierarchii nebo více hierarchií. Kromě toho to nemusí být aktuální část dat, ale každá instance by měla být mapována do hierarchie. TSM instance můžete namapovat na hierarchii jeden nebo více hierarchií.

Hierarchie, které jsou definovány pomocí **ID hierarchie**, **název**, a **zdroj**. Hierarchie cesty, cestu je hierarchie, které chce uživatel vytvořit pořadí shora dolů. nadřazený podřízený. Vlastnosti nadřazené/podřízené položky mapování polí instance.

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

Další informace o modelu časové řady hierarchie z [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Definice chování hierarchie

Vezměte v úvahu následující příklad, kdy má hierarchii H1 "vytváření", "floor" a "místo" jako část definice:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

V závislosti na pole instancí, hierarchie atributy a hodnoty, zobrazí se zobrazí: 

| ID číselné řady čas | Pole instance |
| --- | --- |
| ID 1 | "sestavení" = "1000", "floor" = "10", "místo" = "55"  |
| S ID 2 | "sestavení" = "1000", "místo" = "55" |
| ID3 |  "floor" = "10" |
| U ID 4 | "sestavení" = "1000", "floor" = "10"  |
| ID5 | |

Ve výše například ID1 zobrazuje jako součást hierarchie H1 v uživatelské rozhraní a prostředí, při se klasifikuje jako `Unparented Instances` od jejich není v souladu s hierarchii zadaná data.

## <a name="time-series-model-instances"></a>Instance řady modelu času

Instance jsou časové řady sami. Ve většině případů *deviceId* nebo *assetId* bude jedinečný identifikátor prostředku v prostředí. Instance mít popisné informace k nim má přiřazené názvem vlastnosti instance. Minimálně instance vlastnosti zahrnují informace o hierarchii. Také mohou obsahovat užitečné a popisný data, jako jsou výrobce, operátor nebo datum posledního služby.

Instance, které jsou definovány pomocí *timeSeriesId*, *typeId*, *hierarchyId*, a *instanceFields*. Každá instance se mapuje na jediný *typ*a jeden nebo více hierarchií. Instance dědí všechny vlastnosti z hierarchie, zatímco další *instanceFields* lze přidat další instanci vlastnosti definice.

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

Další informace o modelu časové řady instancí z [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

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

Další informace o nastavení modelu časové řady [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Další postup

Přečtěte si [Azure TSI (Preview) úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

Čtení informace o novém [modelu časové řady](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
