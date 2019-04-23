---
title: Azure prvek části uživatelského rozhraní | Dokumentace Microsoftu
description: Popisuje element Microsoft.Common.Section uživatelského rozhraní pro Azure portal.
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
ms.openlocfilehash: c89b45dd4d8e6c2964f3d2bcbb6c3cef445c79e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251886"
---
# <a name="microsoftcommonsection-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Common.Section
Ovládací prvek, který seskupuje jeden nebo více prvků pod nadpisem.

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
- `elements` musí mít alespoň jeden element a může mít všechny typy elementů s výjimkou `Microsoft.Common.Section`.
- Tento prvek nepodporuje `toolTip` vlastnost.

## <a name="sample-output"></a>Ukázkový výstup
Pro přístup k výstupu hodnoty prvků v `elements`, použijte [basics()](create-uidefinition-functions.md#basics) nebo [steps()](create-uidefinition-functions.md#steps) funkce a zápisu s tečkou:

```json
steps('configuration').section1.text1
```

Prvky typu `Microsoft.Common.Section` mají samotné hodnoty žádný výstup.

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
