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
ms.date: 11/30/2018
ms.openlocfilehash: 4cb83b61068922002c0c308f4d129a2e069beadd
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855789"
---
# <a name="time-series-model"></a>Model Time Series

Tento dokument podrobně popisuje **modelu časové řady** (TSM) v rámci aktualizace Azure Time Series Insights (TSI). Popisuje samotného modelu, své schopnosti a jak začít vytvářet a aktualizuje se vlastní model.

Data shromážděná ze zařízení IoT tradičně chybí kontextové informace kvůli tomu obtížné najít a senzory rychle analyzovat. Hlavní motivace pro TSM je zjednodušuje vyhledávání a analýza dat IoT tím, že kurátorování, údržby a obohacení dat časových řad vám pomůže při přípravě spotřebiteli datových sad. **Time Series modely** hrát zásadní roli v dotazech a navigace, protože jejich contextualize entity zařízení a jiných zařízení. Data se ukládají v TSM využívá čas řady dotazů výpočty s využitím vzorce, které jsou v nich uložené.

![tsm][1]

## <a name="key-capabilities"></a>Klíčové funkce

S cílem usnadnit jednoduchá zábavná a nijak namáhavá ke správě času řady contextualization TSM umožňuje následující funkce Azure Time Series Insights (preview):

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

## <a name="time-series-model-type-json-request-and-response-example"></a>Časové řady Model typu JSON žádostí a odpovědí – příklad

Zadaný požadavek POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/types/$batch?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

S následujícími JSON text a proměnné atributy:

| Atribut | Požadované nebo volitelné |
| --- | --- |
| **Typ**  |  Požaduje se  |
| **Filtr**  |  Nepovinné |
| **value**  | Hodnotu null nebo není zadaný  |
| **interpolace**  |  Nepovinné |
| **Agregace**  |  Požaduje se |

```JSON
{
    "get": null,
    "put": [
        {
            "name": "SampleType",
            "description": "This is type 2",
            "variables": {
                "Avg Temperature": {
                    "kind": "numeric",
                    "filter": null,
                    "value": { "tsx": "$event.temperature.Double" },
                    "interpolation": "None",
                    "aggregation": {"tsx": "avg($value)"}
                },
                "Count Temperature": {
                    "kind": "aggregate",
                    "filter": null,
                    "value": null,
                    "interpolation": "None",
                    "aggregation": {"tsx": "count()"}
                },
                "Min Temperature": {
                    "kind": "aggregate",
                    "filter": null,
                    "value": null,
                    "interpolation": "None",
                    "aggregation": {"tsx": "min($event.temperature)"}
                },
            }
        }
    ]
}
```

Odpověď:

```JSON
{
    "get": null,
    "put": [
        {
            "timeSeriesType": {
                "id": "fc4f526c-da6e-4b85-87f7-16f6cf9b69be",
                "name": "type2",
                "description": "This is type 2",
                "variables": {
                    "Avg Temperature": {
                        "kind": "numeric",
                        "filter": null,
                        "value": { "tsx": "$event.temperature.Double" },
                        "interpolation": "None",
                        "aggregation": {"tsx": "avg($value)"}
                    },
                    "Count Temperature": {
                        "kind": "aggregate",
                        "filter": null,
                        "value": null,
                        "interpolation": "None",
                        "aggregation": {"tsx": "count()"}
                    },
                    "Min Temperature": {
                        "kind": "aggregate",
                        "filter": null,
                        "value": null,
                        "interpolation": "None",
                        "aggregation": {"tsx": "min($event.temperature)"}
                    }
                }
            },
            "error": null
        }
    ]
}
``````

A **výchozí** *typ* odpověď JSON:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "someType1",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

## <a name="variables"></a>Proměnné

Azure TSI typy mají proměnných, jsou tyto pojmenované výpočty přes hodnoty z události. Definice proměnných TSI obsahují pravidla vzorec a výpočty. Definice proměnných zahrnují typ, hodnoty, filtr, snížení a hranice. Proměnné jsou uložené v definici typu v TSM a je možné poskytnout vložené prostřednictvím API pro dotazy přepsat uloženou definici.

Matice níže funguje jako legendu pro definice proměnné:

![tabulka][2]

### <a name="variable-kind"></a>Typ proměnné

Jsou podporovány následující typy proměnných:

* Numerické – průběžné

### <a name="variable-filter"></a>Proměnné filtru

Proměnné filtry zadat klauzuli volitelné filtru k omezení počtu řádků pro výpočty na základě podmínek považují za.

### <a name="variable-value"></a>Hodnota proměnné

Hodnoty proměnných jsou a byste měli použít ve výpočtu. Toto je v události, které jsme by měla odkazovat na sloupec.

### <a name="variable-interpolation"></a>Proměnné interpolace

Proces převodu na hodnotu za interval sady hodnot se nazývá snížení proměnné. Snížení proměnné může být agregovaný zaznamenaná data ze zdroje nebo znovu vytvořena signály pomocí interpolace a agregovat nebo rekonstruovaných signály pomocí interpolace a vzorkování. Hranice proměnné lze přidat do interpolace, tyto rutiny umožňují výpočty zahrnují události mimo rozsah hledání.

TSI Azure (preview) podporuje následující proměnné interpolace: `linear`, `stepright`, a `none`.

### <a name="variable-aggregation"></a>Proměnné agregace

Agregační funkce proměnné umožňuje součást výpočtu. Pokud je proměnná interpolace `null` nebo `none`, pak TSI bude podporovat regulární agregace (konkrétně, **min**, **maximální**, **avg**, **součet** , a **počet**). Pokud je proměnná interpolace `stepright` nebo `linear`, pak bude podporovat TSI **twmin**, **twmax**, **twavg**, a **twsum**. Počet nelze zadat v interpolace.

## <a name="time-series-model-hierarchies"></a>Časové řady modelu hierarchie

Hierarchie uspořádání instance tak, že zadáte názvy vlastností a jejich vztahy. Můžete mít jednu hierarchii nebo více hierarchií. Kromě toho to nemusí být aktuální část dat, ale každá instance by měla být mapována do hierarchie. TSM instance můžete namapovat na hierarchii jeden nebo více hierarchií.

Hierarchie, které jsou definovány pomocí **ID hierarchie**, **název**, a **zdroj**. Hierarchie cesty, cestu je hierarchie, které chce uživatel vytvořit pořadí shora dolů. nadřazený podřízený. Vlastnosti nadřazené/podřízené položky mapování polí instance.

### <a name="time-series-model-hierarchy-json-request-and-response-example"></a>Časové řady modelu hierarchie JSON žádostí a odpovědí – příklad

Zadaný požadavek POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/hierarchies/$batch?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

S text JSON:

```JSON
{
    "get": null,
    "put": [
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
    ]
}
```

Odpověď:

```JSON
{
    "get": null,
    "put": [
        {
            "hierarchy": {
                "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
                "name": "Location",
                "source": {
                    "instanceFieldNames": [
                        "state",
                        "city"
                    ]
                }
            },
            "error": null
        }
    ]
}
```

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

Instance jsou časové řady sami. Ve většině případů to bude *deviceId* nebo *assetId* , který je jedinečný identifikátor prostředku v prostředí. Instance mít popisné informace k nim má přiřazené názvem vlastnosti instance. Minimálně instance vlastnosti zahrnují informace o hierarchii. Také mohou obsahovat užitečné a popisný data, jako jsou výrobce, operátor nebo datum posledního služby.

Instance, které jsou definovány pomocí *timeSeriesId*, *typeId*, *hierarchyId*, a *instanceFields*. Každá instance se mapuje na jediný *typ*a jeden nebo více hierarchií. Instance dědí všechny vlastnosti z hierarchie, zatímco další *instanceFields* lze přidat další instanci vlastnosti definice.

*instanceFields* jsou vlastnosti instance a statická data, která definuje instanci. Hodnoty vlastností hierarchie nebo jiné hierarchie definují současně také podporuje indexování provádět operace vyhledávání.

## <a name="time-series-model-instance-json-request-and-response-example"></a>Časové řady Model instance JSON žádostí a odpovědí – příklad

Zadaný požadavek POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/instances/$batch?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

S text JSON:

```JSON
{
    "get": null,
    "put": [
        {
            "name": "SampleName",
            "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
            "timeSeriesId": [
                "samplePartitionKeyValueOne"
            ],
            "description": "floor 100",
            "hierarchyIds": [
                "e37a4666-9650-42e6-a6d2-788f12d11158"
            ],
            "instanceFields": {
                "state": "California",
                "city": "Los Angeles"
            }
        }
    ]
}
```

Odpověď:

```JSON
{
    "get": null,
    "put": [
        {
            "instance": null,
            "error": null
        },
        {
            "instance": null,
            "error": null
        }
    ]
}
```

## <a name="time-series-model-settings-example"></a>Příklad nastavení modelu řady čas

Zadaný požadavek POST HTTP:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/modelSettings?api-version=API_VERSION
```

| Název | Popis | Příklad: |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Název nového prostředí  | `environment123` |
| API_VERSION  |  Specifikace rozhraní API | `2018-11-01-preview` |

Odpověď:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "someType1",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

## <a name="time-series-model-limits"></a>Časové řady modelu limity

| Parametr |   Omezení |
| --- | --- |
| Velikost objektu pro model entity (typy, hierarchie a instance)|  32 KB, obsahuje vlastnosti |
| Klíče jako vlastnost identifikační číslo volající stanice nakonfigurovali prostřednictvím webu Azure Portal |   Maximální počet 3 |
| Maximální počet typů v prostředí |    1000|
| Maximální počet proměnných v typu |    50|
| Maximální počet hierarchií v prostředí|   32|
| Maximální hloubka hierarchie | 32|
| Maximální počet hierarchie spojenou s instancí 1   |21|
| Maximální počet instancí v prostředí |    500,000|
| Maximální počet polí Instance na instanci |   50|
| Model objektů operace upsert, aktualizace nebo odstranění za sekundu   |100 za sekundu|
| Velikost požadavku rozhraní API modelu časové řady: služby Batch |  8 MB nebo 1 000 objektů modelu (podle toho, co nastane dříve)|
| Čas Velikost Series Model požadavku: / navrhnout vyhledávání   | 32 KB.|
| Velikost požadavku rozhraní API modelu časové řady: služby Batch    | 32 MB|
| Hledání/navrhnout je 32 MB|  32 MB|

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>Další postup

Přečtěte si [Azure TSI (preview) úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

Přečtěte si informace o novém [modelu časové řady](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
