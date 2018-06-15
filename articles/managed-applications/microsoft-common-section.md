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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: e6d7d5d7b205d275c72e96df527a354b072a9dd3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260965"
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
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Poznámky
- `elements` musí obsahovat alespoň jeden element a může obsahovat všechny typy element kromě `Microsoft.Common.Section`.
- Tento element nepodporuje `toolTip` vlastnost.

## <a name="sample-output"></a>Ukázkový výstup
Pro přístup k výstupu hodnoty elementů v `elements`, použijte [basics()](create-uidefinition-functions.md#basics) nebo [steps()](create-uidefinition-functions.md#steps) funkce a s tečkami:

```json
basics('section1').element1
```

Elementy typu `Microsoft.Common.Section` mít žádné hodnoty výstup, sami.

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
