---
title: Prvek ui služby StorageAccountSelector
description: Popisuje prvek ui microsoft.storageaccountaccountselector pro portál Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 4fcaf0e5842ce8a65175d2fc1dfa2483a1203b2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651888"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Element ui účtu Microsoft.Storage.StorageAccountSelector

Ovládací prvek pro výběr nového nebo existujícího účtu úložiště.

## <a name="ui-sample"></a>Ukázka ui

Ovládací prvek zobrazuje výchozí hodnotu.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Ovládací prvek umožňuje uživateli vytvořit nový účet úložiště nebo vybrat existující účet úložiště.

![Microsoft.Storage.StorageAccountSelector nový](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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

- Pokud je `defaultValue.name` zadán, je automaticky ověřena pro jedinečnost. Pokud název účtu úložiště není jedinečný, musí uživatel zadat jiný název nebo zvolit existující účet úložiště.
- Výchozí hodnota `defaultValue.type` pro je **Premium_LRS**.
- Jakýkoli typ, `constraints.allowedTypes` který není zadán v písmenu a), je skrytý a je zobrazen jakýkoli typ, který není zadán v `constraints.excludedTypes` písmenu a). `constraints.allowedTypes`a `constraints.excludedTypes` jsou volitelné, ale nelze je použít současně.
- Pokud `options.hideExisting` je **true**, uživatel nemůže vybrat existující účet úložiště. Výchozí hodnota je **false** (nepravda).

## <a name="next-steps"></a>Další kroky
* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).
