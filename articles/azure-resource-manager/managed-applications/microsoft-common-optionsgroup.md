---
title: OptionsGroup Prvek ui
description: Popisuje prvek ui skupiny Microsoft.Common.OptionsGroup pro portál Azure. Umožňuje uživatelům vybrat z dostupných možností při nasazování spravované aplikace.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652343"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Element ui skupiny Microsoft.Common.OptionsGroup

Ovládací prvek výběru s řadou dostupných možností.

## <a name="ui-sample"></a>Ukázka ui

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

- Popisek `constraints.allowedValues` pro je zobrazovaný text pro položku a jeho hodnota je výstupní hodnota prvku při výběru.
- Pokud je zadán, musí být výchozí `constraints.allowedValues`hodnota popisek v . Pokud není zadán, je `constraints.allowedValues` ve výchozím nastavení vybrána první položka v programu. Výchozí hodnota je **null**.
- `constraints.allowedValues`musí mít alespoň jednu položku.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
