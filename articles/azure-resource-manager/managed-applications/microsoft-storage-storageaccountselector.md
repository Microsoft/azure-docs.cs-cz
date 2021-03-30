---
title: StorageAccountSelector – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Storage. StorageAccountSelector pro Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: fe70c42387e9dedea8943b5dcce04a1f9ded5190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033277"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft. Storage. StorageAccountSelector – element uživatelského rozhraní

Ovládací prvek pro výběr nového nebo existujícího účtu úložiště.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Ovládací prvek zobrazuje výchozí hodnotu.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft-storage-storageaccountselector.png)

Ovládací prvek umožňuje uživateli vytvořit nový účet úložiště nebo vybrat existující účet úložiště.

![Microsoft. Storage. StorageAccountSelector – nové](./media/managed-application-elements/microsoft-storage-storageaccountselector-new.png)

## <a name="schema"></a>Schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Poznámky

- Je-li tento parametr zadán, `defaultValue.name` je automaticky ověřen pro jedinečnost. Pokud název účtu úložiště není jedinečný, musí uživatel zadat jiný název nebo zvolit existující účet úložiště.
- Výchozí hodnota pro `defaultValue.type` je **Premium_LRS**.
- Libovolný typ, který není zadán v `constraints.allowedTypes` , je skrytý a je zobrazen libovolný typ, který není zadán v `constraints.excludedTypes` . `constraints.allowedTypes` a `constraints.excludedTypes` jsou zároveň volitelné, ale nelze je použít současně.
- Pokud `options.hideExisting` má **hodnotu true**, uživatel nemůže zvolit existující účet úložiště. Výchozí hodnota je **false** (nepravda).

## <a name="next-steps"></a>Další kroky
* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
