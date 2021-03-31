---
title: Options – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. Options pro Azure Portal. Umožňuje uživatelům při nasazování spravované aplikace vybrat z dostupných možností.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: aa73b4cbded98291a14792a7151df9fdfb885b53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87004182"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft. Common. Options – element uživatelského rozhraní

Ovládací prvek options umožňuje uživatelům vybrat jednu možnost ze dvou nebo více možností. Uživatel může vybrat pouze jednu možnost.

> [!NOTE]
> V minulosti tento ovládací prvek vykresluje možnosti vodorovně. Nyní ovládací prvek prezentuje možnosti svisle jako přepínače.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

:::image type="content" source="./media/managed-application-elements/microsoft-common-optionsgroup-2.png" alt-text="Microsoft.Common.OptionsGroup":::

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
- `constraints.allowedValues` musí mít alespoň jednu položku.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
