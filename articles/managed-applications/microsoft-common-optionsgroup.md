---
title: Azure prvek uživatelského rozhraní OptionsGroup | Dokumentace Microsoftu
description: Popisuje element Microsoft.Common.OptionsGroup uživatelského rozhraní pro Azure portal.
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 9aee881844e9338cc1da2484a94c8355f2516c82
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720756"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Common.OptionsGroup
Výběr ovládacího prvku s řádkem dostupných možností.

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

## <a name="remarks"></a>Poznámky
- Popisek `constraints.allowedValues` je zobrazený text pro položku a její hodnota je výstupní hodnota elementu, který vyberete.
- Je-li zadán, výchozí hodnota musí být k dispozici v popisku `constraints.allowedValues`. Pokud není zadán, první položky v `constraints.allowedValues` je standardně vybraná. Výchozí hodnota je **null**.
- `constraints.allowedValues` musí mít alespoň jednu položku.

## <a name="sample-output"></a>Ukázkový výstup
```json
"two"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
