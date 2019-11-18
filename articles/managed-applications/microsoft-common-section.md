---
title: Element uživatelského rozhraní oddílu Azure | Microsoft Docs
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. Section pro Azure Portal. Slouží k seskupení prvků na portálu pro nasazení spravovaných aplikací.
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
ms.openlocfilehash: fd2c1105078b918043791fd0f18395409bb32f7c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151710"
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

- `elements` musí mít alespoň jeden element a může mít všechny typy prvků kromě `Microsoft.Common.Section`.
- Tento prvek nepodporuje vlastnost `toolTip`.

## <a name="sample-output"></a>Ukázkový výstup
Pro přístup k výstupním hodnotám prvků v `elements`použijte funkce [Basic ()](create-uidefinition-functions.md#basics) nebo [kroky ()](create-uidefinition-functions.md#steps) a zápis tečky:

```json
steps('configuration').section1.text1
```

Prvky typu `Microsoft.Common.Section` nemají samotné výstupní hodnoty.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
