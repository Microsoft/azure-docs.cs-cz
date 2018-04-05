---
title: Azure elementu rozevírací uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Common.DropDown uživatelského rozhraní pro portál Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 6c92304ae623807ffba05dcdbb576e1cef63b10c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI element
Výběr ovládacího prvku s rozevíracím seznamu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "my value",
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
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Poznámky
- Popisek pro `constraints.allowedValues` je zobrazený text pro položku, a jeho hodnota může být výstupní hodnotu elementu při výběru.
- -Li zadána, výchozí hodnota musí být součástí štítek `constraints.allowedValues`. Pokud není zadaný, první položky v `constraints.allowedValues` je vybrána. Výchozí hodnota je **null**.
- `constraints.allowedValues` musí obsahovat alespoň jednu položku.
- Tento element nepodporuje `constraints.required` vlastnost. Emulovat toto chování, přidejte položku s popisek a hodnota `""` (prázdný řetězec) k `constraints.allowedValues`.

## <a name="sample-output"></a>Ukázkový výstup
```json
"Bar"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
