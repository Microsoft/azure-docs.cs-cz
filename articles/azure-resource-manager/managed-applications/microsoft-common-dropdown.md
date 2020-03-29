---
title: Element rozhraní a součásti dropdown
description: Popisuje prvek ui Microsoft.Common.DropDown pro portál Azure. Slouží k výběru z dostupných možností při nasazování spravované aplikace.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652382"
---
# <a name="microsoftcommondropdown-ui-element"></a>Element ui rozhraní Microsoft.Common.DropDown

Ovládací prvek výběru s rozevíracím seznamem.

## <a name="ui-sample"></a>Ukázka ui

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
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
- Pokud je zadán, musí být výchozí `constraints.allowedValues`hodnota popisek v . Pokud není zadán, je `constraints.allowedValues` vybrána první položka v programu. Výchozí hodnota je **null**.
- `constraints.allowedValues`musí mít alespoň jednu položku.
- Chcete-li emulovat hodnotu, která není vyžadována, `""` přidejte položku s popiskem a hodnotou (prázdný řetězec) do . `constraints.allowedValues`

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
