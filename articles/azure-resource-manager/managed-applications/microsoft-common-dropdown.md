---
title: DropDown – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. DropDown pro Azure Portal. Slouží k výběru z dostupných možností při nasazování spravované aplikace.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652382"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft. Common. DropDown – element uživatelského rozhraní

Ovládací prvek výběru s rozevíracím seznamem.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

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

- Popisek pro `constraints.allowedValues` je zobrazený text pro položku a její hodnota je výstupní hodnota prvku, když je vybraná.
- Je-li tento parametr zadán, musí být výchozí hodnota popisek, který je přítomný v `constraints.allowedValues`. Pokud není zadán, je vybrána první položka v `constraints.allowedValues`. Výchozí hodnota je **null**.
- `constraints.allowedValues` musí mít alespoň jednu položku.
- Chcete-li emulovat hodnotu, která není požadována, přidejte položku s popiskem a hodnotou `""` (prázdný řetězec) pro `constraints.allowedValues`.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
