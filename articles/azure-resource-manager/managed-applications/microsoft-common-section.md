---
title: Oddíl – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. Section pro Azure Portal. Slouží k seskupení prvků na portálu pro nasazení spravovaných aplikací.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 924aff8f2ba3d796b65f52494845f3b10018065c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063982"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft. Common. Section – element uživatelského rozhraní

Ovládací prvek, který seskupí jeden nebo více prvků pod záhlavím.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Common.Section](./media/managed-application-elements/microsoft-common-section.png)

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

- `elements` musí mít alespoň jeden element a může mít všechny typy elementů s výjimkou `Microsoft.Common.Section` .
- Tento prvek nepodporuje `toolTip` vlastnost.

## <a name="sample-output"></a>Ukázkový výstup
Pro přístup k výstupním hodnotám prvků v `elements` použijte funkce [základní ()](create-ui-definition-referencing-functions.md#basics) nebo [kroky ()](create-ui-definition-referencing-functions.md#steps) a zápis tečky:

```json
steps('configuration').section1.text1
```

Prvky typu `Microsoft.Common.Section` nemají samotné žádné výstupní hodnoty.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
