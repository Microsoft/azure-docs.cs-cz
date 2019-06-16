---
title: Azure prvek uživatelského rozhraní StorageAccountSelector | Dokumentace Microsoftu
description: Popisuje element Microsoft.Storage.StorageAccountSelector uživatelského rozhraní pro Azure portal.
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
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: c6d4ef50645902aecd57ceb9fc48b7d99bf22d53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104852"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Storage.StorageAccountSelector
Ovládací prvek pro výběr nového nebo existujícího účtu.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Ovládací prvek zobrazuje výchozí hodnotu.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Ovládací prvek umožňuje uživateli vytvořit nový účet úložiště nebo vyberte existující účet úložiště.

![Microsoft.Storage.StorageAccountSelector new](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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

## <a name="remarks"></a>Poznámky
- Je-li zadána, `defaultValue.name` ser ověří automaticky jedinečný. Pokud název účtu úložiště není jedinečný, musí uživatel zadejte jiný název nebo zvolte existující účet úložiště.
- Výchozí hodnota pro `defaultValue.type` je **Premium_LRS**.
- Jakýkoli typ není zadáno v `constraints.allowedTypes` je skrytá a libovolný typ není zadáno v `constraints.excludedTypes` se zobrazí. `constraints.allowedTypes` a `constraints.excludedTypes` jsou volitelné, ale nelze používat současně.
- Pokud `options.hideExisting` je **true**, uživatel nemůže vybrat existující účet úložiště. Výchozí hodnota je **false**.

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).
