---
title: Prvek ui oddílu
description: Popisuje prvek ui Microsoft.Common.Section pro portál Azure. Slouží k seskupení prvků na portálu pro nasazení spravovaných aplikací.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652252"
---
# <a name="microsoftcommonsection-ui-element"></a>Element ui microsoft.common.section

Ovládací prvek, který seskupuje jeden nebo více prvků pod nadpisem.

## <a name="ui-sample"></a>Ukázka ui

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

- `elements`musí mít alespoň jeden prvek a může `Microsoft.Common.Section`mít všechny typy prvků s výjimkou .
- Tento prvek nepodporuje `toolTip` vlastnost.

## <a name="sample-output"></a>Ukázkový výstup
Chcete-li získat přístup `elements`k výstupním hodnotám prvků v aplikaci , použijte základní [funkce()](create-uidefinition-functions.md#basics) nebo [steps()](create-uidefinition-functions.md#steps) a dot notaci:

```json
steps('configuration').section1.text1
```

Prvky `Microsoft.Common.Section` typu nemají žádné výstupní hodnoty samy o sobě.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
