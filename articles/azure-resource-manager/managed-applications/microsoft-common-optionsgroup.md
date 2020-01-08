---
title: Options – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. Options pro Azure Portal. Umožňuje uživatelům při nasazování spravované aplikace vybrat z dostupných možností.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
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
- Je-li tento parametr zadán, musí být výchozí hodnota popisek, který je přítomný v `constraints.allowedValues`. Pokud není zadaný, ve výchozím nastavení je vybraná první položka v `constraints.allowedValues`. Výchozí hodnota je **null**.
- `constraints.allowedValues` musí mít alespoň jednu položku.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
