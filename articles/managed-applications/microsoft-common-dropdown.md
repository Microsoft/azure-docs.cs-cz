---
title: Azure prvek uživatelského rozhraní rozevíracího seznamu | Dokumentace Microsoftu
description: Popisuje element Microsoft.Common.DropDown uživatelského rozhraní pro Azure portal.
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
ms.openlocfilehash: e78fa419b067c0bad48229dcfd8d4e986fc16903
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62117297"
---
# <a name="microsoftcommondropdown-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Common.DropDown
Výběr ovládacího prvku s rozevíracím seznamu.

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

## <a name="remarks"></a>Poznámky

- Popisek `constraints.allowedValues` je zobrazený text pro položku a její hodnota je výstupní hodnota elementu, který vyberete.
- Je-li zadán, výchozí hodnota musí být k dispozici v popisku `constraints.allowedValues`. Pokud není zadán, první položky v `constraints.allowedValues` zaškrtnuto. Výchozí hodnota je **null**.
- `constraints.allowedValues` musí mít alespoň jednu položku.
- Pro emulaci hodnotu není povinné, přidat položku se popisek a hodnota `""` (prázdný řetězec) do `constraints.allowedValues`.

## <a name="sample-output"></a>Ukázkový výstup
```json
"two"
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
