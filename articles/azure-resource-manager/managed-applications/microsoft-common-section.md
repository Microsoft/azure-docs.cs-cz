---
title: Oddíl – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. Section pro Azure Portal. Slouží k seskupení prvků na portálu pro nasazení spravovaných aplikací.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652252"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft. Common. Section – element uživatelského rozhraní

Ovládací prvek, který seskupí jeden nebo více prvků pod záhlavím.

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

- `elements`musí mít alespoň jeden element a může mít všechny typy elementů s výjimkou `Microsoft.Common.Section`.
- Tento prvek nepodporuje `toolTip` vlastnost.

## <a name="sample-output"></a>Ukázkový výstup
Pro přístup k výstupním hodnotám prvků `elements`v použijte funkce [základní ()](create-uidefinition-functions.md#basics) nebo [kroky ()](create-uidefinition-functions.md#steps) a zápis tečky:

```json
steps('configuration').section1.text1
```

Prvky typu `Microsoft.Common.Section` nemají samotné žádné výstupní hodnoty.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
