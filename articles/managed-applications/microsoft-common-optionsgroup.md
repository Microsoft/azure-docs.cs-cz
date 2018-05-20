---
title: Azure elementu OptionsGroup uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Common.OptionsGroup uživatelského rozhraní pro portál Azure.
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
ms.openlocfilehash: 5387f3911c58b115629c461420737230fce6b85a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Element Microsoft.Common.OptionsGroup uživatelského rozhraní
Ovládací prvek výběru s řádek dostupných možností.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
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
- -Li zadána, výchozí hodnota musí být součástí štítek `constraints.allowedValues`. Pokud není zadaný, první položky v `constraints.allowedValues` je standardně vybraná. Výchozí hodnota je **null**.
- `constraints.allowedValues` musí obsahovat alespoň jednu položku.
- Tento element nepodporuje `constraints.required` vlastnost; musí být vybrána položka úspěšně ověřit.

## <a name="sample-output"></a>Ukázkový výstup
```json
"Bar"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
