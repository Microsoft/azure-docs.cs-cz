---
title: Options – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. Options pro Azure Portal. Umožňuje uživatelům při nasazování spravované aplikace vybrat z dostupných možností.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652343"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft. Common. Options – element uživatelského rozhraní

Ovládací prvek výběru s řádkem dostupných možností.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
"two"
```

## <a name="remarks"></a>Poznámky

- Popisek pro `constraints.allowedValues` je zobrazený text pro položku a její hodnota je výstupní hodnota prvku, když je vybraná.
- Je-li tento parametr zadán, výchozí hodnota musí být popisek přítomný v `constraints.allowedValues` . Není-li tento parametr zadán, je první položka ve `constraints.allowedValues` výchozím nastavení vybrána. Výchozí hodnota je **null**.
- `constraints.allowedValues`musí mít alespoň jednu položku.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
