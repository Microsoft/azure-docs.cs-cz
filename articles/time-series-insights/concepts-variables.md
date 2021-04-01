---
title: Proměnné modelu – Azure Time Series Insights Gen2 | Microsoft Docs
description: Proměnné modelu
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: fd08dc98fa47617bbc7c8d1fff895377837a7327
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736730"
---
# <a name="time-series-model-variables"></a>Proměnné modelu časové řady

Tento článek popisuje proměnné modelu časové řady, které určují vzorce a pravidla výpočtu pro události.

Každá proměnná může být jeden ze tří typů: *Číselná*, *kategorií* a *Aggregate*.

* **Číselné** typy fungují s souvislými číselnými hodnotami.
* **Kategorií** druhy pracují s definovanou sadou diskrétních hodnot.
* **Agregační** typ kombinuje více proměnných jednoho druhu (buď všechny číselné, nebo všechny kategorií).

V následující tabulce jsou uvedeny vlastnosti, které jsou relevantní pro jednotlivé druhy proměnných.

[![Tabulka proměnných modelu časové řady](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Číselné proměnné

| Proměnná – vlastnost | Description |
| --- | ---|
| Filtr proměnných | Filtry jsou volitelné podmíněné klauzule, které omezují počet řádků, které se považují za výpočet. |
| Hodnota proměnné | Hodnoty telemetrie používané pro výpočet pocházející ze zařízení nebo senzorů nebo transformované pomocí výrazů Time Series. Proměnné číselného typu musí být typu *Double*.|
| Proměnlivá interpolace | Interpolace určuje, jak rekonstruovat signál pomocí stávajících dat. Možnosti *kroku* a *lineární* interpolace jsou k dispozici pro číselné proměnné. |
| Agregace proměnných | Provádět výpočty prostřednictvím podporovaných [agregačních funkcí pro číselné proměnné typy](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind). |

Proměnné odpovídají následujícímu příkladu JSON:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>Proměnné kategorií

| Proměnná – vlastnost | Description |
| --- | ---|
| Filtr proměnných | Filtry jsou volitelné podmíněné klauzule, které omezují počet řádků, které se považují za výpočet. |
| Hodnota proměnné | Hodnoty telemetrie používané pro výpočet pocházející ze zařízení nebo senzorů. Proměnné kategorií druhu musí být buď *Long* , nebo *String*. |
| Proměnlivá interpolace | Interpolace určuje, jak rekonstruovat signál pomocí stávajících dat. Možnost interpolace *kroku* je k dispozici pro proměnné kategorií. |
| Kategorie proměnných | Kategorie vytvoří mapování mezi hodnotami, které přicházejí ze zařízení nebo senzorů do popisku. |
| Výchozí kategorie proměnných | Výchozí kategorie je určena pro všechny hodnoty, které nejsou namapované ve vlastnosti "Categories". |

Proměnné odpovídají následujícímu příkladu JSON:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>Agregační proměnné

| Proměnná – vlastnost | Description |
| --- | ---|
| Filtr proměnných | Filtry jsou volitelné podmíněné klauzule, které omezují počet řádků, které se považují za výpočet. |
| Agregace proměnných | Provádět výpočty prostřednictvím podporovaných [agregačních funkcí pro agregované typy proměnných](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind). |

Proměnné odpovídají následujícímu příkladu JSON:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

Proměnné jsou uloženy v definici typu modelu časové řady a lze je poskytnout vloženě prostřednictvím rozhraní API pro přepsání nebo doplnění uložené definice.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [modelu časových řad](./concepts-model-overview.md).

* Přečtěte si další informace o tom, jak definovat proměnné vložené pomocí [rozhraní API pro dotazy](./concepts-query-overview.md).
