---
title: Element uživatelského rozhraní Azure Options | Microsoft Docs
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. Options pro Azure Portal. Umožňuje uživatelům při nasazování spravované aplikace vybrat z dostupných možností.
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
ms.openlocfilehash: 7bec506575f1c526e59487edb67349cdf862f1a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331677"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft. Common. Options – element uživatelského rozhraní
Ovládací prvek výběru s řádkem dostupných možností.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft. Common. Options](./media/managed-application-elements/microsoft.common.optionsgroup.png)

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
- Popisek pro `constraints.allowedValues` je zobrazený text pro položku a jeho hodnota je výstupní hodnota prvku, pokud je vybrána možnost.
- Je-li tento parametr zadán, musí být výchozí hodnota popisek, který je přítomný v `constraints.allowedValues`. Není-li tento parametr zadán, je ve výchozím nastavení vybrána první položka v `constraints.allowedValues`. Výchozí hodnota je **null**.
- `constraints.allowedValues` musí mít alespoň jednu položku.

## <a name="sample-output"></a>Ukázkový výstup
```json
"two"
```

## <a name="next-steps"></a>Další kroky
* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
