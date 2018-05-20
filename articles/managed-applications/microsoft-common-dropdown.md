---
title: Azure elementu rozevírací uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Common.DropDown uživatelského rozhraní pro portál Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: c8966c70fba1cbb17d377223ed5348075ebb7adc
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftcommondropdown-ui-element"></a>Element Microsoft.Common.DropDown uživatelského rozhraní
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
