---
title: Prvek uživatelského rozhraní MultiStorageAccountCombo
description: Popisuje prvek uživatelského rozhraní Microsoft.Storage.MultiStorageAccountCombo pro portál Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651875"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Storage.MultiStorageAccountCombo

Skupina ovládacích prvků pro vytvoření několika účtů úložiště s názvy, které začínají běžnou předponou.

## <a name="ui-sample"></a>Ukázka ui

![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Poznámky

- Hodnota pro `defaultValue.prefix` je zřetězena jedním nebo více plnými písmeny pro generování posloupnosti názvů účtů úložiště. Například pokud `defaultValue.prefix` je **sa** `count` a je **2**, pak jsou generovány názvy účtů úložiště **sa1** a **sa2.** Názvy účtů generovaného úložiště jsou automaticky ověřeny z důvodu jedinečnosti.
- Názvy účtů úložiště jsou generovány lexicographically založené na `count`. Například pokud `count` je 10, pak názvy účtů úložiště končí dvoumístná celá čísla (01, 02, 03).
- Výchozí hodnota `defaultValue.prefix` pro je **null**a for `defaultValue.type` je **Premium_LRS**.
- Jakýkoli typ, `constraints.allowedTypes` který není zadán v písmenu a), je skrytý a je zobrazen jakýkoli typ, který není zadán v `constraints.excludedTypes` písmenu a). `constraints.allowedTypes`a `constraints.excludedTypes` jsou volitelné, ale nelze je použít současně.
- Kromě generování názvů `count` účtů úložiště se používá k nastavení příslušného násobitele prvku. Podporuje statickou hodnotu, například **2**nebo dynamickou `[steps('step1').storageAccountCount]`hodnotu z jiného prvku, například . Výchozí hodnota je **1**.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
