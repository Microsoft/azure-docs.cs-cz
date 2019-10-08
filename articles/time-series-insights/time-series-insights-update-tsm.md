---
title: Model časové řady v Azure Time Series Insights Preview | Microsoft Docs
description: Principy Azure Time Series Insightsho modelu časových řad.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 8f4ed6de5ff47efa441c371b80670c500f57c9bb
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034064"
---
# <a name="time-series-model"></a>Model služby Time Series

Tento článek popisuje součást modelu časové řady Azure Time Series Insights ve verzi Preview. Zabývá se samotným modelem, jeho možnostmi a postupem, jak začít sestavovat a aktualizovat svůj vlastní model.

Data shromážděná ze zařízení IoT neobsahují kontextové informace, což usnadňuje rychlé vyhledání a analýzu senzorů. Hlavním motivací pro model časových řad je zjednodušení hledání a analýza dat IoT. Tento cíl dosahuje tím, že umožňuje zpracovat, udržovat a rozšířit data časových řad, aby bylo možné připravovat datové sady připravené pro uživatele.

Modely časových řad hrají v dotazech a navigaci důležitou roli, protože dát zařízení a entity mimo zařízení. Data, která jsou trvalá v modelu časových řad, plní výpočty dotazů na časovou řadu tím, že využívají vzorce, které jsou v nich uložené.

[1Time – Přehled modelu řady @no__t](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>Klíčové funkce

Díky cíli, který zjednodušuje a usnadňuje správu kontextu časových řad, poskytuje model časových řad následující funkce Time Series Insights ve verzi Preview. Pomůže vám:

* Vytvářet a spravovat výpočty nebo vzorce, transformovat data s využitím skalárních funkcí, agregačních operací a tak dále.
* Definujte vztahy nadřazenosti a podřízenosti, které umožňují navigaci a odkazování a poskytování kontextu telemetrie časových řad.
* Definujte vlastnosti, které jsou přidružené k částem instance *polí instance* a použijte je k vytváření hierarchií.

## <a name="entity-components"></a>Komponenty entit

Modely časových řad mají tři základní komponenty:

* <a href="#time-series-model-types">Typy modelů časových řad</a>
* <a href="#time-series-model-hierarchies">Hierarchie modelů časových řad</a>
* <a href="#time-series-model-instances">Instance modelů časových řad</a>

Tyto součásti jsou kombinovány pro určení modelu časové řady a k uspořádání dat Azure Time Series Insights.

## <a name="time-series-model-types"></a>Typy modelů časových řad

*Typy* modelů časových řad vám pomůžou definovat proměnné nebo vzorce pro provádění výpočtů. Typy jsou přidruženy ke konkrétní instanci Time Series Insights. Typ může mít jednu nebo více proměnných. Například instance Time Series Insights může být typu *snímače teploty*, který se skládá z proměnných *Průměrná teplota*, *Minimální teplota*a *Maximální teplota*. Při zahájení toku dat do Time Series Insights vytvoříme výchozí typ. Výchozí typ lze načíst a aktualizovat z nastavení modelu. Výchozí typy mají proměnnou, která počítá počet událostí.

### <a name="time-series-model-type-json-example"></a>Příklad JSON typu modelu časové řady

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
```

Další informace o typech modelů časových řad naleznete v [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="variables"></a>Proměnné

Time Series Insights typy mají proměnné, které jsou pojmenovány výpočty přes hodnoty z událostí. Definice Time Series Insights proměnných obsahují pravidla vzorce a výpočtu. Mezi definice proměnných patří *druh*, *hodnota*, *Filtr*, *snížení*a *hranice*. Proměnné jsou uloženy v definici typu v modelu časové řady a lze je poskytnout prostřednictvím rozhraní API pro dotaz pro přepsání uložené definice.

Následující matice funguje jako legenda pro definice proměnných:

[@no__t – tabulka definic proměnných 1Type](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Definice | Popis |
| --- | ---|
| Druh proměnné |  Jsou podporovány *číselné* a *agregované* druhy. |
| Filtr proměnných | Filtry proměnných určují volitelnou klauzuli filtru, která omezuje počet řádků, které se považují za výpočet na základě podmínek. |
| Hodnota proměnné | Hodnoty proměnných jsou a měly by být použity v výpočtu. Příslušné pole, na které se má odkazovat na daný datový bod. |
| Agregace proměnných | Agregační funkce proměnné umožňuje část výpočtu. Time Series Insights podporuje regulární agregace (konkrétně, *min*, *Max*, *AVG*, *Sum*a *Count*). |

## <a name="time-series-model-hierarchies"></a>Hierarchie modelů časových řad

Hierarchie organizují instance zadáním názvů vlastností a jejich vztahů. Je možné, že máte jednu hierarchii nebo více hierarchií. Nemusí být aktuální součástí vašich dat, ale každá instance by měla být namapována na hierarchii. Instance modelu časové řady může být namapována na jednu hierarchii nebo více hierarchií.

Hierarchie jsou definovány podle *ID*, *názvu*a *zdroje*hierarchie. Hierarchie mají cestu, která je nejnižším pořadím nadřazených a podřízených hierarchií v hierarchii, kterou si uživatelé chtějí vytvořit. Vlastnosti nadřazeného a podřízeného objektu mapují *pole instance*.

### <a name="time-series-model-hierarchy-json-example"></a>Příklad JSON hierarchie modelu časové řady

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

Další informace o hierarchiích modelu časové řady najdete v [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Chování definice hierarchie

Vezměte v úvahu následující příklad, kdy hierarchie H1 má *sestavení*, *podlahu*a *místnost* v rámci své definice:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

V závislosti na *polích instance*se zobrazí atributy a hodnoty hierarchie, jak je znázorněno v následující tabulce:

| ID časové řady | Pole instance |
| --- | --- |
| ID1 | "sestavování" = "1000", "patra" = "10", "místnost" = "55"  |
| ID 2 | "sestavování" = "1000", "místnost" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "sestavování" = "1000", "patra" = "10"  |
| ID5 | Není nastavené žádné možnosti "budova", "patra" ani "místnost". |

V předchozím příkladu **ID1** a **ID4** zobrazit jako součást hierarchie H1 v nástroji Azure Time Series Insights Explorer a zbývající jsou klasifikovány v rámci *nenadřazených instancí* , protože neodpovídají zadané hierarchii dat.

## <a name="time-series-model-instances"></a>Instance modelů časových řad

Instance jsou vlastní časovou řadou. Ve většině případů je *deviceId* nebo *assetId* jedinečný identifikátor prostředku v prostředí. Instance obsahují popisné informace, které jsou s nimi spojeny s názvem vlastnosti instance. Minimálně vlastnosti instance obsahují informace o hierarchii. Můžou také obsahovat užitečná a popisné údaje, jako je výrobce, operátor nebo poslední datum služby.

Instance jsou definovány pomocí *typeId*, *timeSeriesId*, *Name*, *Description*, *hierarchyIds*a *instanceFields*. Každá instance je mapována pouze na jeden *typ*a jednu nebo více hierarchií. Instance dědí všechny vlastnosti z hierarchií a další *instanceFields* je možné přidat pro další definici vlastnosti instance.

*instanceFields* jsou vlastnosti instance a všech statických dat, která definují instanci. Definují hodnoty vlastností hierarchie nebo mimo hierarchii a zároveň podporují indexování k provádění operací vyhledávání.

Vlastnost *Name* je volitelná a rozlišuje velká a malá písmena. Pokud *název* není k dispozici, bude ve výchozím nastavení nastavené ID časové řady. Pokud je zadán *název* , bude ID časové řady stále k dispozici (Mřížka pod grafy v Průzkumníkovi).

### <a name="time-series-model-instance-json-example"></a>Příklad JSON instance modelu časové řady

Ukázka:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
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

Další informace o instancích modelu časové řady najdete v [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

### <a name="time-series-model-settings-example"></a>Příklad nastavení modelu časové řady

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

Další informace o nastavení modelu časové řady najdete v [referenční dokumentaci](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Azure Time Series Insights a příchozí úložiště ve verzi Preview](./time-series-insights-update-storage-ingress.md).

- Podívejte se na nový [model časové řady](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).