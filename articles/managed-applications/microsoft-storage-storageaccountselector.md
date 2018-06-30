---
title: Azure elementu StorageAccountSelector uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Storage.StorageAccountSelector uživatelského rozhraní pro portál Azure.
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
ms.openlocfilehash: 5de536a562d234a4c463c862aedffc7c7ca5228d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112282"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Element Microsoft.Storage.StorageAccountSelector uživatelského rozhraní
Ovládací prvek pro výběr účtu nový nebo existující úložiště.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Ovládací prvek zobrazí výchozí hodnotu.

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

## <a name="remarks"></a>Poznámky
- -Li zadána, `defaultValue.name` dojde k automatickému ověření jedinečnosti. Pokud název účtu úložiště není jedinečný, musí uživatel zadejte jiný název nebo vybrat existující účet úložiště.
- Výchozí hodnota pro `defaultValue.type` je **Premium_LRS**.
- Žádný typ, nebyly zadány v `constraints.allowedTypes` skryt a jakýmikoli nebyly zadány v `constraints.excludedTypes` se zobrazí. `constraints.allowedTypes` a `constraints.excludedTypes` obě jsou nepovinné, ale nelze používat současně.
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
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).
