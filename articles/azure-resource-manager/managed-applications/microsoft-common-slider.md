---
title: Posuvník – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. Slider pro Azure Portal. Umožňuje uživatelům nastavit hodnotu z rozsahu možností.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87097101"
---
# <a name="microsoftcommonslider-ui-element"></a>Microsoft. Common. Slider – element uživatelského rozhraní

Posuvník umožňuje uživatelům vybrat z rozsahu povolených hodnot.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft. Common. Slider":::

## <a name="schema"></a>Schéma

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
26
```

## <a name="remarks"></a>Poznámky

- `min`Hodnoty a `max` jsou povinné. Nastaví počáteční a koncový bod pro posuvník.
- `showStepMarkers`Výchozí hodnota vlastnosti je true. Značky kroku jsou zobrazeny pouze v případě, že rozsah od minima do max je 100 nebo menší.


## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
