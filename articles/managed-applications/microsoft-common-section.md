---
title: Azure elementu část uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Common.Section uživatelského rozhraní pro portál Azure.
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
ms.openlocfilehash: 90ffae3dd8b05041c34d766e464eb68f793f6066
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062974"
---
# <a name="microsoftcommonsection-ui-element"></a>Element Microsoft.Common.Section uživatelského rozhraní
Ovládací prvek, který seskupuje jeden či více elementů pod nadpisem.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Poznámky
- `elements` musí mít alespoň jeden element a může mít všechny typy element kromě `Microsoft.Common.Section`.
- Tento element nepodporuje `toolTip` vlastnost.

## <a name="sample-output"></a>Ukázkový výstup
Pro přístup k výstupu hodnoty elementů v `elements`, použijte [basics()](create-uidefinition-functions.md#basics) nebo [steps()](create-uidefinition-functions.md#steps) funkce a s tečkami:

```json
steps('configuration').section1.text1
```

Elementy typu `Microsoft.Common.Section` mít žádné hodnoty výstup, sami.

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
